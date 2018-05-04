---
title: Uso de la vista de lista en Xamarin.Android
description: ListView es un componente importante de la interfaz de usuario de las aplicaciones Android. se utiliza en cualquier lugar de listas cortas de opciones de menú para listas largas de contactos o Favoritos de internet. Proporciona una manera sencilla para presentar una lista desplazable de filas que puede estar en formato con un estilo integrado o personalizar ampliamente.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: c5c8222df16b265f3b44f3ee3fcd9053d827cf6a
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="listview"></a>ListView

_ListView es un componente importante de la interfaz de usuario de las aplicaciones Android. se utiliza en cualquier lugar de listas cortas de opciones de menú para listas largas de contactos o Favoritos de internet. Proporciona una manera sencilla para presentar una lista desplazable de filas que puede estar en formato con un estilo integrado o personalizar ampliamente._


## <a name="overview"></a>Información general

Vistas de lista y los adaptadores se incluyen en los bloques de creación más importantes de las aplicaciones de Android. La `ListView` clase proporciona una manera flexible para presentar los datos, ya sea un menú corto o una lista desplegable largo. Proporciona características de facilidad de uso como índices de desplazamiento, rápidos y la selección de uno o varios para ayudarle a crear interfaces de usuario preparadas para dispositivos móviles para sus aplicaciones. Una instancia `ListView` necesita un *adaptador* para llenarlo con los datos que contienen las vistas de la fila.

Esta guía explica cómo implementar `ListView` y los diversos `Adapter` clases en Xamarin.Android. También muestra cómo personalizar la apariencia de un `ListView`, y explica la importancia de fila, volver a usar para reducir el consumo de memoria. También hay una explicación de cómo afecta el ciclo de vida de actividad `ListView` y `Adapter` usar. Si está trabajando en aplicaciones multiplataforma con Xamarin.iOS, la `ListView` control es estructuralmente similar de iOS `UITableView` (y el Android `Adapter` es similar a la `UITableViewSource`).

En primer lugar, presenta un breve tutorial el `ListView` con un ejemplo de código básico. A continuación, se proporcionan vínculos a temas más avanzados que le ayudarán a usar `ListView` en las aplicaciones reales.


> [!NOTE]
> El `RecyclerView` widget es una versión más avanzada y flexible de `ListView`. Dado que `RecyclerView` está diseñado para ser el sucesor `ListView` (y `GridView`), le recomendamos que use `RecyclerView` en lugar de `ListView` para el desarrollo de aplicación nuevo. Para obtener más información, consulte [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).



## <a name="listview-tutorial"></a>Tutorial de ListView

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) es un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que crea una lista de elementos desplazables. Los elementos de lista se insertan automáticamente a la lista mediante un [ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/).

En este tutorial, creará una lista desplazable de nombres de país que se leen desde una matriz de cadenas. Cuando se selecciona un elemento de lista, un mensaje de notificación del sistema mostrará la posición del elemento en la lista.


Inicie un nuevo proyecto denominado **HelloListView**.

