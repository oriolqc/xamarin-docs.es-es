---
title: Trabajar con iconos
ms.topic: article
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 38a2e8b8cd5932bf96c1e0032a6f47627c3ea592
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-icons"></a>Trabajar con iconos

Soluciones de Apple Watch requieren dos conjuntos de iconos:

* Los iconos de aplicación de iOS que aparecerán en el iPhone.
* Iconos de Apple Watch que se representará en un círculo en el menú de inspección y en las pantallas de notificación. El icono de la aplicación de inspección también aparece en la [de Apple Watch](~/ios/watchos/app-fundamentals/settings.md) aplicación iOS.

## <a name="apple-watch-icons"></a>Iconos de Apple Watch

<table align="center" border="1" cellpadding="1" cellspacing="1">
    <tr>
      <td valign="top">
        <b>Icono de la aplicación de iOS</b>
      </td>
      <td valign="top">
Aparece en el iPhone e inicia la aplicación principal </td>
      <td>
        <img src="icons-images/icon-ios.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top" rowspan="3">
        <b>Icono de la aplicación de inspección</b>
      </td>
      <td valign="top">
Aparece en la pantalla principal de Apple Watch </td>
      <td>
        <img src="icons-images/icon-home.png" class="tableimg" />
      </td>
    </tr>
    <tr>
      <td valign="top">
Aparece en las notificaciones de inspección </td>
      <td>
        <img src="icons-images/notification-icon.png" class="tableimg" />
      </td>
    </tr>
    <tr>
      <td valign="top">
Aparece en el <a href="~/ios/watchos/app-fundamentals/settings.md">Apple Watch aplicación iOS</a>
      </td>
      <td>
        <a href="icons-images/watch-app.png">
          <img src="icons-images/watch-app-sml.png" class="tableimg">
        </a>
      </td>
    </tr>
    <tbody>
</table>



## <a name="configuring-your-solution"></a>Configuración de la solución

Para asegurarse de que la aplicación de iOS y aplicación de inspección mostrar el icono y el nombre correcto, siga estas instrucciones para cada proyecto:

### <a name="ios-app"></a>Aplicación iOS

Hacer referencia a la [iconos de aplicación de iOS guía](~/ios/app-fundamentals/images-icons/app-icons.md) para asegurarse de que los iconos de la aplicación iOS están configurados correctamente.

#### <a name="infoplist"></a>Info.plist

La cadena que aparece junto a la aplicación de inspección en la [aplicación de configuración de Apple Watch](~/ios/watchos/app-fundamentals/settings.md) está configurado en el **Info.plist de la aplicación de iOS**.

Confirme que su **Info.plist** tiene un `CFBundleName` clave y valor (Nota: Esto es diferente a la `CFBundleDisplayName`, puede tener ambos):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Aplicación de Apple Watch

Una vez el [aplicación primaria](~/ios/watchos/app-fundamentals/parent-app.md) ha configurado sus iconos, debe agregar un catálogo de activos de icono de aplicación a la aplicación de inspección.

1. Haga doble clic en el proyecto de aplicación de inspección y seleccione **archivo > Agregar > nuevo archivo... > iOS > catálogo de activos** para agregar un catálogo de activos para el proyecto.

 ![](icons-images/newasset.png "Agregar un catálogo de activos para el proyecto")

2. Haga doble clic en el **AppIcons.appiconset/Contents.json** archivo

  ![](icons-images/xcassets-iconset-sml.png "El contenido de AppIcons")

3. Agregue todas las imágenes de watchOS, como se muestra en esta captura de pantalla:

  [ ![](icons-images/appicons-sml.png "Agregue todas las imágenes de watchOS, como se muestra en esta captura de pantalla")](icons-images/appicons.png)

  Hacer referencia a [instrucciones de icono de Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/IconandImageSizes.html) para conocer los tamaños necesarios (las dimensiones también se muestran en la pantalla). Recuerde que se recortarán automáticamente estos iconos para representar en un círculo.

  La lista de iconos debe tener un aspecto similar al siguiente:

  ![](icons-images/xcassets-complete-sml.png "La lista de iconos en el Explorador de soluciones")

4. Para asegurarse de que el catálogo de activos se incluye en la aplicación, agregue la siguiente clave y el valor para el **Info.plist de la aplicación de inspección**:

```xml
<key>XSAppIconAssets</key>
<string>Images.xcassets/AppIcons.appiconset</string>
```

Puede comprobar los iconos se configuran correcta mediante la comprobación de la [aplicación de configuración de Apple Watch](~/ios/watchos/app-fundamentals/settings.md) en el simulador, iPhone o generar un [notificación](~/ios/watchos/platform/notifications.md) y confirmar el icono aparece en la notificación pantalla.

> [!NOTE]
> **Tenga en cuenta**: iconos no pueden tener un canal alfa (la aplicación se rechazarán durante el envío de la tienda de aplicaciones si hay un canal alfa). Puede comprobar si un canal alfa existe y quítela [mediante la aplicación de vista previa en Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).


## <a name="related-links"></a>Vínculos relacionados

- [Guían de Apple icono & imágenes](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/IconandImageSizes.html)
