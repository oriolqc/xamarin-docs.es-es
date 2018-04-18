---
title: Referencia del controlador para juegos de MonoGame
description: Controlador para juegos es una clase estándar, multiplataforma para tener acceso a dispositivos de entrada en MonoGame.
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: e7ce8320441f6b248b183a8698a4f41c78bf2d64
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="monogame-gamepad-reference"></a>Referencia del controlador para juegos de MonoGame

_Controlador para juegos es una clase estándar, multiplataforma para tener acceso a dispositivos de entrada en MonoGame._

`GamePad` puede utilizarse para leer la entrada desde dispositivos de entrada en varias plataformas de MonoGame. Esta guía muestra cómo trabajar con la clase de controlador para juegos. Dado que cada dispositivo de entrada es diferente en el diseño y el número de botones que se proporciona, esta guía incluye diagramas que muestran las diversas asignaciones de dispositivo.

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>Controlador para juegos como sustituto de Xbox360GamePad

La API de XNA original proporciona la `Xbox360GamePad` clase para leer la entrada desde un dispositivo de juego en la consola Xbox 360 o PC. MonoGame ha reemplazado con un `GamePad` clase puesto que no se puede usar controladores de Xbox 360 en la mayoría de las plataformas MonoGame (por ejemplo, iOS o Xbox One). A pesar de que el cambio de nombre, el uso de la `GamePad` clase es similar a la `Xbox360GamePad` clase.

## <a name="reading-input-from-gamepad"></a>Leer la entrada de controlador para juegos

La `GameController` clase ofrece una forma estandarizada de leer la entrada en cualquier plataforma MonoGame. Proporciona información a través de dos métodos:

- `GetState` : devuelve el estado actual de los botones del controlador, sticks analógicas y d-pad.
- `GetCapabilities` : devuelve información acerca de las capacidades de hardware, como si el controlador tiene ciertos botones o que admite la vibración.

### <a name="example-moving-a-character"></a>Ejemplo: Mover un carácter

El código siguiente muestra cómo puede utilizarse el stick USB izquierdo para mover un carácter estableciendo su `XVelocity` y `YVelocity` propiedades. Este código supone que `characterInstance` es una instancia de un objeto que tiene `XVelocity` y `YVelocity` propiedades:

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>Ejemplo: Detectar inserciones

`GamePadState` Proporciona información sobre el estado actual del controlador, por ejemplo, si se presiona un botón determinado. Determinadas acciones, como cuando se facilita un carácter de salto, requieren la comprobación si se ha insertado el botón (no estaba presionada último fotograma, pero no está funcionando en este marco) o liberados (fue hacia abajo el último fotograma, pero no hacia abajo de este marco). 

Para realizar este tipo de lógica, las variables locales que almacenan el fotograma anterior `GamePadState` y el marco actual `GamePadState` debe crearse. En el ejemplo siguiente se muestra cómo almacenar y utilizar el fotograma anterior `GamePadState` implementar saltar:

```csharp
// At class scope:
// Store the last frame's and this frame's GamePadStates.
// "new" them so that code doesn't have to perform null checks:
GamePadState lastFrameGamePadState = new GamePadState();
GamePadState currentGamePadState = new GamePadState();
protected override void Update(GameTime gameTime)
{
    // store off the last state before reading the new one:
    lastFrameGamePadState = currentGamePadState;
    currentGamePadState = GamePad.GetState(PlayerIndex.One);
    bool wasAButtonPushed = 
currentGamePadState.Buttons.A == ButtonState.Pressed
        && lastFrameGamePadState.Buttons.A == ButtonState.Released;
    if(wasAButtonPushed)
    {
        MakeCharacterJump();
    }
...
}
```

### <a name="example-checking-for-buttons"></a>Ejemplo: Comprobación de botones

`GetCapabilities` puede utilizarse para comprobar si un controlador tiene cierto hardware, como un botón determinado o stick analógico. El código siguiente muestra cómo comprobar si los botones B e Y en un controlador en un juego que requiere la presencia de dos botones:

```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```

## <a name="ios"></a>iOS

aplicaciones de iOS admiten la entrada de dispositivo de juego inalámbrica.

> [!IMPORTANT]
> Los paquetes de NuGet para MonoGame 3.5 no incluyen compatibilidad para dispositivos de juego inalámbricos. Uso de la clase de controlador para juegos en iOS requiere genera 3.5 MonoGame de origen o utiliza los archivos binarios de MonoGame 3.6 NuGet. 

### <a name="ios-game-controller"></a>Dispositivos de juego de iOS

La `GamePad` clase devuelve propiedades leídas del controladores inalámbricos. Las propiedades de la `GamePad` proporcionan una buena cobertura en el programa iOS estándar hardware de controlador, como se muestra en el diagrama siguiente:

![](input-images/image1.png "Las propiedades en el controlador de juegos proporcionan una buena cobertura en el programa iOS estándar hardware del controlador, como se muestra en este diagrama")

## <a name="apple-tv"></a>Apple TV

Juegos de Apple TV pueden usar el Siri remoto o dispositivos de juego inalámbricos para la entrada.

### <a name="siri-remote"></a>Siri remoto

*Siri remoto* es el dispositivo de entrada nativo para Apple TV. Aunque se puedan leer los valores de la instancia remota de Siri a través de eventos (como se muestra en el [Siri remoto y controladores de Bluetooth guía](~/ios/tvos/platform/remote-bluetooth.md)), el `GamePad` clase puede devolver valores de la instancia remota de Siri.

Tenga en cuenta que `GamePad` solo puede leer la entrada desde el botón play y toque superficie: 

![](input-images/image2.png "Tenga en cuenta que controlador para juegos solo puede leer la entrada desde el botón play y toque superficie")

Desde la entrada táctil se lea movimiento expuesto la `DPad` propiedad, movimiento de los valores se notifican mediante la `ButtonState` clase. En otras palabras, solo están disponibles como valores `ButtonState.Pressed` o `ButtonState.Released`, en lugar de los valores numéricos o de gestos.

### <a name="apple-tv-game-controller"></a>Dispositivos de juego de TV de Apple

Dispositivos de juego para Apple TV se comportan igual que los dispositivos de juego para aplicaciones de iOS. Para obtener más información, consulte el [iOS sección de dispositivos de juego](#iOS_Game_Controller). 

## <a name="xbox-one"></a>Xbox One

La consola Xbox One admite leer la entrada desde un dispositivo de juego Xbox One.

### <a name="xbox-one-game-controller"></a>Dispositivo de juego de Xbox uno

El dispositivo de juego Xbox One es el dispositivo de entrada más comunes para Xbox One. La `GamePad` clase proporciona valores de entrada del hardware de dispositivo de juego.

![](input-images/image3.png "La clase de controlador para juegos proporciona valores de entrada del hardware de dispositivo de juego")

## <a name="summary"></a>Resumen

Esta guía proporciona información general sobre del MonoGame `GamePad` de la clase, cómo implementar la lógica de lectura de entrada y diagramas comunes de `GamePad` las implementaciones.

## <a name="related-links"></a>Vínculos relacionados

- [MonoGame controlador para juegos](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
