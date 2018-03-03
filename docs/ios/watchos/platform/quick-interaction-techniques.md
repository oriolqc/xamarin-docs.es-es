---
title: "Técnicas de interacción rápida para watchOS 3"
description: "Este artículo explican las técnicas de interacción rápida Apple ha agregado en watchOS 3 y cómo implementarlas en Xamarin.iOS para Apple Watch."
ms.topic: article
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 75a8e807a68a3fccfa76fc7ba1f260818b25174d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="quick-interaction-techniques-for-watchos-3"></a>Técnicas de interacción rápida para watchOS 3

_Este artículo explican las técnicas de interacción rápida Apple ha agregado en watchOS 3 y cómo implementarlas en Xamarin.iOS para Apple Watch._

Proporciona las interacciones del usuario rápido son esenciales para crear aplicaciones de Apple Watch atractivas y complicaciones. Nuevo para watchOS 3, Apple ha agregado compatibilidad para identificadores de gestos, acceso a la copa Digital y nuevas técnicas de notificación de usuario y la navegación. Esto, junto con la compatibilidad agregada para SceneKit y SpriteKit, permiten al desarrollador crear fácilmente enriquecidas y concisa interfaces que son sensibles y rápido.

## <a name="what-are-quick-interactions"></a>¿Cuáles son las interacciones rápido

Para que un desarrollador que se usa para crear aplicaciones para iOS o Mac OS (donde se mide la cantidad de tiempo que un usuario emplea interactuar con la aplicación en minutos u horas), diseñar una aplicación correcta para el Apple Watch, puede ser un desafío y requiere otra enfoque.

En watchOS, el usuario normalmente desea elevar su muñeca, rápidamente interactúan con una aplicación (normalmente por un par de segundos), a continuación, coloque su muñeca y continuar lo que era que estaban haciendo.

Éstos son algunos ejemplos de interacciones rápidos típicos en el de Apple Watch:

- A partir de un temporizador.
- Comprobando la información meteorológica.
- Marcar un elemento de una lista de tareas.

Para lograr estos objetivos, debe ser una aplicación en el de Apple Watch:

- **Concisa** -lo que significa que, con una rápida, eche un vistazo el usuario debería poder obtener la información que necesitan. 
- **Procesables** -lo que significa que los usuarios debe poder tomar decisiones rápidas y bien informadas.
- **Capacidad de respuesta** -lo que significa que el usuario nunca se debe esperar para recibir la información que necesitan o para lograr la acción que deseen.

### <a name="quick-interactions-length"></a>Longitud de las interacciones rápido

Dada la naturaleza concisa de las aplicaciones de Apple Watch, Apple sugiere que la longitud de una interacción rápida ideal debería ser dos segundos o menos. Como resultado de este límite de dos segundos, el programador tendrá que dedicar una cantidad considerable de tiempo tanto diseñar e implementar una aplicación de Apple Watch. 

## <a name="new-watchos-3-features-and-apis"></a>Nueva watchOS 3 características y las API

Apple ha agregado varias características nuevas y las API para WatchKit para ayudar a los desarrolladores agregar interacciones rápidas a sus aplicaciones de Apple Watch:

- watchOS 3 proporciona acceso a nuevos tipos de entrada como del usuario:
    - Identificadores de gestos
    - Rotación de copa digital 
- watchOS 3 proporciona nuevas formas de mostrar y actualizar la información, como:
    - Navegación mejorada de tabla
    - Nueva compatibilidad de marco de trabajo de notificación de usuario
    - Integración de SpriteKit y SceneKit

Mediante la implementación de estas nuevas características, el programador puede asegurarse de que su aplicación watchOS 3 es Glanceable, aplicable y Responsive.

### <a name="gesture-recognizer-support"></a>Compatibilidad con reconocimiento de gestos

Si el desarrollador ha implementado los identificadores de gestos en iOS, debe estar muy familiarizados con cómo funcionan los identificadores de gestos en watchOS 3. Para actualizar, identificadores de gestos son objetos que analizar los eventos de bajo nivel toque movimientos reconocibles, predefinidos.

