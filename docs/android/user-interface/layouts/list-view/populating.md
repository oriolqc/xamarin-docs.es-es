---
title: Relleno de ListView con datos
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: f3162c4da092048cd409f7b32438bc85dcedff19
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740809"
---
# <a name="populating-a-listview-with-data"></a>Relleno de ListView con datos


## <a name="overview"></a>Información general

Para agregar filas a una `ListView` deberá agregarlo a su diseño e implementar un `IListAdapter` con métodos que el `ListView` llamadas se rellene automáticamente. Android incorpora `ListActivity` y `ArrayAdapter` clases que se pueden utilizar sin definir cualquier código o un diseño personalizado de XML. El `ListActivity` clase crea automáticamente un `ListView` y expone un `ListAdapter` propiedad para suministrar las vistas de fila para mostrar a través de un adaptador.

Los adaptadores integrados toman un identificador de recurso de vista como un parámetro que se utiliza para cada fila. Puede usar recursos integrados, como las de `Android.Resource.Layout` por lo que no tiene que escribir su propio.


## <a name="using-listactivity-and-arrayadapterltstringgt"></a>Uso de ListActivity y ArrayAdapter&lt;cadena&gt;

El ejemplo **BasicTable/HomeScreen.cs** muestra cómo utilizar estas clases para mostrar un `ListView` en sólo unas pocas líneas de código:

```csharp
[Activity(Label = "BasicTable", MainLauncher = true, Icon = "@drawable/icon")]
public class HomeScreen : ListActivity {
   string[] items;
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       items = new string[] { "Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers" };
       ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItem1, items);
   }
   protected override void OnListItemClick(ListView l, View v, int position, long id)
}
```


### <a name="handling-row-clicks"></a>Controlar fila hace clic en

Normalmente, un `ListView` también permitirá al usuario tener acceso a una fila para llevar a cabo alguna acción (por ejemplo, reproducir una canción, o llamar a un contacto o mostrar otra pantalla). Para responder a los toques del usuario debe haber uno más método implementan en el `ListActivity` &ndash; `OnListItemClick` &ndash; similar al siguiente:

