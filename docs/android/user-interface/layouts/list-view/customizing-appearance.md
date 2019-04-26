---
title: Personalización de la apariencia de ListView
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: fef81fb5e5d2de79508b43a5612bf56af68d0772
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61178484"
---
# <a name="customizing-a-listviews-appearance"></a>Personalización de la apariencia de ListView


## <a name="overview"></a>Información general

La apariencia de un ListView viene determinado por el diseño de las filas que se muestran. Para cambiar la apariencia de un `ListView`, usar el diseño de una fila diferente.


## <a name="built-in-row-views"></a>Vistas de fila integradas

Hay doce vistas integradas que pueden hacer referencia mediante **Android.Resource.Layout**:

- **TestListItem** &ndash; una línea de texto con formato mínimo.

- **SimpleListItem1** &ndash; una línea de texto.

- **SimpleListItem2** &ndash; dos líneas de texto.

- **SimpleSelectableListItem** &ndash; una línea de texto que admite la selección de uno o varios elementos (agregado en el nivel de API 11).

- **SimpleListItemActivated1** &ndash; Similar a SimpleListItem1, pero el color de fondo indica cuando se selecciona una fila (agregado en el nivel de API 11).

- **SimpleListItemActivated2** &ndash; Similar a SimpleListItem2, pero el color de fondo indica cuando se selecciona una fila (agregado en el nivel de API 11).

- **SimpleListItemChecked** &ndash; muestra marcas de verificación para indicar la selección.

- **SimpleListItemMultipleChoice** &ndash; muestra las casillas de verificación para indicar la selección múltiple.

- **SimpleListItemSingleChoice** &ndash; muestra botones para indicar la selección se excluyen mutuamente.

- **TwoLineListItem** &ndash; dos líneas de texto.

- **ActivityListItem** &ndash; una línea de texto con una imagen.

- **SimpleExpandableListItem** &ndash; pueden expandir o contraer grupos de filas por categorías y cada grupo.

Cada vista integrada de fila tiene un estilo integrado asociado con él. Estas capturas de pantalla muestran cómo aparece cada vista:

[![Capturas de pantalla de TestListItem, SimpleSelectableListItem, SimpleListitem1 y SimpleListItem2](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![Capturas de pantalla de SimpleListItemActivated1, SimpleListItemActivated2, SimpleListItemChecked y SimpleListItemMultipleChecked](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![Capturas de pantalla de SimpleListItemSingleChoice, TwoLineListItem, ActivityListItem y SimpleExpandableListItem](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

El **BuiltInViews/HomeScreenAdapter.cs** archivo de ejemplo (en el **BuiltInViews** solución) contiene el código para generar las pantallas de elemento de lista no es expansible. La vista se establece el `GetView` método similar al siguiente:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

A continuación, se pueden establecer propiedades de la vista haciendo referencia a los identificadores de control estándar `Text1`, `Text2` y `Icon` en `Android.Resource.Id` (sin establecer las propiedades que no contiene la vista o se producirá una excepción):

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

El **BuiltInExpandableViews/ExpandableScreenAdapter.cs** archivo de ejemplo (en el **BuiltInViews** solución) contiene el código para generar la pantalla SimpleExpandableListItem. La vista de grupo se establece el `GetGroupView` método similar al siguiente:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

La vista secundaria se establece el `GetChildView` método similar al siguiente:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

A continuación, se pueden establecer las propiedades de la vista de grupo y la vista secundaria haciendo referencia a la norma `Text1` y `Text2` controlan los identificadores como se indicó anteriormente. La captura de pantalla de SimpleExpandableListItem (mostrado arriba) proporciona un ejemplo de una vista de grupo de una línea (SimpleExpandableListItem1) y una vista de dos líneas secundarios (SimpleExpandableListItem2). Como alternativa, la vista de grupo se puede configurar para dos líneas (SimpleExpandableListItem2) y la vista secundaria puede configurarse para una sola línea (SimpleExpandableListItem1), o ambos grupo a vista y vista secundaria puede tener el mismo número de líneas. 



## <a name="accessories"></a>Accesorios

Las filas pueden tener accesorios que se agregan a la derecha de la vista para indicar el estado de selección:

- **SimpleListItemChecked** &ndash; crea una lista de selección única con una comprobación de que el indicador.

- **SimpleListItemSingleChoice** &ndash; crea listas de tipo de botón de radio que sólo una opción sea posible.

- **SimpleListItemMultipleChoice** &ndash; crea listas de tipo de checkbox que son posibles varias opciones.

Los accesorios mencionados anteriormente se muestran en las pantallas siguientes, en su orden correspondiente:

[![Capturas de pantalla de SimpleListItemChecked SimpleListItemSingleChoice y SimpleListItemMultipleChoice con accesorios](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

Para mostrar una de estas pass accesorios el identificador de recurso de diseño necesarias para el adaptador, a continuación, establece manualmente el estado de selección de las filas necesarias. Esta línea de código muestra cómo crear y asignar un `Adapter` mediante uno de estos diseños:

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

El `ListView` sí admite los modos de selección diferentes, sin tener en cuenta el accesorio que se muestran. Para evitar confusiones, utilizar `Single` modo de selección con `SingleChoice` Accesorios y `Checked` o `Multiple` modo con el `MultipleChoice` estilo. El modo de selección se controla mediante el `ChoiceMode` propiedad de la `ListView`.


### <a name="handling-api-level"></a>Control de nivel de API

Las versiones anteriores de Xamarin.Android implementan enumeraciones como las propiedades de entero. La versión más reciente ha introducido a tipos de enumeración correcta de .NET que facilita enormemente detectar las posibles opciones.

Dependiendo de qué nivel de API de destino, `ChoiceMode` es un entero o una enumeración. El archivo de ejemplo **AccessoryViews/HomeScreen.cs** ha un bloque de comentarios si desea tener como destino la API de jengibre:

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = 1; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```


### <a name="selecting-items-programmatically"></a>Seleccionar elementos mediante programación

Configuración manual de los elementos que son 'seleccionado' se realiza con el `SetItemChecked` método (puede llamarse varias veces para selección múltiple):

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

También necesita el código detectar las selecciones únicas de manera diferente a las selecciones múltiples. Para determinar qué fila se ha seleccionado en `Single` uso del modo el `CheckedItemPosition` propiedad de entero:

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

Para determinar qué filas se han seleccionado en `Multiple` modo necesario para recorrer en iteración el `CheckedItemPositions` `SparseBooleanArray`. Una matriz dispersa es como un diccionario que contiene solo las entradas donde el valor ha cambiado, por lo que debe recorrer la matriz completa busca `true` valores saber lo que se ha seleccionado en la lista como se muestra en el siguiente fragmento de código:

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```


## <a name="creating-custom-row-layouts"></a>Creación de diseños de fila personalizado

Las cuatro vistas de fila integrados son muy sencillas. Para mostrar los diseños más complejos (por ejemplo, una lista de correos electrónicos, tweets o información de contacto) se requiere una vista personalizada. Vistas personalizadas normalmente se declaran como archivos AXML en el **y diseño de los recursos** directorio y, a continuación, cargar mediante su identificador de un adaptador personalizado de recursos. La vista puede contener cualquier número de clases de presentación (por ejemplo, objetos TextView, ImageViews y otros controles) con el diseño, las fuentes y colores personalizados.

En este ejemplo difiere de los ejemplos anteriores de varias maneras:

-  Hereda de `Activity` , no `ListActivity` . Puede personalizar las filas para cualquier `ListView` ; sin embargo, también se pueden incluir otros controles en un `Activity` diseño (como un título, botones u otros elementos de interfaz de usuario). Este ejemplo agrega un encabezado anterior el `ListView` para mostrar.

-  Requiere un archivo de diseño AXML para la pantalla; en los ejemplos anteriores el `ListActivity` no requiere un archivo de diseño. Este AXML contiene un `ListView` controlar la declaración.

-  Requiere un archivo de diseño AXML para representar cada fila. Este archivo AXML contiene los controles de texto e imagen con la configuración de color y fuente personalizada.

-  Utiliza un archivo XML de selector personalizado opcional para establecer la apariencia de la fila cuando está seleccionado.

-  El `Adapter` implementación devuelve un diseño personalizado de la `GetView` invalidar.

-  `ItemClick` debe declararse de forma diferente (un controlador de eventos se adjunta a `ListView.ItemClick` en lugar de una invalidación `OnListItemClick` en `ListActivity`).


Estos cambios se detallan a continuación, comenzando con la creación de vistas de la actividad y de fila personalizado y, a continuación, que abarcan las modificaciones en el adaptador y la actividad para representarlos.


### <a name="adding-a-listview-to-an-activity-layout"></a>Adición de un ListView a un diseño de actividad

Dado que `HomeScreen` ya no hereda de `ListActivity` no tiene una vista predeterminada, por lo que debe crearse un archivo de diseño AXML para la vista del HomeScreen. En este ejemplo, la vista tendrá un encabezado (mediante un `TextView`) y un `ListView` para mostrar los datos. El diseño se define en el **Resources/Layout/HomeScreen.axml** archivo que se muestra aquí:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent">
    <TextView android:id="@+id/Heading"
        android:text="Vegetable Groups"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="#00000000"
        android:textSize="30dp"
        android:textColor="#FF267F00"
        android:textStyle="bold"
        android:padding="5dp"
    />
    <ListView android:id="@+id/List"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:cacheColorHint="#FFDAFF7F"
    />
</LinearLayout>
```

La ventaja de usar un `Activity` con un diseño personalizado (en lugar de un `ListActivity`) reside en la posibilidad de agregar controles adicionales a la pantalla, como el encabezado `TextView` en este ejemplo.


### <a name="creating-a-custom-row-layout"></a>Creación de una plantilla de fila personalizado

Otro archivo de diseño AXML es necesario para contener el diseño personalizado para cada fila que va a aparecer en la vista de lista. En este ejemplo la fila tendrá un fondo verde, marrón texto e imagen alineada a la derecha. El marcado XML de Android para declarar este diseño se describe en **Resources/Layout/CustomView.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout  xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
   android:background="#FFDAFF7F"
   android:padding="8dp">
    <LinearLayout android:id="@+id/Text"
       android:orientation="vertical"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:paddingLeft="10dip">
        <TextView
         android:id="@+id/Text1"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textColor="#FF7F3300"
         android:textSize="20dip"
         android:textStyle="italic"
         />
        <TextView
         android:id="@+id/Text2"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textSize="14dip"
         android:textColor="#FF267F00"
         android:paddingLeft="100dip"
         />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout >
```

Aunque un diseño personalizado fila puede contener muchos controles diferentes, el rendimiento del desplazamiento puede verse afectado por diseños complejos y uso de imágenes (especialmente si tienen que cargarse a través de la red). Vea el artículo de Google para obtener más información sobre solución de problemas de rendimiento desplazable.


### <a name="referencing-a-custom-row-view"></a>Hacer referencia a una vista de fila personalizado

La implementación del ejemplo de adaptador personalizado se encuentra en `HomeScreenAdapter.cs`. El método clave es `GetView` donde carga el AXML personalizado mediante el identificador de recurso `Resource.Layout.CustomView`y, a continuación, Establece las propiedades en cada uno de los controles en la vista antes de devolverlo. Se muestra la clase de adaptador completa:

```csharp
public class HomeScreenAdapter : BaseAdapter<TableItem> {
   List<TableItem> items;
   Activity context;
   public HomeScreenAdapter(Activity context, List<TableItem> items)
       : base()
   {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
   {
       return position;
   }
   public override TableItem this[int position]
   {
       get { return items[position]; }
   }
   public override int Count
   {
       get { return items.Count; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       var item = items[position];
       View view = convertView;
       if (view == null) // no view to re-use, create new
           view = context.LayoutInflater.Inflate(Resource.Layout.CustomView, null);
       view.FindViewById<TextView>(Resource.Id.Text1).Text = item.Heading;
       view.FindViewById<TextView>(Resource.Id.Text2).Text = item.SubHeading;
       view.FindViewById<ImageView>(Resource.Id.Image).SetImageResource(item.ImageResourceId);
       return view;
   }
}
```


### <a name="referencing-the-custom-listview-in-the-activity"></a>Hacer referencia a la ListView en la actividad personalizada

Dado que el `HomeScreen` clase ahora se hereda de `Activity`, un `ListView` campo se declara en la clase que contenga una referencia al control declarado en el AXML:

```csharp
ListView listView;
```

La clase, a continuación, debe cargar el diseño personalizado de la actividad AXML utilizando el `SetContentView` método. A continuación, puede encontrar el `ListView` control en el diseño, a continuación, crea y asigna el adaptador y asigna el controlador de clics. El código para el método OnCreate se muestra aquí:

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

Por último el `ItemClick` debe definirse el controlador; en este caso se limita a mostrar un `Toast` mensaje:

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

La pantalla resultante tendrá este aspecto:

[![Captura de pantalla de la CustomRowView resultante](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)



### <a name="customizing-the-row-selector-color"></a>Personalizar el Color del Selector de fila

Cuando se toca una fila debe aparecer resaltada para comentarios del usuario. Cuando se especifica una vista personalizada como color de fondo como **CustomView.axml** , también invalida el área resaltada de selección. Esta línea de código en **CustomView.axml** establece el fondo a verde claro, pero también significa que no hay ningún indicador visual cuando se toca la fila:

```xml
android:background="#FFDAFF7F"
```

Para volver a habilitar el comportamiento de resaltado y también para personalizar el color que se usa, establezca el atributo background a un selector personalizado en su lugar. El selector declarará el color de fondo predeterminada así como el color de resaltado. El archivo **Resources/Drawable/CustomSelector.xml** contiene la siguiente declaración:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_pressed="false"
  android:state_selected="false"
  android:drawable="@color/cellback" />
<item android:state_pressed="true" >
  <shape>
     <gradient
      android:startColor="#E77A26"
        android:endColor="#E77A26"
        android:angle="270" />
  </shape>
</item>
<item android:state_selected="true"
  android:state_pressed="false"
  android:drawable="@color/cellback" />
</selector>
```

Para hacer referencia en el selector personalizado, cambie el atributo background en **CustomView.axml** para:

```xml
android:background="@drawable/CustomSelector"
```

Una fila seleccionada y la correspondiente `Toast` ahora este aspecto del mensaje:

[![Una fila seleccionada en color naranja, con el mensaje de notificación del sistema Mostrar nombre de la fila seleccionada](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)



### <a name="preventing-flickering-on-custom-layouts"></a>Impedir el parpadeo en los diseños personalizados

Intentos de Android mejorar el rendimiento de `ListView` desplazamiento almacenando en caché la información de diseño. Si tienes larga desplazamiento listas de datos también se debe establecer el `android:cacheColorHint` propiedad en el `ListView` declaración de definición de la actividad AXML (en el mismo valor de color como fondo del diseño de la fila personalizado). No debe incluir esta sugerencia se podría producir un 'parpadeo' como el usuario se desplaza a través de una lista con los colores de fondo de fila personalizado.



## <a name="related-links"></a>Vínculos relacionados

- [BuiltInViews (sample)](https://developer.xamarin.com/samples/BuiltInViews/)
- [AccessoryViews (ejemplo)](https://developer.xamarin.com/samples/AccessoryViews/)
- [CustomRowView (sample)](https://developer.xamarin.com/samples/CustomRowView/)
