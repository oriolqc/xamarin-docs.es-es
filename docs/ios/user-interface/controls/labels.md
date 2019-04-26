---
title: Etiquetas de Xamarin.iOS
description: Este documento describe cómo utilizar las etiquetas de Xamarin.iOS. Describe cómo crear etiquetas y mediante programación con el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: cca74ac74e5077822193f6dd97a69f8d9b823561
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227873"
---
# <a name="labels-in-xamarinios"></a>Etiquetas de Xamarin.iOS

El `UILabel` control se utiliza para mostrar único y varias líneas, texto de sólo lectura. 

## <a name="implementing-a-label"></a>Implementación de una etiqueta

Se crea una nueva etiqueta creando un [ `UILabel` ](xref:UIKit.UILabel):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Las etiquetas y los guiones gráficos

También puede agregar una etiqueta a la interfaz de usuario cuando se usa el Diseñador de iOS. Busque **etiqueta** en el **cuadro de herramientas** y arrástrelo a la vista:

![En el cuadro de herramientas de etiqueta](labels-images/image3.png)

En el panel de propiedades, se pueden ajustar las propiedades siguientes:

![Panel de propiedades de etiqueta](labels-images/image2.png)

- **Contexto de texto** : sin formato o con atributos. Texto sin formato permite establecer el [atributos de formato](#Formatting_Text_and_Label) en toda la cadena. Textos con atributos permite establecer el formato de caracteres diferentes o las palabras en la cadena.
- **Alineación de color, fuente,** : atributos de formato que se pueden aplicar a la etiqueta.
- **Líneas** : establece el número de líneas que puede abarcar la etiqueta. Establezca esta opción en 0 para permitir que la etiqueta para usar tantas líneas según sea necesario.
- **Comportamiento** : se puede establecer en Enabled o resaltada. Habilitada está establecido de forma predeterminada, se mostrará el texto deshabilitado en un color gris más claro. Resaltado está deshabilitada de forma predeterminada y permite la etiqueta se vuelva a dibujar con un estado resaltado cuando se selecciona un usuario.
- **Baselane y salto de línea** : 
    - La línea de base determina cómo el texto estará colocado si los tamaños de fuente es diferente del especificado.
    - Saltos de línea determinan cómo se ajusta o trunca si tiene más de una sola línea una cadena.
- **Autoshrink** : determina cómo se minimizará el tamaño de fuente dentro de una etiqueta, si es necesario.
- **Resaltado, sombra, desplazamiento** : le permite establecer el color estando iluminada y sombra y desplazamiento de la sombra.

## <a name="truncating-and-wrapping"></a>Truncar y ajuste

Para obtener información sobre el uso de la línea se interrumpe en iOS, consulte el [truncar y ajustar el texto](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text) receta.

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Aplicar formato a texto y etiqueta

Dar formato a la cadena que se utiliza en una etiqueta puede establecer atributos en toda la cadena de formato o puede usar las cadenas de atributos. Los ejemplos siguientes muestran cómo implementar estos:

```csharp
label = new UILabel(){
                Text = "Hello, this is a string",
                Font = UIFont.FromName("Papyrus", 20f),
                TextColor = UIColor.Magenta,
                TextAlignment = UITextAlignment.Center
            };
```

```csharp
label.AttributedText = new NSAttributedString(
                "This is some formatted text",
                font: UIFont.FromName("GillSans", 16.0f),
                foregroundColor: UIColor.Blue,
                backgroundColor: UIColor.White
            );
```

Para obtener más información sobre el uso de texto de estilo `NSAttributedString` hacen referencia a la [estilo al texto](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text) receta.

De forma predeterminada, las etiquetas tienen la `Enabled` posible establecerlo en establecido en true, pero está deshabilitado para conceder al usuario una sugerencia de que un control determinado está deshabilitado:

```csharp
label.Enabled = false;
```

Esto establece la etiqueta a un color gris claro, tal como se muestra en la siguiente imagen de ejemplo de la pantalla de restricciones en iOS:

![Botón deshabilitado en iOS](labels-images/image1.png)

También puede establecer los colores del texto resaltado y sombra para el texto de etiqueta para los efectos adicionales:

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Que muestra el texto similar al siguiente:

![Resaltado y sombra que se establezca en texto](labels-images/image4.png)

Para obtener más información acerca de cómo cambiar la fuente de un UILabel, consulte el [cambiar la fuente](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font) receta.





