---
title: Enlazar un. JAR
description: Este tutorial proporciona instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin.Android desde un Android. Archivo JAR.
ms.topic: article
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: bbbf3fb09edb802f1315977fb14ecfe154b2572f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="binding-a-jar"></a>Enlazar un. JAR

_Este tutorial proporciona instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin.Android desde un Android. Archivo JAR._

## <a name="overview"></a>Información general

La Comunidad Android ofrece muchas bibliotecas de Java que se pueden usar en la aplicación. Estas bibliotecas de Java a menudo se empaquetan en. Formato de archivo JAR (archivo de Java), pero se puede empaquetar un. JAR en un *Java enlaces biblioteca* para que su funcionalidad no está disponible para las aplicaciones de Xamarin.Android. El propósito de la biblioteca de enlaces de Java es hacen que las API en el. Archivo JAR disponible para código de C# a través de los contenedores de código generados automáticamente.

Herramientas de Xamarin pueden generar una biblioteca de enlaces de entrada de uno o más. Archivos JAR. La biblioteca de enlaces (. Ensamblado de la DLL) contiene lo siguiente: 

-   El contenido del original. Archivos JAR.

-   Administrado invocable contenedores (MCW), que son C# tipos que ajustan Java correspondiente se escribe en el. Archivos JAR.

El código generado de MCW usa JNI (interfaz Java Native Interface) para reenviar las llamadas de API para subyacente. Archivo JAR. Puede crear bibliotecas de enlaces para cualquiera. Archivo JAR que originalmente estaba destinada para su uso con Android (tenga en cuenta que las herramientas de Xamarin no admiten el enlace de bibliotecas no Android Java actualmente). También puede elegir generar la biblioteca de enlaces sin incluir el contenido de la. Archivo JAR de modo que el archivo DLL tiene una dependencia el. JAR en tiempo de ejecución.

En esta guía, podrá recorrer los conceptos básicos de creación de una biblioteca de enlaces para una sola. Archivo JAR. Mostraremos con un ejemplo donde todos los elementos se insertan directamente &ndash; es decir, donde no hay ninguna personalización o la depuración de enlaces se requiere. 
[Crear enlaces con metadatos](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) ofrece un ejemplo de un escenario más avanzado, donde el proceso de enlace no es completamente automático y se requiere cierta cantidad de intervención manual. Para obtener información general de Java biblioteca enlace en general (con un ejemplo de código básico), consulte [enlace una biblioteca de Java](~/android/platform/binding-java-library/index.md). 

 
## <a name="walkthrough"></a>Tutorial

