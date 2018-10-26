---
title: Enlace un. AAR
description: Este tutorial proporciona instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin.Android de Android. Archivo AAR.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 7f71ccf4ff61c176e73be6d3855136697a5c2130
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104003"
---
# <a name="binding-an-aar"></a>Enlace un. AAR

_Este tutorial proporciona instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin.Android de Android. Archivo AAR._


## <a name="overview"></a>Información general

El *archivo Android (. AAR)* archivo es el formato de archivo para las bibliotecas de Android.
Un archivo. Archivo AAR es una. Archivo ZIP que contiene lo siguiente:

-   Código de Java compilado
-   Identificadores de recursos
-   Recursos
-   Metadatos (por ejemplo, las declaraciones de actividad, los permisos)

En esta guía, podrá recorrer los aspectos básicos de la creación de una biblioteca de enlaces para un único. Archivo AAR. Para obtener información general de biblioteca Java enlace en general (con un ejemplo de código básico), consulte [enlace a una biblioteca de Java](~/android/platform/binding-java-library/index.md).


> [!IMPORTANT]
> Un proyecto de enlace solo puede incluir uno. Archivo AAR. Si el. Dependencias de AAR en Sí. Deben ser incluidos en su propio proyecto de enlace y, a continuación, hace referencia a AAR y, a continuación, esas dependencias. Consulte [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).


## <a name="walkthrough"></a>Tutorial

