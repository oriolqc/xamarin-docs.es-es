---
title: Etiquetas de Xamarin.iOS
description: Este documento describe cómo utilizar las etiquetas en Xamarin.iOS. Describe cómo crear etiquetas mediante programación y con el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 653c6cf8e7d0d3499402ec4f0d62cdd118707176
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789894"
---
# <a name="labels-in-xamarinios"></a>Etiquetas de Xamarin.iOS

El `UILabel` control se utiliza para mostrar único y varias líneas de texto de sólo lectura. 

## <a name="implementing-a-label"></a>Implementación de una etiqueta

Se crea una nueva etiqueta creando un [ `UILabel` ](https://developer.xamarin.com/api/type/UIKit.UILabel/):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Las etiquetas y los guiones gráficos

También puede agregar una etiqueta a la interfaz de usuario cuando se usa el Diseñador de iOS. Busque **etiqueta** en el **cuadro de herramientas** y arrástrelo a la vista:

![Etiqueta de cuadro de herramientas](labels-images/image3.png)

En el panel de propiedades, se pueden ajustar las propiedades siguientes:

![Panel de propiedades de etiqueta](labels-images/image2.png)

- **Contexto de texto** : sin formato o con atributos. Texto sin formato permite establecer el [atributos de formato](#Formatting_Text_and_Label) en toda la cadena. Textos con atributos permite establecer distintos caracteres o palabras en la cadena de formato.
- **Alineación de color, fuente,** : atributos de formato que se pueden aplicar a la etiqueta.
- **Líneas** : establece el número de líneas que puede abarcar la etiqueta. Establezca esta propiedad en 0 para permitir que la etiqueta utilizar tantas líneas según sea necesario.
- **Comportamiento** : se puede establecer en Enabled o resaltada. Habilitada está establecido de forma predeterminada, se mostrará texto deshabilitado en un color gris más claro. Resaltado está deshabilitada de forma predeterminada y permite la etiqueta que se vuelva a dibujar con un estado resaltado cuando se selecciona un usuario.
- **Baselane y salto de línea** : 
    - Línea de base determina cómo será el texto situado si los tamaños de fuente es diferente de la especificada.
    - Saltos de línea determinan cómo se ajusta o truncada si tiene más de una sola línea una cadena.
- **Autoshrink** – determina cómo se minimizará el tamaño de fuente dentro de una etiqueta, si es necesario.
- **Resaltado, sombra, desplazamiento** : le permite establecer el color de sombra e iluminada y desplazamiento de la sombra.

## <a name="truncating-and-wrapping"></a>Truncar y ajuste

Para obtener información sobre el uso de la línea se interrumpe en iOS, consulte el [truncar y ajustar el texto](https://developer.xamarin.com/recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text/) receta.

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Etiqueta y dar formato al texto

Para dar formato a la cadena que se utiliza en una etiqueta puede establecer atributos en toda la cadena de formato o puede usar cadenas con atributos. Los ejemplos siguientes muestran cómo implementar estos:

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

Para obtener más información sobre el uso de aplicación de estilos al texto `NSAttributedString` hacen referencia a la [de estilo al texto](https://developer.xamarin.com/recipes/ios/standard_controls/text_field/style_text/) receta.

De forma predeterminada, las etiquetas tienen el `Enabled` establecido en true, pero es posible establecerlo en deshabilitado para dar al usuario una sugerencia que un control determinado está deshabilitado:

```csharp
label.Enabled = false;
```

Esto establece la etiqueta para un color gris claro, como se muestra en la imagen del ejemplo siguiente de la pantalla de restricciones en iOS:

![Botón deshabilitado en iOS](labels-images/image1.png)

También puede establecer los colores de texto resaltado y sombra para el texto de etiqueta para efectos adicionales:

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Que muestra el texto similar al siguiente:

![Resaltado y sombra se establezca en texto](labels-images/image4.png)

Para obtener más información acerca de cómo cambiar la fuente de un UILabel, consulte el [cambiar la fuente](https://developer.xamarin.com/recipes/ios/standard_controls/labels/change_the_font/) receta.





