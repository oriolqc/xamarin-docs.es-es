---
title: "Conceptos básicos de recursos Android"
ms.topic: article
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: c8f6832f618c37b3593f28c8efaeb87e4df5df03
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="android-resource-basics"></a>Conceptos básicos de recursos Android

Casi todas las aplicaciones Android tendrá algún tipo de recursos en ellas; como mínimo a menudo tienen los diseños de interfaz de usuario en forma de archivos XML. Cuando se crea una aplicación Xamarin.Android, recursos predeterminados son el programa de instalación mediante la plantilla de proyecto Xamarin.Android:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Archivos de recursos](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Archivos de recursos](android-resource-basics-images/01-resource-files-xs.png)
 
-----

Los cinco archivos que componen los recursos predeterminados creados en la carpeta de recursos:

-  **Icon.png** &ndash; el icono predeterminado para la aplicación

-  **Main.axml** &ndash; el archivo de diseño de interfaz de usuario predeterminada para una aplicación. Tenga en cuenta que mientras Android usa la **.xml** Xamarin.Android de extensión de archivo, usa el **.axml** la extensión de archivo.

-  **Strings.XML** &ndash; una tabla de cadenas para ayudar a la localización de la aplicación

-  **AboutResources.txt** &ndash; esto no es necesario y puede eliminarse de forma segura. Simplemente proporciona una introducción de alto nivel de la carpeta de recursos y los archivos en ella.

-  **Resource.Designer.cs** &ndash; este archivo se genera automáticamente y se mantiene Xamarin.Android y contiene el único identificadores asignados a cada recurso. Esto es idéntico en propósito al archivo R.java que tendría una aplicación Android escrita en Java y muy similares. Se crea automáticamente con las herramientas de Xamarin.Android y se volverá a generar de vez en cuando.

<a name="Creating_and_Accessing_Resources" />

## <a name="creating-and-accessing-resources"></a>Crear y obtener acceso a recursos

Creación de recursos es tan simple como agregar archivos al directorio para el tipo de recurso en cuestión. La captura de pantalla siguiente muestra los recursos de cadena para configuraciones regionales de alemán se agregaron a un proyecto. Cuando **Strings.xml** se agregó al archivo, el **acción de compilación** se establece automáticamente en **AndroidResource** con las herramientas de Xamarin.Android:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Acción de Strings.xml establecido en AndroidResource de compilación](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Acción de Strings.xml establecido en AndroidResource de compilación](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

Esto permite que las herramientas de Xamarin.Android compilar correctamente e incrustar los recursos en el archivo APK. Si por alguna razón el **acción de compilación** no está establecido en **recursos Android**, a continuación, los archivos se excluirá el APK y cualquier intento de cargar o acceder a los recursos se producirá un error en tiempo de ejecución y el se bloqueará la aplicación.

Además, es importante tener en cuenta que mientras Android solo admite nombres de archivo en minúsculas para los elementos de recurso, Xamarin.Android es un poco más permisivo con los errores; admitirán los nombres de archivo de mayúsculas y minúsculas. La convención de nombres de imagen consiste en usar minúsculas con caracteres de subrayado como separadores (por ejemplo, **mi\_imagen\_name.png**). Tenga en cuenta que los nombres de recursos no se puede procesar si se utilizan guiones ni espacios como separadores.

Una vez que los recursos se han agregado a un proyecto, hay dos maneras de utilizarlos en una aplicación &ndash; mediante programación (en código) o de archivos XML.

<a name="Referencing_Resources_Programmatically" />

## <a name="referencing-resources-programmatically"></a>Hacer referencia a los recursos mediante programación

Para obtener acceso a estos archivos mediante programación, se asignan un identificador de recurso único. Este identificador de recurso es un entero definido en una clase especial denominada `Resource`, que se encuentra en el archivo **Resource.designer.cs**, y es algo parecido a esto:

```csharp
public partial class Resource
{
    public partial class Attribute
    {
    }
    public partial class Drawable {
        public const int Icon=0x7f020000;
    }
    public partial class Id
    {
        public const int Textview=0x7f050000;
    }
    public partial class Layout
    {
        public const int Main=0x7f030000;
    }
    public partial class String
    {
        public const int App_Name=0x7f040001;
        public const int Hello=0x7f040000;
    }
}
```

Cada identificador de recurso se encuentra dentro de una clase anidada que se corresponde con el tipo de recurso. Por ejemplo, cuando el archivo **Icon.png** se agregó al proyecto, Xamarin.Android actualiza el `Resource` (clase), crear una clase anidada denominada `Drawable` con una constante interna denominada `Icon`.
Esto permite que el archivo **Icon.png** que se haga referencia en código como `Resource.Drawable.Icon`. La `Resource` clase no se debe editar manualmente, tal y como Xamarin.Android sobrescribirá los cambios realizados en él.

Al hacer referencia a los recursos mediante programación (en código), puede tener acceso a través de la jerarquía de clases de recursos que utiliza la sintaxis siguiente:

```xml
@[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **PackageName** &ndash; el paquete que está proporcionando el recurso y solo es necesario cuando se utilizan recursos de otros paquetes.

-  **ResourceType** &ndash; éste es el tipo de recurso anidado que esté dentro de la clase de recurso se ha descrito anteriormente.

-  **Nombre del recurso** &ndash; es el nombre de archivo del recurso (sin la extensión) o el valor del atributo android: name para los recursos que se encuentran en un elemento XML.

<a name="Referencing_Resources_from_XML" />

## <a name="referencing-resources-from-xml"></a>Referencia a recursos de XML

Accede a los recursos en un archivo XML un siguiendo una sintaxis especial:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>.
```

-  **PackageName** &ndash; el paquete que está proporcionando el recurso y solo es necesario cuando se utilizan recursos de otros paquetes.

-  **ResourceType** &ndash; éste es el tipo de recurso anidado que esté dentro de la clase de recursos.

-  **Nombre del recurso** &ndash; es el nombre de archivo del recurso (*sin* la extensión de tipo de archivo) o el valor de la `android:name` atributo para los recursos que se encuentran en un elemento XML.

Por ejemplo, el contenido de un archivo de diseño, **Main.axml**, son los siguientes:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">
    <ImageView android:id="@+id/myImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/flag" />
</LinearLayout>
```

Este ejemplo tiene un [ `ImageView` ](https://developer.xamarin.com/recipes/android/controls/imageview) que requiere un recurso puede dibujar denominado **marca**. El `ImageView` tiene su `src` atributo establecido en  **@drawable/flag** . Cuando se inicia la actividad, tendrá un aspecto Android en el directorio **recurso/Drawable** en un archivo denominado **flag.png** (la extensión de archivo podría ser otro formato de imagen, como **flag.jpg**) cargue ese archivo y mostrarlo en el `ImageView`.
Cuando se ejecuta esta aplicación, sería algo parecido a la siguiente imagen:

![ImageView localizado](android-resource-basics-images/03-localized-screenshot.png)

