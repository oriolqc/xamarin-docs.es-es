---
title: Diseño para aplicaciones de tableta y escritorio
description: En este artículo se explica cómo optimizar los diseños de aplicación de Xamarin.Forms para tabletas, en lugar de teléfonos.
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: b98d1fcf0917b9e25d774a92d56bf90bdd291978
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998641"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Diseño para aplicaciones de tableta y escritorio

Xamarin.Forms es compatible con todos los tipos de dispositivo disponibles en las plataformas compatibles, por lo que los teléfonos, además de las aplicaciones también pueden ejecutar:

* iPad,
* Tabletas Android,
* Tabletas de Windows y equipos de escritorio (que ejecutan Windows 10).

Esta página se describe brevemente:

* admitidos [tipos de dispositivo](#Device_Types), y
* cómo [optimizar](#optimize) diseños para tabletas y teléfonos.

<a name="Device_Types" />

## <a name="device-types"></a>Tipos de dispositivos

Dispositivos de pantalla de mayor tamaño están disponibles para todas las plataformas compatibles con Xamarin.Forms.

### <a name="ipads-ios"></a>iPad (iOS)

La plantilla de Xamarin.Forms automáticamente incluye soporte técnico de iPad configurando el **Info.plist > dispositivos** si se establece en **Universal** (lo que significa que se admiten iPhone y iPad).

Para proporcionar una experiencia de inicio agradable y asegúrese de que se usa la resolución de pantalla completa en todos los dispositivos, debe asegurarse de que un [pantalla de inicio específicas de iPad](~/ios/app-fundamentals/images-icons/launch-screens.md) (utilizar un guión gráfico) se proporciona. Esto garantiza que la aplicación se representa correctamente en dispositivos iPad con mini, iPad y iPad Pro.

Antes de iOS 9 ocupaba todas las aplicaciones de la pantalla completa en el dispositivo, pero ahora pueden realizar algunas iPad [dividir la pantalla multitarea](~/ios/platform/multitasking.md).
Esto significa que la aplicación puede tardar hasta simplemente una columna delgada en el lateral de la pantalla, 50% del ancho de la pantalla, o toda la pantalla.

[![](tablet-images/ipad-sml.png "División de ejemplos de pantalla del iPad")](tablet-images/ipad.png#lightbox "iPad división pantalla de ejemplo")

Funcionalidad de pantalla dividida significa que debe diseñar la aplicación para funcionar bien con tan solo 320 píxeles de anchos o tanto como 1366 píxeles de ancho.

### <a name="android-tablets"></a>Tabletas Android

El ecosistema de Android tiene una gran variedad de tamaños de pantalla compatibles, desde teléfonos pequeños hasta grandes tabletas. Xamarin.Forms puede admitir todos los tamaños de pantalla, pero como con las otras plataformas que es posible que desea ajustar la interfaz de usuario para dispositivos de mayor tamaño.

Cuando se admiten muchas diferentes resoluciones de pantalla, puede proporcionar los recursos de imagen nativa en diferentes tamaños para optimizar la experiencia del usuario.
Revise el [recursos de Android](~/android/app-fundamentals/resources-in-android/index.md) documentación (y, en particular [crear recursos en varios tamaños de pantalla](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) para obtener más información sobre cómo estructurar las carpetas y los nombres de archivo en su aplicación Android proyecto para incluir los recursos de la imagen optimizada de la aplicación.

### <a name="windows-tablets-and-desktops"></a>Escritorios y tabletas de Windows

Para admitir las tabletas y equipos de escritorio con Windows, deberá usar [compatibilidad con Windows UWP](~/xamarin-forms/platform/windows/installation/index.md), que compila las aplicaciones universales que se ejecutan en Windows 10.

Aplicaciones que se ejecutan en equipos de escritorio y tabletas de Windows que pueden ajustarse dimensiones arbitrarias además al funcionamiento de pantalla completa.

[![](tablet-images/splitscreen-sml.png "Ejemplo de la pantalla de división de Windows")](tablet-images/splitscreen.png#lightbox "Windows dividen el ejemplo de la pantalla")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>Optimizar para tableta y escritorio

Puede ajustar la interfaz de usuario de Xamarin.Forms en función de si un teléfono o se usa el dispositivo de escritorio o tableta. Esto significa que puede optimizar la experiencia del usuario para dispositivos con pantallas grandes, como tabletas y equipos de escritorio.


### <a name="deviceidiom"></a>Device.Idiom

Puede usar el [ `Device` ](~/xamarin-forms/platform/device.md) clase para cambiar el comportamiento de la interfaz de usuario o aplicación. Mediante el `Device.Idiom` enumeración puede

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Este enfoque se puede expandir para realizar cambios significativos en los diseños de página individuales, o incluso para procesar completamente diferentes páginas en pantallas más grandes.

### <a name="leveraging-masterdetailpage"></a>Aprovechamiento de MasterDetailPage

El [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) es ideal para pantallas más grandes, especialmente en el iPad que utiliza el [ `UISplitViewController` ](https://developer.xamarin.com/api/type/UIKit.UISplitViewController/) para proporcionar una experiencia de iOS nativo.

Revisión [esta entrada de blog de Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/) para ver cómo se puede adaptar la interfaz de usuario para que los teléfonos usan un diseño y pantallas más grandes pueden utilizar otra (con el `MasterDetailPage`).



## <a name="related-links"></a>Vínculos relacionados

- [Blog de Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [Ejemplo de MyShoppe](https://github.com/jamesmontemagno/myshoppe)