[![Captura de pantalla de un SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Ahora el usuario puede tocar una fila y un `Toast` aparecerá la alerta:

[![Captura de pantalla de aviso que aparece cuando se toca una fila](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>Implementar un ListAdapter

`ArrayAdapter<string>` es fantástico debido a su simplicidad, pero es muy limitado. Sin embargo, a menudo tiene una colección de entidades empresariales, en lugar de simplemente cadenas que se desean enlazar.
Por ejemplo, si los datos se compone de una colección de clases de empleado, que es posible que desee la lista para mostrar solo los nombres de cada empleado. Para personalizar el comportamiento de un `ListView` para controlar qué datos se muestran debe implementar una subclase de `BaseAdapter` reemplazar los cuatro siguientes elementos:

-   **Recuento de** &ndash; para indicar que el control de número de filas que se encuentran en los datos.

-   **GetView** &ndash; para devolver una vista para cada fila, se rellena con datos.
    Este método tiene un parámetro para el `ListView` para pasar de una fila existente, sin usar para volver a usar.

-   **GetItemId** &ndash; devolver un identificador de fila (normalmente, la fila número, aunque puede ser cualquier valor de tipo long que le guste).

-   **Este [int]** indizador &ndash; para devolver los datos asociados con un número de fila en particular.

El código de ejemplo en **BasicTableAdapter/HomeScreenAdapter.cs** muestra cómo crear subclases de `BaseAdapter`:

```csharp
public class HomeScreenAdapter : BaseAdapter<string> {
   string[] items;
   Activity context;
   public HomeScreenAdapter(Activity context, string[] items) : base() {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
  {
       return position;
   }
   public override string this[int position] {  
       get { return items[position]; }
   }
   public override int Count {
       get { return items.Length; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       View view = convertView; // re-use an existing view, if one is available
      if (view == null) // otherwise create a new one
           view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
       view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
       return view;
   }
}
```


### <a name="using-a-custom-adapter"></a>Uso de un adaptador personalizado

Utiliza el adaptador personalizado es similar a la integrada `ArrayAdapter`, pasando un `context` y `string[]` de valores para mostrar:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Dado que este ejemplo utiliza el mismo diseño de fila (`SimpleListItem1`) la aplicación resultante será idéntica al ejemplo anterior.


### <a name="row-view-re-use"></a>Reutilización de vista fila

En este ejemplo hay sólo seis elementos. Dado que la pantalla puede ajustarse a ocho, ninguna fila volver a usar necesario. Al mostrar cientos o miles de filas, sin embargo, sería un desperdicio de memoria crear cientos o miles de `View` objetos cuando solo ocho caben en la pantalla a la vez. Para evitar esta situación, cuando una fila desaparece de la pantalla de que su vista se coloca en una cola para su reutilización. Cuando el usuario se desplaza, la `ListView` llamadas `GetView` para solicitar nuevas vistas para mostrar &ndash; si disponible pasa una vista sin usar en el `convertView` parámetro. En caso contrario, si este valor es null, a continuación, el código debe crear una nueva instancia de la vista, puede volver a establecer las propiedades de ese objeto y volver a usarla.

El `GetView` método debe seguir este patrón para volver a usar las vistas de fila:

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

Implementaciones del adaptador personalizado deben *siempre* volver a usar el `convertView` objeto antes de crear nuevas vistas para asegurarse de que no quedarse sin memoria cuando se muestran las listas largas.

Algunas implementaciones del adaptador (como el `CursorAdapter`) no tiene un `GetView` método, sino que requieren dos métodos diferentes `NewView` y `BindView` que aplicar la fila volver a usar al separar las responsabilidades de `GetView` en dos métodos. Hay un `CursorAdapter` ejemplo más adelante en el documento.


## <a name="enabling-fast-scrolling"></a>Habilitar el desplazamiento rápido

Desplazamiento rápido ayuda al usuario a desplazarse por las listas largas proporcionando un 'identificador' que actúa como una barra de desplazamiento para tener acceso directamente a una parte de la lista. Esta captura de pantalla muestra el identificador de desplazamiento rápido:

[![Captura de pantalla de desplazamiento rápido con un identificador de desplazamiento](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

Lo que hace que el identificador de desplazamiento rápido aparecen es tan sencillo como la configuración de la `FastScrollEnabled` propiedad `true`:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>Agregar un índice de sección

Un índice de la sección proporciona comentarios adicionales para los usuarios cuando estén desplazamiento rápido a través de una larga lista &ndash; muestra que han desplazado a 'sección'. Para hacer que el índice de la sección que aparezca la subclase de adaptador debe implementar la `ISectionIndexer` interfaz para proporcionar el texto de índice, dependiendo de las filas que se muestran:

[![Captura de pantalla de H que aparecen encima de sección que comienza con H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Para implementar `ISectionIndexer` deberá agregar tres métodos a un adaptador:

-   **GetSections** &ndash; proporciona una lista completa de la sección de títulos de índice que podrían mostrarse. Este método requiere una matriz de objetos de Java, por lo que el código necesita para crear un `Java.Lang.Object[]` de una colección. NET. En nuestro ejemplo devuelve una lista de los caracteres iniciales en la lista como `Java.Lang.String` .

-   **GetPositionForSection** &ndash; devuelve la primera posición de fila para un índice determinado de sección.

-   **GetSectionForPosition** &ndash; devuelve el índice de la sección que se mostrará para una fila determinada.


El ejemplo `SectionIndex/HomeScreenAdapter.cs` archivo implementa los métodos y algún código adicional en el constructor. El constructor basa el índice de la sección crear bucles en todas las filas y extraer el primer carácter del título (los elementos ya deben estar ordenados para que funcione).

```csharp
alphaIndex = new Dictionary<string, int>();
for (int i = 0; i < items.Length; i++) { // loop through items
   var key = items[i][0].ToString();
   if (!alphaIndex.ContainsKey(key))
       alphaIndex.Add(key, i); // add each 'new' letter to the index
}
sections = new string[alphaIndex.Keys.Count];
alphaIndex.Keys.CopyTo(sections, 0); // convert letters list to string[]

// Interface requires a Java.Lang.Object[], so we create one here
sectionsObjects = new Java.Lang.Object[sections.Length];
for (int i = 0; i < sections.Length; i++) {
   sectionsObjects[i] = new Java.Lang.String(sections[i]);
}
```

Con las estructuras de datos creadas, el `ISectionIndexer` métodos son muy simples:

```csharp
public Java.Lang.Object[] GetSections()
{
   return sectionsObjects;
}
public int GetPositionForSection(int section)
{
   return alphaIndexer[sections[section]];
}
public int GetSectionForPosition(int position)
{   // this method isn't called in this example, but code is provided for completeness
    int prevSection = 0;
    for (int i = 0; i < sections.Length; i++)
    {
        if (GetPositionForSection(i) > position)
        {
            break;
        }
        prevSection = i;
    }
    return prevSection;
}
```

Los títulos de sección índice no es necesario asignar 1:1 para sus secciones reales. Esto es por eso la `GetPositionForSection` método existe.
`GetPositionForSection` Proporciona una oportunidad para asignar cualquier los índices están en la lista de índice para cualquier secciones están en la vista de lista. Por ejemplo, puede tener una "z" en el índice, pero no puede tener una sección de la tabla para todas las letras, por lo que en lugar de la asignación de "z" 26, puede asignar a 25 o 24 o cualquier índice de sección "z" se debe asignar al.



## <a name="related-links"></a>Vínculos relacionados

- [BasicTableAndroid (sample)](https://developer.xamarin.com/samples/monodroid/BasicTableAndroid/)
- [BasicTableAdapter (sample)](https://developer.xamarin.com/samples/monodroid/BasicTableAdapter/)
- [FastScroll (ejemplo)](https://developer.xamarin.com/samples/monodroid/FastScroll/)