En el siguiente tutorial, vamos a crear una biblioteca de enlaces para [Picasso](http://square.github.io/picasso/), un Android popular. Archivo JAR que proporciona la imagen de carga y el almacenamiento en caché de funcionalidad. Usaremos los siguientes pasos para enlazar **2.x.x.jar picasso** para crear un nuevo ensamblado de .NET que podemos usar en un proyecto Xamarin.Android: 

1. Cree un nuevo proyecto de biblioteca de enlaces de Java.

2. Agregar el. Archivo JAR al proyecto.

3. Establecer la acción de compilación adecuados para el. Archivo JAR.

4. Elija una plataforma de destino que el. Admite JAR.

5. Compile la biblioteca de enlaces.

Una vez que hemos creado la biblioteca de enlaces, se desarrollará una pequeña aplicación de Android que muestra nuestra capacidad para llamar a las API en la biblioteca de enlaces. En este ejemplo, deseamos tener acceso a métodos de **2.x.x.jar picasso**:

```java
package com.squareup.picasso

public class Picasso
{ 
    ...
    public static Picasso with (Context context) { ... };
    ...
    public RequestCreator load (String path) { ... };
    ...
}

```csharp
After we generate a Bindings Library for **picasso-2.x.x.jar**,
we can call these methods from C#. For example:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>Creación de la biblioteca de enlaces

Antes de empezar con los pasos siguientes, descargue [2.x.x.jar picasso](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

En primer lugar, cree un nuevo proyecto de biblioteca de enlaces. En Visual Studio para Mac o en Visual Studio, cree una nueva solución y seleccione la *biblioteca enlaces Android* plantilla. (Las capturas de pantalla en este tutorial utilizan Visual Studio, pero Visual Studio para Mac es muy similar). Llame a la solución **JarBinding**: 

[![Crear proyecto de biblioteca de JarBinding](binding-a-jar-images/01-new-bindings-library-sml.png)](binding-a-jar-images/01-new-bindings-library.png#lightbox)

La plantilla incluye una **archivos JAR** carpeta donde se agrega la. JAR(s) al proyecto de biblioteca de enlaces. Haga clic en el **archivos JAR** carpeta y seleccione **Agregar > elemento existente**: 

[![Agregar elemento existente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Navegue hasta la **picasso 2.x.x.jar** archivo descargado anteriormente, selecciónelo y haga clic en **agregar**: 

[![Seleccione el archivo jar y haga clic en Agregar](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Compruebe que la **2.x.x.jar picasso** archivo se ha agregado correctamente al proyecto: 

[![JAR agregado al proyecto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Cuando se crea un proyecto de biblioteca de enlaces de Java, debe especificar si el. JAR es pueden incrustar en la biblioteca de enlaces o empaqueta por separado. Para ello, especifique uno de los siguientes *acciones de compilación*: 

-   **EmbeddedJar** &ndash; el. JAR se incrustarán en la biblioteca de enlaces.

-   **InputJar** &ndash; el. JAR se mantienen separado de la biblioteca de enlaces.

Normalmente, se utiliza el **EmbeddedJar** acción de compilación para que el. JAR se empaqueta de manera automática en la biblioteca de enlaces. Esta es la opción más sencilla &ndash; código de bytes de Java en el. JAR se convierte en código de bytes de Dex y se incrusta (junto con los contenedores CCW administrados) en el APK. Si desea mantener el. JAR independiente de la biblioteca de enlaces, puede usar el **InputJar** option; sin embargo, debe asegurarse de que el. Archivo JAR está disponible en el dispositivo que ejecuta la aplicación. 

Establece la acción de compilación en **EmbeddedJar**: 

[![Seleccione la acción de compilación EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

A continuación, abra el proyecto de propiedades para configurar el *.NET Framework de destino*. Si el. JAR usa las API de Android, establezca la plataforma de destino en el nivel de API que el. Se espera el JAR. Normalmente, el desarrollador de la. Archivo JAR indicará qué nivel de API (o niveles) que el. JAR es compatible con. (Para obtener más información sobre la configuración de .NET Framework de destino y niveles de API de Android en general, vea [niveles de API de Android descripción](~/android/app-fundamentals/android-api-levels.md).)

Establecer nivel de la API de destino de la biblioteca de enlaces (en este ejemplo, estamos utilizando el nivel de API 19): 

[![Nivel de API de destino establecido en API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


Por último, compile la biblioteca de enlaces. Aunque pueden aparecer algunos mensajes de advertencia, el proyecto de biblioteca de enlaces debe compilarse correctamente y generar un resultado. DLL en la siguiente ubicación: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>Uso de la biblioteca de enlaces

Para consumir esto. DLL de la aplicación Xamarin.Android, realice lo siguiente:

1.  Agregue una referencia a la biblioteca de enlaces.

2.  Realizar llamadas en el. JAR a través de los contenedores CCW administrados. 

En los pasos siguientes, crearemos una aplicación mínima que utiliza la biblioteca de enlaces para descargar y mostrar una imagen en un `ImageView`; "trabajo pesado" se realiza mediante el código que se encuentra en el. Archivo JAR. 

En primer lugar, cree una nueva aplicación de Xamarin.Android que utiliza la biblioteca de enlaces. Haga clic en la solución y seleccione **Agregar nuevo proyecto**; asigne al nuevo proyecto **BindingTest**. Estamos creando esta aplicación en la misma solución que la biblioteca de enlaces para simplificar este tutorial; Sin embargo, la aplicación que utiliza la biblioteca de enlaces en su lugar, puede residir en una solución distinta: 

[![Agregar nuevo proyecto BindingTest](binding-a-jar-images/07-add-new-project-sml.png)](binding-a-jar-images/07-add-new-project.png#lightbox)

Haga clic en el **referencias** nodo de la **BindingTest** de proyecto y seleccione **Agregar referencia...** :

[![Derecho Agregar referencia](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Compruebe el **JarBinding** proyecto creado anteriormente y haga clic en **Aceptar**:

[![Seleccione proyecto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Abra la **referencias** nodo de la **BindingTest** proyecto y compruebe que la **JarBinding** referencia está presente: 

[![JarBinding aparece en las referencias](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Modificar el **BindingTest** diseño (**Main.axml**) para que tenga un único `ImageView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/imageView" />
</LinearLayout>
```

Agregue el siguiente `using` instrucción **MainActivity.cs** &ndash; Esto hace posible tener acceso fácilmente a los métodos de basados en Java `Picasso` clase que reside en la biblioteca de enlaces:

```csharp
using Com.Squareup.Picasso;
```

Modificar el `OnCreate` método para que use la `Picasso` clase para cargar una imagen de una dirección URL y mostrarlo en el `ImageView`: 

```csharp
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
        ImageView imageView = FindViewById<ImageView>(Resource.Id.imageView);

        // Use the Picasso jar library to load and display this image:
        Picasso.With (this)
            .Load ("http://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

Compilar y ejecutar el **BindingTest** proyecto. La aplicación se va a iniciar, y tras un breve retraso (en función de las condiciones de red), debe descargar y mostrar una imagen similar a la captura de pantalla siguiente:

[![Ejecución de captura de pantalla de BindingTest](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

¡Enhorabuena! Se ha enlazado correctamente una biblioteca de Java. JAR y usar en la aplicación Xamarin.Android.
 
 
## <a name="summary"></a>Resumen

En este tutorial, creamos una biblioteca de enlaces de un tercero. JAR de archivo, agrega la biblioteca de enlaces a una aplicación de prueba mínimo y, a continuación, se ejecutó la aplicación para comprobar que el código de C# puede llamar a código de Java que residen en el. Archivo JAR. 



## <a name="related-links"></a>Vínculos relacionados

- [Generar una biblioteca de enlaces de Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
