---
title: Técnicas de interacción rápida para watchOS 3 en Xamarin
description: En este artículo se trata las técnicas de interacción rápida Apple ha agregado en watchOS 3 y cómo implementarlos en Xamarin.iOS para Apple Watch.
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6a8e74860efd606ae6dd565ea7e3f67884eefc11
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103223"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Técnicas de interacción rápida para watchOS 3 en Xamarin

_En este artículo se trata las técnicas de interacción rápida Apple ha agregado en watchOS 3 y cómo implementarlos en Xamarin.iOS para Apple Watch._

Proporcionar las interacciones del usuario rápido son esenciales para crear atractivas aplicaciones de Apple Watch y complicaciones. Nuevo para watchOS 3, Apple ha agregado compatibilidad para los reconocedores de gestos, acceso a la corona Digital y nuevas técnicas de notificación de usuario y la navegación. Esto, junto con la compatibilidad agregada para SceneKit y SpriteKit, permiten al desarrollador crear fácilmente interfaces enriquecidas y ofrecer rápidos y dinámico.

## <a name="what-are-quick-interactions"></a>¿Cuáles son las interacciones rápidas

Para un desarrollador que se usa para crear aplicaciones para iOS o macOS (donde la cantidad de tiempo que invierte en un usuario interactúa con la aplicación se mide en horas o minutos), diseñar una aplicación correctamente para el Apple Watch, puede ser un desafío y requiere otro enfoque.

En watchOS, el usuario normalmente desea elevar sus relojes de pulsera, rápidamente interactuar con una aplicación (normalmente por un breve par de segundos), a continuación, coloque su muñeca y continuar lo que era que estaban haciendo.

Los siguientes son algunos ejemplos de interacciones rápidas típicas en el Apple Watch:

- Inicie un temporizador.
- Comprobando la información meteorológica.
- Marcar un elemento de una lista de tareas.

Para lograr estos objetivos, debe ser una aplicación en el Apple Watch:

- **Ofrecer** -lo que significa que, con una rápida, eche un vistazo el usuario debe ser capaz de obtener la información que necesitan. 
- **Procesables** -lo que significa que los usuarios debe poder tomar decisiones rápidas y bien fundamentadas.
- **Capacidad de respuesta** -lo que significa que el usuario nunca debe esperar para recibir la información que necesitan o para lograr la acción que deseen.

### <a name="quick-interactions-length"></a>Longitud de las interacciones rápidas

Dada la naturaleza ofrecer de aplicaciones de Apple Watch, Apple sugiere que la longitud ideal de una interacción rápida debe ser de dos segundos o menos. Como resultado de este límite de dos segundos, el desarrollador tendrá que dedicar una cantidad considerable de tiempo tanto diseñar e implementar una aplicación de Apple Watch. 

## <a name="new-watchos-3-features-and-apis"></a>Nuevo watchOS 3 características y API

Apple ha agregado varias características nuevas y las API para WatchKit para ayudar a los desarrolladores agregar interacciones rápidas a sus aplicaciones de Apple Watch:

- watchOS 3 proporciona acceso a los nuevos tipos de entrada como del usuario:
    - Reconocedores de gestos
    - Rotación de corona digital 
- watchOS 3 proporciona nuevas formas de mostrar y actualizar la información, como:
    - Navegación mejorada de tabla
    - Nueva compatibilidad de marco de trabajo de notificación de usuario
    - Integración de SpriteKit y SceneKit

Mediante la implementación de estas nuevas características, el desarrollador puede asegurarse de que su aplicación para watchOS 3 es Glanceable, aplicable y con capacidad de respuesta.

### <a name="gesture-recognizer-support"></a>Compatibilidad del reconocedor de gestos

Si el desarrollador ha implementado los reconocedores de gestos en iOS, deben ser muy familiarizados con cómo funcionan los reconocedores de gestos en watchOS 3. Para actualizar, los reconocedores de gestos son objetos que analizar los eventos de función táctil de bajo nivel en gestos reconocibles, previamente definidos.

watchOS 3 será compatible con los reconocedores de gestos cuatro siguientes:

- Tipos de gestos discretos:
    - El gesto de deslizar rápidamente (`WKSwipeGestureRecognizer`).
    - El gesto de pulsar (`WKTapGestureRecognizer`).
