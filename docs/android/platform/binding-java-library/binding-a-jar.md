---
title: Enlace un. JAR
description: Este tutorial proporciona instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin.Android de Android. Archivo JAR.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 3c84b29807fd4a181ed867095645005bf9ba4df0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120013"
---
# <a name="binding-a-jar"></a>Enlace un. JAR

_Este tutorial proporciona instrucciones paso a paso para crear una biblioteca de enlaces de Java de Xamarin.Android de Android. Archivo JAR._

## <a name="overview"></a>Información general

La Comunidad Android ofrece muchas bibliotecas de Java que puede usar en la aplicación. Estas bibliotecas de Java a menudo se empaquetan en. Formato de archivo JAR (Java Archive), pero se puede empaquetar una. JAR en un *biblioteca de enlaces de Java* para que su funcionalidad no está disponible para las aplicaciones de Xamarin.Android. El propósito de la biblioteca de enlaces de Java es hacen que las API en el. Archivo JAR disponibles para C# código a través de contenedores de código generado automáticamente.

Las herramientas de Xamarin pueden generar una biblioteca de enlaces de entrada de uno o más. Archivos JAR. La biblioteca de enlaces (. Ensamblado DLL) contiene lo siguiente: 

-   El contenido del original. Archivos JAR.

-   Administrado que se puede llamar contenedores (MCW), que son C# tipos que ajustan los tipos de Java correspondiente dentro de la. Archivos JAR.

El código generado de MCW utiliza JNI (Java Native Interface) para reenviar las llamadas de API subyacente. Archivo JAR. Puede crear bibliotecas de enlaces para cualquiera. Archivo JAR que estaba destinaba originalmente para usarse con Android (tenga en cuenta que las herramientas de Xamarin no admite actualmente el enlace de bibliotecas no son de Android Java). También puede optar por crear la biblioteca de enlaces sin incluir el contenido de la. Archivo del producto para que el archivo DLL tiene una dependencia el. JAR en tiempo de ejecución.

En esta guía, podrá recorrer los aspectos básicos de la creación de una biblioteca de enlaces para un único. Archivo JAR. Le explicaremos con un ejemplo de dónde va todo derecho &ndash; es decir, donde ninguna personalización o la depuración de los enlaces necesaria. 
[Creación de enlaces con metadatos](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) ofrece un ejemplo de un escenario más avanzado, donde el proceso de enlace no es completamente automático y se requiere cierta cantidad de intervención manual. Para obtener información general de biblioteca Java enlace en general (con un ejemplo de código básico), consulte [enlace a una biblioteca de Java](~/android/platform/binding-java-library/index.md). 

 
## <a name="walkthrough"></a>Tutorial