Vamos a crear una biblioteca de enlaces para un ejemplo de archivo de almacenamiento Android que se creó en Android Studio, [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Esto. AAR contiene un `TextCounter` clase con métodos estáticos que contar el número de vocales y consonantes en una cadena. Además, **textanalyzer.aar** contiene un recurso de imagen para ayudar a mostrar los resultados del recuento.

Vamos a usar los pasos siguientes para crear una biblioteca de enlaces de la. Archivo AAR:

1. Cree un nuevo proyecto de biblioteca de enlaces de Java.

2. Agrega una única. Archivo AAR al proyecto. Un proyecto de enlace solo puede contener un único. AAR.

3. Configure la acción adecuada para el. Archivo AAR.

4. Elija una plataforma de destino que el. Admite AAR.

5. Compile la biblioteca de enlaces.

Una vez que hemos creado la biblioteca de enlaces, desarrollaremos una pequeña aplicación de Android que pide al usuario una cadena de texto, las llamadas. Métodos de AAR para analizar el texto, recupera la imagen desde el. AAR y muestra los resultados junto con la imagen.

Tendrá acceso la aplicación de ejemplo la `TextCounter` clase de **textanalyzer.aar**:

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

Además, esta aplicación de ejemplo recuperará y mostrar un recurso de imagen que se empaqueta en **textanalyzer.aar**:

[![Imagen de monkey Xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Este recurso de imagen reside en **res/drawable/monkey.png** en **textanalyzer.aar**.



### <a name="creating-the-bindings-library"></a>Creación de la biblioteca de enlaces

Antes de empezar con los pasos siguientes, descargue el ejemplo [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) archivo Android:

1.  Cree un nuevo proyecto de biblioteca de enlaces a partir de la plantilla de biblioteca de enlaces de Android. Puede usar Visual Studio para Mac o Visual Studio (las capturas de pantalla siguiente muestran Visual Studio, pero Visual Studio para Mac es muy similar). Nombre de la solución **AarBinding**:

    [![Crear proyecto AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.w157.png)](binding-an-aar-images/01-new-bindings-library-vs.w157.png#lightbox)

2.  La plantilla incluye un **archivos JAR** carpeta donde se agrega su. AAR(s) al proyecto de biblioteca de enlaces. Haga clic en el **archivos JAR** carpeta y seleccione **Agregar > elemento existente**:

    [![Agregar elemento existente](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3.  Navegue hasta la **textanalyzer.aar** archivo descargado anteriormente, selecciónelo y haga clic en **agregar**:

    [![Agregar textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4.  Compruebe que la **textanalyzer.aar** archivo se agregó correctamente al proyecto:

    [![Se agregó el archivo textanalyzer.aar](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5.  Establezca la acción de compilación **textanalyzer.aar** a `LibraryProjectZip`. En Visual Studio para Mac, haga clic en **textanalyzer.aar** para establecer la acción de compilación. En Visual Studio, se puede establecer la acción de compilación el **propiedades** panel):

    [![Configuración de la acción de compilación de textanalyzer.aar a LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6.  Abra el proyecto de propiedades para configurar el *.NET Framework de destino*. Si el. AAR usa las API de Android, establezca la plataforma de destino en el nivel de API que el. Espera AAR. (Para obtener más información sobre la configuración de .NET Framework de destino y los niveles de API de Android en general, consulte [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).)

    Establecer el nivel de API de destino para la biblioteca de enlaces. En este ejemplo, se pueden utilizar la plataforma más reciente (nivel de API 23) de nivel de API porque nuestra **textanalyzer** no tiene una dependencia en las API de Android:

    [![Establecer el nivel de destino en la API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7.  Compile la biblioteca de enlaces. El proyecto de biblioteca de enlaces debe compilarse correctamente y generar una salida. Archivo DLL en la siguiente ubicación: **AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>Uso de la biblioteca de enlaces

Para consumir esto. Archivo DLL en la aplicación de Xamarin.Android, primero debe agregar una referencia a la biblioteca de enlaces. Use los pasos siguientes:

1.  Estamos creando esta aplicación en la misma solución que la biblioteca de enlaces para simplificar este tutorial. (La aplicación que consume la biblioteca de enlaces también puede residir en una solución diferente.) Crear una nueva aplicación de Xamarin.Android: haga clic en la solución y seleccione **Agregar nuevo proyecto**. Asigne al nuevo proyecto **BindingTest**:

    [![Crear nuevo proyecto BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2.  Haga clic en el **referencias** nodo de la **BindingTest** del proyecto y seleccione **Agregar referencia...** :

    [![Haga clic en Agregar referencia](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3.  Seleccione el **AarBinding** proyecto que creó anteriormente y haga clic en **Aceptar**:

    [![Compruebe el proyecto de enlace AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4.  Abra el **referencias** nodo de la **BindingTest** proyecto para comprobar que la **AarBinding** referencia está presente:

    [![AarBinding aparece en las referencias](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


Si desea ver el contenido del proyecto de biblioteca de enlace, puede hacer doble clic para abrirlo en la referencia de la **Examinador de objetos**. Puede ver el contenido asignado de la `Com.Xamarin.Textcounter` espacio de nombres (asignado desde Java `com.xamarin.textanalyzezr` paquete) y puede ver los miembros de la `TextCounter` clase:

[![Ver el Examinador de objetos](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

La captura de pantalla anterior resalta los dos `TextAnalyzer` métodos que llamará a la aplicación de ejemplo: `NumConsonants` (que ajusta el Java subyacente `numConsonants` método), y `NumVowels` (que ajusta el Java subyacente `numVowels` método).



### <a name="accessing-aar-types"></a>Acceso a. Tipos de AAR

Después de agregar una referencia a la aplicación en la que apunta a la biblioteca de enlace, puede tener acceso a tipos de Java en el. AAR que tendría acceso C# tipos (gracias a la C# contenedores). C#puede llamar código de la aplicación `TextAnalyzer` métodos, como se muestra en este ejemplo:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

En el ejemplo anterior, estamos llamando a métodos estáticos la `TextCounter` clase. Sin embargo, también puede crear instancias de clases y métodos de instancia. Por ejemplo, si su. AAR ajusta una clase denominada `Employee` que tiene el método de instancia `buildFullName`, puede crear una instancia `MyClass` y utilizarlo tal como se muestra aquí:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

Los pasos siguientes agregan código a la aplicación para que solicite al usuario para el texto, usa `TextCounter` para analizar el texto y, a continuación, muestra los resultados.

Reemplace el **BindingTest** diseño (**Main.axml**) con el siguiente código XML. Este diseño tiene un `EditText` para la entrada de texto y dos botones para iniciar los recuentos de vocal y consonante:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

Reemplace el contenido de **MainActivity.cs** con el código siguiente. Tal como se muestra en este ejemplo, la llamada de controladores de eventos de botón ajustado `TextCounter` métodos que residen en el. AAR y use notificaciones del sistema para mostrar los resultados. Tenga en cuenta la `using` instrucción para el espacio de nombres de la biblioteca dependiente (en este caso, `Com.Xamarin.Textcounter`):

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

Compilar y ejecutar el **BindingTest** proyecto. Iniciará la aplicación y presentar la captura de pantalla de la izquierda (el `EditText` se inicializa con algo de texto, pero pueden pulsar para cambiarlo). Al tocar **recuento vocales**, una notificación del sistema muestra el número de vocales, tal como se muestra en la parte derecha:

[![Capturas de pantalla de la ejecución BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Pulse el **recuento CONSONANTES** botón. Además, puede modificar la línea de texto y pulse estos botones nuevo para probar diferentes vocal y consonante recuentos.



### <a name="accessing-aar-resources"></a>Acceso a. Recursos de AAR

El combinaciones de herramientas de Xamarin el **R** datos desde el. AAR en su aplicación **recursos** clase. Como resultado, puede tener acceso. Recursos de AAR desde su diseño (y desde el código subyacente) de la misma manera que para acceder a los recursos que se encuentran en el **recursos** ruta de acceso del proyecto.

Para obtener acceso a un recurso de imagen, usa el **Resource.Drawable** el nombre de la imagen se empaqueta dentro de la. AAR. Por ejemplo, puede hacer referencia **image.png** en el. Archivo AAR mediante `@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

También puede tener acceso a los diseños de recursos que residen en el. AAR. Para ello, usa el **Resource.Layout** nombre para el diseño empaquetado en el. AAR. Por ejemplo:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

El **textanalyzer.aar** ejemplo contiene un archivo de imagen que se encuentra en **res/drawable/monkey.png**. Vamos a tener acceso a este recurso de imagen y su uso en nuestra aplicación de ejemplo:

Editar el **BindingTest** diseño (**Main.axml**) y agregue un `ImageView` al final de la `LinearLayout` contenedor. Esto `ImageView` muestra la imagen que se encuentra en **@drawable/monkey**; esta imagen se cargará desde la sección de recursos de **textanalyzer.aar**:

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

Compilar y ejecutar el **BindingTest** proyecto. La aplicación se inicie y presentar la captura de pantalla de la izquierda &ndash; cuando pulse en **recuento CONSONANTES**, los resultados se muestran tal como se muestra en la parte derecha:

[![BindingTest Mostrar recuento consonante](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


¡Enhorabuena! Se ha enlazado correctamente una biblioteca de Java. ¡AAR!



## <a name="summary"></a>Resumen

En este tutorial, hemos creado una biblioteca de enlaces para un. Archivo de AAR, agrega la biblioteca de enlaces a una aplicación de prueba mínima y se ejecutó la aplicación para comprobar que nuestra C# código puede llamar a código de Java que residen en el. Archivo AAR.
Además, hemos ampliado la aplicación para obtener acceso y mostrar un recurso de imagen que se encuentra en el. Archivo AAR.


## <a name="related-links"></a>Vínculos relacionados

- [Creación de una biblioteca de enlaces de Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Enlace de un .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (ejemplo)](https://developer.xamarin.com/samples/monodroid/JavaIntegration/AarBinding)
- [Proyecto de 44573 uno de error no puede enlazar varios archivos .aar](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
