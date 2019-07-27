---
title: Número de Xamarin. Android
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 2c7f0de2347e614b8c24de32bf3f88362a212a94
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510408"
---
# <a name="xamarinandroid-spinner"></a>Número de Xamarin. Android

[`Spinner`](xref:Android.Widget.Spinner)es un widget que presenta una lista desplegable para seleccionar elementos. En esta guía se explica cómo crear una aplicación sencilla que muestre una lista de opciones en un control de número, seguida de modificaciones que muestran otros valores asociados a la opción seleccionada.

## <a name="basic-spinner"></a>Control de número básico

En la primera parte de este tutorial, creará un widget de número simple que muestra una lista de planetas. Cuando se selecciona un planeta, un mensaje de notificación de sistema muestra el elemento seleccionado:

[![Capturas de pantallas de ejemplo de la aplicación HelloSpinner](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

Inicie un nuevo proyecto denominado **HelloSpinner**.

Abra **Resources/layout/main. axml** e inserte el siguiente código XML:

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

Observe que el [`TextView`](xref:Android.Widget.TextView)atributo `android:text` de y el [`Spinner`](xref:Android.Widget.Spinner)atributo `android:prompt` de hacen referencia al mismo recurso de cadena. Este texto se comporta como título del widget. Cuando se aplica a [`Spinner`](xref:Android.Widget.Spinner), el texto del título aparecerá en el cuadro de diálogo de selección que aparece al seleccionar el widget.

Edite Resources **/Values/Strings. XML** y modifique el archivo para que tenga el siguiente aspecto:

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

El segundo `<string>` elemento define la cadena de título [`TextView`](xref:Android.Widget.TextView) a la que hace [`Spinner`](xref:Android.Widget.Spinner) referencia y en el diseño anterior.
El `<string-array>` elemento define la lista de cadenas que se mostrarán como lista en el [`Spinner`](xref:Android.Widget.Spinner) widget.

Ahora Abra **MainActivity.CS** y agregue la siguiente `using` instrucción:

```csharp
using System;
```

A continuación, inserte el siguiente código para [`OnCreate()`](xref:Android.App.Activity.OnCreate*)el método):

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

Una vez `Main.axml` establecido el diseño como la vista de contenido, [`Spinner`](xref:Android.Widget.Spinner) el widget se captura desde el diseño [`FindViewById<>(int)`](xref:Android.App.Activity.FindViewById*)con.
El[`CreateFromResource()`](xref:Android.Widget.ArrayAdapter.CreateFromResource*)
a continuación, crea un [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)nuevo, que enlaza cada elemento de la matriz de cadenas con la apariencia inicial [`Spinner`](xref:Android.Widget.Spinner) de (que es cómo aparecerá cada elemento en el control de número cuando se selecciona). El `Resource.Array.planets_array` identificador hace referencia `string-array` a la definición anterior `Android.Resource.Layout.SimpleSpinnerItem` y el identificador hace referencia a un diseño de la apariencia del control de número estándar, definido por la plataforma.
[`SetDropDownViewResource`](xref:Android.Widget.ArrayAdapter.SetDropDownViewResource*)
se llama a para definir la apariencia de cada elemento cuando se abre el widget. Por último, [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) se establece para asociar todos sus elementos [`Spinner`](xref:Android.Widget.Spinner) al estableciendo la [`Adapter`](xref:Android.Widget.ArrayAdapter) propiedad.

Ahora proporcione un método de devolución de llamada que notifique a la aplicación cuando se ha seleccionado un [`Spinner`](xref:Android.Widget.Spinner)elemento de. Este método debería ser similar al siguiente:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

Cuando se selecciona un elemento, el remitente se convierte en un [`Spinner`](xref:Android.Widget.Spinner) para que se pueda tener acceso a los elementos. Mediante el `Position` uso de la `ItemEventArgs`propiedad en, puede averiguar el texto del objeto seleccionado y usarlo para mostrar un [`Toast`](xref:Android.Widget.Toast).

Ejecutar la aplicación; debería tener este aspecto:

[![Ejemplo de captura de pantalla de control de número con Mars seleccionado como planeta](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>Control de número mediante pares clave-valor

A menudo es necesario usar `Spinner` para mostrar los valores de clave asociados a algún tipo de datos que usa la aplicación. Dado `Spinner` que no funciona directamente con pares clave-valor, debe almacenar el par clave-valor por separado, rellenar `Spinner` los valores de clave y, a continuación, usar la posición de la clave seleccionada en el control de número para buscar el valor de datos asociado. 

En los pasos siguientes, la aplicación **HelloSpinner** se modifica para mostrar la temperatura media del planeta seleccionado:

Agregue la siguiente `using` instrucción a **MainActivity.CS**:

```csharp
using System.Collections.Generic;
```

Agregue la siguiente variable de instancia a `MainActivity` la clase.
Esta lista contendrá pares clave-valor para los planetas y las temperaturas medias:

```csharp
private List<KeyValuePair<string, string>> planets;
```

En el `OnCreate` método, agregue el código siguiente antes `adapter` de que se declare:

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

Este código crea un almacén simple para los planetas y sus temperaturas medias asociadas. (En una aplicación real, una base de datos se usa normalmente para almacenar claves y sus datos asociados).

Inmediatamente después del código anterior, agregue las líneas siguientes para extraer las claves y colocarlas en una lista (en orden):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

Pase esta lista al `ArrayAdapter` constructor (en lugar `planets_array` del recurso):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

Modifique `spinner_ItemSelected` para que la posición seleccionada se use para buscar el valor (la temperatura) asociada al planeta seleccionado:

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

Ejecutar la aplicación; la notificación del sistema debe tener el siguiente aspecto:

[![Ejemplo de selección de planeta que muestra la temperatura](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)

## <a name="resources"></a>Recursos

- [`Resource.Layout`](xref:Android.Resource.Layout)
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`Spinner`](xref:Android.Widget.Spinner)

*Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la*
licencia de atribución de[*Creative Commons 2,5*](http://creativecommons.org/licenses/by/2.5/).