watchOS 3 será compatible con los identificadores de gesto de las siguientes cuatro:

- Tipos de movimientos discretos:
    - El gesto de deslizar rápidamente (`WKSwipeGestureRecognizer`).
    - El gesto de pulsar (`WKTapGestureRecognizer`).
- Tipos de movimiento continuo:
    - El movimiento panorámico (`WKPanGestureRecognizer`).
    - El gesto presionándolos una larga (`WKLongPressGestureRecognizer`).

Para implementar uno de los nuevos identificadores de gestos, arrástrelo hasta una superficie de diseño en el Diseñador de Visual Studio de iOS para Mac y configure sus propiedades.

En el código, responder a la acción del reconocedor podrá controlar el movimiento se desencadena por el usuario. De nuevo, esto se lleva a cabo de la misma manera como se tratar en iOS.

#### <a name="discrete-gesture-states"></a>Estados de gestos discretos

Para los gestos discretos, la acción se llama cuando se reconoce el movimiento y un estado (`WKGestureRecognizerState`) se asigna como:

[ ![](quick-interaction-techniques-images/quick01.png "Estados de gestos discretos")](quick-interaction-techniques-images/quick01.png)

Todos los movimientos discretos comienzan el `Possible` estado y transiciones a cualquiera la `Failed` o `Recognized` estado. Al usar gestos discretos, el desarrollador generalmente no tratar directamente con el estado. En su lugar, se basan en la acción que se llama cuando el movimiento se reconoce solo.

#### <a name="continuous-gesture-states"></a>Estados de movimiento continuo

Movimientos continuadas son ligeramente diferentes de movimientos discretos, donde la acción se llama varias veces, tal y como se reconoce el movimiento:

[ ![](quick-interaction-techniques-images/quick02.png "Estados de movimiento continuo")](quick-interaction-techniques-images/quick02.png)

Una vez más, movimientos continua empieza en el `Possible` estado, pero a través de varias actualizaciones de progreso. Aquí el programador deberá tener en cuenta el estado del reconocedor y actualizar la interfaz de usuario de la aplicación durante la `Changed` fase hasta que el movimiento se finalmente `Recognized` o `Canceled`.

#### <a name="gesture-recognizer-usage-tips"></a>Sugerencias de uso del reconocimiento de gestos

Apple recomienda lo siguiente al trabajar con identificadores de gestos en watchOS 3:

- Agregue los identificadores de gestos para agrupar elementos en lugar de los controles individuales. Puesto que el de Apple Watch tiene un tamaño más pequeño de la pantalla física, los elementos de grupo tienden a ser más grande y más fácil de destinos para el usuario presione. Además, los identificadores de gestos pueden entrar en conflicto con integrados movimientos ya se encuentran en los controles de interfaz de usuario nativa.
- Establecer las relaciones de dependencia en el guión gráfico de la aplicación de la inspección.
- Algunos gesto tienen prioridad sobre otros tipos de gestos, como:
    - Desplazamiento
    - Exigir la modificación
 
### <a name="digital-crown-rotation"></a>Rotación de copa digital

Mediante la implementación de compatibilidad con copa Digital en sus aplicaciones watchOS 3, un desarrollador puede proporcionar navegación mayor velocidad y precisión interacciones para sus usuarios.

Desde watchOS 2, podría utilizar la aplicación de Apple Watch el `WKInterfacePicker` objeto para tener acceso a la copa Digital al proporcionar una lista de `WKPickerItems` y un estilo de selector (lista, apiladas o secuencia de imágenes). watchOS, a continuación, permite al usuario que utilice la copa Digital para seleccionar un elemento de la lista.

Cuando se usa un `WKInterfacePicker`, WatchKit lleva a cabo la mayoría del trabajo por:

- Dibujo de la lista y los elementos de interfaz individual.
- Procesar los eventos de copa Digital.
- Al llamar a una acción cuando se selecciona un elemento.

Nuevo watchOS 3, el programador ahora cuenta con acceso directo a los eventos de rotación de copa Digital que les permite crear sus propios elementos de interfaz de usuario que respondan a los valores de rotación.

Se proporciona un acceso copa digital por los siguientes elementos:

