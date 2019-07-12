---
title: Multitarea para iPad en Xamarin.iOS
description: iOS 9 es compatible con dos aplicaciones que se ejecutan al mismo tiempo, través de diapositivas o vista en dos paneles. También admite vídeo reproduciéndose Picture In Picture.
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 888e00fbdbf30b5b2842bc30822a55f57372eb34
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831810"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Multitarea para iPad en Xamarin.iOS

_iOS 9 es compatible con dos aplicaciones que se ejecutan al mismo tiempo, través de diapositivas o vista en dos paneles. También admite vídeo reproduciéndose Picture In Picture._

![](multitasking-images/about02-sml.png "Ejemplo de pantalla de dividir") ![](multitasking-images/about03-sml.png "ejemplo de imagen en imagen")

iOS 9 agrega compatibilidad con multitarea ejecuta dos aplicaciones al mismo tiempo en un hardware específico iPad. Multitarea para iPad es compatible a través de las siguientes características:

- [**Diapositiva sobre** ](#Slide-Over) -permite al usuario ejecutar temporalmente una segunda aplicación de iOS en una diapositiva panel (ya sea en el lado derecho o izquierdo de la pantalla según la dirección del idioma) que abarca aproximadamente el 25% de la aplicación principal que se está ejecutando actualmente. Deslice a través solo está disponible en un iPad Pro, iPad Air, iPad 2 de aire, iPad 2 Mini, Mini 3 iPad o iPad Mini 4.
- [**Vista en dos paneles** ](#Split-View) -en hardware iPad admitidas (iPad Air 2, 4 Mini iPad e iPad solo para Pro), el usuario puede seleccionar una segunda aplicación y ejecutarlo en paralelo con la aplicación que se está ejecutando en un modo de pantalla dividida. El usuario puede controlar el porcentaje de la pantalla principal que ocupa cada aplicación.
- [**Imagen en imagen** ](#Picture-in-Picture) : para las aplicaciones que se reproduzcan reproducir contenido de vídeo, el vídeo puede ahora en una ventana movible y redimensionable que flota sobre las demás aplicaciones que se están ejecutando actualmente en el dispositivo iOS. El usuario tiene control total sobre el tamaño y posición de esta ventana. Imagen en imagen sólo está disponible en un iPad Pro, iPad Air, iPad 2 de aire, iPad 2 Mini, Mini 3 iPad o iPad Mini 4.

Hay una serie de aspectos que hay que tener en cuenta al [que admiten la multitarea en su aplicación](#Supporting-Multitasking-in-your-App), incluidos:

- [Tamaño de pantalla y la orientación](#Screen-Size-Considerations)
- [Métodos abreviados de teclado de Hardware personalizado](#Custom-Hardware-Keyboard-Shortcuts)
- [Administración de recursos](#Resource-Management-Considerations)

Como desarrollador de aplicaciones también pueden [rechazar multitarea](#Opting-Out-of-Multitasking), incluidos [deshabilitando la reproducción de vídeo de PIP](#Disabling-PIP-Video-Playback).

En este artículo se describen los pasos necesarios para asegurarse de que la aplicación de Xamarin.iOS se ejecuta correctamente en un entorno multitarea o cómo rechazar la multitarea si no es una buena elección para la aplicación.

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**Multitarea para iPad de vídeo**


<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>Guía de inicio rápido de multitarea

Para admitir **deslice sobre** o **vista dividida** la aplicación debe hacer lo siguiente:

- Se compilan en iOS 9 (o posterior).
- Utilizar un guión gráfico para su pantalla de inicio (y no los activos de imagen).
- Utilizar un guión gráfico con diseño automático y las clases de tamaño para su interfaz de usuario.
- Admite todos los 4 iOS orientaciones de dispositivos (vertical, vertical de arriba hacia abajo, horizontal izquierda y derecha horizontal).

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>Acerca de multitarea para iPad

iOS 9 ofrece nuevas capacidades de multitarea en iPad con la introducción de _deslice sobre_, _vista dividida_ (iPad Air 2, 4 Mini iPad y iPad Pro) y _Picture in Picture_. Le echamos un vistazo más de cerca a estas características en las secciones siguientes.

<a name="Slide-Over" />

### <a name="slide-over"></a>Diapositiva a través de

La característica deslice sobre permite al usuario seleccionar una segunda aplicación y mostrarlo en un panel deslizante pequeño para proporcionar una interacción rápida. El panel de diapositiva a través es temporal y se cerrará cuando el usuario vuelve a funcionar de nuevo con la aplicación principal.

[![](multitasking-images/about01.png "El panel de diapositiva a través de")](multitasking-images/about01.png#lightbox)

Lo principal que debe recordar es que el usuario decida qué dos aplicaciones se ejecutarán en paralelo y que el desarrollador ejerce ningún control sobre este proceso. Como resultado, hay algunas cosas que debe hacer para asegurarse de que la aplicación de Xamarin.iOS se ejecuta correctamente en el panel de una diapositiva a través de:

- **Usar el diseño automático y las clases de tamaño** , porque ahora se puede ejecutar la aplicación de Xamarin.iOS en el panel lateral deslizantes, ya no puede confiar en el dispositivo, su tamaño de pantalla o la orientación del diseño de la interfaz de usuario. Para asegurarse de que la aplicación escala su interfaz correctamente, deberá usar el diseño automático y las clases de tamaño. Para obtener más información, consulte nuestra [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentación.
- **Usar eficazmente los recursos** , debido a la aplicación ahora se puede compartir el sistema con otra aplicación en ejecución, es fundamental que la aplicación usa los recursos del sistema de forma eficaz. Cuando se convierte en memoria dispersa, el sistema finalizará automáticamente la aplicación que está consumiendo la mayor cantidad de memoria. Consulte Apple [Guía de la eficiencia de energía para aplicaciones iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obtener más detalles.

Deslice a través solo está disponible en un iPad Pro, iPad Air, iPad 2 de aire, iPad 2 Mini, Mini 3 iPad o iPad Mini 4. Para más información sobre cómo preparar la aplicación para Deslizar a través, consulte Apple [adopción de mejoras de multitarea en iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentación.

<a name="Split-View" />

### <a name="split-view"></a>Vista dividida

En el hardware de iPad admitidas (iPad Air 2, 4 Mini iPad e iPad solo para Pro), el usuario puede elegir una segunda aplicación y ejecutarlo en paralelo con la aplicación que se está ejecutando en un modo de pantalla dividida. El usuario puede controlar el porcentaje de la pantalla principal que ocupa cada aplicación arrastrando una pantalla divisor.

[![](multitasking-images/about02.png "La vista en dos paneles")](multitasking-images/about02.png#lightbox)

Al igual que diapositivas sobre el usuario decide qué dos aplicaciones se ejecutarán en paralelo y de nuevo, el desarrollador no tiene ningún control sobre este proceso. Como resultado, la vista dividida coloca requisitos similares en una aplicación de Xamarin.iOS:

- **Usar el diseño automático y las clases de tamaño** , porque ahora se puede ejecutar la aplicación de Xamarin.iOS en un modo de pantalla dividida en tamaño especificado del usuario, ya no puede confiar en el dispositivo, su tamaño de pantalla o la orientación del diseño de la interfaz de usuario. Para asegurarse de que la aplicación escala su interfaz correctamente, deberá usar el diseño automático y las clases de tamaño. Para obtener más información, consulte nuestra [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentación.
- **Usar eficazmente los recursos** , debido a la aplicación ahora se puede compartir el sistema con otra aplicación en ejecución, es fundamental que la aplicación usa los recursos del sistema de forma eficaz. Cuando se convierte en memoria dispersa, el sistema finalizará automáticamente la aplicación que está consumiendo la mayor cantidad de memoria. Consulte Apple [Guía de la eficiencia de energía para aplicaciones iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obtener más detalles.

Para más información sobre cómo preparar la aplicación para la vista en dos paneles, consulte Apple [adopción de mejoras de multitarea en iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentación.

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Imagen en imagen

La nueva imagen en función de la imagen (también conocido como _PIP_) permite al usuario ver un vídeo en una pequeña ventana flotante que el usuario puede colocar en cualquier lugar en la pantalla por encima de otras aplicaciones en ejecución.

[![](multitasking-images/about03.png "Un ejemplo de imagen en la ventana flotante de imagen")](multitasking-images/about03.png#lightbox)

Como con diapositivas sobre y vista en dos paneles, el usuario tiene control total sobre un vídeo en la imagen del modo de imagen. Si la función de la aplicación principal es vídeo, necesitará alguna modificación para que se comporten correctamente en modo PIP. En caso contrario, se requiere ningún cambio para admitir PIP.

Para que la aplicación ver el vídeo PIP a la solicitud del usuario, deberá usar ya sea _AVKit_ o _AV Foundation API_. Media Player framework se ha depreciado en iOS 9 y no es compatible con PIP.

Imagen en imagen sólo está disponible en un iPad Pro, iPad Air, iPad 2 de aire, iPad 2 Mini, Mini 3 iPad o iPad Mini 4. Para obtener más información, consulte nuestra [aplicación de ejemplo PictureInPicture](https://developer.xamarin.com/samples/ios/iOS9/) y Apple [imagen en imagen de inicio rápido](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) documentación.

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>Permitir la multitarea en la aplicación

Para cualquier aplicación de Xamarin.iOS existente, que admiten la multitarea es una tarea transparente siempre y cuando la aplicación ya sigue las guías de diseño de Apple y los procedimientos recomendados para iOS 8. Esto significa que la aplicación debe usar guiones gráficos con diseño automático y las clases de tamaño para sus diseños de interfaz de usuario (consulte nuestra [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obtener más información).

Para estas aplicaciones, es necesario para admitir la multitarea y se comporten bien dentro de él poco o ningún cambio. Si la interfaz de usuario de la de su aplicación, se creó mediante otros métodos, como colocar directamente y cambiar el tamaño de los elementos de interfaz de usuario en C# código o si se basa en los tamaños de pantalla del dispositivo específico o las orientaciones, necesitará una modificación significativa para admitir la multitarea de iOS 9 correctamente.

Para admitir la multitarea de iOS 9 en cualquier nueva aplicación de Xamarin.iOS, vuelva a utilizar guiones gráficos con diseño automático y las clases de tamaño para todos los diseños de interfaz de usuario de la aplicación e implementar las instrucciones en las secciones siguientes.

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>Consideraciones de orientación y tamaño de pantalla

Antes de iOS 9, puede diseñar su aplicación frente a tamaños de pantalla del dispositivo específico y orientaciones. Dado que ahora se puede ejecutar una aplicación en un panel diapositiva horizontal o en modo de vista en dos paneles, puede encontrar sí se ejecuten en una clase de tamaño horizontal normales o compact en iPad, independientemente del tamaño físico de pantalla o la orientación del dispositivo.

[![](multitasking-images/sizeclasses01.png "Consideraciones de orientación y tamaño de pantalla")](multitasking-images/sizeclasses01.png#lightbox)

En un iPad, una aplicación de pantalla completa tiene clases normales de tamaño horizontal y vertical. Todos los iPhone, pero el iPhone 6 Plus e iPhone 6s Plus, tiene las clases de tamaño compacto en ambas direcciones en cualquier orientación. El iPhone 6 Plus e iPhone 6s Plus en modo horizontal tiene una clase de tamaño horizontal normal y una clase de tamaño vertical Compact (muy similar a un Mini iPad).

En dispositivos iPad que admiten la diapositiva sobre y vista en dos paneles, puede acabar con las combinaciones siguientes:

| **Orientación** | **Aplicación principal** | **Aplicación secundaria** |
|--- |--- |--- |
| **Vertical** |75% de la pantalla<br />Compact Horizontal<br />Regular Vertical|25% de la pantalla<br />Compact Horizontal<br />Regular Vertical|
| **Landscape** |75% de la pantalla<br />Regular Horizontal<br />Regular Vertical|25% de la pantalla<br />Compact Horizontal<br />Regular Vertical|
| **Landscape** |50% de la pantalla<br />Compact Horizontal<br />Regular Vertical|50% de la pantalla<br />Compact Horizontal<br />Regular Vertical|

En el ejemplo [MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) aplicación, si se ejecuta en un iPad en el modo horizontal de pantalla completa, presentará la lista y la vista de detalles al mismo tiempo:

[![](multitasking-images/sizeclasses03.png "La lista y la vista de detalle presentada al mismo tiempo")](multitasking-images/sizeclasses03.png#lightbox)

Si la misma aplicación se ejecuta en el panel de una diapositiva a través, se presenta como una clase de tamaño Horizontal Compact y solo muestra la lista:

[![](multitasking-images/sizeclasses04.png "Sólo la lista que aparece cuando el dispositivo es horizontal")](multitasking-images/sizeclasses04.png#lightbox)

Para asegurarse de que la aplicación se comporta correctamente en estas situaciones, debe adoptar las colecciones de rasgo junto con las clases de tamaño y se ajustan a la `IUIContentContainer` y `IUITraitEnvironment` interfaces. Consulte Apple [referencia de clase UITraitCollection](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) y nuestro [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) guía para obtener más información.

Además, ya no puede confiar en los límites de la pantalla de dispositivos para definir el área visible de la aplicación, deberá usar los límites de la ventana de la aplicación en su lugar. Puesto que los límites de ventana están totalmente bajo el control del usuario, no puede ajustarlos o evitar que el usuario cambie estos límites mediante programación.

Por último, la aplicación debe usar un archivo de guión gráfico para presentar su pantalla de inicio en lugar de usar un conjunto de **.png** archivos de imagen y admite todas las orientaciones de interfaz cuatro (vertical, vertical de arriba hacia abajo, horizontal izquierda y derecha horizontal) para considerarse para la que se ejecuta en un panel de diapositivas en o en modo de vista en dos paneles.

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>Métodos abreviados de teclado de Hardware personalizado

En iOS 9 que se ejecutan en un iPad, Apple tiene soporte extendido para teclados de hardware. iPad siempre han incluido compatibilidad con teclado externo básica a través de Bluetooth y algunos teclados de fabricantes creados de teclado que incluyen autoemisor claves específicos de iOS.

Ahora, con iOS 9, las aplicaciones pueden crear sus propios métodos abreviados de teclado. Además, algunos métodos abreviados de teclado básicos están disponibles como **comando C** (copia), **comando X** (Cortar), **comando V** (pegar) y **comando-Mayús-H**  (inicio), sin una aplicación que se va a específicamente escrito responder a ellos.

**Ficha de comando** , aparecerá un selector de aplicación que permite al usuario cambiar rápidamente entre las aplicaciones desde el teclado, como Mac OS:

[![](multitasking-images/keyboard01.png "El modificador de la aplicación")](multitasking-images/keyboard01.png#lightbox)

Si una aplicación de iOS 9 incluye métodos abreviados de teclado, el usuario puede mantener presionada el **comando**, **opción** o **Control** claves para mostrarlos en un cuadro emergente:

[![](multitasking-images/keyboard02.png "El elemento emergente de métodos abreviados de teclado")](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>Definir métodos abreviados de teclado personalizados

Si agregamos el código siguiente a una vista o un controlador de vista en nuestra aplicación, cuando esa vista o controlador está visible, un método abreviado de teclado personalizado estará disponible:

```csharp
#region Custom Keyboard Shortcut
public override bool CanBecomeFirstResponder {
    get { return true; }
}

public override UIKeyCommand[] KeyCommands {
    get {

        var keyCommand = UIKeyCommand.Create (new NSString("n"), UIKeyModifierFlags.Command, new Selector ("NewEntry"), new NSString("New Entry"));
        return new UIKeyCommand[]{ keyCommand };
    }
}

[Export("NewEntry")]
public void NewEntry() {

    // Add a new entry
    ...

}
#endregion
```

En primer lugar, se muestra cómo reemplazar el `CanBecomeFirstResponder` propiedad y devolución `true` para la vista o el controlador de vista puedan recibir entradas de teclado. 

A continuación, se muestra cómo reemplazar el `KeyCommands` propiedad y crear un nuevo `UIKeyCommand` para el **comando N** pulsación de tecla. Cuando se activa la pulsación de tecla, llamamos el `NewEntry` método (que se exponen en iOS 9 con el `Export` comando) para realizar la acción solicitada.

Si ejecutamos esta aplicación en un iPad con un teclado de hardware conectados y el usuario escribe **comando N**, se agregará una nueva entrada a la lista. Si el usuario mantiene presionado el **comando** clave, se mostrará la lista de accesos directos:

[![](multitasking-images/keyboard03.png "El elemento emergente de métodos abreviados de teclado")](multitasking-images/keyboard03.png#lightbox)

Vea el ejemplo [aplicación realizar varias tareas](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) para una implementación de ejemplo.

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>Consideraciones sobre la administración de recursos

Incluso para las aplicaciones que ya está usando las guías de diseño y los procedimientos recomendados de iOS 8, administración eficaz de los recursos todavía podría ser un problema. En iOS 9, las aplicaciones ya no tienen uso exclusivo de memoria, CPU o de otros recursos del sistema.

Como resultado, debe ajustar la aplicación de Xamarin.iOS para usar de forma eficaz los recursos del sistema o enfrenta a la terminación en situaciones de memoria insuficiente. Esto es igualmente cierto de las aplicaciones que opten por multitarea, desde una segunda aplicación es posible que se pueden ejecutar en un panel deslice sobre o una imagen en la ventana de la imagen que se requieren recursos adicionales o causando la frecuencia de actualización quede por debajo de 60 fotogramas por segundo.

Tenga en cuenta las siguientes acciones del usuario y sus implicaciones:

- **Escribir texto en un panel deslice sobre** -incluso si la aplicación no tiene ninguna entrada de texto, ahora se puede mostrar el teclado del sistema a través de su interfaz de usuario. Como resultado, la aplicación podría necesitar responder a las notificaciones teclado en pantalla (por ejemplo, mostrar y ocultar el teclado).
- **Ejecuta una segunda aplicación en un panel deslice sobre** -la nueva aplicación ahora se ejecuta en primer plano y competir con la aplicación existente para los recursos del sistema como la memoria y ciclos de CPU.
- **Reproducir un vídeo en una ventana de PIP** : no solo puede esta ventana cubrir parte de la interfaz de la aplicación, pero la aplicación que inicia el vídeo aún está ejecutándose en segundo plano y consumo de recursos de CPU y memoria.

Para asegurarse de que la aplicación usa recursos de forma eficaz, debe hacer lo siguiente:

- **Generar perfiles de la aplicación con Instruments** -busque pérdidas de memoria, uso de CPU de manifiestos y las áreas donde la aplicación podría estar bloqueando el subproceso principal.
- **Responder a los métodos de las transiciones de estado** : en su **AppDelegate.cs** reemplazo de archivos y la respuesta al estado cambian métodos como la aplicación escribir o devolver desde el fondo. Libera los recursos innecesarios como imágenes, datos o vistas y controlador de vista.
- **Probar Side-by-Side con aplicaciones con uso intensivo de memoria** : ejecutar la aplicación mediante deslice Out y vista dividida en hardware físico de iOS con una aplicación intensiva de memoria como los mapas (en modo de vista de satélite) y comprobar que ambas aplicaciones sigan respondiendo y se bloquea.

Consulte Apple [Guía de la eficiencia de energía para aplicaciones iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obtener más información sobre la administración de recursos.

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>Dejar de participar en multitarea

Apple sugiere lo que todas las aplicaciones de iOS 9 admiten multitarea, mientras que puede haber razones muy específicas para una aplicación no demasiado, como juegos o aplicaciones de cámara que requieren la pantalla completa para que funcione correctamente.

Para que la aplicación de Xamarin.iOS dejar de participar en que se va a ejecutar en cualquier un deslice Out panel o en modo de vista en dos paneles, editar el proyecto **Info.plist** de archivos y comprobar **requiere pantalla completa**:

[![](multitasking-images/fullscreen01.png "Dejar de participar en multitarea")](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> Mientras que deciden no utilizar multitarea impide que la aplicación que se ejecuten en la diapositiva Out o vista en dos paneles, que no impide que otra aplicación que se ejecuten en la diapositiva Out o una imagen en imagen de vídeo muestre junto con la aplicación.

<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>Deshabilitar la reproducción de vídeo de PIP

En la mayoría de los casos, la aplicación debe permitir al usuario jugar cualquier contenido de vídeo que muestra en una imagen en la ventana flotante de imagen. Sin embargo, puede haber situaciones donde es posible que no se desea esto, como vídeos de juego escena Cortar.

Para desactivar la reproducción de vídeo de PIP, haga lo siguiente en la aplicación:

- Si usas un `AVPlayerViewController` para mostrar vídeo, establezca el `AllowsPictureInPicturePlayback` propiedad `false`.
- Si usas el `AVPlayerLayer` para mostrar vídeo, no cree una instancia un `AVPictureInPictureController`.
- Si usas un `WKWebView` para mostrar vídeo, establezca el `AllowsPictureInPictureMediaPlayback` propiedad `false`.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha cubierto los pasos necesarios para asegurarse de que una aplicación de Xamarin.iOS se ejecutará y se comporten correctamente en iOS 9 nueva capacidad multitarea para iPad. Además, se ha descrito opten horizontal de multitarea para las aplicaciones que no resulta una buena opción.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Realizar varias tareas (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adopción de mejoras de la multitarea en iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [Entradas del blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
