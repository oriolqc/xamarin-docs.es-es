---
title: "Diseño de aplicaciones de escritorio y de Tablet PC"
ms.topic: article
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: 053696ebf37e73e3b121e2aa52b80b7ea1b8ed64
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Diseño de aplicaciones de escritorio y de Tablet PC

Xamarin.Forms admite todos los tipos de dispositivo disponibles en las plataformas compatibles, por lo que además de teléfonos, aplicaciones también pueden ejecutarse:

* iPad,
* Tabletas Android,
* Tabletas de Windows y equipos de escritorio (que ejecuta Windows 8.1 o Windows 10).

Esta página se describe brevemente:

* admiten [tipos de dispositivo](#Device_Types), y
* cómo [optimizar](#optimize) diseños para tabletas y teléfonos.

<a name="Device_Types" />

## <a name="device-types"></a>Tipos de dispositivo

Dispositivos de pantalla mayor están disponibles para todas las plataformas compatibles con Xamarin.Forms.

### <a name="ipads-ios"></a>iPad (iOS)

La plantilla de Xamarin.Forms incluye automáticamente compatibilidad iPad configurando la **Info.plist > dispositivos** si se establece en **Universal** (lo que significa que se admiten iPhone y iPad).

Para proporcionar una experiencia de inicio agradable y asegúrese de que la resolución de pantalla completa se utiliza en todos los dispositivos, debe asegurarse de que un [pantalla de inicio del iPad específica](~/ios/app-fundamentals/images-icons/launch-screens.md) (utilizando un guión gráfico) se proporciona. Esto garantiza que la aplicación se representa correctamente en dispositivos iPad con mini, iPad y iPad Pro.

Antes de iOS 9 tardaron todas las aplicaciones de la pantalla completa en el dispositivo, pero ahora puede realizar algunas iPad [dividir pantalla multitarea](~/ios/platform/multitasking.md).
Esto significa que la aplicación podría tardar hasta simplemente una columna delgada en el lateral de la pantalla, 50% del ancho de la pantalla, o toda la pantalla.

[ ![](tablet-images/ipad-sml.png "iPad ejemplo de pantalla de división")](tablet-images/ipad.png "iPad ejemplo de pantalla de división")

Funcionalidad de pantalla dividida significa que debe diseñar la aplicación para funcionar correctamente con tan solo 320 píxeles de ancho o tanto como 1366 píxeles de ancho.

### <a name="android-tablets"></a>Tabletas Android

El ecosistema de Android tiene una gran variedad de tamaños de pantalla compatibles, desde teléfonos pequeños hasta grandes tabletas. Xamarin.Forms puede admitir todos los tamaños de pantalla, pero como con las otras plataformas puede ajustar la interfaz de usuario para los dispositivos más grandes.

Cuando se admiten muchos diferentes resoluciones de pantalla, puede proporcionar los recursos de imagen nativa de diferentes tamaños para optimizar la experiencia del usuario.
Revise el [recursos Android](~/android/app-fundamentals/resources-in-android/index.md) documentación (y, en particular [crear recursos para varios tamaños de pantalla](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) para obtener más información sobre cómo estructurar las carpetas y los nombres de archivo en su aplicación Android proyecto para incluir recursos de imagen optimizada de la aplicación.

### <a name="windows-tablets-and-desktops"></a>Tabletas de Windows y equipos de escritorio

Para admitir las tabletas y equipos de escritorio con Windows, debe usar uno de los dos tipos de proyecto compatibles:

* [Windows 8.1](~/xamarin-forms/platform/windows/installation/tablet.md) -
  compila aplicaciones específicamente para Windows 8.1 tabletas y equipos de escritorio.
* [Soporte técnico de Windows UWP](~/xamarin-forms/platform/windows/installation/universal.md) -
  compila las aplicaciones universales que se ejecutan en Windows 10 teléfonos, tabletas y equipos de escritorio.

Aplicaciones que se ejecutan en equipos de escritorio y las tabletas de Windows al que pueden ajustarse a las dimensiones arbitrarios además funcionamiento de pantalla completa.

[ ![](tablet-images/splitscreen-sml.png "Windows dividir pantalla ejemplo")](tablet-images/splitscreen.png "Windows dividir ejemplo de pantalla")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>Optimizar para Tablet PC y escritorio

Puede ajustar la interfaz de usuario de Xamarin.Forms en función de si un teléfono o dispositivo de tableta/escritorio está siendo utilizado. Esto significa que se puede optimizar la experiencia del usuario para dispositivos de pantalla de gran tamaño, como tabletas y equipos de escritorio.


### <a name="deviceidiom"></a>Device.Idiom

Puede usar el [ `Device` ](~/xamarin-forms/platform/device.md) clase para cambiar el comportamiento de la interfaz de usuario o aplicación. Mediante el `Device.Idiom` puede (enumeración)

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Este enfoque se puede expandir para realizar cambios significativos en los diseños de página individuales, o incluso para representar las páginas completamente diferentes en pantallas más grandes.

### <a name="leveraging-masterdetailpage"></a>Aprovechamiento de MasterDetailPage

El [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) es ideal para pantallas más grandes, sobre todo en el iPad donde usa la [ `UISplitViewController` ](https://developer.xamarin.com/api/type/UIKit.UISplitViewController/) para proporcionar una experiencia de iOS nativo.

Revisión [esta entrada de blog de Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/) para ver cómo se puede adaptar la interfaz de usuario para que los teléfonos usan un diseño y pantallas más grandes pueden utilizar otra (con la `MasterDetailPage`).



## <a name="related-links"></a>Vínculos relacionados

- [Xamarin Blog](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [Ejemplo de MyShoppe](https://github.com/jamesmontemagno/myshoppe)
