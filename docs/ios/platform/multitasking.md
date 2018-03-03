---
title: Multitarea para iPad
description: "iOS 9 es compatible con dos aplicaciones que se ejecutan en el mismo tiempo, utilizando diapositiva sobre o la vista en dos paneles. También admite la reproducción de imagen en la imagen de vídeo."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 8e5bb4747811729adf5363b0a893b0f85108b220
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="multitasking-for-ipad"></a>Multitarea para iPad

_iOS 9 es compatible con dos aplicaciones que se ejecutan en el mismo tiempo, utilizando diapositiva sobre o la vista en dos paneles. También admite la reproducción de imagen en la imagen de vídeo._

![](multitasking-images/about02-sml.png "Ejemplo de pantalla de dividir") ![ ] (multitasking-images/about03-sml.png "ejemplo de imagen en imagen")

iOS 9 agrega compatibilidad con multitarea dos aplicaciones en ejecución al mismo tiempo en un hardware específico iPad. Multitarea para iPad es compatible a través de las siguientes características:

- [**Diapositiva sobre** ](#Slide-Over) -permite al usuario que ejecute temporalmente una segunda aplicación de iOS en una diapositiva hacia afuera panel (ya sea en el lado derecho o izquierdo de la pantalla basándose en la dirección de idioma) que cubre aproximadamente un 25% de la aplicación principal que se está ejecutando actualmente. Deslice sobre solo está disponible en un iPad Pro iPad aire, iPad 2 de aire, iPad 2 Mini, iPad Mini 3 o iPad Mini 4.
- [**Vista en dos paneles** ](#Split-View) -en hardware iPad admitidos (iPad aire 2, 4 Mini iPad e iPad Pro sólo), el usuario puede seleccionar una segunda aplicación y ejecutar en paralelo con la aplicación que se está ejecutando en un modo de pantalla de división. El usuario puede controlar el porcentaje de la pantalla principal que se ocupa de cada aplicación.
- [**Imagen en imagen** ](#Picture-in-Picture) : para las aplicaciones que se reproduce contenido de vídeo de reproducción, el vídeo puede ahora en una ventana de tamaño variable y que puede moverse flotará sobre las otras aplicaciones que se están ejecutando actualmente en el dispositivo iOS. El usuario tiene control total sobre el tamaño y la posición de esta ventana. Imagen en imagen sólo está disponible en un iPad Pro iPad aire, iPad 2 de aire, iPad 2 Mini, iPad Mini 3 o iPad Mini 4.

Hay varias cosas a tener en cuenta cuando [admitir multitarea en la aplicación](#Supporting-Multitasking-in-your-App), incluido:

- [Tamaño de pantalla y la orientación](#Screen-Size-Considerations)
- [Métodos abreviados de teclado de Hardware personalizado](#Custom-Hardware-Keyboard-Shortcuts)
- [Administración de recursos](#Resource-Management-Considerations)

Como desarrollador de aplicaciones también puede [desactivación de multitarea](#Opting-Out-of-Multitasking), incluido [deshabilitar la reproducción de vídeo de PIP](#Disabling-PIP-Video-Playback).

En este artículo se tratan los pasos necesarios para asegurarse de que se ejecuta la aplicación Xamarin.iOS correctamente en un entorno multitarea o cómo optar por no multitarea si no es un buen ajuste de la aplicación.

<a name="Multitasking-QuickStart" />

## <a name="multitasking-quickstart"></a>Inicio rápido de multitarea

Para admitir **deslice sobre** o **vista dividida** la aplicación debe hacer lo siguiente:

 - Se basa en iOS 9 (o posterior).
 - Use un guión gráfico para su pantalla de inicio (y no activos de imagen).
 - Use un guión gráfico con clases de tamaño y Autodiseño para su interfaz de usuario.
 - Admite todos los 4 iOS dispositivo orientaciones (vertical, boca abajo vertical, horizontal izquierda y derecha horizontal).

<a name="Multitasking" />

## <a name="about-multitasking-for-ipad"></a>Acerca de multitarea para iPad

iOS 9 ofrece capacidades nuevas y multitarea en iPad con la introducción de _deslice sobre_, _vista dividida_ (iPad 2 de aire, iPad Mini 4 y iPad Pro) y _imagen en imagen_. En las secciones siguientes se echará un vistazo más de cerca a estas características.

<a name="Slide-Over" />

### <a name="slide-over"></a>Diapositiva sobre

La función deslice Over permite al usuario seleccionar una segunda aplicación y mostrarlo en un panel deslizante pequeño para proporcionar interacción rápida. El panel de la diapositiva sobre es temporal y se cerrará cuando el usuario vuelve a funcionar de nuevo con la aplicación principal.

[ ![](multitasking-images/about01.png "El panel de la diapositiva sobre")](multitasking-images/about01.png)

Lo principal que hay que recordar es que el usuario decida qué dos aplicaciones se ejecutarán en paralelo y que el programador no tiene control sobre este proceso. Como resultado, hay algunas cosas que debe hacer para asegurarse de que se ejecuta la aplicación Xamarin.iOS correctamente en un panel de la diapositiva sobre:

- **Usar las clases de tamaño y Autodiseño** , porque la aplicación de Xamarin.iOS ahora pueden ejecutarse en el panel lateral de desplazamiento horizontal, ya no puede confiar en el dispositivo, su tamaño de pantalla o su orientación al diseño de la interfaz de usuario. Para asegurarse de que la aplicación escale su interfaz correctamente, debe usar las clases de tamaño y diseño automático. Para obtener más información, consulte nuestro [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) documentación.
- **Usar recursos de manera eficaz** : dado que la aplicación puede ahora va a compartir el sistema con otra aplicación en ejecución, es fundamental que la aplicación utiliza eficazmente los recursos del sistema. Cuando se convierte en memoria dispersa, el sistema finalizará automáticamente la aplicación que consume más memoria. Vea de Apple [Guía de eficiencia de energía para aplicaciones iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obtener más detalles.

Deslice sobre solo está disponible en un iPad Pro iPad aire, iPad 2 de aire, iPad 2 Mini, iPad Mini 3 o iPad Mini 4. Para más información acerca de cómo preparar la aplicación deslice sobre, consulte de Apple [adoptar multitarea mejoras en iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentación.

<a name="Split-View" />

### <a name="split-view"></a>Vista dividida

En un hardware iPad admitidos (iPad aire 2, 4 Mini iPad e iPad Pro sólo), el usuario puede seleccionar una segunda aplicación y ejecutar en paralelo con la aplicación que se está ejecutando en un modo de pantalla de división. El usuario puede controlar el porcentaje de la pantalla principal que ocupa cada aplicación arrastrando una pantalla divisor.

[ ![](multitasking-images/about02.png "La vista en dos paneles")](multitasking-images/about02.png)

Al igual que la diapositiva sobre el usuario decide qué dos aplicaciones se ejecutarán en paralelo y de nuevo, el desarrollador no tiene ningún control sobre este proceso. Como resultado, la vista en dos paneles coloca requisitos similares en una aplicación de Xamarin.iOS:

- **Usar las clases de tamaño y Autodiseño** , porque la aplicación de Xamarin.iOS ahora pueden ejecutarse en un modo de pantalla dividida en tamaño especificado del usuario, ya no puede confiar en el dispositivo, su tamaño de pantalla o su orientación al diseño de la interfaz de usuario. Para asegurarse de que la aplicación escale su interfaz correctamente, debe usar las clases de tamaño y diseño automático. Para obtener más información, consulte nuestro [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) documentación.
- **Usar recursos de manera eficaz** : dado que la aplicación puede ahora va a compartir el sistema con otra aplicación en ejecución, es fundamental que la aplicación utiliza eficazmente los recursos del sistema. Cuando se convierte en memoria dispersa, el sistema finalizará automáticamente la aplicación que consume más memoria. Vea de Apple [Guía de eficiencia de energía para aplicaciones iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obtener más detalles.

Para más información acerca de cómo preparar la aplicación para la vista dividida, vea de Apple [adoptar multitarea mejoras en iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) documentación.

<a name="Picture-in-Picture" />

### <a name="picture-in-picture"></a>Imagen en imagen

La nueva imagen en función de la imagen (también conocido como _PIP_) permite al usuario ver un vídeo en una ventana flotante pequeño que el usuario puede colocar en cualquier lugar en la pantalla por encima de otras aplicaciones en ejecución.

[ ![](multitasking-images/about03.png "Un ejemplo de imagen en la ventana flotante de imagen")](multitasking-images/about03.png)

Como con diapositivas sobre y vista en dos paneles, el usuario tiene control total sobre ver un vídeo en la imagen en el modo de imagen. Si la función principal de la aplicación es ver vídeo, será necesario realizar alguna modificación se comporten correctamente en modo PIP. En caso contrario, se requiere ningún cambio para admitir PIP.

Para que la aplicación mostrar el vídeo PIP en la solicitud del usuario, debe estar utilizando _AVKit_ o _AV Foundation API_. El marco de Reproductor multimedia se ha depreciado en iOS 9 y no es compatible con PIP.

Imagen en imagen sólo está disponible en un iPad Pro iPad aire, iPad 2 de aire, iPad 2 Mini, iPad Mini 3 o iPad Mini 4. Para obtener más información, vea nuestra [aplicación de ejemplo de PictureInPicture](https://developer.xamarin.com/samples/ios/iOS9/) y de Apple [imagen en Inicio rápido de imagen](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) documentación.

<a name="Supporting-Multitasking-in-your-App" />

## <a name="supporting-multitasking-in-your-app"></a>Admitir multitarea en la aplicación

Para cualquier aplicación Xamarin.iOS existente, admitir multitarea es una tarea transparente siempre y cuando la aplicación ya sigue las guías de diseño de Apple y prácticas recomendadas para iOS 8. Esto significa que la aplicación debe usar guiones gráficos con clases de tamaño y Autodiseño sus diseños de interfaz de usuario (vea nuestro [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) para obtener más información).

Para estas aplicaciones, se requieren para admitir multitarea y se comporten correctamente dentro de él cambios poca o ninguna. Si la interfaz de usuario de la aplicación, se creó mediante otros métodos como colocar directamente y ajustar el tamaño de los elementos de interfaz de usuario en código C# o si se basa en tamaños de pantalla de dispositivo específico o las orientaciones, será necesario efectuar modificaciones significativas para admitir iOS 9 multitarea correctamente.

Para admitir multitarea de iOS 9 en cualquier aplicación Xamarin.iOS nuevo, vuelva a utilizar guiones gráficos con clases de tamaño y diseño automático para todos los diseños de interfaz de usuario de la aplicación e implementar las instrucciones que aparecen en las secciones siguientes.

<a name="Screen-Size-Considerations" />

### <a name="screen-size-and-orientation-considerations"></a>Tamaño de pantalla y las consideraciones de orientación

Antes de iOS 9, se pudieron diseñar su aplicación frente a dispositivo específico tamaños y orientaciones. Dado que ahora se puede ejecutar una aplicación en un panel deslice fuera o en modo de vista en dos paneles, encontrará ejecutándose en la vista una clase de tamaño horizontal normales o compact en iPad, independientemente del tamaño físico de orientación o de la pantalla del dispositivo.

[ ![](multitasking-images/sizeclasses01.png "Tamaño de pantalla y las consideraciones de orientación")](multitasking-images/sizeclasses01.png)

En un iPad, una aplicación de pantalla completa tiene clases regulares de tamaño horizontal y vertical. Todos los iPhone, pero el iPhone 6 Plus e iPhone 6s Plus, hay clases de tamaño compacto en ambas direcciones en cualquier orientación. IPhone 6 Plus e iPhone 6s Plus en modo horizontal tiene una clase de tamaño horizontal normal y una clase de tamaño vertical Compact (muy parecido a un iPad Mini).

En iPad que admiten la diapositiva sobre y vista en dos paneles, puede acabar con las siguientes combinaciones:

<table width=100% border="1px">
    <tr>
        <td><b>Orientación</b></td>
        <td><b>Aplicación principal</b></td>
        <td><b>Aplicación secundaria</b></td>
    </tr>
    <tr>
        <td><b>Portrait</b></td>
        <td>75% de la pantalla<br/>Compact Horizontal<br/>Vertical normal</td>
        <td>25% de pantalla<br/>Compact Horizontal<br/>Vertical normal</td>
    </tr>
    <tr>
        <td><b>Horizontal</b></td>
        <td>75% de la pantalla<br/>Horizontal normal<br/>Vertical normal</td>
        <td>25% de pantalla<br/>Compact Horizontal<br/>Vertical normal</td>
    </tr>
    <tr>
        <td><b>Horizontal</b></td>
        <td>50% de pantalla<br/>Compact Horizontal<br/>Vertical normal</td>
        <td>50% de pantalla<br/>Compact Horizontal<br/>Vertical normal</td>
    </tr>
</table>

En el ejemplo [MuliTask](https://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) aplicación, si se ejecuta pantalla completa en un iPad en el modo horizontal, presentará la lista y la vista de detalles al mismo tiempo:

[ ![](multitasking-images/sizeclasses03.png "La lista y la vista de detalle que se presentan al mismo tiempo")](multitasking-images/sizeclasses03.png)

Si la misma aplicación se ejecuta en un panel de la diapositiva sobre, se debe disponer como una clase de tamaño Horizontal Compact y solo muestra la lista:

[ ![](multitasking-images/sizeclasses04.png "Solo la lista presentada cuando el dispositivo esté horizontal")](multitasking-images/sizeclasses04.png)

Para asegurarse de que la aplicación se comporta correctamente en estas situaciones, debe adoptar las colecciones de rasgo junto con las clases de tamaño y se ajusta a la `IUIContentContainer` y `IUITraitEnvironment` interfaces. Vea de Apple [UITraitCollection Class Reference](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) y nuestro [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) guía para obtener más información.

Además, ya no se puede confiar en los límites de pantalla de dispositivos para definir el área visible de la aplicación, deberá usar los límites de la ventana de la aplicación en su lugar. Puesto que los límites de ventana son totalmente bajo el control del usuario, no puede ajustarlos mediante programación o evitar que el usuario cambie estos límites.

Por último, la aplicación debe usar un archivo de guión gráfico para presentar su pantalla de inicio en lugar de utilizar un conjunto de **.png** archivos de imagen y admite todas las orientaciones de la interfaz cuatro (vertical, boca abajo vertical, horizontal derecho e izquierdo horizontal) para considerarse para la que se ejecuta en un panel de la diapositiva sobre o en modo de vista en dos paneles.

<a name="Custom-Hardware-Keyboard-Shortcuts" />

### <a name="custom-hardware-keyboard-shortcuts"></a>Métodos abreviados de teclado de Hardware personalizado

En iOS 9 que se ejecuta en un iPad, Apple tiene el soporte extendido de teclados de hardware. iPad siempre ha incluido soporte básico teclado externo a través de Bluetooth y algunos teclados de fabricantes creados de teclado que incluyen cableadas claves específicos de iOS.

Ahora, con iOS 9, las aplicaciones pueden crear sus propios métodos abreviados de teclado. Además, algunos métodos abreviados de teclado básico están disponibles como **comando C** (copiar), **comando-X** (Cortar), **V comando** (pegar) y **comando-Mayús-H**  (principal), sin una aplicación que se va a específicamente escrito responden a ellas.

**Ficha comando** abrirá un selector de aplicación que permite al usuario cambiar rápidamente entre las aplicaciones desde el teclado, como Mac OS:

[ ![](multitasking-images/keyboard01.png "El selector de la aplicación")](multitasking-images/keyboard01.png)

Si una aplicación de iOS 9 incluye métodos abreviados de teclado, el usuario puede mantener presionada el **comando**, **opción** o **Control** claves para que se muestren en una ventana emergente:

[ ![](multitasking-images/keyboard02.png "La ventana emergente de métodos abreviados de teclado")](multitasking-images/keyboard02.png)

#### <a name="defining-custom-keyboard-shortcuts"></a>Definir métodos abreviados de teclado

Si se agrega el código siguiente a una vista o un controlador de vista en nuestra aplicación, cuando esa vista o controlador está visible, un método abreviado de teclado personalizados estarán disponible:

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

En primer lugar, reemplazamos el `CanBecomeFirstResponder` propiedad y valor devuelto `true` para la vista o el controlador de vista puedan recibir entradas mediante teclado. 

A continuación, reemplazamos el `KeyCommands` propiedad y crear un nuevo `UIKeyCommand` para el **comando N** pulsación de tecla. Cuando se activa la pulsación de tecla, llamamos a la `NewEntry` método (que se exponen en iOS 9 con el `Export` comando) para realizar la acción solicitada.

Si ejecutamos esta aplicación en un iPad con un teclado de hardware conectado y el usuario escribe **comando N**, se agregará una nueva entrada a la lista. Si el usuario mantiene presionado el **comando** clave, se mostrará la lista de accesos directos:

[ ![](multitasking-images/keyboard03.png "La ventana emergente de métodos abreviados de teclado")](multitasking-images/keyboard03.png)

Vea el ejemplo [aplicación realizar varias tareas](http://developer.xamarin.com/samples/monotouch/ios9/MultiTask/) para una implementación de ejemplo.

<a name="Resource-Management-Considerations" />

### <a name="resource-management-considerations"></a>Consideraciones sobre la administración de recursos

Incluso para las aplicaciones que ya está usando las guías de diseño y prácticas recomendadas de iOS 8, administración de recursos eficaz podría seguir siendo un problema. En iOS 9, las aplicaciones ya no tienen uso exclusivo de memoria, CPU o de otros recursos del sistema.

Como resultado, debe ajustar la aplicación de Xamarin.iOS para usar de forma eficaz los recursos del sistema o que se enfrenta terminación en situaciones de memoria insuficiente. Esto es cierto igualmente de las aplicaciones que desactivación de multitarea, desde otra aplicación puede seguir ejecutando en un panel de la diapositiva sobre o una imagen en la ventana de la imagen que requieren recursos adicionales o haciendo que la frecuencia de actualización quede por debajo de 60 fotogramas por segundo.

Tenga en cuenta las siguientes acciones del usuario y sus implicaciones:

- **Escribir texto en un panel de la diapositiva sobre** -incluso si la aplicación no tiene ninguna entrada de texto, ahora puede mostrarse el teclado del sistema sobre su interfaz de usuario. Como resultado, la aplicación podría necesitar responder a las notificaciones en pantalla teclado (por ejemplo, mostrar y ocultar el teclado).
- **Ejecuta una segunda aplicación en un panel de la diapositiva sobre** -la nueva aplicación ahora se ejecuta en primer plano y compita con la aplicación existente para recursos del sistema como la memoria y ciclos de CPU.
- **Reproducir un vídeo en una ventana de PIP** : no solo puede cubrir esta ventana parte de la interfaz de la aplicación, pero la aplicación que inicia el vídeo aún se ejecuta en segundo plano y consumir recursos de CPU y memoria.

Para asegurarse de que la aplicación usa recursos de forma eficaz, debe hacer lo siguiente:

- **Generar perfiles de la aplicación con instrumentos** -busque pérdidas de memoria, manifiesto áreas donde la aplicación podría estar bloqueando el subproceso principal y el uso de CPU.
- **Responder a los métodos de las transiciones de estado** : en la **AppDelegate.cs** invalidación de archivo y la respuesta al estado cambian los métodos, como la aplicación escribiendo o devolver desde el fondo. Liberar cualquier innecesarias activos como imágenes, datos o vistas y controlador de vista.
- **Pruebas en paralelo con aplicaciones de uso intensivo de memoria** : ejecutar la aplicación utilizando diapositiva Out tanto la vista en dos paneles en hardware de E/s físicas con una aplicación de uso intensivo de memoria como las asignaciones (en modo de vista de satélite) y comprobar que ambas aplicaciones sigan respondiendo y no se bloqueará.

Vea de Apple [Guía de eficiencia de energía para aplicaciones iOS](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) para obtener más información sobre la administración de recursos.

<a name="Opting-Out-of-Multitasking" />

## <a name="opting-out-of-multitasking"></a>Fuera de la aceptación de multitarea

Mientras Apple sugiere que todas las aplicaciones de iOS 9 admiten multitarea, es posible que motivos muy concretos para una aplicación no demasiado, por ejemplo, juegos o aplicaciones de cámara que requieren la pantalla completa para que funcione correctamente.

Para que la aplicación de Xamarin.iOS a la cancelación de la que se va a ejecutar ya sea un diapositiva Out en el panel de o en modo de vista en dos paneles, edite el proyecto **Info.plist** de archivos y compruebe **pantalla completa requiere**:

[ ![](multitasking-images/fullscreen01.png "Fuera de la aceptación de multitarea")](multitasking-images/fullscreen01.png)

> [!IMPORTANT]
> **Nota:** mientras Opting horizontal de multitarea impide que la aplicación se ejecuta en la diapositiva Out o vista en dos paneles, lo hace **no** evitar que otra aplicación se ejecuten en la diapositiva Out o una imagen de vídeo de la imagen de mostrar junto con su aplicación.




<a name="Disabling-PIP-Video-Playback" />

### <a name="disabling-pip-video-playback"></a>Deshabilitar la reproducción de vídeo de PIP

En la mayoría de los casos, la aplicación debe permitir al usuario jugar cualquier contenido de vídeo que muestra una imagen en la ventana flotante de imagen. Sin embargo, puede haber situaciones donde esto no se podría desear, como vídeos de juego escena Cortar.

Para la desactivación de reproducción de vídeo de PIP, realice lo siguiente en la aplicación:

 - Si usas un `AVPlayerViewController` para mostrar el vídeo, establezca el `AllowsPictureInPicturePlayback` propiedad `false`.
 - Si usas el `AVPlayerLayer` para mostrar el vídeo, no crear una instancia de un `AVPictureInPictureController`.
 - Si usas un `WKWebView` para mostrar el vídeo, establezca el `AllowsPictureInPictureMediaPlayback` propiedad `false`.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se trata los pasos necesarios para asegurarse de que una aplicación de Xamarin.iOS ejecutará y se comporten correctamente en la nueva capacidad de multitarea del 9 de iOS para iPad. Además, había cubierto horizontal de la aceptación de multitarea para las aplicaciones que no es una buena opción.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Realizar varias tareas (ejemplo)](http://developer.xamarin.comhttps://developer.xamarin.com/samples/monotouch/ios9/MultiTask/)
- [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Adopción multitarea mejoras en iPad](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [entrada de blog](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)
