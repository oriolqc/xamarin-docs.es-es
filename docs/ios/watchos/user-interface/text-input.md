---
title: Trabajar con la entrada de texto
ms.topic: article
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: b606773b44d32b724fe7f4dfa2ef18fe34b85cae
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-text-input"></a>Trabajar con la entrada de texto

El Apple Watch no proporciona un teclado para los usuarios escribir texto, sin embargo, es compatible con algunas alternativas de inspección sencillo:

- Seleccionar de una lista predefinida de opciones de texto
- Dictado de Siri,
- Elegir un emoji
- Scribble de reconocimiento de escritura a mano de letra por letra (introducido en watchOS 3).

El simulador no admite actualmente el dictado, pero se pueden realizar las pruebas en la entrada de texto otras opciones del controlador, como Scribble, como se muestra aquí:

![](text-input-images/textinput-sml.png "Probar la opción de scribble")

Para aceptar la entrada de texto en una aplicación de inspección:

1. Crear una matriz de cadenas de opciones predefinidas.
2. Llame a `PresentTextInputController` con la matriz, si quiere permitir emoji o no y un `Action` que se llama cuando el usuario ha terminado.
3. En la acción de conclusión, comprobar el resultado de la entrada y tome las medidas adecuadas en la aplicación (posiblemente establecer valor de texto de una etiqueta).

El siguiente fragmento de código presenta tres opciones predefinidas para el usuario:

```csharp
var suggest = new string[] {"Get groceries", "Buy gas", "Post letter"};

PresentTextInputController (suggest, WatchKit.WKTextInputMode.AllowEmoji, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
    // this only works if result is a text response (Plain or AllowEmoji)
        enteredText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (enteredText);
        // do something, such as myLabel.SetText(enteredText);
    }
});
```

El `WKTextInputMode` enumeración tiene tres valores:

- Simple
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>Simple

Cuando se establece el modo normal, el usuario puede elegir:

- Dictado,
- Scribble, o
- desde una lista predefinida que proporciona la aplicación.

[ ![](text-input-images/plain-scribble-sml.png "Dictado, Scribble, o desde una lista predefinida que proporciona la aplicación")](text-input-images/plain-scribble.png)

El resultado siempre se devuelve como un `NSObject` que se puede convertir en un `string`.

## <a name="emoji"></a>Emoji

Hay dos tipos de emoji:

- Regular emoji de Unicode
- Imágenes animadas

Cuando el usuario elige un emoji Unicode, se devuelve como una cadena.

Si se selecciona una imagen animada emoji el `result` en la finalización controlador contendrá un `NSData` objeto que contiene el emoji `UIImage`.

## <a name="accepting-dictation-only"></a>Aceptar sólo dictado

Para llevar al usuario directamente a la pantalla de dictado sin mostrar ninguna sugerencia (o la opción de Scribble):

- pasar una matriz vacía de la lista de sugerencias, y
- Set `WatchKit.WKTextInputMode.Plain`.

```csharp
PresentTextInputController (new string[0], WatchKit.WKTextInputMode.Plain, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
        dictatedText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (dictatedText);
        // do something, such as myLabel.SetText(dictatedText);
    }
});
```

Cuando se habla el usuario, la pantalla de inspección muestra la siguiente pantalla que incluye el texto tal y como se entiende (por ejemplo "Esto es una prueba"):

![](text-input-images/dictation.png "Cuando se habla el usuario, la pantalla de inspección muestra el texto tal y como se entiende")

Una vez que presione el **realiza** se devolverá el texto del botón.



## <a name="related-links"></a>Vínculos relacionados

- [Del Apple documento de texto y etiquetas](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [Introducción a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