- `WKCrownSequencer` : Proporciona acceso a rotaciones por segundo.
- `WKCrownDelegate` : Proporciona acceso a los eventos de rotación delta.

#### <a name="rotations-per-second"></a>Rotaciones por segundo

Obtiene acceso a las rotaciones por segundo desde la copa Digital es útil al trabajar con física según las animaciones. Para obtener acceso a las rotaciones por segundo, use la `CrownSequencer` propiedad de la `WKInterfaceController` de la extensión de inspección. Por ejemplo:

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Deltas rotatorio

Use los Deltas de rotación de la copa Digital para contar el número de rotaciones. Use la `CrownDidRotate` invalidar el método de la `WKCrownDelegate` para tener acceso a los Deltas de rotación. Por ejemplo:

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

Aquí la aplicación mantiene un acumulador (`AccumulatedRotations`) para determinar el número de rotaciones. Un giro completo de la copa Digital es igual a una diferencial acumulado de `1.0` y sería un giro a la mitad `0.5`.

Apple ha dejado depende del desarrollador para determinar cómo se corresponden los recuentos de rotación a la confidencialidad de los cambios en el elemento de interfaz de usuario que se está actualizando.

El inicio de sesión (`+/-`) de las diferencias rotatorio indica la dirección que el usuario está girando la copa Digital:

[ ![](quick-interaction-techniques-images/quick03.png "El inicio de sesión de las diferencias rotatorio indica la dirección que el usuario está girando la copa Digital")](quick-interaction-techniques-images/quick03.png)


Si el usuario se desplaza hacia arriba, WatchKit devolverá deltas positivos y si se desplace hacia abajo, deltas negativo se devolverán, con independencia de qué orientación al usuario es llevar la inspección en.

#### <a name="digital-crown-focus"></a>Enfoque de copa digital

Al igual que otros elementos de interfaz, la copa Digital tiene el concepto de concentración. Este enfoque puede desvía la copa Digital a otros elementos de la interfaz en función de cómo el usuario está interactuando con el reloj. 

Por ejemplo, cualquiera de los siguientes controles podría robar el foco de la la copa Digital:

- Selector de
- Slider
- Controlador de desplazamiento

Es depende del desarrollador determinar el momento de su elemento de interfaz personalizada debe ser el foco de la la copa Digital. Apple sugiere utilizando los nuevos identificadores de gestos para ganar el foco en el elemento de interfaz de usuario personalizado.

### <a name="vertical-paging"></a>Paginación vertical

La manera estándar que un usuario desplaza una vista de tabla en una aplicación de watchOS es desplácese hasta el elemento deseado de datos, pulse en una fila específica para mostrar la vista detallada, pulse el botón Atrás cuando termine de ver los detalles y repita el proceso para cualquier otra información que el y está interesado en desde dentro de la tabla:

[ ![](quick-interaction-techniques-images/quick04.png "Mover entre una tabla y la vista de detalle")](quick-interaction-techniques-images/quick04.png)

Nuevo para watchOS 3, el desarrollador puede habilitar paginación Vertical en sus controles de vista de tabla. Esta característica está habilitada, el usuario puede desplazarse para encontrar una fila de la vista de tabla y puntee en la fila para ver sus detalles como antes. Sin embargo, puede ahora deslícese hacia arriba para seleccionar la fila siguiente de la tabla o hacia abajo para seleccionar la fila anterior (o usar la copa Digital), sin tener que volver a la vista de tabla en primer lugar:

[ ![](quick-interaction-techniques-images/quick05.png "Mover entre una tabla y la vista de detalle y Deslizar rápidamente hacia arriba y abajo para desplazarse entre las demás filas")](quick-interaction-techniques-images/quick05.png)

Para habilitar este modo, abra el guión gráfico de la aplicación watchOS de Xcode para su edición, seleccione la vista de tabla y compruebe la **paginación de detalle Vertical** casilla de verificación:

[ ![](quick-interaction-techniques-images/quick06.png "Active la casilla de paginación de detalle Vertical")](quick-interaction-techniques-images/quick06.png)

