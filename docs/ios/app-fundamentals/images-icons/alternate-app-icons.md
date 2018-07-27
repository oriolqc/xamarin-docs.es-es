---
title: Iconos de aplicación alternativos en Xamarin.iOS
description: Este documento describe cómo usar los iconos de aplicación alternativos en Xamarin.iOS. Describe cómo agregar estos iconos a un proyecto de Xamarin.iOS, cómo modificar el archivo Info.plist y cómo administrar el icono de la aplicación mediante programación.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 3f009d84d1d38c379f65de52949c66f3e86fc654
ms.sourcegitcommit: ffb0f3dbf77b5f244b195618316bbd8964541e42
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2018
ms.locfileid: "39275994"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Iconos de aplicación alternativos en Xamarin.iOS

_En este artículo se describe el uso de iconos de aplicación alternativos en Xamarin.iOS._

Apple ha agregado varias mejoras a iOS 10.3 que permitan a una aplicación administrar su icono:

 - `ApplicationIconBadgeNumber` : Obtiene o establece el distintivo de icono de la aplicación en el Springboard.
 - `SupportsAlternateIcons` -If `true` la aplicación tiene un conjunto alternativo de iconos.
 - `AlternateIconName` -Devuelve el nombre del icono alternativo seleccionado actualmente o `null` si mediante el icono principal.
 - `SetAlternameIconName` : Use este método para cambiar el icono de la aplicación en el icono alternativo especificado.

![](alternate-app-icons-images/icons04.png "Un ejemplo de alerta cuando una aplicación cambia su icono")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Adición de iconos alternativos a un proyecto de Xamarin.iOS

Para permitir que una aplicación que cambie a un icono alternativo, necesitará una colección de imágenes de icono que se incluirán en el proyecto de aplicación de Xamarin.iOS. Estas imágenes no se puede agregar al proyecto mediante el típico `Assets.xcassets` método, se debe agregar a la **recursos** carpeta directamente.

Haga lo siguiente:

1. Seleccione las imágenes de icono requerido en una carpeta, selecciónelos y arrástrelos hasta la **recursos** carpeta en el **el Explorador de soluciones**:

    ![](alternate-app-icons-images/icons00.png "Seleccione las imágenes de iconos en una carpeta")

2. Cuando se le solicite, seleccione **copia**, **usar la misma acción para todos los archivos seleccionados** y haga clic en el **Aceptar** botón:

    ![](alternate-app-icons-images/icons02.png "Agregar archivo al cuadro de diálogo de carpeta")

3. El **recursos** carpeta tendrá un aspecto similar al siguiente cuando complete:

    ![](alternate-app-icons-images/icons01.png "La carpeta de recursos debe tener este aspecto")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modificar el archivo Info.plist

Con las imágenes necesarias que se agrega a la **recursos** carpeta, el [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) clave se deberá agregarse al proyecto **Info.plist** archivo. Esta clave se definirá el nombre del nuevo icono y las imágenes que lo componen.

Haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el archivo **Info.plist** para abrirlo para su edición.
2. Cambie a la **origen** vista.
3. Agregar un **agrupar iconos** clave y dejar el **tipo** establecido en **diccionario**.
4. Agregar un `CFBundleAlternateIcons` clave y establecer el **tipo** a **diccionario**.
5. Agregar un `AppIcon2` clave y establecer el **tipo** a **diccionario**. Este será el nombre del nuevo conjunto de icono de aplicación alternativos.
6. Agregar un `CFBundleIconFiles` clave y establecer el **tipo** a **matriz**
7. Agregar una nueva cadena a la `CFBundleIconFiles` matriz para cada archivo de icono salir de la extensión y el `@2x`, `@3x`, sufijos etc. (ejemplo `100_icon`). Repita este paso para todos los archivos que componen el conjunto de iconos alternativos.
8. Agregar un `UIPrerenderedIcon` clave para el `AppIcon2` diccionario, establezca el **tipo** a **booleano** y el valor en **No**.
9. Guarde los cambios en el archivo.

Resultante **Info.plist** archivo debe ser similar al siguiente cuando complete:

![](alternate-app-icons-images/icons03.png "El archivo Info.plist completado")

O bien, como esta si se abre en un editor de texto:

```xml
<key>CFBundleIcons</key>
<dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>AppIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>100_icon</string>
                <string>114_icon</string>
                <string>120_icon</string>
                <string>144_icon</string>
                <string>152_icon</string>
                <string>167_icon</string>
                <string>180_icon</string>
                <string>29_icon</string>
                <string>40_icon</string>
                <string>50_icon</string>
                <string>512_icon</string>
                <string>57_icon</string>
                <string>58_icon</string>
                <string>72_icon</string>
                <string>76_icon</string>
                <string>80_icon</string>
                <string>87_icon</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <false/>
        </dict>
    </dict>
</dict>
```

<a name="Managing-the-Apps-Icon" />

## <a name="managing-the-apps-icon"></a>Administrar el icono de aplicación 

Con las imágenes de icono que se incluye en el proyecto de Xamarin.iOS y **Info.plist** archivo configurado correctamente, el programador puede utilizar una de las muchas características nuevas que se agregó a iOS 10.3 para controlar el icono de la aplicación.

El `SupportsAlternateIcons` propiedad de la `UIApplication` clase permite al desarrollador ver si una aplicación admite iconos alternativos. Por ejemplo:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

El `ApplicationIconBadgeNumber` propiedad de la `UIApplication` clase permite al desarrollador a obtener o establecer el número actual de notificación de icono de la aplicación en el Springboard. El valor predeterminado es cero (0). Por ejemplo:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

El `AlternateIconName` propiedad de la `UIApplication` clase permite al desarrollador obtener el nombre del icono de aplicación alternativo seleccionado actualmente o no devuelve `null` si la aplicación está usando el icono principal. Por ejemplo:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

El `SetAlternameIconName` propiedad de la `UIApplication` clase permite al desarrollador cambiar el icono de la aplicación. Pase el nombre del icono para seleccionar o `null` para devolver el icono principal. Por ejemplo:

```csharp
partial void UsePrimaryIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName (null, (err) => {
        Console.WriteLine ("Set Primary Icon: {0}", err);
    });
}

partial void UseAlternateIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName ("AppIcon2", (err) => {
        Console.WriteLine ("Set Alternate Icon: {0}", err);
    });
}
```

Cuando se ejecuta la aplicación y el usuario seleccione un icono alternativo, se mostrará una alerta similar al siguiente:

![](alternate-app-icons-images/icons04.png "Un ejemplo de alerta cuando una aplicación cambia su icono")

Si el usuario cambia el icono principal, se mostrará una alerta similar al siguiente:

![](alternate-app-icons-images/icons05.png "Un ejemplo de alerta cuando una aplicación cambia al icono principal")

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de agregar iconos de aplicación alternativos a un proyecto de Xamarin.iOS y usarlas dentro de la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de iOSTenThree](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