- Tipos de gesto continua:
    - El movimiento panorámico (`WKPanGestureRecognizer`).
    - El gesto de pulsación de larga (`WKLongPressGestureRecognizer`).

Para implementar uno de los reconocedores de gestos de nuevo, arrástrelo hasta una superficie de diseño en iOS Designer en Visual Studio para Mac y configurar sus propiedades.

En el código, responder a la acción del reconocedor para controlar el gesto que se desencadena por el usuario. Nuevamente, esto se hace de la misma manera como se controlaría en iOS.

#### <a name="discrete-gesture-states"></a>Estados de gesto discretos

Para los gestos discretos, la acción se llama cuando se reconoce el movimiento y un estado (`WKGestureRecognizerState`) se asigna como:

[![](quick-interaction-techniques-images/quick01.png "Estados de gesto discretos")](quick-interaction-techniques-images/quick01.png#lightbox)

Todos los gestos discretos que comienzan el `Possible` estado y realizan la transición ya sea el `Failed` o `Recognized` estado. Al usar gestos discretos, el desarrollador no tratar generalmente directamente con el estado. En su lugar, dependen de la acción que se llama cuando se reconoce solo el movimiento.

#### <a name="continuous-gesture-states"></a>Estados de gesto continua

Los gestos continua son ligeramente diferentes de gestos discretos, donde la acción se llama varias veces según se reconoce el gesto de:

[![](quick-interaction-techniques-images/quick02.png "Estados de gesto continua")](quick-interaction-techniques-images/quick02.png#lightbox)

Nuevamente, gestos continua empieza en el `Possible` estado, pero a través de varias actualizaciones de progreso. Aquí el desarrollador debe considerar el estado del reconocedor y actualizar la interfaz de usuario de la aplicación durante la `Changed` hasta que el gesto es, por último, la fase `Recognized` o `Canceled`.

#### <a name="gesture-recognizer-usage-tips"></a>Sugerencias de uso del reconocedor de gestos

Apple sugiere lo siguiente al trabajar con los reconocedores de gestos en watchOS 3:

- Agregue los reconocedores de gestos para agrupar elementos en lugar de los controles individuales. Puesto que el Apple Watch tiene un tamaño más pequeño de la pantalla física, los elementos de grupo tienden a ser más grande y más fácil de destinos para el usuario presione. Además, los reconocedores de gestos pueden entrar en conflicto con gestos ya está en los controles de interfaz de usuario nativos integrados.
- Establecer las relaciones de dependencia en el guión gráfico de la aplicación de inspección.
- Algunos gestos tienen prioridad sobre otros tipos de gestos, como:
    - El desplazamiento
    - Force Touch
 
### <a name="digital-crown-rotation"></a>Rotación de corona digital

Mediante la implementación de soporte técnico de corona Digital en sus aplicaciones para watchOS 3, un desarrollador puede proporcionar navegación mayor velocidad y precisión de las interacciones para sus usuarios.

Desde watchOS 2, podría usar la aplicación de Apple Watch el `WKInterfacePicker` objeto para tener acceso a la corona Digital al proporcionar una lista de `WKPickerItems` y un selector de estilo (lista, apiladas o secuencia de imágenes). watchOS, a continuación, permite al usuario usar la corona Digital para seleccionar un elemento de la lista.

Cuando se usa un `WKInterfacePicker`, WatchKit es controlar la mayor parte del trabajo por:

- Dibujo de la lista y los elementos de interfaz individual.
- Procesando los eventos de corona Digital.
- Al llamar a una acción cuando se selecciona un elemento.

Nuevo watchOS 3, el programador ahora cuenta con acceso directo a los eventos de rotación de corona Digital, lo que permite crear sus propios elementos de interfaz de usuario que responden a los valores de rotación.

Acceso de corona digital se proporciona mediante los siguientes elementos:

- `WKCrownSequencer` : Proporciona acceso a las rotaciones por segundo.
- `WKCrownDelegate` : Proporciona acceso a los eventos de rotación delta.

#### <a name="rotations-per-second"></a>Rotaciones por segundo

Obtener acceso a las rotaciones por segundo desde la corona Digital es útil al trabajar con leyes físicas en función de las animaciones. Para acceder a las rotaciones por segundo, use el `CrownSequencer` propiedad de la `WKInterfaceController` de la extensión de inspección. Por ejemplo:

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Diferencias de rotación

Use los Deltas de rotación de la corona Digital para contar el número de giros. Use la `CrownDidRotate` invalide el método de la `WKCrownDelegate` para tener acceso a los Deltas de rotación. Por ejemplo:

```csharp
using System;
using WatchKit;
using Foundation;

namespace MonkeyWatch.MonkeySeeExtension
{
    public class CrownDelegate : WKCrownDelegate
    {
        #region Computed Properties
        public double AccumulatedRotations { get; set;}
        #endregion

        #region Constructors
        public CrownDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void CrownDidRotate (WKCrownSequencer crownSequencer, double rotationalDelta)
        {
            base.CrownDidRotate (crownSequencer, rotationalDelta);

            // Accumulate rotations
            AccumulatedRotations += rotationalDelta;
        }
        #endregion
    }
}
```

Aquí la aplicación mantiene un acumulador (`AccumulatedRotations`) para determinar el número de giros. Una rotación completa de la corona Digital es igual a un delta acumulado de `1.0` y sería un giro mitad `0.5`.

Apple ha dejado depende del desarrollador para determinar cómo se corresponden con los recuentos de giro a la confidencialidad de los cambios en el elemento de interfaz de usuario que se está actualizando.

El inicio de sesión (`+/-`) de las diferencias de giro indica la dirección que el usuario está volviendo la corona Digital:

[![](quick-interaction-techniques-images/quick03.png "El inicio de sesión de las diferencias de giro indica la dirección que el usuario está volviendo la corona Digital")](quick-interaction-techniques-images/quick03.png#lightbox)


Si el usuario se desplaza hacia arriba, WatchKit devolverá deltas positivos y si se desplace hacia abajo, a continuación, negativo deltas se devolverá, independientemente de qué orientación al usuario está gastando de la inspección en.

#### <a name="digital-crown-focus"></a>Enfoque de corona digital

Al igual que otros elementos de interfaz, la corona Digital tiene el concepto de foco. Este enfoque puede desvía la corona Digital a otros elementos de interfaz en función de cómo el usuario está interactuando con el reloj. 

Por ejemplo, cualquiera de los siguientes controles podría robar el foco de la corona Digital:

- Selector
- Slider
- Controlador de desplazamiento

Es el desarrollador para determinar cuándo deben ser el enfoque de la corona Digital sus elementos de interfaz personalizado. Apple sugiere el uso de los reconocedores de gestos de nuevo para obtener el foco en el elemento de interfaz de usuario personalizado.

### <a name="vertical-paging"></a>Paginación vertical

Es la forma estándar que un usuario desplaza una vista de tabla en una aplicación para watchOS para desplazarse a la parte deseada de datos, pulse en una fila específica para mostrar la vista detallada, pulse el botón Atrás cuando termine de ver los detalles y repita el proceso para cualquier otra información que el y están interesados en desde dentro de la tabla:

[![](quick-interaction-techniques-images/quick04.png "Mover entre una tabla y la vista de detalles")](quick-interaction-techniques-images/quick04.png#lightbox)

Nuevo para watchOS 3, el desarrollador puede habilitar paginación Vertical en sus controles de vista de tabla. Con esta característica habilitada, el usuario puede desplazarse para encontrar una fila de la vista de tabla y pulse en la fila para ver sus detalles como antes. Sin embargo, puede ahora deslícese hacia arriba al seleccionar la fila siguiente de la tabla o hacia abajo para seleccionar la fila anterior (o utilice la corona Digital), todo ello sin tener que volver a la vista de tabla en primer lugar:

[![](quick-interaction-techniques-images/quick05.png "Mover entre una tabla y la vista de detalle y desplazándose hacia arriba y abajo para moverse entre las demás filas")](quick-interaction-techniques-images/quick05.png#lightbox)

Para habilitar este modo, abra el guión gráfico de la aplicación para watchOS en Xcode para editar, seleccione la vista de tabla y compruebe el **Vertical detalle paginación** casilla de verificación:

[![](quick-interaction-techniques-images/quick06.png "Active la casilla de paginación de detalle Vertical")](quick-interaction-techniques-images/quick06.png#lightbox)

Asegúrese de que la tabla está utilizando objetos Segue para mostrar la vista detallada y guarde los cambios en el guión gráfico y vuelva a Visual Studio para Mac sincronizar.

El desarrollador puede hacer mediante programación que paginación Vertical a una fila determinada mediante el siguiente código en una vista de tabla:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

Cuando se usa paginación Vertical, el desarrollador debe tener en cuenta que WatchKit controlará automáticamente la carga previa de los controladores y como resultado, algunos métodos de ciclo de vida del controlador pueden llamarse antes de la interfaz de usuario es visible.

### <a name="notification-enhancements"></a>Mejoras de notificación

Notificación son la forma principal de interacción rápida que suele tener un usuario en watchOS y han estado disponible desde el primer Apple Watch y watchOS 1.

Una interacción rápida típica de notificación es como sigue:

1. El usuario siente la hápticos de notificación cuando se recibe una notificación de nuevo.
2. Generan su muñeca para ver la interfaz de aspecto corto para la notificación.
3. Si continúan mantener sus relojes de pulsera provocados, watchOS se pasa automáticamente a la interfaz de notificación de aspecto largo.

Hay varias maneras de que un usuario puede responder a la notificación:

- R bien definido y presenta la notificación, el usuario no hacer nada y simplemente descartarla.
- También es posible que al pulsar la notificación para iniciar la aplicación para watchOS.
- Para una notificación de que es compatible con las acciones personalizadas, el usuario puede seleccionar una de las acciones personalizadas. Estos pueden ser:
    - **Acciones de primer plano** -estos inicie la aplicación para llevar a cabo la acción.
    - **Acciones en segundo plano** : se enruten siempre al iPhone en watchOS 2, pero puede enrutarse a la watchApp en watchOS 3.

Novedades de watchOS 3:

* Notificación de usar una API similar en todas las plataformas (iOS, watchOS, tvOS y macOS).
* Notificación local se puede programar en el Apple Watch.
* Notificación en segundo plano se enrutará a la extensión de la aplicación si se programaron en el Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Entrega y programación de notificación

Notificación de iPhone del usuario estará al día para el Apple Watch cuando ocurre lo siguiente:

* Pantalla del iPhone está desactivada.
* El Apple Watch se está agotado y se ha desbloqueado.

En watchOS 3, notificaciones locales se pueden programar en el Apple Watch y solo se entregan en el reloj. Es el programador para programar una notificación de iPhone correspondiente si se requiere la aplicación.

Al incluir el mismo identificador de notificación en el Apple Watch y iPhone versiones de las notificaciones, impide que las notificaciones duplicadas de mostrarse en el reloj. La versión de Apple Watch de la notificación tendrá prioridad sobre la versión de iPhone.

Puesto que watchOS 3 utiliza el mismo `UINotification` framework API como 10, iOS, consulte nuestra iOS 10 [marco de trabajo de notificación de usuario](~/ios/platform/user-notifications/index.md) documentación para obtener más detalles.

### <a name="using-spritekit-and-scenekit"></a>Uso de SpriteKit y SceneKit

Nuevo para watchOS 3, el desarrollador ahora puede usar objetos SpritKit y SceneKit en diseño de la interfaz de usuario de la aplicación para presentar gráficos 2D y 3D.

Se han agregado dos nuevas clases de interfaz para admitir esta característica:

- `WKInterfaceSKScene` -Para trabajar con gráficos en 2D SpriteKit.
- `WKInterfaceSCNScene` -Para trabajar con gráficos 3D de SceneKit.

Para usar estos objetos, basta con arrastrarlas a la superficie de diseño dentro de guión gráfico de la aplicación de inspección en Interface Builder de Xcode y use la **Inspector de atributos** para configurarlos.

Desde este punto, trabajar con SpriteKit o SceneKit escenas funciona igual como lo hace dentro de una aplicación de iOS. La aplicación watch presentará un `WKInterfaceSKScene` llamando a uno de los `Present` métodos. SceneKit, basta con establecer la `Scene` propiedad de la `WKInterfaceSCNScene` objeto.

## <a name="actionable-complications"></a>Complicaciones que requieren acción

En watchOS 2, Apple introdujo complicaciones para aplicaciones de terceros 3rd. En watchOS 3, Apple ha ampliado las capacidades que un desarrollador puede incluir en una complicación de WatchKit. 

Además, más de la compilación en inspección caras ahora pueden incluir las complicaciones y se enfrenta a inspección existente que ya admitidas complicaciones incluir ahora complicaciones aún más.

También nuevo es la capacidad de un usuario rápidamente deslice el dedo hacia izquierdo o derecho realizar la transición a través de todas las caras de inspección que han instalado en su Apple Watch. Con la nueva galería en la aplicación de iPhone de la Apple Watch complementaria, el usuario puede agregar y personalizar las esferas de reloj nuevo y cualquiera de las complicaciones que pueden incluir.

Debido a estas nuevas características, Apple sugiere que cada aplicación en Apple Watch también debe incluir al menos una complicación y por lo tanto, todos de la aplicación nativa de Apple Watch ahora tienen complicaciones.

Complicaciones proporcionan las siguientes características a una aplicación:

- Son altamente ofrecer puesto que siempre están presentes en la pantalla del reloj.
- Con frecuencia se actualizan las complicaciones por watchOS. Cualquier aplicación que incluye una complicación en actual del reloj del usuario se actualiza al menos dos veces en una hora.
- Cualquier aplicación con una complicación en actual del reloj del usuario se mantiene en memoria, lo que hace que la aplicación iniciar rápidamente y mejora la velocidad de las respuestas de la aplicación.
- Complicaciones facilitan el usuario iniciar la funcionalidad específica de una aplicación para watchOS.

## <a name="glanceable-notification"></a>Ofrecer notificación

Notificación en Apple Watch proporcionan una manera excelente y personalizable para informar al usuario de eventos o la nueva información como los mensajes entrantes o lograr un objetivo en una aplicación de entrenamiento de rápidamente.

Mediante el uso de una notificación, se puede presentar rápidamente información valiosa al usuario. En muchas situaciones, una notificación bien diseñada puede eliminar la necesidad de que el usuario iniciar realmente la aplicación.

Nuevo para watchOS 3, todas las notificaciones ahora admiten:

- SpriteKit
- SceneKit
- Vídeo en línea

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Interfaz de usuario mejorada con SpriteKit y SceneKit

Normalmente, un desarrollador podría pensar de interfaz de usuario de juego cuando se mencionan SpriteKit y SceneKit. Sin embargo, SpriteKit y SceneKit pueden ser útil para crear interfaces de usuario que incluyen los diseños personalizados, contenidos que no sean de juegos y las animaciones que no son posibles en WatchKit por sí solo en caso contrario.

Por ejemplo, una notificación de usuario desde una aplicación de uso compartido de fotografía puede usar SpriteKit para proporcionar una experiencia de usuario enriquecida mediante la inclusión del usuario que publicó la imagen junto con una imagen real y otra información personalizada que enriquece la experiencia del usuario.

Además, se pueden mezclar SpriteKit y SceneKit con elementos estándar de WatchKit UI en el diseño de interfaz de usuario de la aplicación.

## <a name="simple-navigation"></a>Navegación simple

watchOS 3 presenta varias maneras de que un desarrollador puede simplificar la navegación en sus aplicaciones para watchOS, como el nuevo [paginación Vertical](#Vertical-Paging), [compatibilidad del reconocedor de gestos](#Gesture-Recognizer-Support) y [corona Digital Rotación](#Digital-Crown-Rotation) características presentan más arriba.

La corona Digital es única para el Apple Watch y se puede usar de muchas maneras diferentes para simplificar la navegación. Por ejemplo, una aplicación de temporizador puede utilizar la corona Digital para eliminar los datos a través de las longitudes de temporizador disponibles.

Gestos personalizados pueden presentar formas nuevos y únicos para el usuario interactuar con una aplicación de inspección y también se pueden usar para simplificar la navegación de la aplicación.

Apple sugiere buscando maneras de combinar todas las nuevas características de interacción rápida agregadas en watchOS 3 para presentar enriquecido, fácil y rápido usar interfaces de aplicación para watchOS.

## <a name="finishing-the-quick-interaction"></a>Finalizar la interacción rápida

Una experiencia de interacción rápida bien diseñada le ofrecerá la confianza para colocar su muñeca (y desactivar con la aplicación) cuándo han finalizado la interacción actual.

Donde esto específicamente se convierte en un problema es cuando la aplicación del reloj es realizar cualquier tipo de conexión de red o compartir información con su aplicación complementaria de iPhone. A menudo, esto puede conducir a un indicador de espera mientras la transacción lleva a cabo, que no es deseable durante una interacción rápida. Considere el ejemplo siguiente:

[![](quick-interaction-techniques-images/quick07.png "Diagrama de la aplicación del reloj realizando una conexión de red y compartir información con su aplicación complementaria de iPhone")](quick-interaction-techniques-images/quick07.png#lightbox)

1. El usuario elige un elemento de compra en la inspección.
2. Al pulsar el botón comprar.
3. La aplicación inicia la transacción de la red y muestra un indicador de carga.
4. Poco después, se completa la transacción y la aplicación muestra una confirmación de compra.
5. El usuario quita su muñeca y desconecta con la aplicación.

Desde el momento en que el usuario pulsa el botón Comprar hasta que se complete la transacción, que tienen sus relojes de pulsera provoca examinando un indicador de carga. Para solucionar esta situación, Apple sugiere que el desarrollador debe presentar comentarios instantáneos al usuario en lugar de mostrar un indicador de carga. 

Mediante el modelo propuesto de Apple, eche un vistazo al volver a la misma interacción rápida:

[![](quick-interaction-techniques-images/quick08.png "Diagrama del modelo propuesto manzanas")](quick-interaction-techniques-images/quick08.png#lightbox)

1. El usuario elige un elemento de compra en la inspección.
2. Al pulsar el botón comprar.
3. La aplicación inicia la transacción de la red y muestra un mensaje que indica que la compra se ha iniciado correctamente.
4. El usuario quita su muñeca y desconecta con la aplicación.
5. Cuando la transacción se complete correctamente más tarde algún tiempo, la aplicación muestra una notificación Local para informar al usuario de una compra correcta.

Este tiempo, tan pronto como el usuario pulsa el botón de compra se muestran un mensaje que indica que ha iniciado la compra, por lo que se pueden quitar su muñeca con confianza y finalizar la interacción rápida en este momento. Más adelante se les informa de que el éxito o fracaso de la transacción en una notificación al usuario. De este modo, el usuario está interactuando con la aplicación solo durante las fases del proceso de "activas".

Para las aplicaciones que realizan funciones de red, puede usar un fondo `NSURLSession` para controlar la comunicación de red con una tarea de descarga. Esto permitirá que la aplicación que se reactive en segundo plano para procesar la información descargada. Para aplicaciones que requieren un procesamiento en segundo plano, utilice una aserción de la tarea en segundo plano para controlar el procesamiento necesario.

## <a name="quick-interaction-design-tips"></a>Sugerencias de diseño de interacción rápida

Puesto que la longitud deseada de una interacción rápida es de dos segundos o menos, el desarrollador debe centrarse en el diseño de las interacciones de la aplicación desde el principio del proceso de diseño. Buscar áreas donde esas interacciones pueden simplificar (mediante la técnica presentada más arriba) y usar las nuevas características de watchOS 3 para que la aplicación rápida y receptiva.

Apple sugiere lo siguiente:

- Se centran en las interacciones rápidas poniendo al día las características más usadas de la aplicación.
- Utilice complicaciones y notificaciones del usuario a la luz de características y funciones comunes.
- Crear la interfaz de usuario enriquecidas y ofrecer con SceneKit y SpriteKit.
- Siempre que sea posible, simplificar la navegación dentro de la aplicación.
- Nunca realice el usuario espera, que puedan colocar su muñeca y desactivar con la aplicación tan pronto como sea posible.

## <a name="summary"></a>Resumen

En este artículo se trata las técnicas de interacción rápida Apple ha agregado en watchOS 3 y cómo implementarlos en Xamarin.iOS para Apple Watch.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
