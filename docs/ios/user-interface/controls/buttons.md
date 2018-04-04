---
title: Botones
description: La clase UIButton se usa para representar distintos diferentes estilos de botón en las pantallas de iOS. Esta sección presentan las distintas opciones para trabajar con botones de iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: c2c33103c005a5ed567b1c4703846f887d824ac4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="buttons"></a>Botones

_La clase UIButton se usa para representar distintos diferentes estilos de botón en las pantallas de iOS. Esta sección presentan las distintas opciones para trabajar con botones de iOS._

La `UIButton`clase representa un control de botón en iOS. 

Propiedades de botón se pueden editar en el `Properties Pad` del Diseñador de iOS:


![](buttons-images/properties.png "El panel de propiedades del Diseñador de iOS")

## <a name="creating-a-button"></a>Crear un botón

Un UIButton puede crearse en a través de unas pocas líneas de código.

En primer lugar, crear una instancia de un nuevo botón y especificar el tipo de botón que se necesita:

```csharp
UIButton myButton = new UIButton(UIButtonType.System);
```

El UIButtonType debe especificarse como uno de los siguientes:

- **Sistema** -éste es el tipo de botón estándar usado por iOS y es el tipo que va a utilizar con más frecuencia.
- **DetailDisclosure** -presenta un tipo de botón que se utiliza para ocultar o mostrar información detallada "Bajar".
- **InfoDark** -un oscuro detallada información botón muestra una "i" en un círculo.
- **InfoLight** -una luz detallada información botón muestra una "i" en un círculo.
- **AddContact** -mostrar el botón como un botón Agregar contacto.
- **Personalizado** -le permite personalizar varias características del botón.

Para obtener más información acerca de los tipos de botón puede encontrarse en el [tipos de botones](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/create_different_types_of_buttons/) receta.

A continuación, defina el tamaño en pantalla y la ubicación del botón. Ejemplo:

```csharp
myButton.Frame = new CGRect (25, 25, 300, 150);
```

Para cambiar el texto en un botón, use la `SetTitle` propiedad en el botón, que requiere que establezca una cadena de texto y un `UIControlStyle`. Por ejemplo:

```csharp
myButton.SetTitle("Hello, World!", UIControlState.Normal);
```

Establecer distintas propiedades para cada estado le permite comunicarse más información al usuario (p. ej. Asegúrese del color del texto gris para el estado deshabilitado). Puede cambiar entre cada estado mediante el Diseñador de iOS, o se puede hacer mediante programación. Para obtener más información sobre el texto del botón de opción y el estado, consulte la [establecer el texto del botón](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/set_button_text/) receta.

## <a name="dealing-with-user-interactions"></a>Trabajar con las interacciones del usuario


Botones no son muy útiles a menos que hagan algo al hacer clic en. 

En iOS eventos en los botones casi siempre son eventos de toque, como el uso interactúa con el botón en su pantalla tocándolo. Se muestra una lista de todos los eventos de control posibles [aquí](https://developer.apple.com/documentation/uikit/uicontrolevents), pero el evento de uso más frecuente en iOS es `TouchUpInside`. A continuación, puede crear un controlador de eventos para hacer algo una vez que se ha presionado el botón:


```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

### <a name="adding-events-in-the-ios-designer"></a>Agregar eventos en el Diseñador de iOS
 
Utilice la ficha de eventos en el panel de propiedades para agregar eventos a los controles.

Seleccione el evento y escriba el nombre de un controlador de eventos nuevo o seleccione uno en la lista. Esto creará un nuevo método parcial en la clase de controlador de vista.

![Ficha de eventos](buttons-images/image1.png)

## <a name="styling-a-button"></a>Aplicar un estilo a un botón

UIButtons son diferentes de mayoría UIKit controla en cuanto que tienen un estado, por lo que simplemente no se puede cambiar el título, tendrá que cambiar para cada `UIControlState`. Establecer el Color del título y el Color de sombra se realiza de forma similar:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Además, puede utilizar texto con atributos como título del botón. Por ejemplo:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Tipos de botones personalizados


Al establecer el `Custom` tipo de botón, el objeto no tiene ninguna representación de manera predeterminada. Puede configurar la apariencia del botón estableciendo una imagen para los diferentes Estados. Por ejemplo, el código siguiente muestra cómo agregar imágenes diferentes para el `Normal`, `Highlighted` y `Selected` estados:


```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```


Dependiendo de si el usuario toca el botón o no, se presentarán como una de las siguientes imágenes (`Normal`, `Highlighted` y `Selected` Estados respectivamente):


![](buttons-images/image22.png "Estado de UIButton Normal")
![](buttons-images/image23.png "estado UIButton resaltado")
![](buttons-images/image24.png "estado UIButton seleccionado")

Para obtener más información sobre cómo trabajar con botones personalizados, consulte la [utiliza una imagen de un botón](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/use_an_image_for_a_button/).


## <a name="related-links"></a>Vínculos relacionados

- [Libro de UIButton](https://developer.xamarin.com/workbooks/ios/user-interface/UIbutton/uibutton.workbook)
