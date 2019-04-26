---
title: Apariencia de API de Xamarin.iOS
description: iOS le permite aplicar la configuración de la propiedad visual en un nivel de clase estática en lugar de en objetos individuales para que el cambio se aplica a todas las instancias de ese control en la aplicación.
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/15/2018
ms.openlocfilehash: bfbc902b0912527fea6aaa58c6706ef5a0ccbf8e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61178346"
---
# <a name="appearance-api-in-xamarinios"></a>Apariencia de API de Xamarin.iOS

_iOS le permite aplicar la configuración de la propiedad visual en un nivel de clase estática en lugar de en objetos individuales para que el cambio se aplica a todas las instancias de ese control en la aplicación._

Esta funcionalidad se expone en Xamarin.iOS a través de una variable static `Appearance` propiedad en todos los controles de UIKit que lo admiten. Por lo tanto, se puede personalizar fácilmente la apariencia visual (propiedades como como imagen tinte de color y fondo) para dar una apariencia coherente a la aplicación. La API de apariencia se introdujo en iOS 5 y si bien algunas partes de los han quedado obsoletas en iOS 9 sigue siendo una buena forma de lograr algunos estilos y efectos de temas en las aplicaciones de Xamarin.iOS.

## <a name="overview"></a>Información general

iOS permite que personalizar la apariencia de muchos controles de UIKit para que los controles estándares se ajustan a la personalización de marca que desea aplicar a la aplicación.

Hay dos maneras diferentes de aplicar una apariencia personalizada:

- **Directamente en una instancia del control** : puede establecer el color del matiz, imagen de fondo y posición del título (así como otros atributos) en varios controles, incluidos las barras de herramientas, barras de navegación, controles deslizantes y botones.

- **Establecer valores predeterminados en la propiedad estática de apariencia** : los atributos personalizables para cada control se exponen a través de la `Appearance` propiedad estática. Las personalizaciones que se aplica a estas propiedades se usará como el valor predeterminado para cualquier control de ese tipo que se crea después de establecer la propiedad.

La aplicación de ejemplo del aspecto muestra los tres métodos, como se muestra en estas capturas de pantalla:

[![](introduction-to-the-appearance-api-images/appearance01-sml.png "La aplicación de ejemplo del aspecto muestra los tres métodos")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

A partir de iOS 8, el proxy de apariencia se ha ampliado a TraitCollections.
 `AppearanceForTraitCollection` puede utilizarse para establecer la apariencia predeterminada de una colección de rasgo determinado. Puede leer más sobre esto en el [Introducción a guiones gráficos](~/ios/user-interface/storyboards/unified-storyboards.md) guía.

## <a name="setting-appearance-properties"></a>Establecer las propiedades de apariencia

En la primera pantalla, se utiliza la clase estática de apariencia para definir el estilo de los botones y elementos de amarillo/naranja similar al siguiente:

```csharp
// Set the default appearance values
UIButton.Appearance.TintColor = UIColor.LightGray;
UIButton.Appearance.SetTitleColor(UIColor.FromRGB(0,127,14), UIControlState.Normal);

UISlider.Appearance.ThumbTintColor = UIColor.Red;
UISlider.Appearance.MinimumTrackTintColor = UIColor.Orange;
UISlider.Appearance.MaximumTrackTintColor = UIColor.Yellow;

UIProgressView.Appearance.ProgressTintColor = UIColor.Yellow;
UIProgressView.Appearance.TrackTintColor = UIColor.Orange;
```

Los estilos del elemento verde se establecen como éste, en el `ViewDidLoad` método que invalida los valores predeterminados y la *apariencia* clase estática:

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>Uso de UIAppearance en Xamarin.Forms

La API de apariencia puede ser útil al [aplicar estilos a la aplicación de iOS](~/xamarin-forms/platform/ios/formatting.md#uiappearance) en soluciones de Xamarin.Forms. Unas pocas líneas en el `AppDelegate` clase puede ayudar a implementar una combinación de colores específicos sin tener que crear un [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

### <a name="custom-themes-and-uiappearance"></a>UIAppearance y temas personalizados

iOS permite muchos atributos visuales del usuario que los controles de interfaz estando "temáticos" con el *UIAppearance* API para forzar que todas las instancias de un control determinado para tener la misma apariencia. Esto se expone como una propiedad Appearance en muchas clases de control de interfaz de usuario, no en las instancias individuales del control. Establecer una propiedad de presentación en estático `Appearance` propiedad afecta a todos los controles de ese tipo en la aplicación.

Para comprender mejor el concepto, considere un ejemplo.

Para cambiar un determinado `UISegmentedControl` para que tenga un tono de color fucsia, se haría referencia al control específico en nuestra pantalla como esta en `ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

Como alternativa, establezca el valor en el panel de propiedades del diseñador:

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "Tinte de panel de propiedades")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

La siguiente imagen ilustra que esto establece el tono sólo el control denominado 'sg1'.

[![](introduction-to-the-appearance-api-images/image53.png "Establecer el tinte de control individual")](introduction-to-the-appearance-api-images/image53.png#lightbox)

Para establecer muchos controles de esta manera sería ineficaz completamente, por lo que podemos establecer estático en su lugar `Appearance` propiedad en la propia clase. Esto se muestra en el código siguiente:

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

La siguiente imagen ilustra ahora ambos controles segmentados con el aspecto que se establece en Magenta:

[![](introduction-to-the-appearance-api-images/image54.png "Establecer el tinte de control apariencia")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` se deben establecer las propiedades al principio del ciclo de vida de aplicación, como en el AppDelegate `FinishedLaunching` evento, o en un ViewController antes de que se muestran los controles afectados.

Hacer referencia a la [Introducción a la API de apariencia](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) para obtener más información.

## <a name="related-links"></a>Vínculos relacionados

- [Apariencia (ejemplo)](https://developer.xamarin.com/samples/monotouch/Appearance/)
- [Referencia del protocolo UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Apariencia en Xamarin.Forms](~/xamarin-forms/platform/ios/formatting.md#uiappearance)
