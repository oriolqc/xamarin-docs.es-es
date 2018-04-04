---
title: Iconos de aplicación alternativo
description: Este artículo incluye el uso de iconos de aplicaciones alternativo en Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 8d9f27d58a881878aabeda4326805eec726c247c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="alternate-app-icons"></a>Iconos de aplicación alternativo

_Este artículo incluye el uso de iconos de aplicaciones alternativo en Xamarin.iOS._

Apple ha agregado varias mejoras para iOS 10.3 que permiten a una aplicación administrar su icono:

 - `ApplicationIconBadgeNumber` -Obtiene o establece el identificador del icono de aplicación en el Springboard.
 - `SupportsAlternateIcons` -If `true` la aplicación tiene un conjunto alternativo de iconos.
 - `AlternateIconName` -Devuelve el nombre del icono alternativo seleccionado actualmente o `null` si utilizando el icono principal.
 - `SetAlternameIconName` : Utilice este método para cambiar el icono de la aplicación al icono alternativo determinado.

![](alternate-app-icons-images/icons04.png "Un ejemplo de alerta cuando una aplicación cambia su icono")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Agregar iconos alternativas a un proyecto Xamarin.iOS

Para permitir que una aplicación cambiar a un icono alternativo, necesitará una colección de imágenes de icono que se incluirá en el proyecto de aplicación de Xamarin.iOS. Estas imágenes no se puede agregar al proyecto con típica `Assets.xcassets` método, se debe agregar a la **recursos** carpeta directamente.

Haga lo siguiente:

1. Seleccione las imágenes de icono necesario en una carpeta, seleccione todos y arrástrelos hasta la **recursos** carpeta en el **el Explorador de soluciones**:

    ![](alternate-app-icons-images/icons00.png "Seleccione las imágenes de iconos de una carpeta")

2. Cuando se le pida, seleccione **copia**, **usar la misma acción para todos los archivos seleccionados** y haga clic en el **Aceptar** botón:

    ![](alternate-app-icons-images/icons02.png "El archivo agregar al cuadro de diálogo de carpeta")

3. El **recursos** carpeta debe ser similar al siguiente cuando completa:

    ![](alternate-app-icons-images/icons01.png "La carpeta de recursos debe tener el siguiente aspecto")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modificar el archivo Info.plist

Con las imágenes necesarias agregadas a la **recursos** carpeta, el [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) clave deberá agregarse al proyecto **Info.plist** archivo. Esta clave definirá el nombre del nuevo icono y las imágenes que lo componen.

Haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el archivo **Info.plist** para abrirlo para su edición.
2. Cambie a la **origen** vista.
3. Agregar un **agrupar iconos** clave y dejar el **tipo** establecido en **diccionario**.
4. Agregar un `CFBundleAlternateIcons` clave y establecer el **tipo** a **diccionario**.
5. Agregar un `AppIcons2` clave y establecer el **tipo** a **diccionario**. Este será el nombre del nuevo conjunto de icono de aplicación alternativo.
6. Agregar un `CFBundleIconFiles` clave y establecer el **tipo** a **matriz**
7. Agregar una nueva cadena en el `CFBundleIconFiles` matriz para cada archivo de icono omitiendo la extensión y el `@2x`, `@3x`, sufijos etc. (ejemplo `100_icon`). Repita este paso para todos los archivos que componen el conjunto de iconos alternativo.
8. Agregar un `UIPrerenderedIcon` clave para la `AppIcons2` diccionario, establecer el **tipo** a **booleano** y el valor en **No**.
9. Guarde los cambios en el archivo.

Resultante **Info.plist** archivo debe ser similar al siguiente cuando completa:

![](alternate-app-icons-images/icons03.png "El archivo Info.plist completado")

O bien, al igual que este si abrir en un editor de texto:

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

## <a name="managing-the-apps-icon"></a>Administrar el icono de la aplicación 

Con las imágenes de icono incluidas en el proyecto Xamarin.iOS y **Info.plist** archivo configurado correctamente, el programador puede utilizar una de las muchas nuevas características agregadas a iOS 10.3 para controlar el icono de la aplicación.

El `SupportsAlternateIcons` propiedad de la `UIApplication` clase permite al desarrollador comprobar si una aplicación compatible con iconos alternativos. Por ejemplo:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

El `ApplicationIconBadgeNumber` propiedad de la `UIApplication` clase permite al desarrollador obtener o establecer el número actual de distintivo del icono de la aplicación en el Springboard. El valor predeterminado es cero (0). Por ejemplo:

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

Cuando se ejecuta la aplicación y el usuario seleccione un icono alternativo, aparecerá una alerta similar al siguiente:

![](alternate-app-icons-images/icons04.png "Un ejemplo de alerta cuando una aplicación cambia su icono")

Si el usuario vuelve a cambiar al icono principal, se mostrará una alerta similar al siguiente:

![](alternate-app-icons-images/icons05.png "Un ejemplo de alerta cuando una aplicación cambia al icono de principal")

<a name="Summary" />

## <a name="summary"></a>Resumen

Agregar iconos de aplicación alternativa a un proyecto de Xamarin.iOS y usarlas dentro de la aplicación se ha descrito en este artículo.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de iOSTenThree](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
