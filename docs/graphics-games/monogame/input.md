---
title: Referencia de GamePad MonoGame
description: Este documento describe GamePad, una clase multiplataforma para tener acceso a dispositivos de entrada en MonoGame. Describe cómo leer la entrada desde el controlador para juegos y proporciona código de ejemplo.
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 235166b78dfbd4998086a2925a54137f1922f5d1
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507011"
---
# <a name="monogame-gamepad-reference"></a>Referencia de GamePad MonoGame

_GamePad es una clase estándar y multiplataforma para tener acceso a dispositivos de entrada en MonoGame._

`GamePad` puede usarse para leer la entrada desde dispositivos de entrada en varias plataformas de MonoGame. Esta guía muestra cómo trabajar con la clase de controlador para juegos. Puesto que cada dispositivo de entrada es diferente en el diseño y el número de botones que proporciona, esta guía incluye diagramas que muestran las diversas asignaciones de dispositivo.

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>GamePad como sustituto de Xbox360GamePad

La API de XNA original proporcionado el `Xbox360GamePad` clase para leer la entrada desde un dispositivo de juego en la consola Xbox 360 o PC. MonoGame ha reemplazado con un `GamePad` clase puesto que no se puede usar controladores de Xbox 360 en la mayoría de las plataformas de MonoGame (por ejemplo, iOS o en Xbox One). A pesar de que el cambio de nombre, el uso de la `GamePad` clase es similar a la `Xbox360GamePad` clase.

## <a name="reading-input-from-gamepad"></a>Leer la entrada de GamePad

La `GamePad` clase proporciona una forma estandarizada de leer la entrada en cualquier plataforma MonoGame. Proporciona información a través de dos métodos:

- `GetState` : devuelve el estado actual de los botones del controlador, sticks analógicas y cruceta.
- `GetCapabilities` : devuelve información acerca de las capacidades de hardware, como si el controlador tiene ciertos botones o admite la vibración.

### <a name="example-moving-a-character"></a>Ejemplo: Mover un carácter

El código siguiente muestra cómo puede utilizarse el stick thumb izquierdo para mover un carácter estableciendo su `XVelocity` y `YVelocity` propiedades. Este código supone que `characterInstance` es una instancia de un objeto que tiene `XVelocity` y `YVelocity` propiedades:

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>Ejemplo: Detectar inserciones

`GamePadState` Proporciona información sobre el estado actual del controlador, por ejemplo, si se presiona un botón determinado. Algunas acciones, como hacer que un carácter de salto, requieran la comprobación si se ha presionado el botón (no estaba inactivo último fotograma, pero está fuera de este marco de servicio) o liberados (era hacia abajo del último fotograma, pero no hacia abajo de este marco).

Para realizar este tipo de lógica, las variables locales que almacenan el fotograma anterior `GamePadState` y el marco actual `GamePadState` debe crearse. El ejemplo siguiente muestra cómo almacenar y usar el marco anterior `GamePadState` implementar saltar:

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

### <a name="example-checking-for-buttons"></a>Ejemplo: Comprobación de los botones

`GetCapabilities` puede usarse para comprobar si un controlador tiene cierto hardware, como un botón determinado o stick analógico. El código siguiente muestra cómo comprobar si los botones B e Y en un controlador en un juego que requiere la presencia de los dos botones:

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
> Los paquetes de NuGet para MonoGame 3.5 no incluyen compatibilidad con dispositivos de juego inalámbricos. Con la clase de controlador para juegos en iOS requiere compilar MonoGame 3.5 desde el origen o mediante los archivos binarios de NuGet 3.6 de MonoGame.

### <a name="ios-game-controller"></a>Controlador de juego de iOS

La `GamePad` clase devuelve las propiedades que se leen los controladores inalámbricos. Las propiedades en el `GamePad` proporcionan buena cobertura para el estándar de iOS hardware del controlador, tal como se muestra en el diagrama siguiente:

![](input-images/image1.png "Las propiedades en el controlador para juegos proporcionan buena cobertura de iOS estándar hardware del controlador, tal como se muestra en este diagrama")

## <a name="apple-tv"></a>Apple TV

Juegos de Apple TV pueden usar el Siri Remote o inalámbrica de juego para la entrada.

### <a name="siri-remote"></a>Siri remoto

*Siri Remote* es el dispositivo de entrada nativo para Apple TV. Aunque se pueden leer valores del repositorio remoto Siri a través de eventos (como se muestra en el [guía Siri Remote y Bluetooth controladores](~/ios/tvos/platform/remote-bluetooth.md)), el `GamePad` clase puede devolver valores desde el servidor remoto de Siri.

Tenga en cuenta que `GamePad` solo puede leer la entrada en el botón play y toque superficie:

![](input-images/image2.png "Tenga en cuenta que GamePad solo puede leer la entrada en el botón play y toque superficie")

Desde la entrada táctil, movimiento expuesta se lee a través de la `DPad` propiedad, el movimiento de los valores se notifican mediante la `ButtonState` clase. En otras palabras, solo están disponibles como valores `ButtonState.Pressed` o `ButtonState.Released`, en lugar de valores numéricos o de gestos.

### <a name="apple-tv-game-controller"></a>Dispositivo de juego de TV de Apple

Dispositivos de juego para Apple TV se comportan exactamente igual para los controladores de juego para aplicaciones iOS. Para obtener más información, consulte el [iOS dispositivos de juego](#ios-game-controller) sección. 

## <a name="xbox-one"></a>Xbox One

La consola Xbox One admite leer la entrada desde un dispositivo de juego Xbox One.

### <a name="xbox-one-game-controller"></a>Dispositivo de juego de Xbox One

El dispositivo de juego Xbox One es el dispositivo de entrada más comunes para Xbox One. La `GamePad` clase proporciona los valores de entrada desde el hardware del dispositivo de juego.

![](input-images/image3.png "La clase de controlador para juegos proporciona valores de entrada desde el hardware del dispositivo de juego")

## <a name="summary"></a>Resumen

Esta guía proporciona información general sobre MonoGame `GamePad` clase, cómo implementar la lógica de lectura de entrada y los diagramas de common `GamePad` implementaciones.

## <a name="related-links"></a>Vínculos relacionados

- [MonoGame GamePad](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
