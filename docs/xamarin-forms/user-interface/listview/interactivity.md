---
title: Interactividad de ListView
description: En este artículo se explica cómo agregar interactividad a un ListView Xamarin.Forms mediante la implementación de las selecciones, acciones de contexto y Deslizar para actualizar.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/13/2018
ms.openlocfilehash: 77a48e36f33fc690306f5e590f9f4f3064fe1ddf
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2018
ms.locfileid: "39202947"
---
# <a name="listview-interactivity"></a>Interactividad de ListView

ListView es compatible con la interacción con los datos que presenta a través de los métodos siguientes:

- [**Selección & derivaciones** ](#selectiontaps) &ndash; responder a derivaciones y selecciones/deselections de elementos. Habilitar o deshabilitar la selección de fila (habilitada de forma predeterminada).
- [**Acciones de contexto** ](#Context_Actions) &ndash; exponer funcionalidad por cada elemento, por ejemplo, pasar el dedo para eliminar.
- [**Incorporación de cambios para actualizar** ](#Pull_to_Refresh) &ndash; implementar la expresión de deslizar para actualizar a la que los usuarios esperan experiencias nativas.

<a name="selectiontaps" />

## <a name="selection--taps"></a>Derivaciones de & selección

El [ `ListView` ](xref:Xamarin.Forms.ListView) modo de selección se controla estableciendo la [ `ListView.SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propiedad con un valor de la [ `ListViewSelectionMode` ](xref:Xamarin.Forms.ListViewSelectionMode) enumeración:

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) indica que se puede seleccionar un solo elemento con el elemento seleccionado se resalta. Este es el valor predeterminado.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) indica que no se pueden seleccionar elementos.

Cuando un usuario puntea un elemento, se activan dos eventos:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) se desencadena cuando se selecciona un elemento nuevo.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) se desencadena cuando se pulsa un elemento.

> [!NOTE]
> Pulsar dos veces el mismo elemento se desencadenarán dos [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) eventos, pero tendrá solo desencadena una sola [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) eventos.

Cuando el [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propiedad está establecida en [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single), los elementos de la [ `ListView` ](xref:Xamarin.Forms.ListView) puede seleccionarse el [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) y [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) se desencadena eventos y el [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propiedad se establecerá en el valor del elemento seleccionado.

Cuando el [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propiedad está establecida en [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), los elementos de la [ `ListView` ](xref:Xamarin.Forms.ListView) no puede seleccionarse el [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) no se desencadenará el evento y el [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propiedad permanecerá `null`. Sin embargo, [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) todavía se desencadena eventos y el elemento punteado aparecerán resaltado brevemente durante la derivación.

Cuando se selecciona un elemento y el [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) se cambia la propiedad de [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single) a [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propiedad se establecerá en `null` y [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) se desencadenará el evento con un `null` elemento.

El siguientes capturas de pantalla se muestra un [ `ListView` ](xref:Xamarin.Forms.ListView) con el modo de selección predeterminado:

![](interactivity-images/selection-default.png "ListView con selección habilitada")

### <a name="disabling-selection"></a>Deshabilitar selección

Para deshabilitar [ `ListView` ](xref:Xamarin.Forms.ListView) selección conjunto el [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propiedad [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

<a name="Context_Actions" />

## <a name="context-actions"></a>Acciones de contexto
A menudo, los usuarios deseen realizar acciones en un elemento en un `ListView`. Por ejemplo, considere la posibilidad de obtener una lista de mensajes de correo electrónico en la aplicación de correo. En iOS, puede deslizar para eliminar un mensaje::

![](interactivity-images/context-default.png "ListView con acciones de contexto")

Acciones de contexto se pueden implementar en C# y XAML. A continuación encontrará guías específicas para ambos, pero primero vamos a Eche un vistazo a algunos detalles de implementación clave para ambos.

Acciones de contexto se crean mediante `MenuItem`s. Los eventos TAP para MenuItems generados por el MenuItem, no el ListView. Esto es diferente de cómo se controlan los eventos tap para las celdas, donde el ListView provoca el evento en lugar de la celda. Dado que el ListView está provocando el evento, su controlador de eventos tiene información de clave, al igual que el elemento se ha seleccionado o pulsa.

De forma predeterminada, un elemento de menú no tiene ninguna manera de saber qué celda pertenece. `CommandParameter` está disponible en `MenuItem` para almacenar objetos, como el objeto subyacente ViewCell de MenuItem. `CommandParameter` se puede establecer en XAML y C#.

### <a name="c"></a>C#  

Acciones de contexto se pueden implementar en cualquier `Cell` subclase (siempre y cuando no se use como un encabezado de grupo) mediante la creación de `MenuItem`s y agregarlas a la `ContextActions` colección para la celda. Tiene las siguientes propiedades se pueden configurar para la acción de contexto:

* **Texto** &ndash; la cadena que aparece en el elemento de menú.
* **Hacer clic en** &ndash; el evento cuando se hace clic en el elemento.
* **IsDestructive** &ndash; (opcional) cuando sea true el elemento se representa de forma diferente, en iOS.

Se pueden agregar varias acciones de contexto a una celda, pero solo uno debe tener `IsDestructive` establecido en `true`. El código siguiente muestra cómo las acciones de contexto se agregarían a un `ViewCell`:

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

### <a name="xaml"></a>XAML

`MenuItem`s también se pueden crear mediante declaración en una colección de XAML. El XAML siguiente muestra una celda personalizada con dos acciones de contexto implementadas:

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore" CommandParameter="{Binding .}"
               Text="More" />
            <MenuItem Clicked="OnDelete" CommandParameter="{Binding .}"
               Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

En el archivo de código subyacente, asegúrese de que el `Clicked` se implementan los métodos:

```csharp
public void OnMore (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> El `NavigationPageRenderer` para Android tiene una reemplazable `UpdateMenuItemIcon` método que se puede usar para cargar los iconos de un personalizado `Drawable`. Esta invalidación permite usar las imágenes SVG como iconos en `MenuItem` instancias en Android.

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>Deslizar para actualizar
Los usuarios han llegado a esperar que extraer en una lista de datos se actualizará esa lista. `ListView` es compatible con esta-de-fábrica. Para habilitar la funcionalidad de deslizar para actualizar, establezca `IsPullToRefreshEnabled` en true:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Extrae la extracción de actualización como el usuario:

![](interactivity-images/refresh-start.png "Incorporación de cambios de ListView a actualizar en curso")

Incorporación de cambios de actualización como el usuario ha publicado la incorporación de cambios. Esto es lo que ve el usuario mientras se está actualizando la lista: ![](interactivity-images/refresh-in-progress.png "extracción ListView a actualizar completa")

ListView expone algunos eventos que le permiten dar respuesta a eventos de deslizar para actualizar.

-  El `RefreshCommand` se va a invocar y `Refreshing` evento denominado. `IsRefreshing` se establecerá en `true`.
-  Debe realizar cualquier código es necesario para actualizar el contenido de la vista de lista, en el comando o evento.
-  Al actualizar es completar, llame a `EndRefresh` o establecer `IsRefreshing` a `false` para indicar a la vista de lista que haya terminado.

El `CanExecute` se respetan propiedades, que proporciona un medio para controlar si el comando de actualización de incorporación de cambios debe estar habilitado.



## <a name="related-links"></a>Vínculos relacionados

- [Interactividad de ListView (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [notas de la versión 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [notas de la versión 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
