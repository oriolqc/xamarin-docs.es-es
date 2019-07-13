---
title: Iconos de aplicación de Xamarin.iOS
description: 'Este documento describe cómo trabajar con varios iconos de aplicación de Xamarin.iOS: el icono de la aplicación, Spotlight iconos, los iconos de la configuración e ilustraciones de iTunes.'
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2017
ms.openlocfilehash: cf2da9f7fe8d90c52517118cc3b4aa0e046d1f69
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864548"
---
# <a name="application-icons-in-xamarinios"></a>Iconos de aplicación de Xamarin.iOS

Los siguientes temas se tratarán en detalle:

* [Aplicación, Spotlight y configuración de iconos](#icon-types) -los diferentes tipos de iconos necesarios para una aplicación de iOS.
* [Administrar iconos con catálogos de activos](#managing) : administrar iconos de aplicación con catálogos de activos.
* [Ilustraciones de iTunes](#itunes) -proporcionando las ilustraciones de iTunes necesarios para el método Ad Hoc de la entrega de la aplicación.

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>Aplicación, Spotlight e iconos de configuración

En la misma manera que una aplicación Xamarin.iOS puede utilizar los activos de imagen para los controles de interfaz de usuario y como iconos de documento, los activos de imagen pueden utilizarse para proporcionar iconos de aplicación. Las capturas de pantalla siguiente desde un iPad muestran los tres usos de los iconos en iOS:

- **Icono de la aplicación** -todas las aplicaciones de iOS deben definir un icono de aplicación. Éste es el icono que aprovechará el usuario desde la pantalla principal de iOS para iniciar la aplicación. Además, este icono se utiliza por Game Center, si procede. Ejemplo: 

    [![](app-icons-images/000.png "Icono de la aplicación")](app-icons-images/000-full.png#lightbox)
- **Icono de Spotlight** : cada vez que el usuario escribe el nombre de una aplicación en una búsqueda de Spotlight, este icono se muestra. Ejemplo: 

    [![](app-icons-images/000a.png "Icono de contenido destacado")](app-icons-images/000a-full.png#lightbox)
- **Icono de configuración** : si el usuario escribe el **configuración** aplicación en su dispositivo iOS, este icono se mostrará al final de la **configuración** lista para la aplicación. Ejemplo: 

    [![](app-icons-images/000b.png "Icono de configuración")](app-icons-images/000b-full.png#lightbox)

Los siguientes tamaños de activos de imagen y resoluciones será necesaria para admitir todos los tipos de icono requeridos por una aplicación de Xamarin.iOS para iOS 5 a través de iOS 9 (o posterior):

### <a name="iphone-icon-sizes"></a>Tamaño de los iconos de iPhone

- **iPhone: iOS 9 y 10 (iPhone 6 y 7 Plus)**

    ||3x|
    |---|---|
    |Icono de aplicación|180x180|
    |Contenido destacado|120x120|
    |Configuración|87x87|

- **iPhone: iOS 7 y 8**

    ||1x|2x|
    |---|---|---|
    |Icono de aplicación|60x60<sup>1</sup>|120x120|
    |Contenido destacado|40x40<sup>2</sup>|80x80|
    |Configuración|-|-|

- **iPhone: iOS 5 y 6**

    ||1x|2x|
    |---|---|---|
    |Icono de aplicación|57x57|114x114|
    |Contenido destacado|29x29|58x58|
    |Configuración|29x29<sup>3, 4</sup>|58x58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>Tamaño de los iconos de iPad

- **iPad: iOS 9 & 10**

    ||2x (iPad Pro)|
    |---|---|
    |Icono de aplicación|167x167<sup>6</sup>|
    |Contenido destacado|120x120<sup>6</sup>|
    |Configuración|58x58<sup>5</sup>|

- **iPad: iOS 7 y 8**

    ||1x|2x|
    |---|---|---|
    |Icono de aplicación|76x76|152x152|
    |Contenido destacado|40x40|80x80|
    |Configuración|-|-|

- **iPad: iOS 5 y 6**

    ||1x|2x|
    |---|---|---|
    |Icono de aplicación|72x72|144x144|
    |Contenido destacado|50x50|100x100|
    |Configuración|29x29<sup>3, 5</sup>|58x58<sup>3, 5</sup>|

 1. Tanto con Visual Studio para Mac y Xcode ya no se admite establecer 1 imagen x para iOS 7.
 2. No se admite el establecimiento de una imagen de 1 x para iOS 7 cuando se usa catálogos de activos.
 3. iOS 7 y 8 use los mismos tamaños de imagen como iOS 5 y 6.
 4. Usa las mismas imágenes y tamaños como el icono Noticias destacadas.
 5. Usa los mismos iconos de tamaño como el iPhone.
 6. Solo se admite con conjuntos de imágenes del catálogo de activos.
 
 Para obtener más información acerca de los iconos, consulte Apple [icono y tamaños de imagen](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) documentación.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>Administrar iconos con catálogos de activos

Para los iconos, un especial `AppIcon` conjunto de imágenes se puede agregar a la `Assets.xcassets` archivo de proyecto de la aplicación. Todas las versiones de la imagen debe admitir todas las resoluciones se incluyen en el _xcasset_ y agrupados juntos. Un editor especial en Visual Studio para Mac permite al desarrollador incluir y configurar estas imágenes gráficamente.

Para usar un catálogo de recursos, realice lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Desplácese hacia abajo hasta la **iconos de aplicación** sección.
3. Desde el **origen** lista desplegable lista, asegúrese de **AppIcon** está seleccionado: 

    ![](app-icons-images/migrate01.png "Asegúrese de que está seleccionado AppIcon")
4. Desde el **el Explorador de soluciones**, haga doble clic en el `Assets.xcassets` archivo para abrirlo y editarlo: 

    ![](app-icons-images/asset01.png "El archivo Assets.xcassets en el Explorador de soluciones")
5. Seleccione `AppIcon` en la lista de los recursos para mostrar el `Icon Editor`:

    ![](app-icons-images/asset02.png "El editor AppIcon")
6. Haga clic en el tipo de icono especificado y seleccione un archivo de imagen para el tipo o tamaño necesario o bien, arrastre la imagen de una carpeta y colóquela en el tamaño deseado.
7. Haga clic en el **abierto** botón para incluir la imagen en el proyecto y establézcalo en la xcasset.
8. Repita este paso para todas las imágenes necesarias.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el **Info.plist** de archivos en el **el Explorador de soluciones**:

    ![](app-icons-images/icon01w.png "Seleccione el archivo Info.plist")
2. Haga clic en el **activos visuales** ficha y haga clic en el **usar catálogo de recursos** situado bajo **iconos de aplicación**: 

    ![](app-icons-images/icon02w.png "Seleccione la pestaña activos visuales")
3. Desde el **el Explorador de soluciones**, expanda el **catálogo de activos** carpeta: 

    ![](app-icons-images/image009.png "Expanda la carpeta del catálogo de activos")
4. Haga doble clic en el **Media** archivo para abrirlo en el editor: 

    ![](app-icons-images/image010.png "Abra el archivo multimedia en el editor")
5. En el **Explorador de propiedades** el programador puede seleccionar los diferentes tipos y tamaños de iconos necesarios.
6. Haga clic en el tipo de icono especificado y seleccione un archivo de imagen para el tipo o tamaño necesario.
7. Haga clic en el **abierto** botón para incluir la imagen en el proyecto y establézcalo en la xcasset.
8. Repita este paso para todas las imágenes necesarias.

-----

Este es el método preferido de incluidos y administración de activos de imagen que se usará para proporcionar iconos de aplicación, Spotlight y configuración de una aplicación.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migración de Info.plist a catálogos de activos

Para una aplicación de Xamarin.iOS existente con el `Info.plist` para administrar sus iconos de archivo, se recomienda que el desarrollador cambiarla a usar el `AppIcons` activo de imagen dentro de la `Assets.xcassets`.

Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Desplácese hacia abajo hasta la **iconos de aplicación** sección.
3. Desde el **origen** lista desplegable, seleccione **migrar a catálogos de activos**: 

    ![](app-icons-images/migrate02.png "Seleccione migra a catálogos de activos")
4. Los iconos existentes definidos en el `Info.plist` archivo se migrarán a un `AppIcons` imagen conjunto agregado a `Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "El conjunto de imágenes de AppIcons en el Assets.xcassets")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Haga clic en el iPhone sección de iconos: 

    ![](app-icons-images/image007.png "Editor de iconos de iPhone RHE")
3. Desplácese hacia abajo hasta la **iconos** sección.
4. Desde el **catálogo de activos** lista desplegable, seleccione **catálogos de activos de uso**.
5. Los iconos existentes definidos en el `Info.plist` archivo se migrarán a un `Images` conjunto agregado a `Assets.xcassets`.
6. Guarde los cambios en el archivo `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Ilustraciones de iTunes

Si usa el método Ad Hoc de la entrega de la aplicación (ya sea para usuarios corporativos o para realizar pruebas beta en dispositivos reales), el desarrollador también debe incluir un 512 x 512 y una imagen de 1024 x 1024 que se usará para representar la aplicación en iTunes.

Para especificar las ilustraciones de iTunes, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Desplácese hasta la **ilustraciones de iTunes** sección del editor: 

    ![](app-icons-images/itunes01.png "Desplácese hasta la sección del material gráfico del editor de iTunes")
3. Para que falte alguna imagen, haga clic en la miniatura en el editor, seleccione el archivo de imagen para las ilustraciones de iTunes deseado en el cuadro de diálogo Abrir archivo y haga clic en el **Aceptar** botón.
4. Repita este paso hasta que todos necesitan se especifican imágenes de la aplicación.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.

2. Haga clic en el **activos visuales** pestaña y expanda el **ilustraciones de iTunes**: 

    ![](app-icons-images/itunes01w.png "Edición de iTunes Artwork en Visual Studio")
3. Para que falte alguna imagen, haga clic en la miniatura en el editor, seleccione el archivo de imagen para las ilustraciones de iTunes deseado en el cuadro de diálogo Abrir archivo y haga clic en el **abierto** botón.
4. Repita este paso hasta que todos necesitan se especifican imágenes de la aplicación.

-----

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icono personalizado y directrices para la creación de imagen](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
