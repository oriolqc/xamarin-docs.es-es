---
title: Rellenar un control ListView con datos
ms.topic: article
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: 74d8533d0a757a307d88125701a482dfefd5eec2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="populating-a-listview-with-data"></a>Rellenar un control ListView con datos

<a name="overview" />

## <a name="overview"></a>Información general

Para agregar filas a una `ListView` debe agregarlo a su diseño e implementación un `IListAdapter` con métodos que la `ListView` llamadas se rellene automáticamente. Android incorpora `ListActivity` y `ArrayAdapter` clases que se pueden utilizar sin definir cualquier diseño personalizado XML o código. El `ListActivity` clase crea automáticamente un `ListView` y expone un `ListAdapter` propiedad para proporcionar las vistas de fila para mostrar a través de un adaptador.

Los adaptadores integrados toman un identificador de recurso de la vista como un parámetro que se usa para cada fila. Puede usar recursos integrados, como los de `Android.Resource.Layout` por lo que no es necesario escribir su propio.

<a name="Using_ListActivity_and_ArrayAdapterString" />

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>Usar ListActivity y ArrayAdapter&lt;cadena&gt;

El ejemplo **BasicTable/HomeScreen.cs** muestra cómo utilizar estas clases para mostrar un `ListView` en unas pocas líneas de código:

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

<a name="Handling_Row_Clicks" />

### <a name="handling-row-clicks"></a>Control de la fila hace clic en

Normalmente un `ListView` también permitirá al usuario tener acceso a una fila para llevar a cabo alguna acción (como reproducir una canción, o llamar a un contacto o mostrar otra pantalla). Para responder a los últimos retoques de usuario debe haber uno más método implementan en el `ListActivity` &ndash; `OnListItemClick` &ndash; similar a la siguiente:

[![Captura de pantalla de un SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Ahora el usuario puede tocar una fila y un `Toast` aparezca una alerta:

[![Captura de pantalla de notificación que aparece cuando una fila se toca](populating-images/basictable2.png)](populating-images/basictable2.png)

<a name="Implementing_a_ListAdapter" />

## <a name="implementing-a-listadapter"></a>Implementar un ListAdapter

`ArrayAdapter<string>` es excelente debido a su simplicidad, pero es muy limitada. Sin embargo, a menudo tiene una colección de entidades de negocio, en lugar de simplemente cadenas que se desea enlazar.
Por ejemplo, si los datos se componen de una colección de clases de empleado, tal vez le interese la lista simplemente mostrar los nombres de cada empleado. Para personalizar el comportamiento de un `ListView` para controlar qué datos se muestran debe implementar una subclase de `BaseAdapter` reemplazar los cuatro elementos siguientes:

-   **Recuento de** &ndash; para indicar que el control de cuántas filas se encuentran en los datos.

-   **GetView** &ndash; para obtener una vista de cada fila, se rellena con datos.
    Este método tiene un parámetro para el `ListView` para pasar de una fila existente, sin usar para volver a usar.

-   **GetItemId** &ndash; devolver un identificador de fila (normalmente la fila número, aunque puede ser cualquier valor de tipo long que le guste).

-   **Este [int]** indizador &ndash; para devolver los datos asociados con un número de filas determinado.

El código de ejemplo de **BasicTableAdapter/HomeScreenAdapter.cs** muestra cómo crear subclases de `BaseAdapter`:

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

<a name="Using_a_Custom_Adapter" />

### <a name="using-a-custom-adapter"></a>Utiliza un adaptador personalizado

Utiliza el adaptador personalizado es similar a la integrada `ArrayAdapter`, pasando un `context` y `string[]` de valores para mostrar:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Dado que este ejemplo utiliza el mismo diseño de fila (`SimpleListItem1`) la aplicación resultante será idéntica al ejemplo anterior.

<a name="Row_View_Re-Use" />

### <a name="row-view-re-use"></a>Fila vista volver a usar

En este ejemplo hay sólo seis elementos. Dado que la pantalla caben ocho, ninguna fila volver a usar necesario. Al mostrar cientos o miles de filas, sin embargo, sería una pérdida de memoria para crear cientos o miles de `View` objetos cuando solo ocho encaje en la pantalla a la vez. Para evitar esta situación, cuando una fila desaparece de la pantalla de que la vista se coloca en una cola para su reutilización. Cuando el usuario se desplaza, la `ListView` llamadas `GetView` para solicitar nuevas vistas para mostrar &ndash; si es disponible pasa una vista no utilizada en el `convertView` parámetro. En caso contrario, si este valor es null, a continuación, el código debe crear una nueva instancia de la vista, puede volver a establecer las propiedades de ese objeto y volver a usarlo.

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

Implementaciones del adaptador personalizado deben *siempre* volver a usar el `convertView` objeto antes de crear nuevas vistas para asegurarse de no quedarse sin memoria al mostrar listas largas.

Algunas implementaciones del adaptador (como el `CursorAdapter`) no tiene un `GetView` método, en su lugar requieren dos métodos diferentes `NewView` y `BindView` que aplicar la reutilización de la fila mediante la separación de las responsabilidades de `GetView` en dos métodos. Hay un `CursorAdapter` ejemplo más adelante en el documento.

<a name="Enabling_Fast_Scrolling" />

## <a name="enabling-fast-scrolling"></a>Habilitar desplazamiento rápido

Desplazamiento rápido ayuda al usuario a desplazarse a través de listas largas proporcionando un 'identificador' que actúa como una barra de desplazamiento para acceder directamente a una parte de la lista. Esta captura de pantalla muestra el identificador de desplazamiento rápido:

[![Captura de pantalla de desplazamiento rápido con un identificador de desplazamiento](populating-images/fastscroll.png)](populating-images/fastscroll.png)

Haciendo que el identificador de desplazamiento rápido que aparezca es tan sencillo como configuración de la `FastScrollEnabled` propiedad `true`:

```csharp
ListView.FastScrollEnabled = true;
```

<a name="Adding_a_Section_Index" />

### <a name="adding-a-section-index"></a>Agregar un índice de sección

Un índice de sección proporciona comentarios adicionales para los usuarios cuando están desplazamiento rápido a través de una lista larga &ndash; muestra que haya desplazado 'sección'. Para hacer que el índice de sección que aparezca la subclase de adaptador debe implementar la `ISectionIndexer` interfaz para proporcionar el texto de índice dependiendo de las filas que se muestran:

[![Captura de pantalla de H aparezcan por encima de la sección que comienza con H](populating-images/sectionindex.png)](populating-images/sectionindex.png)

Para implementar `ISectionIndexer` necesita agregar tres métodos para un adaptador:

-   **GetSections** &ndash; proporciona una lista completa de sección títulos de índice que pudieron mostrarse. Este método requiere una matriz de objetos de Java, por lo que el código necesita para crear un `Java.Lang.Object[]` de una colección. NET. En nuestro ejemplo devuelve una lista de los caracteres iniciales en la lista como `Java.Lang.String` .

-   **GetPositionForSection** &ndash; devuelve la primera posición de fila para un índice de sección dada.

-   **GetSectionForPosition** &ndash; devuelve el índice de sección que se mostrará para una fila determinada.


El ejemplo `SectionIndex/HomeScreenAdapter.cs` archivo implementa los métodos y código adicional en el constructor. El constructor crea el índice de sección, recorriendo en iteración cada fila y extraer el primer carácter del título (los elementos ya deben estar ordenados para que funcione).

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

Los títulos de índice de sección no es necesario asignar 1:1 a sus secciones reales. Se trata de por qué la `GetPositionForSection` método existe.
`GetPositionForSection` Proporciona una oportunidad para asignar los índices están en la lista de índice en las secciones están en la vista de lista. Por ejemplo, puede tener a la "z" en el índice, pero puede que no tenga una sección de la tabla para todas las letras, por lo que en lugar de la asignación de "z" y 26, puede asignar a 25 o 24 o el índice de sección "z" se debe asignar al.



## <a name="related-links"></a>Vínculos relacionados

- [BasicTableAndroid (ejemplo)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (ejemplo)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [FastScroll (ejemplo)](https://developer.xamarin.com/samples/FastScroll/)