Crear un archivo XML denominado **list_item.xml** y guardarlo en el interior del **recursos/diseño/** carpeta. Inserte el siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

Este archivo define el diseño para cada elemento que se colocarán en el [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Abra `MainActivity.cs` y modifique la clase para extender [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (en lugar de [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)):

```csharp
public class MainActivity : ListActivity
{
```

Inserte el siguiente código para el [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) método:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args)
    {
        Toast.MakeText(Application, ((TextView)args.View).Text, ToastLength.Short).Show();
    };
}
```

Observe que esto no carga un archivo de diseño para la actividad (lo que suele realizar con [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))).
En su lugar, establecer el [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/) propiedad se agrega automáticamente una [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) para rellenar toda la pantalla de la [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/).
Este método toma una [ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), que administra la matriz de elementos de lista que se colocarán en el [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).
El [ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/) constructor toma la aplicación [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/), la descripción del diseño para cada elemento de lista (creado en el paso anterior) y un `T[]` o [ `Java.Util.IList<T>` ](https://developer.xamarin.com/api/type/Java.Util.IList/) matriz de objetos que se va a insertar en el [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) (definido a continuación).

El [ `TextFilterEnabled` ](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/) propiedad activa texto filtrado para el [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/), de modo que cuando el usuario comienza a escribir, se filtrará la lista.

El [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) evento puede utilizarse para suscribirse controladores para los clics. Cuando un elemento en el [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) es hacer clic en, se llama al controlador y un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) se muestra el mensaje, con el texto del elemento ha hecho clic.

Puede usar diseños de elemento de lista proporcionados por la plataforma en lugar de definir su propio archivo de diseño para la [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).
Por ejemplo, pruebe a usar `Android.Resource.Layout.SimpleListItem1` en lugar de `Resource.Layout.list_item`.

Agregue las siguientes `using` instrucción:

```csharp
using System;
```
A continuación, agregue la siguiente matriz de cadena como un miembro de `MainActivity`:

```csharp
static readonly string[] countries = new String[] {
    "Afghanistan","Albania","Algeria","American Samoa","Andorra",
    "Angola","Anguilla","Antarctica","Antigua and Barbuda","Argentina",
    "Armenia","Aruba","Australia","Austria","Azerbaijan",
    "Bahrain","Bangladesh","Barbados","Belarus","Belgium",
    "Belize","Benin","Bermuda","Bhutan","Bolivia",
    "Bosnia and Herzegovina","Botswana","Bouvet Island","Brazil","British Indian Ocean Territory",
    "British Virgin Islands","Brunei","Bulgaria","Burkina Faso","Burundi",
    "Cote d'Ivoire","Cambodia","Cameroon","Canada","Cape Verde",
    "Cayman Islands","Central African Republic","Chad","Chile","China",
    "Christmas Island","Cocos (Keeling) Islands","Colombia","Comoros","Congo",
    "Cook Islands","Costa Rica","Croatia","Cuba","Cyprus","Czech Republic",
    "Democratic Republic of the Congo","Denmark","Djibouti","Dominica","Dominican Republic",
    "East Timor","Ecuador","Egypt","El Salvador","Equatorial Guinea","Eritrea",
    "Estonia","Ethiopia","Faeroe Islands","Falkland Islands","Fiji","Finland",
    "Former Yugoslav Republic of Macedonia","France","French Guiana","French Polynesia",
    "French Southern Territories","Gabon","Georgia","Germany","Ghana","Gibraltar",
    "Greece","Greenland","Grenada","Guadeloupe","Guam","Guatemala","Guinea","Guinea-Bissau",
    "Guyana","Haiti","Heard Island and McDonald Islands","Honduras","Hong Kong","Hungary",
    "Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica",
    "Japan","Jordan","Kazakhstan","Kenya","Kiribati","Kuwait","Kyrgyzstan","Laos",
    "Latvia","Lebanon","Lesotho","Liberia","Libya","Liechtenstein","Lithuania","Luxembourg",
    "Macau","Madagascar","Malawi","Malaysia","Maldives","Mali","Malta","Marshall Islands",
    "Martinique","Mauritania","Mauritius","Mayotte","Mexico","Micronesia","Moldova",
    "Monaco","Mongolia","Montserrat","Morocco","Mozambique","Myanmar","Namibia",
    "Nauru","Nepal","Netherlands","Netherlands Antilles","New Caledonia","New Zealand",
    "Nicaragua","Niger","Nigeria","Niue","Norfolk Island","North Korea","Northern Marianas",
    "Norway","Oman","Pakistan","Palau","Panama","Papua New Guinea","Paraguay","Peru",
    "Philippines","Pitcairn Islands","Poland","Portugal","Puerto Rico","Qatar",
    "Reunion","Romania","Russia","Rwanda","Sqo Tome and Principe","Saint Helena",
    "Saint Kitts and Nevis","Saint Lucia","Saint Pierre and Miquelon",
    "Saint Vincent and the Grenadines","Samoa","San Marino","Saudi Arabia","Senegal",
    "Seychelles","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Islands",
    "Somalia","South Africa","South Georgia and the South Sandwich Islands","South Korea",
    "Spain","Sri Lanka","Sudan","Suriname","Svalbard and Jan Mayen","Swaziland","Sweden",
    "Switzerland","Syria","Taiwan","Tajikistan","Tanzania","Thailand","The Bahamas",
    "The Gambia","Togo","Tokelau","Tonga","Trinidad and Tobago","Tunisia","Turkey",
    "Turkmenistan","Turks and Caicos Islands","Tuvalu","Virgin Islands","Uganda",
    "Ukraine","United Arab Emirates","United Kingdom",
    "United States","United States Minor Outlying Islands","Uruguay","Uzbekistan",
    "Vanuatu","Vatican City","Venezuela","Vietnam","Wallis and Futuna","Western Sahara",
    "Yemen","Yugoslavia","Zambia","Zimbabwe"
  };
```

Se trata de la matriz de cadenas que se colocarán en el [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Ejecute la aplicación. Puede desplazarse por la lista o escriba para filtrarlos y luego haga clic en un elemento para ver un mensaje. Verá algo parecido a esto:

[![Captura de pantalla de ejemplo de ListView con nombres de país](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Tenga en cuenta que el uso de una matriz de cadenas codificadas de forma rígida no es la mejor práctica de diseño. Uno se utiliza en este tutorial para simplificar, para mostrar el [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) widget. Lo más recomendable es hacer referencia a una matriz de cadenas que se define un recurso externo, por ejemplo, como con un `string-array` recursos en el proyecto **Resources/Values/Strings.xml** archivo. Por ejemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloListView</string>
  <string-array name="countries_array">
    <item>Bahrain</item>
    <item>Bangladesh</item>
    <item>Barbados</item>
    <item>Belarus</item>
    <item>Belgium</item>
    <item>Belize</item>
    <item>Benin</item>
  </string-array>
</resources>
```

Para usar estas cadenas de recursos para la [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), sustituya el original [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/) línea con lo siguiente:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```
Ejecute la aplicación. Verá algo parecido a esto:

[![Captura de pantalla de ejemplo de ListView con una lista más pequeña de nombres](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)


## <a name="going-further-with-listview"></a>Si continúa con el control ListView

Los temas restantes (vinculados a continuación) echar un vistazo completa de trabajar con la `ListView` clase y los diferentes tipos de tipos de adaptador que puede usar con él. La estructura es como se detalla a continuación:

-   **Apariencia visual** &ndash; partes de la `ListView` control y cómo funcionan.

-   **Clases de** &ndash; información general de las clases utilizadas para mostrar un `ListView`.

-   **Mostrar datos en un control ListView** &ndash; cómo mostrar una lista simple de datos; cómo implementar `ListView's` características de facilidad de uso; cómo usar diseños de fila integrada diferente; y cómo adaptadores ahorrar memoria al volver a usar las vistas de fila.

-   **Apariencia personalizada** &ndash; cambiar el estilo de la `ListView` con diseños personalizados, las fuentes y colores.

-   **Uso de SQLite** &ndash; cómo mostrar los datos de una base de datos de SQLite con un `CursorAdapter`.

-   **Ciclo de vida de actividad** &ndash; consideraciones de diseño al implementar `ListView` actividades, incluidas donde en el ciclo de vida debe rellenar los datos y cuándo se deben liberar los recursos.

La discusión (ha dividido en seis partes) comienza con una visión general de la `ListView` propia clase antes de introducir progresivamente más complejos ejemplos de cómo se usa.

-   [Elementos y funcionalidad de ListView](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [Rellenar un control ListView con datos](~/android/user-interface/layouts/list-view/populating.md)
-   [Personalización de la apariencia de ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [Uso de CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [Uso de ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView y ciclo de vida de actividad](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>Resumen

Este conjunto de temas que se introdujo `ListView` y proporciona algunos ejemplos de cómo usar las características integradas de la `ListActivity`. También se han descrito las implementaciones personalizadas de `ListView` que permite coloridos diseños y con una base de datos de SQLite y tratado brevemente la relevancia del ciclo de vida de actividad su `ListView` implementación.


## <a name="related-links"></a>Vínculos relacionados

- [AccessoryViews (ejemplo)](https://developer.xamarin.com/samples/AccessoryViews/)
- [BasicTableAndroid (ejemplo)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (ejemplo)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [BuiltInViews (ejemplo)](https://developer.xamarin.com/samples/BuiltInViews/)
- [CustomRowView (ejemplo)](https://developer.xamarin.com/samples/CustomRowView/)
- [FastScroll (ejemplo)](https://developer.xamarin.com/samples/FastScroll/)
- [SectionIndex (ejemplo)](https://developer.xamarin.com/samples/SectionIndex/)
- [SimpleCursorTableAdapter (ejemplo)](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter (ejemplo)](https://developer.xamarin.com/samples/CursorTableAdapter/)
- [Tutorial del ciclo de vida de actividad](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Trabajar con tablas y las celdas (de Xamarin.iOS)](~/ios/user-interface/controls/tables/index.md)
- [Referencia de clase de ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Referencia de clase ListActivity](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [Referencia de clases de BaseAdapter](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [Referencia de clase ArrayAdapter](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [Referencia de clase CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
