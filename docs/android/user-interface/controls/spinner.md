---
title: Spinner
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 90b4755cdb4b8248c2b731d070d720076d4dda40
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946054"
---
# <a name="spinner"></a>Spinner

[`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) es un widget que presenta una lista desplegable para seleccionar los elementos. Esta guía explica cómo crear una aplicación sencilla que muestra una lista de opciones en un control de número, seguido de las modificaciones que se muestran otros valores asociados con la opción seleccionada.

## <a name="basic-spinner"></a>Control de giro básica

En la primera parte de este tutorial, creará un widget spinner simple que muestra una lista de los planetas. Cuando se selecciona un planeta, un mensaje de notificación muestra el elemento seleccionado:

[![Capturas de pantalla de ejemplo de aplicación HelloSpinner](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

Inicie un nuevo proyecto denominado **HelloSpinner**.

Abra **Resources/Layout/Main.axml** e inserte el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:padding="10dip"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dip"
        android:text="@string/planet_prompt"
    />
    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:prompt="@string/planet_prompt"
    />
</LinearLayout>
```

Tenga en cuenta que el [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)del `android:text` atributo y el [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)del `android:prompt` atributo ambos hacen referencia al mismo recurso de cadena. Este texto se comporta como un título para el widget. Cuando se aplica a la [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/), el texto del título aparecerá en el cuadro de diálogo de selección que aparece al seleccionar el widget.

Editar **Resources/Values/Strings.xml** y modifique el archivo tenga el siguiente aspecto:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloSpinner</string>
  <string name="planet_prompt">Choose a planet</string>
  <string-array name="planets_array">
    <item>Mercury</item>
    <item>Venus</item>
    <item>Earth</item>
    <item>Mars</item>
    <item>Jupiter</item>
    <item>Saturn</item>
    <item>Uranus</item>
    <item>Neptune</item>
  </string-array>
</resources>
```

El segundo `<string>` elemento define la cadena de título al que hace referencia el [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) y [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) en el diseño anterior.
El `<string-array>` elemento define la lista de cadenas que se mostrará como la lista en el [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) widget.

Ahora abra **MainActivity.cs** y agregue la siguiente `using` instrucción:

```csharp
using System;
```

A continuación, inserte el código siguiente para el [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
método:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    Spinner spinner = FindViewById<Spinner> (Resource.Id.spinner);

    spinner.ItemSelected += new EventHandler<AdapterView.ItemSelectedEventArgs> (spinner_ItemSelected);
    var adapter = ArrayAdapter.CreateFromResource (
            this, Resource.Array.planets_array, Android.Resource.Layout.SimpleSpinnerItem);

    adapter.SetDropDownViewResource (Android.Resource.Layout.SimpleSpinnerDropDownItem);
    spinner.Adapter = adapter;
}
```

Después de la `Main.axml` diseño se establece como la vista de contenido, el [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) widget se captura desde el diseño con [ `FindViewById<>(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
El [`CreateFromResource()`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.CreateFromResource/p/Android.Content.Context/System.Int32/System.Int32/)
método, a continuación, crea un nuevo [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/), que enlaza cada elemento de la matriz de cadena a la apariencia inicial para el [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) (que es el aspecto de cada elemento en el control de número cuando se selecciona) . El `Resource.Array.planets_array` referencias de identificador el `string-array` definido anteriormente y el `Android.Resource.Layout.SimpleSpinnerItem` identificador hace referencia a un diseño para la apariencia del control de giro estándar, definido por la plataforma.
[`SetDropDownViewResource`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.SetDropDownViewResource/p/System.Int32/)
se llama para definir la apariencia de cada elemento cuando se abre el widget. Por último, el [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) está establecido para asociar todos sus elementos con el [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) estableciendo el [ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter) propiedad.

Ahora proporcionan un método de devolución de llamada que elementos NOTIFY la aplicación cuando se ha seleccionado un elemento de la [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/). Este es el aspecto que debería este método:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

Cuando se selecciona un elemento, el remitente se convierte en un [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) para que se pueden tener acceso a los elementos. Mediante el `Position` propiedad en el `ItemEventArgs`, puede averiguar el texto del objeto seleccionado y utilizarla para mostrar un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/).

Ejecute la aplicación. debe ver así:

[![Captura de pantalla ejemplo de control de giro con Mars seleccionado como el planeta](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>Spinner mediante pares clave/valor

A menudo es necesario usar `Spinner` para mostrar los valores de clave que están asociados con algún tipo de datos usados por la aplicación. Dado que `Spinner` no funciona directamente con pares clave/valor, debe almacenar el par clave/valor por separado, rellene el `Spinner` con valores de clave, a continuación, utilizar la posición de la clave seleccionada en el control de número para buscar el valor de datos asociados. 

En los pasos siguientes, el **HelloSpinner** aplicación se ha modificado para mostrar la temperatura media del planeta seleccionado:

Agregue el siguiente `using` instrucción **MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

Agregue la siguiente variable de instancia para el `MainActivity` clase.
Esta lista contendrá los pares clave/valor para los planetas y las temperaturas mean:

```csharp
private List<KeyValuePair<string, string>> planets;
```

En el `OnCreate` método, agregue el código siguiente antes de `adapter` se ha declarado:

```csharp
planets = new List<KeyValuePair<string, string>>
{
    new KeyValuePair<string, string>("Mercury", "167 degrees C"),
    new KeyValuePair<string, string>("Venus", "464 degrees C"),
    new KeyValuePair<string, string>("Earth", "15 degrees C"),
    new KeyValuePair<string, string>("Mars", "-65 degrees C"),
    new KeyValuePair<string, string>("Jupiter" , "-110 degrees C"),
    new KeyValuePair<string, string>("Saturn", "-140 degrees C"),
    new KeyValuePair<string, string>("Uranus", "-195 degrees C"),
    new KeyValuePair<string, string>("Neptune", "-200 degrees C")
};
```

Este código crea un almacén para los planetas y sus asociados de las temperaturas mean simple. (En una aplicación real, una base de datos normalmente se usa para almacenar las claves y sus datos asociados).

Inmediatamente después del código anterior, agregue las líneas siguientes para extraer las claves y póngalos en una lista (en orden):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

Pasar esta lista para la `ArrayAdapter` constructor (en lugar de la `planets_array` recursos):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

Modificar `spinner_ItemSelected` para que la posición seleccionada se utiliza para buscar el valor (la temperatura) asociado con el planeta seleccionado:

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

Ejecute la aplicación. la notificación del sistema debe tener este aspecto:

[![Ejemplo de selección del planeta mostrar la temperatura](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)
   
  

## <a name="resources"></a>Recursos

-   [`Resource.Layout`](https://developer.xamarin.com/api/type/Android.Resource+Layout/) 
-   [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) 
-   [`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) 

*Las partes de esta página son modificaciones en función de trabajo creado y compartido por el Android Open Source Project y usarse de acuerdo con los términos descritos en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
