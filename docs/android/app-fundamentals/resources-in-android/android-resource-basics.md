---
title: Conceptos básicos de recursos de Android
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: 207644f5a5d3d346214ba090dcd450e55fde2657
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241323"
---
# <a name="android-resource-basics"></a>Conceptos básicos de recursos de Android

Casi todas las aplicaciones Android tendrán algún tipo de recursos en ellas. como mínimo a menudo tienen diseños de interfaz de usuario en forma de archivos XML. Cuando se crea una aplicación de Xamarin.Android, los recursos predeterminados son el programa de instalación de la plantilla de proyecto de Xamarin.Android:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Archivos de recursos](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Archivos de recursos](android-resource-basics-images/01-resource-files-xs.png)
 
-----

Los cinco archivos que componen los recursos predeterminados se crearon en la carpeta de recursos:

-  **Icon.png** &ndash; el icono predeterminado para la aplicación

-  **Main.axml** &ndash; el archivo de diseño de interfaz de usuario predeterminada para una aplicación. Tenga en cuenta que mientras que Android usa la **.xml** extensión de archivo, Xamarin.Android usa el **.axml** la extensión de archivo.

-  **Strings.XML** &ndash; una tabla de cadenas para ayudar con la localización de la aplicación

-  **AboutResources.txt** &ndash; esto no es necesario y es posible que se eliminen de forma segura. Solo se proporciona una introducción de alto nivel de la carpeta de recursos y los archivos en ella.

-  **Resource.Designer.cs** &ndash; este archivo se genera automáticamente y se mantiene Xamarin.Android y contiene el único Id. asignado a cada recurso. Esto es muy similar y propósito idéntico al archivo R.java que tendría una aplicación Android escrita en Java. Se crea automáticamente con las herramientas de Xamarin.Android y se volverá a generar de vez en cuando.


## <a name="creating-and-accessing-resources"></a>Creación y acceso a recursos

Creación de recursos es tan sencillo como agregar archivos al directorio para el tipo de recurso en cuestión. La siguiente captura de pantalla muestra los recursos de cadena para configuraciones regionales de alemán se agregaron a un proyecto. Cuando **Strings.xml** se agregó al archivo, el **acción de compilación** se establece automáticamente en **AndroidResource** por las herramientas de Xamarin.Android:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Acción para Strings.xml establecido en AndroidResource de compilación](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Acción para Strings.xml establecido en AndroidResource de compilación](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

Esto permite que las herramientas de Xamarin.Android compilar correctamente e incrustar los recursos en el archivo APK. Si por alguna razón el **acción de compilación** no está establecido en **recursos Android**, a continuación, los archivos se excluirán del Apk y cualquier intento de cargar o acceder a los recursos se producirá un error en tiempo de ejecución y el se bloqueará la aplicación.

Además, es importante tener en cuenta que, aunque solo Android admite nombres de archivo en minúsculas para los elementos de recurso, Xamarin.Android es un poco más permisivo con; admitirán los nombres de archivo de mayúsculas y minúsculas. La convención de nombres de imagen es usar minúsculas con caracteres de subrayado como separadores (por ejemplo, **mi\_imagen\_name.png**). Tenga en cuenta que no se puede procesar los nombres de recursos si se usan guiones ni espacios como separadores.

Una vez que se han agregado a un proyecto de recursos, hay dos maneras de usarlas en una aplicación &ndash; mediante programación (dentro de código) o desde archivos XML.


## <a name="referencing-resources-programmatically"></a>Hacer referencia a recursos mediante programación

Para obtener acceso a estos archivos mediante programación, se les asignan un identificador de recurso único. Este identificador de recurso es un entero definido en una clase especial denominada `Resource`, que se encuentra en el archivo **Resource.designer.cs**, y es algo parecido a esto:

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

Cada identificador de recurso se encuentra dentro de una clase anidada que se corresponde con el tipo de recurso. Por ejemplo, cuando el archivo **Icon.png** se agregó al proyecto, Xamarin.Android se actualiza el `Resource` (clase), crear una clase anidada denominada `Drawable` dentro de una constante denominada `Icon`.
Esto permite que el archivo **Icon.png** va a hacer referencia en el código como `Resource.Drawable.Icon`. La `Resource` clase no se debe editar manualmente, ya que Xamarin.Android sobrescribirá los cambios realizados en él.

Al hacer referencia a los recursos mediante programación (en código), se puede obtener acceso a través de la jerarquía de clases de recursos que usa la sintaxis siguiente:

```xml
@[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **Nombre del paquete** &ndash; el paquete que está proporcionando el recurso y solo es obligatorio cuando se utilizan los recursos de otros paquetes.

-  **ResourceType** &ndash; este es el tipo de recurso anidado que esté dentro de la clase de recurso se ha descrito anteriormente.

-  **Nombre del recurso** &ndash; es el nombre de archivo del recurso (sin la extensión) o el valor del atributo android: name para los recursos que se encuentran en un elemento XML.


## <a name="referencing-resources-from-xml"></a>Hacer referencia a recursos de XML

Se tiene acceso a recursos en un archivo XML un siguiendo una sintaxis especial:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>.
```

-  **Nombre del paquete** &ndash; el paquete que está proporcionando el recurso y solo es obligatorio cuando se utilizan los recursos de otros paquetes.

-  **ResourceType** &ndash; este es el tipo de recurso anidado que esté dentro de la clase de recursos.

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

Este ejemplo tiene un [ `ImageView` ](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview) que requiere un recurso drawable denominado **marca**. El `ImageView` tiene su `src` atributo establecido en **@drawable/flag**. Cuando se inicia la actividad, tendrá un aspecto Android dentro del directorio **recursos/Drawable** para un archivo denominado **flag.png** (la extensión de archivo podría ser otro formato de imagen, como **flag.jpg**) cargue ese archivo y lo mostrará en el `ImageView`.
Cuando se ejecuta esta aplicación, sería algo parecido a la siguiente imagen:

![ImageView localizado](android-resource-basics-images/03-localized-screenshot.png)

