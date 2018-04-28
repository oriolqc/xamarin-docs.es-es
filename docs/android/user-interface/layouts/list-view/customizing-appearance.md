---
title: Personalizar la apariencia de un control ListView
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: f4d737e31304a88f3ec293f90267a35ed1f2c7a5
ms.sourcegitcommit: a69439ad4c9fd0abe759143687d3b23582573d90
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="customizing-a-listviews-appearance"></a>Personalizar la apariencia de un control ListView


## <a name="overview"></a>Información general

La apariencia de un control ListView viene determinada por el diseño de las filas que se va a mostrar. Para cambiar la apariencia de un `ListView`, usar un diseño de fila diferente.


## <a name="built-in-row-views"></a>Vistas de fila integrado

Hay doce vistas integradas que pueden hacer referencia mediante **Android.Resource.Layout**:

- **TestListItem** &ndash; una línea de texto con un formato mínimo.

- **SimpleListItem1** &ndash; una línea de texto.

- **SimpleListItem2** &ndash; dos líneas de texto.

- **SimpleSelectableListItem** &ndash; una línea de texto que admite la selección de uno o varios elementos (agregado en el nivel de API 11).

- **SimpleListItemActivated1** &ndash; Similar a SimpleListItem1, pero el color de fondo indica cuando se selecciona una fila (agregado en el nivel de API 11).

- **SimpleListItemActivated2** &ndash; Similar a SimpleListItem2, pero el color de fondo indica cuando se selecciona una fila (agregado en el nivel de API 11).

- **SimpleListItemChecked** &ndash; muestra marcas de verificación para indicar la selección.

- **SimpleListItemMultipleChoice** &ndash; muestra las casillas de verificación para indicar la selección de varias respuestas.

- **SimpleListItemSingleChoice** &ndash; muestra botones para indicar la selección se excluyen mutuamente.

- **TwoLineListItem** &ndash; dos líneas de texto.

- **ActivityListItem** &ndash; una línea de texto con una imagen.

- **SimpleExpandableListItem** &ndash; pueden expandir o contraer grupos de filas por categorías y cada grupo.

Cada vista de fila integrado tiene un estilo integrado asociado a él. Estas capturas de pantalla muestran cómo aparece cada vista:

[![Capturas de pantalla de TestListItem, SimpleSelectableListItem, SimpleListitem1 y SimpleListItem2](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![Capturas de pantalla de SimpleListItemActivated1, SimpleListItemActivated2, SimpleListItemChecked y SimpleListItemMultipleChecked](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![Capturas de pantalla de SimpleListItemSingleChoice, TwoLineListItem, ActivityListItem y SimpleExpandableListItem](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

El **BuiltInViews/HomeScreenAdapter.cs** archivo de ejemplo (en el **BuiltInViews** solución) contiene el código para generar las pantallas de elemento de lista no se puede expandir. La vista se establece el `GetView` método similar al siguiente:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

A continuación, se pueden establecer propiedades de la vista haciendo referencia a los identificadores de control estándar `Text1`, `Text2` y `Icon` en `Android.Resource.Id` (sin establecer propiedades que no contiene la vista o se producirá una excepción):

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

A continuación, se pueden establecer las propiedades de la vista de grupo y la vista secundaria haciendo referencia a la norma `Text1` y `Text2` controlar identificadores como se indicó anteriormente. La captura de pantalla de SimpleExpandableListItem (que se muestra arriba) proporciona un ejemplo de una vista de grupo de una línea (SimpleExpandableListItem1) y una vista de dos líneas secundarios (SimpleExpandableListItem2). Como alternativa, la vista de grupo se puede configurar para dos líneas (SimpleExpandableListItem2) y la vista secundaria puede configurarse para una línea (SimpleExpandableListItem1), o ambos grupo Vista y vista de elemento secundario puede tener el mismo número de líneas. 



## <a name="accessories"></a>Accesorios

Las filas pueden tener accesorios que se agregan a la derecha de la vista para indicar el estado de selección:

- **SimpleListItemChecked** &ndash; crea una lista de selección única con una comprobación de que el indicador.

- **SimpleListItemSingleChoice** &ndash; crea listas de tipo de botón de radio que solo una opción sea posible.

- **SimpleListItemMultipleChoice** &ndash; crea listas de tipo de casilla de verificación que son posibles varias opciones.

Los accesorios mencionados anteriormente se ilustran en las siguientes pantallas, en su orden correspondiente:

[![Capturas de pantalla de SimpleListItemChecked, SimpleListItemSingleChoice y SimpleListItemMultipleChoice con accesorios](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

Para mostrar uno de estos accesorios pase el identificador de recurso de diseño necesarias para el adaptador, a continuación, establece manualmente el estado de selección de las filas solicitadas. Esta línea de código muestra cómo crear y asignar un `Adapter` mediante uno de estos diseños:

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

El `ListView` sí es compatible con diferentes modos de selección, sin tener en cuenta el descriptor de acceso que se va a mostrar. Para evitar confusiones, use `Single` el modo de selección con `SingleChoice` Accesorios y `Checked` o `Multiple` modo con el `MultipleChoice` estilo. El modo de selección se controla mediante la `ChoiceMode` propiedad de la `ListView`.


### <a name="handling-api-level"></a>Control de nivel de API

Las versiones anteriores de Xamarin.Android implementan enumeraciones como propiedades de entero. La versión más reciente presenta a los tipos de enumeración de .NET pertinentes que resulta más fácil detectar las posibles opciones.

Dependiendo de qué nivel de API se dirige a, `ChoiceMode` es un entero o una enumeración. El archivo de ejemplo **AccessoryViews/HomeScreen.cs** con un bloque de comentario si desea tener como destino la API de monigote:

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```


### <a name="selecting-items-programmatically"></a>Seleccionar elementos mediante programación

Establecer manualmente los elementos que son 'seleccionado' se realiza con el `SetItemChecked` método (puede recibir varias llamadas de selección múltiple):

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

El código también debe detectar selecciones únicas de manera diferente a las selecciones múltiples. Para determinar qué fila se ha seleccionado en `Single` modo use la `CheckedItemPosition` propiedad de entero:

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

Para determinar qué filas se han seleccionado en `Multiple` modo necesario para recorrer en bucle la `CheckedItemPositions` `SparseBooleanArray`. Una matriz dispersa es como un diccionario que contiene solo las entradas que ha cambiado el valor, por lo que debe atravesar la matriz completa buscando `true` valores saber lo que se ha seleccionado en la lista como se muestra en el siguiente fragmento de código:

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```


## <a name="creating-custom-row-layouts"></a>La creación de diseños de fila personalizado

Las cuatro vistas de fila integradas son muy simples. Para mostrar los diseños más complejos (por ejemplo, una lista de los correos electrónicos, o tweets o información de contacto) se requiere una vista personalizada. Vistas personalizadas generalmente se declaran como archivos AXML en el **recursos/diseño** directorio y, a continuación, cargar mediante su identificador de un adaptador personalizado de recursos. La vista puede contener cualquier número de clases de presentación (por ejemplo, TextViews, ImageViews y otros controles) con el diseño, las fuentes y colores personalizados.

En este ejemplo se diferencia de los ejemplos anteriores de varias maneras:

-  Hereda de `Activity` , no `ListActivity` . Puede personalizar filas para cualquier `ListView` ; sin embargo, también pueden incluirse otros controles en un `Activity` diseño (por ejemplo, un encabezado, botones ni otros elementos de interfaz de usuario). Este ejemplo agrega un encabezado anterior el `ListView` para mostrar.

-  Requiere un archivo de diseño AXML para la pantalla; en los ejemplos anteriores el `ListActivity` no requiere un archivo de diseño. Este AXML contiene un `ListView` controlar la declaración.

-  Requiere un archivo de diseño AXML para representar cada fila. Este archivo AXML contiene los controles de texto e imagen con la configuración de color y fuente personalizada.

-  Utiliza un archivo XML de selector personalizado opcional para establecer el aspecto de la fila cuando se selecciona.

-  El `Adapter` implementación devuelve un diseño personalizado de la `GetView` invalidar.

-  `ItemClick` debe declararse de forma diferente (un controlador de eventos se adjunta a `ListView.ItemClick` en lugar de un reemplazo `OnListItemClick` en `ListActivity`).


Estos cambios se detallan a continuación, a partir de creación de vista de la actividad y la vista de fila personalizado y, a continuación, que tratan sobre las modificaciones en el adaptador y la actividad para representarlos.


### <a name="adding-a-listview-to-an-activity-layout"></a>Agregar un control ListView a un diseño de actividad

Dado que `HomeScreen` ya no hereda de `ListActivity` no tiene una vista predeterminada, por lo que debe crearse un archivo AXML de diseño para la vista del HomeScreen. En este ejemplo, la vista tendrá un encabezado (con un `TextView`) y un `ListView` para mostrar los datos. El diseño se define en el **Resources/Layout/HomeScreen.axml** archivo que se muestra aquí:

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

La ventaja de utilizar un `Activity` con un diseño personalizado (en lugar de un `ListActivity`) reside en su capacidad agregar controles adicionales a la pantalla, como el encabezado `TextView` en este ejemplo.


### <a name="creating-a-custom-row-layout"></a>Crear un diseño de fila personalizado

Otro archivo de diseño AXML es necesario para contener el diseño personalizado para cada fila que va a aparecer en la vista de lista. En este ejemplo la fila tendrá un fondo verde, marrón texto e imagen alineado a la derecha. El marcado XML Android para declarar este diseño se describe en **Resources/Layout/CustomView.axml**:

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

Mientras que un diseño de fila personalizado puede contener muchos controles diferentes, desplazamiento rendimiento puede verse afectado por diseños complejos y uso de imágenes (especialmente si tienen que cargarse a través de la red). Vea el artículo de Google para obtener más información sobre solucionando problemas de rendimiento de desplazamiento.


### <a name="referencing-a-custom-row-view"></a>Hacer referencia a una vista de fila personalizado

La implementación del ejemplo de adaptador personalizado se encuentra en `HomeScreenAdapter.cs`. El método clave es `GetView` donde carga el AXML personalizado con el identificador de recurso `Resource.Layout.CustomView`y, a continuación, Establece propiedades en cada uno de los controles en la vista antes de devolverlo. Se muestra la clase de adaptador completa:

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


### <a name="referencing-the-custom-listview-in-the-activity"></a>Hacer referencia a la vista de lista personalizado en la actividad

Dado que la `HomeScreen` clase hereda ahora de `Activity`, un `ListView` campo se declara en la clase que contenga una referencia al control que se declara en el AXML:

```csharp
ListView listView;
```

La clase, a continuación, debe cargar el diseño personalizado de la actividad AXML mediante el `SetContentView` método. A continuación, puede encontrar el `ListView` control en el diseño, a continuación, crea y asigna el adaptador y asigna el controlador de clic. El código para el método de OnCreate se muestra aquí:

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

Por último, el `ItemClick` controlador debe definirse; en este caso se limita a mostrar un `Toast` mensaje:

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

La pantalla resultante tiene el siguiente aspecto:

[![Captura de pantalla de la CustomRowView resultante](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)



### <a name="customizing-the-row-selector-color"></a>Personalizar el Color del Selector de fila

Cuando una fila se toca debe aparecer resaltada comentarios sobre el usuario. Cuando se especifica una vista personalizada como color de fondo como **CustomView.axml** , también invalida el resalte de la selección. Esta línea de código en **CustomView.axml** establece el fondo en color verde claro, pero también significa que no hay ningún indicador visual cuando se toca la fila:

```xml
android:background="#FFDAFF7F"
```

Para volver a habilitar el comportamiento de resaltado y también para personalizar el color que se usa, establezca el atributo de fondo para un selector personalizado en su lugar. El selector declarará tanto el color de fondo predeterminado, así como el color de resaltado. El archivo **Resources/Drawable/CustomSelector.xml** contiene la siguiente declaración:

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

Una fila seleccionada y la correspondiente `Toast` mensaje ahora es similar al siguiente:

[![Una fila seleccionada en color naranja, con el mensaje de notificación del sistema muestra el nombre de la fila seleccionada](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)



### <a name="preventing-flickering-on-custom-layouts"></a>Evitar el parpadeo en los diseños personalizados

Android intenta mejorar el rendimiento de `ListView` desplazamiento almacenando en memoria caché información de diseño. Si tiene tiempo desplazamiento listas de datos también se debe establecer el el `android:cacheColorHint` propiedad en el `ListView` declaración de definición de la actividad AXML (en el mismo valor de color como fondo del diseño de la fila personalizado). Si no se incluye esta sugerencia podrían 'parpadeo' como el usuario se desplaza a través de una lista con los colores de fondo de fila personalizado.



## <a name="related-links"></a>Vínculos relacionados

- [BuiltInViews (ejemplo)](https://developer.xamarin.com/samples/BuiltInViews/)
- [AccessoryViews (ejemplo)](https://developer.xamarin.com/samples/AccessoryViews/)
- [CustomRowView (ejemplo)](https://developer.xamarin.com/samples/CustomRowView/)
