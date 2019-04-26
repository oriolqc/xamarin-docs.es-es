---
title: Trabajar con iconos en Xamarin de watchOS
description: Este documento describe los distintos iconos necesarios para una aplicación de watchOS y cómo configurar una solución para incluir estos iconos.
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/26/2018
ms.openlocfilehash: 435af10484827826d53b767c2738e3945e0bae42
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345260"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>Trabajar con iconos en Xamarin de watchOS

Las soluciones de Apple Watch requieren dos conjuntos de iconos:

* Los iconos de aplicación de iOS que aparecerán en el iPhone.
* Iconos de Apple Watch se representará en un círculo en el menú de inspección y en las pantallas de notificación. También aparece el icono de la aplicación de inspección en el [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) aplicación iOS.

## <a name="apple-watch-icons"></a>Iconos de Apple Watch

| | | |
|-|-|-|
|Icono de la aplicación de iOS|Aparece en el iPhone e inicia la aplicación primaria|![icono de la aplicación de iOS](icons-images/icon-ios.png)|
|Icono de la aplicación de inspección|Aparece en la pantalla principal de Apple Watch|![icono de la aplicación para watchOS](icons-images/icon-home.png)|
||Aparece en las notificaciones de inspección|![icono de notificación de watchOS](icons-images/notification-icon.png)|
||Aparece en el [Apple Watch aplicación iOS](~/ios/watchos/app-fundamentals/settings.md)|![icono de la aplicación de inspección de iOS](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Configuración de la solución

Para asegurarse de que la aplicación de iOS y aplicación del reloj muestran el nombre correcto y el icono, siga estas instrucciones para cada proyecto:

### <a name="ios-app"></a>Aplicación de iOS

Hacer referencia a la [iconos de aplicación de iOS guía](~/ios/app-fundamentals/images-icons/app-icons.md) para asegurarse de que están configurados correctamente los iconos de la aplicación iOS.

#### <a name="infoplist"></a>Info.plist

La cadena que aparece junto a la aplicación del reloj en la [aplicación de configuración de Apple Watch](~/ios/watchos/app-fundamentals/settings.md) está configurado en el **Info.plist de la aplicación de iOS**.

Confirme que su **Info.plist** tiene un `CFBundleName` clave y valor (Nota: Esto es diferente a la `CFBundleDisplayName`, puede tener ambos):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Aplicación de Apple Watch

Una vez su [aplicación primaria](~/ios/watchos/app-fundamentals/parent-app.md) ¿configuró sus iconos, deberá agregar un catálogo de recursos de icono de aplicación para la aplicación del reloj.

1. Haga doble clic en el proyecto de aplicación de inspección y seleccione **archivo > Agregar > nuevo archivo... > iOS > catálogo de activos** para agregar un catálogo de recursos al proyecto.

 ![](icons-images/newasset.png "Agregar un catálogo de recursos al proyecto")

2. Haga doble clic en el **AppIcon.appiconset/Contents.json** archivo

  ![](icons-images/xcassets-iconset-sml.png "El contenido de AppIcon")

3. Agregue todas las imágenes de watchOS, como se muestra en esta captura de pantalla:

  [![](icons-images/appicons-sml.png "Agregue todas las imágenes de watchOS, como se muestra en esta captura de pantalla")](icons-images/appicons.png#lightbox)

  Consulte [directrices del icono de Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/) para los tamaños necesarios (las dimensiones también se muestran en la pantalla). Recuerde que estos iconos se recortarán automáticamente para representar en un círculo.

  La lista de icono debe tener un aspecto similar al siguiente:

  ![](icons-images/xcassets-complete-sml.png "La lista de iconos en el Explorador de soluciones")

4. Para asegurarse de que el catálogo de activos se incluirán en la aplicación, agregue la siguiente clave y el valor para el **Info.plist de la aplicación de inspección**:

```xml
<key>XSAppIconAssets</key>
<string>Images.xcassets/AppIcon.appiconset</string>
```

Puede comprobar los iconos se configuran correcto comprobando la [Apple Watch configuración de la aplicación](~/ios/watchos/app-fundamentals/settings.md) en el simulador, iPhone o generar un [notificación](~/ios/watchos/platform/notifications.md) y confirmar el icono aparece en la notificación pantalla.

> [!NOTE]
> Los iconos no pueden tener un canal alfa (la aplicación se rechazarán durante el envío de App Store si existe un canal alfa). Puede comprobar si un canal alfa existe y quítela [mediante la aplicación de vista previa en Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).


## <a name="related-links"></a>Vínculos relacionados

- [Guía de icono e imágenes de watchOS de Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
