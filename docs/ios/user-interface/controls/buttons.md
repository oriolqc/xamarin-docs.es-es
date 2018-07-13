---
title: Botones de Xamarin.iOS
description: La clase UIButton se usa para representar varios estilos diferentes del botón en las pantallas de iOS. Esta guía presentan las distintas opciones para trabajar con los botones de iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2018
ms.openlocfilehash: 32f6330ad2fddc2e8386d6e574918a011f3bebad
ms.sourcegitcommit: be4da0cd7e1a915e3b8932a7e3d6bcd74c7055be
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38986009"
---
# <a name="buttons-in-xamarinios"></a>Botones de Xamarin.iOS

En iOS, la `UIButton` clase representa un control de botón.

Se pueden modificar las propiedades de un botón mediante programación o con el **panel de propiedades** de iOS Designer:

![El panel de propiedades de iOS Designer](buttons-images/properties.png "el panel de propiedades del Diseñador de iOS")

## <a name="creating-a-button-programmatically"></a>Crear un botón mediante programación

Un `UIButton` pueden crearse con sólo unas pocas líneas de código.

- Crear una instancia de un botón y especificar su tipo:

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  Tipo del botón especificado por un `UIButtonType`:

  - `UIButtonType.System` -Un botón de uso general
  - `UIButtonType.DetailDisclosure` : Indica la disponibilidad de información detallada, normalmente sobre un elemento específico de una tabla
  - `UIButtonType.InfoDark` : Indica la disponibilidad de información de configuración; color oscuro
  - `UIButtonType.InfoLight` : Indica la disponibilidad de información de configuración; color claro
  - `UIButtonType..AddContact` : Indica que se puede agregar un contacto
  - `UIButtonType.Custom` -Botón personalizable

  Para obtener más información sobre los diferentes tipos de botones, eche un vistazo en:
  
  - El [tipos de botón personalizado](#custom-button-types) sección de este documento
  - El [botón tipos](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons) receta
  - Apple [directrices de interfaz humana de iOS](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/).

- Definir el tamaño y la posición del botón:

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- Establece el texto del botón. Use la `SetTitle` método, que requiere el texto y un `UIControlState` valor:

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  Para obtener más información sobre cómo aplicar un estilo a un botón y establecer su texto, consulte:

  - El [aplicar un estilo a un botón](#styling-a-button) sección de este documento
  - El [establecer texto del botón](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text) receta.

## <a name="handling-a-button-tap"></a>Pulsar un botón de control

Para responder a tocar un botón, proporcionar un controlador para el botón `TouchUpInside` eventos:

```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` no es el evento de botón solo está disponible. `UIButton` es una clase secundaria de `UIControl`, que define [muchos eventos diferentes](https://developer.xamarin.com/api/type/UIKit.UIControlEvent/).

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>Usar el Diseñador de iOS para especificar controladores de eventos de botón

Use la **eventos** pestaña de la **panel de propiedades** para especificar controladores de eventos para un botón de diversos eventos.

Para el evento correspondiente, escriba el nombre de un nuevo controlador de eventos o seleccione uno de la lista. Esto creará un controlador de eventos en el código de controlador de vista del botón.

![Pestaña eventos del panel de propiedades](buttons-images/image1.png "pestaña eventos del panel de propiedades")

## <a name="styling-a-button"></a>Aplicar un estilo a un botón

`UIButton` los controles pueden existir en un número de estados diferentes, cada uno especificado por un `UIControlState` valor – `Normal`, `Disabled`, `Focused`, `Highlighted`, etcetera. Cada estado se puede proporcionar un estilo único, especificado mediante programación o con el Diseñador de iOS.

> [!NOTE]
> Para obtener una lista completa de todos los `UIControlState` valores, eche un vistazo a la [ `UIKit.UIControlState enumeration` ](https://developer.xamarin.com/api/type/UIKit.UIControlState/) documentación.

Por ejemplo, para establecer el color del título y el color de sombra para `UIControlState.Normal`:

```csharp
button.SetTitleColor(UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

El código siguiente establece el título del botón en una cadena (estilizado) con atributos para `UIControlState.Normal` y `UIControlState.Highlighted`:

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Tipos de botón personalizado

Botones con un `UIButtonType` de `Custom` no tiene ningún estilo predeterminado. Sin embargo, es posible configurar la apariencia del botón estableciendo una imagen de sus estados diferentes:

```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

Dependiendo de si el usuario toca el botón o no, se representará como una de las siguientes imágenes (`UIControlState.Normal`, `UIControlState.Highlighted` y `UIControlState.Selected` indica, respectivamente):

![UIControlState.Normal](buttons-images/image22.png "UIControlState.Normal")
![UIControlState.Highlighted](buttons-images/image23.png "UIControlState.Highlighted") 
 ![UIControlState.Selected](buttons-images/image24.png "UIControlState.Selected")

Para obtener más información sobre cómo trabajar con botones personalizados, consulte el [utiliza una imagen de un botón](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button) receta.

## <a name="related-links"></a>Vínculos relacionados

- [Libro UIButton](https://developer.xamarin.com/workbooks/ios/user-interface/UIbutton/uibutton.workbook)