Asegúrese de que la tabla utilice Segues para mostrar la vista detallada y guarde los cambios en el guión gráfico y vuelva a Visual Studio para Mac sincronizar.

El programador mediante programación puede ocupar paginación Vertical a una fila determinada mediante el código siguiente en una vista de tabla:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

Cuando se utiliza la paginación Vertical, el desarrollador debe tener en cuenta que WatchKit controlará automáticamente la precarga de controladores y como resultado, pueden llamarse algunos métodos de ciclo de vida de controlador antes de la interfaz de usuario es visible.

### <a name="notification-enhancements"></a>Mejoras de notificación

Notificación son la forma principal de interacción rápida que suele tener un usuario en watchOS y han estado disponible desde la primera de Apple Watch y watchOS 1.

Una interacción de notificación rápida típica es la siguiente:

1. El usuario siente hápticos de notificación cuando se recibe una notificación de nuevo.
2. Activan su muñeca para ver la interfaz de buscar corto para la notificación.
3. Si se siguen mantener su muñeca generada, watchOS pasa automáticamente a la interfaz de notificación de buscar largo.

Hay varias maneras de que un usuario pueda responder a la notificación:

- Para bien definidos y presentan la notificación, el usuario no hacer nada y simplemente descartar la notificación.
- También puede puntear en de la notificación para iniciar la aplicación watchOS.
- Para una notificación que admite acciones personalizadas, el usuario puede seleccionar una de las acciones personalizadas. Pueden ser:
    - **Acciones de primer plano** -se inicie la aplicación para llevar a cabo la acción.
    - **Acciones en segundo plano** : siempre se enrutan a iPhone en watchOS 2 pero puede enrutarse a la watchApp en watchOS 3.

Novedades de watchOS 3:

* Notificación de usar una API similar para todas las plataformas (iOS, watchOS, tvOS y macOS).
* Notificación local se puede programar en el de Apple Watch.
* Notificación de fondo se enrutará a la extensión de la aplicación si se programaron en el de Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Entrega y programación de notificación

Las notificaciones de iPhone del usuario será al día a la de Apple Watch cuando ocurre lo siguiente:

* Pantalla del iPhone está desactivada.
* El Apple Watch se se ha gastado y se ha desbloqueado.

En watchOS 3, notificaciones Local se pueden programar en el de Apple Watch y solo se entregan en el reloj. Está en funcionamiento el el programador para programar un iPhone correspondiente notificación si se requiere la aplicación.

Al incluir el mismo identificador de notificación en las versiones de iPhone de las notificaciones y Apple Watch, se evita que las notificaciones duplicadas de mostrarse en el reloj. La versión de Apple Watch de la notificación tendrá prioridad sobre la versión de iPhone.

Debido a que watchOS 3 utiliza los mismos `UINotification` framework API como iOS 10, visite nuestro iOS 10 [marco de notificación de usuario](~/ios/platform/user-notifications/index.md) documentación para obtener más detalles.

### <a name="using-spritekit-and-scenekit"></a>Uso de SpriteKit y SceneKit

Nuevo para watchOS 3, el programador puede ahora utilizar objetos SpritKit y SceneKit en el diseño de la interfaz de usuario de su aplicación para presentar gráficos 2D y 3D.

Se han agregado dos nuevas clases de interfaz para admitir esta característica:

- `WKInterfaceSKScene` -Para trabajar con gráficos 2D SpriteKit.
- `WKInterfaceSCNScene` -Para trabajar con gráficos 3D SceneKit.

Para utilizar estos objetos, simplemente arrástrelos hasta la superficie de diseño dentro de guión gráfico de la aplicación de la inspección en el generador de interfaz de Xcode y utilice el **atributos Inspector** para configurarlos.

Desde este punto, trabajar con el SpriteKit o SceneKit plano funciona igual que lo hace dentro de una aplicación de iOS. La aplicación de inspección presentará un `WKInterfaceSKScene` llamando a uno de los `Present` métodos. SceneKit, basta con establecer la `Scene` propiedad de la `WKInterfaceSCNScene` objeto.

## <a name="actionable-complications"></a>Complicaciones procesables