En el siguiente tutorial, vamos a crear una biblioteca de enlaces para [Picasso](http://square.github.io/picasso/), una popular Android. Archivo JAR que proporciona la imagen de carga y la funcionalidad de almacenamiento en caché. Usaremos los siguientes pasos para enlazar **picasso 2.x.x.jar** para crear un nuevo ensamblado de .NET que podemos usar en un proyecto de Xamarin.Android: 

1. Cree un nuevo proyecto de biblioteca de enlaces de Java.

2. Agregar el. Archivo JAR en el proyecto.

3. Configure la acción adecuada para el. Archivo JAR.

4. Elija una plataforma de destino que el. Es compatible con el archivo JAR.

5. Compile la biblioteca de enlaces.

Una vez que hemos creado la biblioteca de enlaces, desarrollaremos una pequeña aplicación de Android que muestra nuestra capacidad para llamar a las API en la biblioteca de enlaces. En este ejemplo, queremos tener acceso a los métodos de **picasso 2.x.x.jar**:

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
```

Después de que se genere una biblioteca de enlaces para **picasso 2.x.x.jar**, podemos llamar a estos métodos desde C#. Por ejemplo:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>Creación de la biblioteca de enlaces

Antes de empezar con los pasos siguientes, descargue [picasso 2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

En primer lugar, cree un nuevo proyecto de biblioteca de enlaces. En Visual Studio para Mac o Visual Studio, cree una nueva solución y seleccione el *biblioteca de enlaces de Android* plantilla. (Las capturas de pantalla en este tutorial utiliza Visual Studio, pero Visual Studio para Mac es muy similar). Nombre de la solución **JarBinding**: 

[![Crear proyecto de biblioteca JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

La plantilla incluye un **archivos JAR** carpeta donde se agrega su. JAR(s) al proyecto de biblioteca de enlaces. Haga clic en el **archivos JAR** carpeta y seleccione **Agregar > elemento existente**: 

[![Agregar elemento existente](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Navegue hasta la **picasso 2.x.x.jar** archivo descargado anteriormente, selecciónelo y haga clic en **agregar**: 

[![Seleccione el archivo jar y haga clic en Agregar](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Compruebe que la **picasso 2.x.x.jar** archivo se agregó correctamente al proyecto: 

[![Archivo JAR que se agregan al proyecto](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

Al crear un proyecto de biblioteca de enlaces de Java, debe especificar si el. Archivo JAR está incrustado en la biblioteca de enlaces o empaqueta por separado. Para ello, especifique uno de los siguientes *acciones de compilación*: 

-   **EmbeddedJar** &ndash; el. Archivo JAR se incrustarán en la biblioteca de enlaces.

-   **InputJar** &ndash; el. JAR se mantienen separado de la biblioteca de enlaces.

Normalmente, se usa el **EmbeddedJar** acción de compilación para que el. Automáticamente se empaqueta el archivo JAR en la biblioteca de enlaces. Esta es la opción más sencilla &ndash; código de bytes de Java en el. Archivo JAR se convierte en código de bytes Dex y se incrusta (junto con los contenedores RCW administrados) en el APK. Si desea mantener el. JAR independiente de la biblioteca de enlaces, puede usar el **InputJar** opción; sin embargo, debe asegurarse de que el. Archivo JAR está disponible en el dispositivo que ejecuta la aplicación. 

Establece la acción de compilación en **EmbeddedJar**: 

[![Seleccione la acción de compilación EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

A continuación, abra el proyecto de propiedades para configurar el *.NET Framework de destino*. Si el. JAR usa las API de Android, establezca la plataforma de destino en el nivel de API que el. Se espera el archivo JAR. Normalmente, el desarrollador de la. Archivo JAR indicará qué nivel de API (o niveles) que el. Es compatible con archivo JAR. (Para obtener más información sobre la configuración de .NET Framework de destino y los niveles de API de Android en general, consulte [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).)

Establecer el destino de la API de nivel de la biblioteca de enlaces (en este ejemplo, estamos usando el nivel de API 19): 

[![Nivel de API de destino establecido en 19 de API](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


Por último, compile la biblioteca de enlaces. Aunque es posible que se muestren algunos mensajes de advertencia, el proyecto de biblioteca de enlaces debe compilarse correctamente y generar una salida. Archivo DLL en la siguiente ubicación: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>Uso de la biblioteca de enlaces

Para consumir esto. Archivo DLL en la aplicación de Xamarin.Android, realice lo siguiente:

1.  Agregue una referencia a la biblioteca de enlaces.

2.  Realizar llamadas en el. JAR a través de los contenedores RCW administrados. 

En los siguientes pasos, vamos a crear una aplicación mínima que utiliza la biblioteca de enlaces para descargar y mostrar una imagen en un `ImageView`; "pesado" se realiza mediante el código que reside en el. Archivo JAR. 

En primer lugar, cree una nueva aplicación de Xamarin.Android que consume la biblioteca de enlaces. Haga clic en la solución y seleccione **Agregar nuevo proyecto**; el nuevo proyecto el nombre **BindingTest**. Estamos creando esta aplicación en la misma solución que la biblioteca de enlaces con el fin de simplificar este tutorial; Sin embargo, la aplicación que consume la biblioteca de enlaces en su lugar, podría residir en una solución diferente: 

[![Agregar nuevo proyecto BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Haga clic en el **referencias** nodo de la **BindingTest** del proyecto y seleccione **Agregar referencia...** :

[![Agregar referencia de derecho](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Compruebe el **JarBinding** proyecto que creó anteriormente y haga clic en **Aceptar**:

[![Seleccione el proyecto JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Abra el **referencias** nodo de la **BindingTest** del proyecto y compruebe que la **JarBinding** referencia está presente: 

[![JarBinding aparece en referencias](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

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

Agregue el siguiente `using` instrucción **MainActivity.cs** &ndash; Esto hace posible tener acceso fácilmente a los métodos de basada en Java `Picasso` clase que reside en la biblioteca de enlaces:

```csharp
using Com.Squareup.Picasso;
```

Modificar el `OnCreate` método para que use el `Picasso` clase para cargar una imagen desde una dirección URL y lo mostrará en el `ImageView`: 

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

Compilar y ejecutar el **BindingTest** proyecto. La aplicación se va a iniciar y después de un breve retraso (en función de las condiciones de red), debe descargar y mostrar una imagen similar a la captura de pantalla siguiente:

[![Ejecución de la captura de pantalla de BindingTest](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

¡Enhorabuena! Se ha enlazado correctamente una biblioteca de Java. JAR y usado en la aplicación de Xamarin.Android.
 
 
## <a name="summary"></a>Resumen

En este tutorial, hemos creado una biblioteca de enlaces para un tercero. JAR de archivo, agrega la biblioteca de enlaces a una aplicación de prueba mínima y, a continuación, se ejecutó la aplicación para comprobar que nuestra C# código puede llamar a código de Java que residen en el. Archivo JAR. 



## <a name="related-links"></a>Vínculos relacionados

- [Creación de una biblioteca de enlaces de Java (vídeo)](https://university.xamarin.com/classes#10090)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
