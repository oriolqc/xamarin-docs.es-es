---
title: Spinner
ms.topic: article
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: b7c850d0ea06d69c3601081c1e9cde193903eb27
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="spinner"></a>Spinner

[`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) es un widget que presenta una lista desplegable para seleccionar elementos. Esta guía explica cómo crear una aplicación sencilla que muestra una lista de opciones en un control de giro, seguido de las modificaciones que se muestran otros valores asociados con la opción seleccionada.

## <a name="basic-spinner"></a>Spinner básica

En la primera parte de este tutorial, creará un widget spinner simple que muestra una lista de planetas. Cuando se selecciona un planeta, un mensaje de notificación del sistema muestra el elemento seleccionado:

[![Capturas de pantalla de ejemplo de aplicación de HelloSpinner](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

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

Tenga en cuenta que la [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)del `android:text` atributo y el [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)del `android:prompt` atributo ambos hacen referencia al mismo recurso de cadena. Este texto se comporta como un título para el widget. Cuando se aplica a la [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/), el texto del título aparecerá en el cuadro de diálogo de selección que aparece al seleccionar el widget.

Editar **Resources/Values/Strings.xml** y modifique el archivo debe ser similar a esto:

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
El `<string-array>` elemento define la lista de cadenas que se mostrará como la lista en la [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) widget.

Ahora, abra **MainActivity.cs** y agregue el siguiente `using` instrucción:

```csharp
using System;
```

A continuación, inserte el siguiente código para el [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) método:

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
El [ `CreateFromResource()` ](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.CreateFromResource/p/Android.Content.Context/System.Int32/System.Int32/) método, a continuación, crea un nuevo [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/), que enlaza cada elemento de la matriz de cadena a la apariencia inicial de la [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) (que es la apariencia de cada elemento en el control de giro cuando se selecciona). El `Resource.Array.planets_array` referencias de identificador la `string-array` definido anteriormente y el `Android.Resource.Layout.SimpleSpinnerItem` Id. hace referencia a un diseño de la apariencia de giro estándar definido por la plataforma.
[`SetDropDownViewResource`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.SetDropDownViewResource/p/System.Int32/) se llama para definir la apariencia de cada elemento cuando se abre el widget. Por último, el [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) se establece para asociar todos sus elementos con el [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) estableciendo la [ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter) propiedad.

Ahora, proporcionan un método de devolución de llamada que elementos NOTIFY la aplicación cuando se ha seleccionado un elemento de la [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/). Este es el aspecto que debería este método:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

Cuando se selecciona un elemento, el remitente se convierte en una [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) por lo que pueden tener acceso a los elementos. Mediante el `Position` propiedad en el `ItemEventArgs`, puede buscar el texto del objeto seleccionado y utilizarla para mostrar un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/).

Ejecute la aplicación; debería ser similar al siguiente:

[![Captura de pantalla ejemplo de control de giro con Mars seleccionado como el planeta](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>Spinner mediante pares clave/valor

A menudo es necesario utilizar `Spinner` para mostrar los valores de clave que se asocian con algún tipo de datos utilizados por la aplicación. Dado que `Spinner` no funciona directamente con pares de clave/valor, debe almacenar el par clave/valor por separado, rellenar el `Spinner` con valores de clave, a continuación, utilizar la posición de la clave seleccionada en el control de número para buscar el valor de datos asociados. 

En los pasos siguientes, el **HelloSpinner** aplicación se ha modificado para mostrar la temperatura media para el planeta seleccionado:

Agregue el siguiente `using` instrucción **MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

Agregue la siguiente variable de instancia para el `MainActivity` clase.
Esta lista contendrá los pares clave/valor para los planetas y su temperatura media:

```csharp
private List<KeyValuePair<string, string>> planets;
```

En el `OnCreate` método, agregue el código siguiente antes de `adapter` se declara:

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

Este código crea un almacén simple para planetas y su temperatura media asociado. (En una aplicación del mundo real, una base de datos normalmente se usa para almacenar las claves y sus datos asociados).

Inmediatamente después del código anterior, agregue las líneas siguientes para extraer las claves y colocarlos en una lista (en orden):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

Pasar esta lista para la `ArrayAdapter` constructor (en lugar de la `planets_array` recurso):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

Modificar `spinner_ItemSelected` para que la posición seleccionada se usa para buscar el valor (la temperatura) asociado con el planeta seleccionado:

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

Ejecute la aplicación; el sistema debe tener este aspecto:

[![Ejemplo de selección de planeta mostrar la temperatura](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)
   
  

## <a name="resources"></a>Recursos

-   [`Resource.Layout`](https://developer.xamarin.com/api/type/Android.Resource+Layout/) 
-   [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) 
-   [`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) 

*Algunas partes de esta página son las modificaciones que se basa en el trabajo creado y comparten la Android Open Source Project y usarse de acuerdo con los términos que se describe en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