En watchOS 2, Apple incorporó complicaciones para 3rd aplicaciones de terceros. En watchOS 3, Apple ha ampliado las capacidades que un desarrollador puede incluir en una complicación WatchKit. 

Además, más de integrado en inspección caras ahora pueden incluir las complicaciones y se enfrenta a inspección existente que ya complicaciones compatibles puede ahora incluye las complicaciones aún más.

También nuevo es la capacidad de un usuario rápidamente deslice el dedo izquierdo o derecho realizar la transición a través de todas las caras de inspección que ha instalado en su Apple Watch. Mediante la nueva galería en la aplicación de iPhone del de Apple Watch complementaria, el usuario puede agregar y personalizar nueva caras de inspección y cualquiera de las complicaciones que pueden incluir.

Debido a estas nuevas características, Apple recomienda que todas las aplicaciones en Apple Watch también deben incluir al menos una complicación y por lo tanto, de la aplicación nativa de Apple Watch ahora tienen complicaciones.

Complicaciones proporcionan las siguientes características a una aplicación:

- Únicamente son muy concisa, puesto que siempre están presentes en la pantalla del reloj.
- Con frecuencia se actualizan las complicaciones por watchOS. Cualquier aplicación que incluye una complicación en pantalla de watch mostrado actualmente del usuario se actualiza al menos dos veces por hora.
- Cualquier aplicación con una complicación en pantalla de watch mostrado actualmente del usuario se mantiene en la memoria que hace que la aplicación inicie rápidamente y aumenta la velocidad de las respuestas de la aplicación.
- Complicaciones facilitan el usuario iniciar una funcionalidad específica en una aplicación watchOS.

## <a name="glanceable-notification"></a>Notificación concisa

Notificación en Apple Watch proporcionan una manera estupenda y personalizable para rápidamente informar al usuario de eventos o la nueva información como mensajes entrantes o para lograr un objetivo en una aplicación de entrenamiento.

Mediante el uso de una notificación, se puede presentar rápidamente información valiosa para el usuario. En muchas situaciones, una notificación bien diseñada puede quitar la necesidad de que el usuario iniciar realmente la aplicación.

Nuevo para watchOS 3, todas las notificaciones ahora admiten:

- SpriteKit
- SceneKit
- Vídeo en línea

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Interfaz de usuario mejorada con SpriteKit y SceneKit

Normalmente, un desarrollador que piense juego interfaz de usuario cuando se mencionan SpriteKit y SceneKit. Sin embargo, SpriteKit y SceneKit pueden ser útil para crear interfaces de usuario que incluyen diseños personalizados, contenidos y las animaciones que de lo contrario no son posibles en WatchKit por sí sola no juegos.

Por ejemplo, una notificación de usuario desde una aplicación de uso compartido de fotografías sirve SpriteKit para proporcionar una experiencia de usuario enriquecida mediante la inclusión del usuario que registra la imagen junto con una imagen real y otra información personalizada que enriquece la experiencia del usuario.

Además, se pueden mezclar SpriteKit y SceneKit con los elementos WatchKit UI estándar en el diseño de la interfaz de usuario de la aplicación.

## <a name="simple-navigation"></a>Navegación simple

