---
title: "Agregar formato específicos de iOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: c84bc8286ad5f73acb2d7e9eb13467e2c3bfb9b9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="adding-ios-specific-formatting"></a>Agregar formato específicos de iOS

Una manera de establecer específicos de iOS formato consiste en crear un [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para un control y estilos de conjunto específico de la plataforma y los colores de cada plataforma.

Otras opciones para controlar la manera en la apariencia de la aplicación de iOS Xamarin.Forms incluyen:

* Configuración Mostrar opciones en [ **Info.plist**](#info-plist)
* Establecer estilos de control a través de la [ `UIAppearance` API](#uiappearance)

Estas alternativas se describen a continuación.

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>Personalizar Info.plist

El **Info.plist** archivo le permite configurar algunos aspectos de renderering de una aplicación de iOS, por ejemplo, cómo (y si) se muestra la barra de estado.

Por ejemplo, el [tareas ejemplo](https://developer.xamarin.com/samples/xamarin-forms/Todo/) usa el código siguiente para establecer la barra de color y color del texto de navegación en todas las plataformas:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

El resultado se muestra en el siguiente fragmento de pantalla. Tenga en cuenta que los elementos de la barra de estado son negros (Esto no se puede establecer en Xamarin.Forms porque es una característica específica de la plataforma).

![](theme-images/status-default-sml.png "Temas de iOS")

Lo ideal es que la barra de estado también sería blanca - algo podemos realizar directamente en el proyecto de iOS. Agregue las siguientes entradas a la **Info.plist** para forzar la barra de estado esté en blanco:

![](theme-images/info-plist.png "iOS entradas Info.plist")

o editar correspondiente **Info.plist** archivo directamente para incluir:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

Ahora, cuando se ejecuta la aplicación, la barra de navegación es verde y su texto es blanco (debido a un formato Xamarin.Forms) *y* el texto de la barra de estado también es blancos gracias a la configuración específica de iOS:

![](theme-images/status-white-sml.png "Temas de iOS")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

El [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) puede usarse para establecer propiedades visuales en varios controles de iOS *sin* tener que crear un [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

Adición de una sola línea de código para el **AppDelegate.cs** `FinishedLaunching` método aplicar estilo a todos los controles de un tipo determinado mediante sus `Appearance` propiedad. El código siguiente contiene dos ejemplos: globalmente aplicar un estilo a la pestaña de barras y cambiar el control:

**AppDelegate.cs** en el proyecto de iOS

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

De forma predeterminada, el icono de la barra de fichas seleccionada en una [ `TabbedPage` ](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) sería azul:

![](theme-images/tabbar-default.png "De forma predeterminada iOS icono de la barra de ficha en TabbedPage")

Para cambiar este comportamiento, establezca el `UITabBar.Appearance` propiedad:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Esto hace que la pestaña seleccionada sea verde:

![](theme-images/tabbar-custom.png "Icono de la barra de ficha en TabbedPage de iOS verde")

Uso de esta API permite personalizar la apariencia de la Xamarin.Forms `TabbedPage` en iOS con muy poco código. Hacer referencia a la [solo puede conducir al personalizar pestañas](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/ios/customize-tabs/) para obtener más información sobre cómo usar un representador personalizado para definir un tipo específico de la pestaña.

### <a name="uiswitch"></a>UISwitch

El `Switch` control es otro ejemplo que puede aplicar fácilmente estilos:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Estas capturas de dos pantalla mostrar el valor predeterminado `UISwitch` control a la izquierda y la versión personalizada (configuración `Appearance`) a la derecha en el [tareas ejemplo](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "Color de UISwitch predeterminado") ![ ] (theme-images/switch-custom.png "personalizado UISwitch Color")

### <a name="other-controls"></a>Otros controles

Muchos controles de interfaz de usuario de iOS pueden tener sus colores predeterminados y otros atributos que se establecen a través de la [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).



## <a name="related-links"></a>Vínculos relacionados

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personalizar las fichas](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/ios/customize-tabs/)
