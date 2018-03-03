---
title: API de apariencia
description: "iOS le permite aplicar la configuración de la propiedad visual en un nivel de clase estática en lugar de en objetos individuales para que el cambio se aplica a todas las instancias de ese control en la aplicación."
ms.topic: article
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 6d2a454665691c028fe8307940a5662a98ab9c98
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="appearance-api"></a>API de apariencia

_iOS le permite aplicar la configuración de la propiedad visual en un nivel de clase estática en lugar de en objetos individuales para que el cambio se aplica a todas las instancias de ese control en la aplicación._

Esta funcionalidad se expone en Xamarin.iOS a través de una variable static `Appearance` propiedad en todos los controles de UIKit que lo admiten. Apariencia visual (propiedades como como imagen de fondo y colores de tono), por tanto, puede personalizarse fácilmente para dar una apariencia coherente a la aplicación. La API de apariencia se introdujo en iOS 5 y mientras algunas partes de la han quedado obsoletas en iOS 9 sigue siendo una buena manera de lograr algunos estilos y efectos de temas en aplicaciones de Xamarin.iOS.

## <a name="overview"></a>Información general

iOS permite que personalizar la apariencia de muchos controles de UIKit para conseguir que los controles estándares se ajusta a la personalización de marca que se va a aplicar a la aplicación.

Hay dos maneras diferentes de aplicar una apariencia personalizada:

- **Directamente en una instancia del control** : puede establecer el color del matiz, imagen de fondo y posición del título (así como otros atributos) en varios controles, incluidas las barras de herramientas, barras de navegación, botones y controles deslizantes.

- **Establecer valores predeterminados en la propiedad estática de apariencia** – atributos personalizables para cada control se exponen a través de la `Appearance` propiedad estática. Las personalizaciones que se aplica a estas propiedades se utilizará como valor predeterminado para cualquier control de ese tipo que se crea después de establece la propiedad.

La aplicación de ejemplo de apariencia muestra los tres métodos, como se muestra en estas capturas de pantalla:

 [ ![](introduction-to-the-appearance-api-images/appearance01.png "La aplicación de ejemplo de apariencia muestra los tres métodos")](introduction-to-the-appearance-api-images/appearance01.png)

A partir de iOS 8, el proxy de apariencia se ha ampliado a TraitCollections.
 `AppearanceForTraitCollection` puede usarse para establecer la apariencia predeterminada en una colección de rasgo determinado. Puede leer más sobre esto en la [Introducción a guiones gráficos](~/ios/user-interface/storyboards/unified-storyboards.md) guía.


## <a name="setting-appearance-properties"></a>Establecer las propiedades de apariencia

En la primera pantalla, se utiliza la clase estática de apariencia para definir el estilo de los botones y los elementos de color amarillo/naranja similar al siguiente:

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

Los estilos de elemento verde se establecen como este, en la `ViewDidLoad` método que invalida los valores predeterminados y la *apariencia* clase estática:

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

La API de apariencia puede ser útil cuando [aplicar un estilo a la aplicación de iOS](~/xamarin-forms/platform/ios/theme.md#uiappearance) en soluciones de Xamarin.Forms. Unas cuantas líneas en el `AppDelegate` clase puede ayudar a implementar una combinación de colores específicos sin tener que crear un [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).


### <a name="custom-themes-and-uiappearance"></a>UIAppearance y temas personalizados

iOS permite muchos atributos visuales del usuario de controles de interfaz como "con temas" usando la *UIAppearance* API para forzar que todas las instancias de un control determinado para tener el mismo aspecto. Esto se expone como una propiedad de apariencia en muchas clases de controles de interfaz de usuario, no en las instancias individuales del control. Establecer una propiedad de pantalla en el método estático `Appearance` propiedad afecta a todos los controles de ese tipo en la aplicación.

Para comprender mejor el concepto, considere un ejemplo.

Para cambiar un determinado `UISegmentedControl` para un tono de color fucsia, se debería hacer referencia al control específico en nuestra pantalla este aspecto en `ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

Como alternativa, establezca el valor en el panel de propiedades del diseñador: 

[ ![](introduction-to-the-appearance-api-images/propertiespadtint.png "Tinte de panel de propiedades")](introduction-to-the-appearance-api-images/propertiespadtint.png)

La siguiente imagen ilustra que esto establece la tinta en el control con el nombre 'sg1'.

 [ ![](introduction-to-the-appearance-api-images/image53.png "Establecer el tono de control individual")](introduction-to-the-appearance-api-images/image53.png)

Para establecer muchos controles de esta manera sería completamente eficaz, por lo que podemos establecer en su lugar el método estático `Appearance` propiedad en la propia clase. Esto se muestra en el código siguiente:

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

La imagen siguiente muestra ahora ambos controles segmentados con el aspecto que se establece en fucsia:

 [ ![](introduction-to-the-appearance-api-images/image54.png "Establecer el tono de control apariencia")](introduction-to-the-appearance-api-images/image54.png)

`Appearance` propiedades se deben establecer al principio del ciclo de vida de aplicación, como en el AppDelegate `FinishedLaunching` evento, o en un ViewController antes de que se muestran los controles afectados.


Hacer referencia a la [Introducción a la API de apariencia](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) para obtener más información.


## <a name="related-links"></a>Vínculos relacionados

- [Apariencia (ejemplo)](https://developer.xamarin.com/samples/monotouch/IntroToAppearance/)
- [Referencia del protocolo de UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Apariencia de Xamarin.Forms](~/xamarin-forms/platform/ios/theme.md#uiappearance)