watchOS 3 presenta varias formas de que un desarrollador puede simplificar la navegación dentro de sus aplicaciones watchOS como el nuevo [paginación Vertical](#Vertical-Paging), [compatibilidad con el reconocimiento de gesto](#Gesture-Recognizer-Support) y [copa Digital Rotación](#Digital-Crown-Rotation) características presentan anteriormente.

La copa Digital es único para el Apple Watch y puede utilizarse de muchas maneras diferentes para simplificar la navegación. Por ejemplo, una aplicación de temporizador puede usar la copa Digital para eliminar a través de longitudes de temporizador disponibles.

Gestos personalizados pueden presentar formas nuevos y únicos para el usuario interactuar con una aplicación de la inspección y también pueden utilizarse para simplificar la navegación de la aplicación.

Apple sugerir busca maneras de combinar todas las nuevas características de interacción rápida agregadas en watchOS 3 para presentar enriquecida, fácil y rápido usar las interfaces de aplicación de watchOS.

## <a name="finishing-the-quick-interaction"></a>Finalizar la interacción rápida

Una experiencia de interacción rápida bien diseñada le dará al usuario la confianza para quitar su muñeca (y desactivar con la aplicación) cuando haya terminado la interacción actual.

Donde esto específicamente se convierte en un problema es cuando la aplicación de inspección está realizando cualquier tipo de conexión de red o compartir información con su aplicación de iPhone complementario. A menudo, esto puede conducir a un indicador de espera mientras la transacción lleva a cabo, lo que no es deseable durante una interacción rápida. Considere el ejemplo siguiente:

[ ![](quick-interaction-techniques-images/quick07.png "Diagrama de la aplicación de inspección realizar una conexión de red y compartir información con su aplicación de iPhone complementarias")](quick-interaction-techniques-images/quick07.png)

1. El usuario elige un elemento de compra en el reloj.
2. Pulse el botón comprar.
3. La aplicación inicia la transacción de red y muestra un indicador de carga.
4. Poco después, completa la transacción y la aplicación muestra una confirmación de compra.
5. El usuario quita su muñeca y desconecta con la aplicación.

Desde el momento en que el usuario puntee en el botón de compra hasta que se complete la transacción, tienen su muñeca genera examinando un indicador de carga. Para solucionar esta situación, Apple sugiere que el desarrollador debe presentar una evaluación instantánea para el usuario en lugar de mostrar un indicador de carga. 

Usar modelo sugerido de Apple, eche un vistazo al volver a la misma interacción rápida:

[ ![](quick-interaction-techniques-images/quick08.png "Diagrama del modelo sugerido de manzanas")](quick-interaction-techniques-images/quick08.png)

1. El usuario elige un elemento de compra en el reloj.
2. Pulse el botón comprar.
3. La aplicación inicia la transacción de red y muestra un mensaje que indica que la compra se ha iniciado correctamente.
4. El usuario quita su muñeca y desconecta con la aplicación.
5. Cuando la transacción se completa correctamente más tarde algún tiempo, la aplicación muestra una notificación Local para informar al usuario de una compra correcta.

Este tiempo, tan pronto como el usuario puntee en el botón de compra se muestran un mensaje que indica que ha iniciado la compra, lo que permite quitar su muñeca y finalizar la interacción rápida en este momento con total confianza. Más adelante se les informa de que el éxito o fracaso de la transacción en una notificación al usuario. De esta manera, el usuario está interactuando con la aplicación solo durante las fases del proceso de "activas".

Para las aplicaciones que realizan funciones de red, puede usar un fondo `NSURLSession` para controlar la comunicación de red con una tarea de descarga. Esto permitirá que la aplicación pueden reactivarse en segundo plano para procesar la información descargada. Para las aplicaciones que requieren el procesamiento en segundo plano, utilice una aserción de la tarea de segundo plano para controlar el procesamiento necesario.

## <a name="quick-interaction-design-tips"></a>Sugerencias de diseño de interacción rápida

Puesto que la longitud deseada de una interacción rápida es de dos segundos o menos, el desarrollador debe centrarse en el diseño de las interacciones de la aplicación desde el principio del proceso de diseño. Buscar áreas donde esas interacciones pueden simplificar (mediante la técnica presentada anteriormente) y usar las nuevas características de watchOS 3 para que la aplicación rápida y eficaz.

Apple sugiere lo siguiente:

- Se centran en las interacciones rápida si se ponen al día de las características más usadas de la aplicación.
- Utilice las complicaciones y las notificaciones de usuario para exponer las características y funciones comunes.
- Crear interfaz de usuario enriquecida, concisa con SceneKit y SpriteKit.
- Siempre que sea posible, simplificar la navegación dentro de la aplicación.
- Nunca realice el usuario espera, permitirles que quite su muñeca y desactivar con la aplicación tan pronto como sea posible.

## <a name="summary"></a>Resumen

En este artículo se trata las técnicas de interacción rápida Apple ha agregado en watchOS 3 y cómo implementarlas en Xamarin.iOS para Apple Watch.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
