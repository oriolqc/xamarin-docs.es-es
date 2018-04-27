---
title: Interactividad de ListView
description: Agregar interactividad a la vista de lista mediante la implementación de las selecciones, deslice el dedo para eliminar y actualización de extracción.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: d16c45b7f7cb7eef67a905b6fbcb9b8675b0b2ba
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="listview-interactivity"></a>Interactividad de ListView

ListView admite interactuar con los datos que y se muestra a través de los siguientes medios:

- [**Selección & derivaciones** ](#selectiontaps) &ndash; responder a derivaciones y selecciones/deselections de elementos. Habilitar o deshabilitar la selección de fila (habilitada de forma predeterminada).
- [**Acciones de contexto** ](#Context_Actions) &ndash; exponen funcionalidad por producto, por ejemplo, deslice el dedo para eliminar.
- [**Actualización de extracción** ](#Pull_to_Refresh) &ndash; implementar la expresión de actualización de extracción que los usuarios esperan de experiencias nativo.

<a name="selectiontaps" />

## <a name="selection--taps"></a>Selección & derivaciones
`ListView` admite la selección de un elemento a la vez. Selección está activada de forma predeterminada. Cuando un usuario puntee en un elemento, se activan dos eventos: `ItemTapped` y `ItemSelected`. Tenga en cuenta que al puntear en el mismo elemento dos veces no se activarán varios `ItemSelected` eventos, pero se activará varios `ItemTapped` eventos. Tenga en cuenta también que `ItemSelected` se llamará si se anula la selección de un elemento.

Tenga en cuenta que `ItemSelected` se llama cuando se anula la selección de elementos y cuando se seleccionan. Esto significa que debe comprobar si hay valores null `SelectedItem` en su `ItemSelected` controlador de eventos antes de utilizarla:

```csharp
void OnSelection (object sender, SelectedItemChangedEventArgs e)
{
  if (e.SelectedItem == null) {
    return; //ItemSelected is called on deselection, which results in SelectedItem being set to null
  }
  DisplayAlert ("Item Selected", e.SelectedItem.ToString (), "Ok");
  //((ListView)sender).SelectedItem = null; //uncomment line if you want to disable the visual selection state.
}
```

### <a name="disabling-selection"></a>Deshabilitar selección

Si desea deshabilitar la selección, controlar el `ItemSelected` eventos y establezca la `SelectedItem` propiedad en null:

```csharp
SelectionDemoList.ItemSelected += (sender, e) => {
    ((ListView)sender).SelectedItem = null;
};
```

Con selección habilitada:

![](interactivity-images/selection-default.png "ListView con selección habilitada")

<a name="Context_Actions" />

## <a name="context-actions"></a>Acciones de contexto
A menudo, los usuarios desearán realizar acciones en un elemento en un `ListView`. Por ejemplo, considere la posibilidad de obtener una lista de mensajes de correo electrónico en la aplicación de correo electrónico. En iOS, puede deslizar rápidamente para eliminar un mensaje::

![](interactivity-images/context-default.png "ListView con acciones de contexto")

Acciones de contexto se pueden implementar en C# y XAML. A continuación encontrará guías específicas en ambos casos, pero primero vamos a Eche un vistazo a algunos detalles de implementación clave para ambos.

Acciones de contexto se crean mediante `MenuItem`s. Eventos de derivación para objetos MenuItem se generan mediante el MenuItem, no en la vista de lista. Esto es diferente de cómo se administran los eventos de derivación para las celdas, donde el control ListView genera el evento en lugar de la celda. Dado que la vista de lista está generando el evento, su controlador de eventos se proporciona información de clave, al igual que el elemento se ha seleccionado o puntea.

De forma predeterminada, un objeto MenuItem no tiene ninguna manera de saber qué celda que pertenece. `CommandParameter` está disponible en `MenuItem` para almacenar objetos, como el objeto detrás ViewCell del MenuItem. `CommandParameter` se puede establecer en XAML y C#.

### <a name="c"></a>C#  

Acciones de contexto se pueden implementar en cualquier `Cell` subclase (si no la está usando como un encabezado de grupo) mediante la creación de `MenuItem`s y agregándolos a la `ContextActions` colección para la celda. Tiene las siguientes propiedades se pueden configurar para la acción de contexto:

* **Texto** &ndash; la cadena que aparece en el elemento de menú.
* **Hacer clic en** &ndash; el evento cuando se hace clic en el elemento.
* **IsDestructive** &ndash; (opcional) cuando sea true el elemento se representa de manera diferente, en iOS.

Varias acciones de contexto pueden agregarse a una celda, sin embargo, debe tener solo una `IsDestructive` establecido en `true`. El código siguiente muestra cómo se debe agregar acciones de contexto para un `ViewCell`:

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

`MenuItem`s también se pueden crear mediante declaración en una colección de XAML. El código XAML siguiente muestra una celda personalizada con dos acciones de contexto implementadas:

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

En el archivo de código subyacente, asegúrese del `Clicked` se implementan métodos:

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

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>En la actualización de extracción
Los usuarios han llegado a esperar que extraer hacia abajo en una lista de datos se actualizará dicha lista. `ListView` es compatible con esta out-of-the-box. Para habilitar la funcionalidad de actualización de extracción, establezca `IsPullToRefreshEnabled` en true:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Extrae la extracción de actualización como el usuario:

![](interactivity-images/refresh-start.png "Extracción de ListView para actualizar en curso")

Extracción de actualización que el usuario ha publicado la extracción. Esto es lo que ve el usuario mientras se está actualizando la lista: ![ ] (interactivity-images/refresh-in-progress.png "extracción ListView para actualizar completa")

ListView expone algunos eventos que le permiten responder a eventos de actualización de extracción.

-  El `RefreshCommand` se invocará y `Refreshing` evento denominado. `IsRefreshing` se establecerá en `true`.
-  Debe realizar cualquier código es necesario para actualizar el contenido de la vista de lista, ya sea en el comando o el evento.
-  Al actualizar es completar, llame a `EndRefresh` o establecer `IsRefreshing` a `false` para indicar a la vista de lista que haya terminado.

El `CanExecute` se respeta propiedad, que proporciona un medio para controlar si se debe habilitar el comando de actualización de extracción.



## <a name="related-links"></a>Vínculos relacionados

- [ListView interactividad (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [notas de la versión 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [notas de la versión 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
