---
title: Adición de formato específica de iOS
description: En este artículo se explica cómo establecer la apariencia específica de iOS sin usar a un representador personalizado de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 3b8a440617dedfbe23f869e865b3cedae21d6c5b
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241382"
---
# <a name="adding-ios-specific-formatting"></a>Adición de formato específica de iOS

Una manera de establecer iOS específicos de formato es crear un [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para un control y los estilos específicos de la plataforma y los colores para cada plataforma.

Otras opciones para controlar la manera en la apariencia de la aplicación de iOS Xamarin.Forms incluyen:

* Configuración Mostrar opciones en [ **Info.plist**](#info-plist)
* Establecer estilos de control a través de la [ `UIAppearance` API](#uiappearance)

Estas alternativas se describen a continuación.

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>Personalización de Info.plist

El **Info.plist** archivo le permite configurar algunos aspectos de renderering de una aplicación de iOS, por ejemplo, cómo (y si) se muestra la barra de estado.

Por ejemplo, el [ejemplo "todo"](https://developer.xamarin.com/samples/xamarin-forms/Todo/) usa el código siguiente para establecer la barra de color y color del texto de navegación en todas las plataformas:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

El resultado se muestra en el siguiente fragmento de pantalla. Tenga en cuenta que los elementos de la barra de estado son de color negros (no se puede establecer en Xamarin.Forms porque es una característica específica de la plataforma).

![](theme-images/status-default-sml.png "Temas de iOS")

Lo ideal es que la barra de estado también sería blanco: algo podemos realizar directamente en el proyecto de iOS. Agregue las siguientes entradas a la **Info.plist** para forzar la barra de estado blanco:

![](theme-images/info-plist.png "las entradas del archivo Info.plist de iOS")

o editar correspondiente **Info.plist** directamente a incluyen:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

Ahora cuando se ejecuta la aplicación, la barra de navegación es verde y su texto es blanco (debido a un formato de Xamarin.Forms) *y* el texto de la barra de estado también es blanco gracias a la configuración específica de iOS:

![](theme-images/status-white-sml.png "Temas de iOS")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

El [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) puede usarse para establecer propiedades visuales en muchos controles de iOS *sin* tener que crear un [representador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

Adición de una sola línea de código para el **AppDelegate.cs** `FinishedLaunching` método aplicar estilo a todos los controles de un tipo determinado mediante sus `Appearance` propiedad. El código siguiente contiene dos ejemplos: globalmente aplicar estilos a la pestaña de barras y cambiar el control:

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

De forma predeterminada, el icono de barra de la pestaña seleccionada en un [ `TabbedPage` ](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) sería azul:

![](theme-images/tabbar-default.png "De forma predeterminada iOS icono de la barra de ficha en TabbedPage")

Para cambiar este comportamiento, establezca el `UITabBar.Appearance` propiedad:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Esto hace que la pestaña seleccionada a verde:

![](theme-images/tabbar-custom.png "Icono de la barra de ficha en TabbedPage de iOS verde")

Uso de esta API le permite personalizar la apariencia de Xamarin.Forms `TabbedPage` en iOS con muy poco código. Hacer referencia a la [receta personalizar pestañas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) para obtener más detalles sobre el uso de un representador personalizado para establecer una fuente específica de la pestaña.

### <a name="uiswitch"></a>UISwitch

El `Switch` control es otro ejemplo que puede cambiar fácilmente el estilo:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Estas capturas de dos pantalla mostrar el valor predeterminado `UISwitch` control a la izquierda y la versión personalizada (opción `Appearance`) a la derecha en el [ejemplo "todo"](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "Color de UISwitch predeterminado") ![ ] (theme-images/switch-custom.png "personalizado UISwitch Color")

### <a name="other-controls"></a>Otros controles

Muchos controles de interfaz de usuario de iOS pueden tener sus colores predeterminados y otros atributos establecidos mediante la [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).



## <a name="related-links"></a>Vínculos relacionados

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personalizar las fichas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
