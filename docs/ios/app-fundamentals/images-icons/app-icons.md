---
title: "Iconos de aplicación"
description: "Esta artículo cubre incluidos y administrar un recurso de imagen en una aplicación Xamarin.iOS para usarse como un icono de la aplicación."
ms.topic: article
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/2017
ms.openlocfilehash: df9059b0e64b4a05b554f25b5f9d7f6031406633
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="application-icons"></a>Iconos de aplicación

_Esta artículo cubre incluidos y administrar un recurso de imagen en una aplicación Xamarin.iOS para usarse como un icono de la aplicación._

Los siguientes temas se tratarán en detalle:

* [Aplicaciones, servicios y los iconos de configuración](#icon-types) -los diferentes tipos de iconos necesarios para una aplicación de iOS.
* [Administración de iconos con catálogos de activos](#managing) : administración de iconos de aplicación con catálogos de activos.
* [Ilustración de iTunes](#itunes) -proporcionando iTunes ilustración necesarios para el método Ad-Hoc de la entrega de la aplicación.

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>Aplicaciones, servicios y los iconos de configuración

De la misma manera que una aplicación de Xamarin.iOS puede utilizar los activos de imagen para los controles de interfaz de usuario y como iconos de documento, activos de imagen pueden usarse para proporcionar iconos de la aplicación. Las capturas de pantalla siguientes desde un iPad, muestran los tres usos de los iconos en iOS:

- **Icono de la aplicación** -todas las aplicaciones de iOS deben definir un icono de la aplicación. Éste es el icono que se van a puntear el usuario desde la pantalla principal de iOS para iniciar la aplicación. Además, se usa este icono por centro de juegos, si procede. Ejemplo: 

    [![](app-icons-images/000.png "Icono de la aplicación")](app-icons-images/000-full.png#lightbox)
- **Noticias destacadas icono** : cada vez que el usuario escribe el nombre de una aplicación en una búsqueda de Spotlight, se muestra este icono. Ejemplo: 

    [![](app-icons-images/000a.png "Icono de noticias destacadas")](app-icons-images/000a-full.png#lightbox)
- **Icono de configuración** : si el usuario escribe la **configuración** aplicación en su dispositivo iOS, este icono se mostrará al final de la **configuración** lista para la aplicación. Ejemplo: 

    [![](app-icons-images/000b.png "Icono de configuración")](app-icons-images/000b-full.png#lightbox)

Se necesitarán los siguientes tamaños de activos de imagen y las soluciones para admitir todos los tipos de icono requiere una aplicación de Xamarin.iOS destinatarios iOS 5 a través de iOS 9 (o posterior):

<table cellpadding="7" cellspacing="0" width="100%">
    <tr valign="top">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="5" align="center" bgcolor="#F0F0F0"><b>iPhone</b></td>
    </tr>
    <tr valign="center">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 5 y 6</b></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 7 & 8</b></td>
        <td align="center" bgcolor="#F9F9F9"><b>iOS 9 & 10<b><br/><i>(iPhone 6 y 7 más)</i></td>
    </tr>
    <tr valign="top" bgcolor="#F0F0F0">
        <td width="200" align="center"><b>Tipo de icono</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>3x</b></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Icono de aplicación</td>
        <td align="center">57x57</td>
        <td align="center">114x114</td>
        <td align="center" style="color:#BBBBBB;">60x60<sup>(1)</sup></td>
        <td align="center">120x120</td>
        <td align="center">180x180</td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Noticias destacadas</td>
        <td align="center">29x29</td>
        <td align="center">58x58</td>
        <td align="center" style="color:#BBBBBB;">40x40<sup>(2)</sup></td>
        <td align="center">80x80</td>
        <td align="center">120x120</td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Configuración</td>
        <td align="center" style="color:#BBBBBB;">29x29<sup>(3)(4)</sup></td>
        <td align="center" style="color:#BBBBBB;">58x58<sup>(3)(4)</sup></td>
        <td align="center">-</td>
        <td align="center">-</td>
        <td align="center">87x87</td>
    </tr>
</table>

<table cellpadding="7" cellspacing="0" width="100%">
    <tr valign="top">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="5" align="center" bgcolor="#F0F0F0"><b>iPad</b></td>
    </tr>
    <tr valign="center">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 5 y 6</b></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 7 & 8</b></td>
        <td colspan="1" align="center" bgcolor="#F9F9F9"><b>iOS&nbsp;9 & 10</b></td>
    </tr>
    <tr valign="top" bgcolor="#F0F0F0">
        <td width="200" align="center"><b>Tipo de icono</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>2x<br/>iPad&nbsp;Pro</b></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Icono de aplicación</td>
        <td align="center">72x72</td>
        <td align="center">144x144</td>
        <td align="center">76x76</td>
        <td align="center">152x152</td>
        <td align="center">167x167<sup>(6)</sup></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Noticias destacadas</td>
        <td align="center">50x50</td>
        <td align="center">100x100</td>
        <td align="center">40x40</td>
        <td align="center">80x80</td>
        <td align="center" style="color:#BBBBBB;">120x120<sup>(5)</sup></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Configuración</td>
        <td align="center" style="color:#BBBBBB;">29x29<sup>(3)(5)</sup></td>
        <td align="center" style="color:#BBBBBB;">58x58<sup>(3)(5)</sup></td>
        <td align="center">-</td>
        <td align="center">-</td>
        <td align="center" style="color:#BBBBBB;">58x58<sup>(5)</sup></td>
    </tr>
</table>

1. _Tanto Visual Studio para Mac y Xcode ya no se admite la configuración de 1 imagen x para iOS 7._
2. _No se admite la configuración de una imagen de 1 x para iOS 7 cuando se usa Asset catálogos._
3. _iOS 7 y 8 usar los mismos tamaños de imagen como iOS 5 y 6._
4. _Usa las mismas imágenes y tamaños como el icono de servicios._
5. _Utiliza los mismos iconos de tamaño como iPhone._
6. _Solo se admite con conjuntos de imágenes del catálogo de activos._

Para obtener más información acerca de los iconos, vea de Apple [icono y tamaños de las imágenes](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) documentación.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>Administración de iconos con catálogos de activos

Para los iconos, una clase especial `AppIcons` conjunto de imágenes puede agregarse a la `Assets.xcassets` archivo de proyecto de la aplicación. Todas las versiones de la imagen debe admitir todas las resoluciones se incluyen en el _xcasset_ y agrupan. Un editor de Visual Studio para Mac especial permite al desarrollador incluir y configurar estas imágenes gráficamente.

Para utilizar un catálogo de activos, haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Desplácese hacia abajo hasta la **iconos de aplicación** sección.
3. Desde el **origen** lista desplegable lista, asegúrese de **AppIcons** está seleccionado: 

    ![](app-icons-images/migrate01.png "Asegúrese de que está seleccionado AppIcons")
4. Desde el **el Explorador de soluciones**, haga doble clic en el `Assets.xcassets` archivo para abrirlo y editarlo: 

    ![](app-icons-images/asset01.png "El archivo Assets.xcassets en el Explorador de soluciones")
5. Seleccione `AppIcons` en la lista de activos para mostrar el `Icon Editor`: 

    ![](app-icons-images/asset02.png "El editor de AppIcons")
6. Haga clic en el tipo de icono y seleccione un archivo de imagen para el tipo o tamaño necesario o bien, arrastre en una imagen de una carpeta y colóquela en el tamaño deseado.
7. Haga clic en el **abiertos** botón para incluir la imagen en el proyecto y establézcalo en la xcasset.
8. Repita este paso para todas las imágenes necesarias.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga doble clic en el **Info.plist** un archivo en el **el Explorador de soluciones**:

    ![](app-icons-images/icon01w.png "Seleccione Info.plist")
2. Haga clic en el **activos visuales** ficha y haga clic en el **usar el catálogo de Asset** situado bajo **iconos de aplicación**: 

    ![](app-icons-images/icon02w.png "Seleccione la pestaña activos visuales")
4. Desde el **el Explorador de soluciones**, expanda la **catálogo de activos** carpeta: 

    ![](app-icons-images/image009.png "Expanda la carpeta del catálogo de activos")
5. Haga doble clic en el **Media** archivo para abrirlo en el editor: 

    ![](app-icons-images/image010.png "Abra el archivo multimedia en el editor")
6. En el **el Explorador de propiedades** el programador puede seleccionar los diferentes tipos y tamaños de iconos necesarios.
7. Haga clic en el tipo de icono y seleccione un archivo de imagen para el tipo o el tamaño necesario.
8. Haga clic en el **abiertos** botón para incluir la imagen en el proyecto y establézcalo en la xcasset.
9. Repita este paso para todas las imágenes necesarias.

-----

Este es el método preferido de incluidos y administración de activos de imagen que se usará para proporcionar iconos de aplicaciones, servicios y la configuración para una aplicación.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migración de Info.plist a catálogos de activos

Para una aplicación Xamarin.iOS existente mediante el `Info.plist` para administrar sus iconos de archivo, se recomienda que el desarrollador cambiarla a usar el `AppIcons` activos de imagen dentro de la `Assets.xcassets`.

Haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Desplácese hacia abajo hasta la **iconos de aplicación** sección.
3. Desde el **origen** lista desplegable, seleccione **migración a Asset catálogos**: 

    ![](app-icons-images/migrate02.png "Seleccione migra a catálogos de activos")
4. Los iconos existentes definen en el `Info.plist` archivo se migrará a un `AppIcons` Establecer imagen agregado a `Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "El conjunto de imágenes de AppIcons en el Assets.xcassets")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Haga clic en el iPhone sección de iconos: 

    ![](app-icons-images/image007.png "Editor de iconos de RHE iPhone")
3. Desplácese hacia abajo hasta la **iconos** sección.
4. Desde el **catálogo de activos** lista desplegable, seleccione **Use Asset catálogos**.
5. Los iconos existentes definen en el `Info.plist` archivo se migrará a un `Images` conjunto agregado a `Assets.xcassets`.
6. Guarde los cambios en el archivo `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Ilustraciones de iTunes

Si usa el método Ad-Hoc de la entrega de la aplicación (ya sea para los usuarios corporativos o para pruebas beta en dispositivos reales), el desarrollador también debe incluir un 512 x 512 y una imagen de 1024 x 1024 que se usará para representar la aplicación en iTunes.

Para especificar las ilustraciones de iTunes, haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Desplácese hasta la **iTunes ilustración** sección del editor: 

    ![](app-icons-images/itunes01.png "Desplácese hasta la sección del material gráfico del editor de iTunes")
3. Para las imágenes que faltan, haga clic en la miniatura en el editor, seleccione el archivo de imagen para la ilustración iTunes deseado en el cuadro de diálogo Abrir archivo y haga clic en el **Aceptar** botón.
4. Repita este paso hasta que todos los necesitan se especifican imágenes de la aplicación.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.

2. Haga clic en el **activos visuales** pestaña y expanda el **iTunes ilustración**: 

    ![](app-icons-images/itunes01w.png "Edición de iTunes ilustraciones en Visual Studio")
4. Para las imágenes que faltan, haga clic en la miniatura en el editor, seleccione el archivo de imagen para la ilustración iTunes deseado en el cuadro de diálogo Abrir archivo y haga clic en el **abiertos** botón.
5. Repita este paso hasta que todos los necesitan se especifican imágenes de la aplicación.

-----

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icono personalizado y directrices para la creación de imagen](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
