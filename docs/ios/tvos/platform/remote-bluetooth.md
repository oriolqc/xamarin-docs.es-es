---
title: Siri remoto y controladores de Bluetooth
description: Este artículo tratan admitir los nuevos dispositivos de juego Siri remoto y Bluetooth en sus aplicaciones Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 5b5893278acad999efd94c89f1ca923100f5cf7c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="siri-remote-and-bluetooth-controllers"></a>Siri remoto y controladores de Bluetooth

_Este artículo tratan admitir los nuevos dispositivos de juego Siri remoto y Bluetooth en sus aplicaciones Xamarin.tvOS._


Los usuarios de la aplicación Xamarin.tvOS no será su interacción con su interfaz directamente como con iOS donde pulse imágenes en la pantalla del dispositivo, pero indirectamente de a través de la sala utilizando la [Siri remoto](#The-Siri-Remote).

Si la aplicación es un juego, puede, opcionalmente, generar en la compatibilidad de entidad 3ª, realizados para iOS (MFI) [dispositivos de juego Bluetooth](#Bluetooth-Game-Controllers) en la aplicación también.

[![](remote-bluetooth-images/intro01.png "El Bluetooth remoto y el dispositivo de juego")](remote-bluetooth-images/intro01.png#lightbox)

Este artículo se describe la [Siri remoto](#The-Siri-Remote), [Touch movimientos de superficie](#Touch-Surface-Gestures) y [Siri remoto botones](#Siri-Remote-Buttons) y muestra cómo trabajar con ellos a través de [gestos y Guiones gráficos](#Gestures-and-Storyboards), [gestos y código](#Gestures-and-Code) y [control de eventos de bajo nivel](#Low-Level-Event-Handling). Por último, se trata [trabajar con dispositivos de juego](#Working-with-Game-Controllers) en una aplicación Xamarin.tvOS.

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>El control remoto Siri

El modo principal de que los usuarios será su interacción con la televisión de Apple y la aplicación Xamarin.tvOS, es a través de la instancia remota de Siri incluye. Apple ha diseñado el control remoto para cubrir la distancia entre el usuario sentado en el sofá y la interfaz de usuario de Apple TV muestran en el espacio en la pantalla de TV.

El desafío que un desarrollador de aplicaciones de tvOS es crear una interfaz de usuario rápida, fácil de usar y visualmente atractivo que aprovecha la superficie de táctil de Siri Remote, acelerómetro, giroscopio y botones.

[![](remote-bluetooth-images/remote01.png "El control remoto Siri")](remote-bluetooth-images/remote01.png#lightbox)

La instancia remota de Siri tiene las siguientes características y usos previstos dentro de la aplicación tvOS:

|Característica|Uso de aplicación general|Uso de aplicaciones de juegos|
|---|---|---|
|**Toque la superficie**<br />Deslice el dedo para navegar, presione para seleccionar el modo mantener presionado para los menús contextuales.|**Tap/Swipe**<br />Interfaz de usuario de navegación entre elementos puede recibir el foco.<br /><br />**Click**<br />Activa el elemento (enfocado) seleccionado.|**Tap/Swipe**<br />Depende de diseño de juego y puede usarse como un panel de D pulsando en los bordes.<br /><br />**Click**<br />Ejecutar la función del botón primario.|
|**Menu**<br />Presione para volver a la pantalla anterior o un menú.|Vuelve a la pantalla anterior y se cierra como Apple TV Home screen desde la pantalla principal de la aplicación.|Pausar y reanudar el juego, vuelve a la pantalla anterior y se cierra como Apple TV Home screen desde la pantalla principal de la aplicación.|
|**Siri/búsqueda**<br />En los países con Siri, mantenga presionada para el control de voz, en todos los otros países, muestra la pantalla de búsqueda.|N/D|N/D|
|**Reproducir/pausa**<br />Reproducir y pausar medio o proporciona una función secundaria en las aplicaciones.|Inicia la reproducción de multimedia y pausar y reanudar la reproducción.|Realiza la función del botón secundario u omite el vídeo de introducción (si existe).|
|**Página principal**<br />Presione para volver a la pantalla Inicio, haga doble clic para mostrar la ejecución de aplicaciones, mantenga presionada al dispositivo de modo de suspensión.|N/D|N/D|
|**Volumen**<br />Controles asociados volumen equipamiento de audio/vídeo.|N/D|N/D|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>Superficies gestos de toque

Touch superficie de Siri Remote es capaz de detectar una variedad de un solo dedo movimientos que puede responder a la aplicación Xamarin.tvOS:

|Deslice el dedo|Clic|Puntee en|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|Mueve la selección (foco) entre los elementos de interfaz de usuario en pantalla (hacia arriba, abajo a la izquierda, derecha). Deslizar rápidamente puede utilizarse para desplazarse por las listas grandes de contenido rápidamente mediante inercia.|Activa el elemento seleccionado de (enfocado) o actúa como el botón primario en un juego. Al hacer clic en y que contiene pueden activar los menús contextuales o funciones secundarias.|Pulse ligeramente en la superficie tocan los bordes actúa como botones direccionales en un panel de D, mover el foco hacia arriba, abajo, a la izquierda o derecha según el área punteadas. Dependiendo de la aplicación, puede utilizarse para mostrar los controles ocultos.|

Apple proporciona las siguientes sugerencias para trabajar con gestos de toque superficie:

* **Diferenciar entre los clics y derivaciones** -clic es una acción intencionada por el usuario y se adapta perfectamente para la selección, activación y el botón primario de un juego. Pulse es más sutil y debe usarse con moderación porque el usuario a menudo contiene la instancia remota de Siri en su mano y accidentalmente puede activar un evento Tap fácilmente.
* **No volver a definir movimientos estándar** -el usuario tiene una expectativa que movimientos específicos llevará a cabo acciones específicas, no debe definir el significado o la función de estos movimientos de la aplicación. La única excepción es una aplicación de juego durante el juego activo.
* **Definir nuevos movimientos moderación** -nuevo, el usuario no tiene una expectativa que movimientos específicos llevará a cabo acciones específicas. Debe evitar definir gestos personalizados para llevar a cabo acciones estándar. Y una vez más, juegos son la excepción más habitual donde gestos personalizados pueden agregar play divertido, atractiva para el juego.
* **Si es necesario, responder a D-Pad puntea** : ligeramente punteando en la esquina bordes de la superficie táctil se reaccionar como un control de dirección en un enfoque móvil del dispositivo de juego o una dirección hacia arriba, hacia abajo, izquierda o hacia la derecha. Si es necesario, se debe responder a estos movimientos en su aplicación o un juego.

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Botones de Siri remoto

Además de gestos en la superficie de táctil, la aplicación puede responder al usuario, haga clic en la superficie de Touch o al presionar el botón de reproducción/pausa. Si tiene acceso remoto Siri con el marco de controlador de juego, también puede detectar la pulsación del botón de menú. 

Además, se pueden detectar las pulsaciones de botón de menú con un reconocedor de movimiento estándar `UIKit` elementos. Si interceptar la pulsación del botón de menú, podrá ser responsable de cerrar la vista y el controlador de la vista actual y volver a la anterior.

> [!IMPORTANT]
> Debería **siempre** asignar una función al botón reproducir/pausa en el servidor remoto. Tener un botón no es funcional puede hacer que su aplicación buscar roto al usuario final. Si no tiene una función válida para este botón, asigne la misma función que el botón primario (Touch superficie haga clic en).




<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>Movimientos y los guiones gráficos

La manera más fácil trabajar con el control remoto en la aplicación Xamarin.tvOS Siri es agregar identificadores de movimiento a las vistas en el Diseñador de interfaz.

Para agregar un reconocedor de gestos, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo en el Diseñador de la interfaz.
2. Arrastre un **pulse reconocedor de movimientos** desde el **biblioteca** y colóquela en la vista: 

    [![](remote-bluetooth-images/storyboard01.png "Un reconocedor de movimiento de derivación")](remote-bluetooth-images/storyboard01.png#lightbox)
3. Comprobar **seleccione** en el **botón** sección de la **Inspector de atributo**: 

    [![](remote-bluetooth-images/storyboard02.png "Active")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **Seleccione** significa el gesto responderá a hacer clic en el usuario la **Touch superficie** en el servidor remoto Siri. También tiene la opción de responder a la **menú**, **reproducir/pausa**, **una**, **hacia abajo**, **izquierda** y **Derecha** botones.
5. A continuación, conecte un **acción** desde el **pulse reconocedor de movimientos** y llámelo `TouchSurfaceClicked`: 

    [![](remote-bluetooth-images/storyboard03.png "Una acción el reconocedor de movimientos de derivación")](remote-bluetooth-images/storyboard03.png#lightbox)
6. Guardar los cambios y volver a Visual Studio para Mac.

Editar el controlador de vista (ejemplo `FirstViewController.cs`) de archivos y agregue el siguiente código para controlar el movimiento que se va a desencadenar:

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class FirstViewController : UIViewController
    {
        ...

        #region Custom Actions
        partial void TouchSurfaceClicked (Foundation.NSObject sender) {
            // Handle click here
            ...
        }
        #endregion
    }
}
```

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). En concreto la [creación de la interfaz de usuario](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface) y [escribir el código con las salidas y las acciones](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) secciones.

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>Movimientos y código

Si lo desea, puede crear movimientos directamente en código C# y agregar a las vistas en la interfaz de usuario. Por ejemplo, para agregar una serie de identificadores de gesto de deslizar rápidamente, modifique el controlador de vista y agregue el código siguiente:

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class SecondViewController : UIViewController
    {
        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();    

            // Wire-up gestures
            var upGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Up";
                ButtonLabel.Text = "Swiped Up";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Up
            };
            this.View.AddGestureRecognizer (upGesture);

            var downGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Down";
                ButtonLabel.Text = "Swiped Down";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Down
            };
            this.View.AddGestureRecognizer (downGesture);

            var leftGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Left";
                ButtonLabel.Text = "Swiped Left";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Left
            };
            this.View.AddGestureRecognizer (leftGesture);

            var rightGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Right";
                ButtonLabel.Text = "Swiped Right";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Right
            };
            this.View.AddGestureRecognizer (rightGesture);
        }
        #endregion
    }
}
```

<a name="Low-Level-Event-Handling" />

## <a name="low-level-event-handling"></a>Control de eventos de bajo nivel

Si va a crear un tipo personalizado basado en `UIKit` en la aplicación Xamarin.tvOS (por ejemplo `UIView`), también tiene la capacidad para proporcionar control de bajo nivel de presionar el botón a través de `UIPress` eventos. 

A `UIPress` eventos es tvOS lo que un `UITouch` eventos es iOS, excepto `UIPress` devuelve información acerca de botón pulsa en la instancia remota de Siri u otro conectado dispositivos Bluetooth (por ejemplo, un dispositivo de juego). `UIPress` describen los eventos de la pulsación del botón y su estado (Began, cancelado, cambiado o terminado). 

Para los botones analógica en dispositivos como dispositivos de juego de Bluetooth, `UIPress` también devuelve la cantidad de forzar que se va a aplicar al botón. El `Type` propiedad de la `UIPress` evento define qué botón físico ha cambiado estado, mientras que el resto de las propiedades describen el cambio que se ha producido.

El código siguiente muestra un ejemplo de control de bajo nivel `UIPress` eventos para un `UIView`:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvRemote
{
    public partial class EventView : UIView
    {
        #region Computed Properties
        public override bool CanBecomeFocused {
            get {
                return true;
            }
        }
        #endregion

        #region 
        public EventView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void PressesBegan (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesBegan (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Red;
                }
            }
        }

        public override void PressesCancelled (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesCancelled (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }

        public override void PressesChanged (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesChanged (presses, evt);
        }

        public override void PressesEnded (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesEnded (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }
        #endregion
    }
}
```

Al igual que con `UITouch` eventos, si necesita implementar cualquiera de los `UIPress` invalidaciones de eventos, debe implementar los cuatro.

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>Dispositivos de juego de Bluetooth

Además el mando a distancia Siri estándar que se suministra con el Apple TV, parte 3ª, realizados para iOS dispositivos de juego de Bluetooth (MFI) se pueden emparejar con Apple TV y usar para controlar la aplicación Xamarin.tvOS.

[![](remote-bluetooth-images/game01.png "Dispositivos de juego de Bluetooth")](remote-bluetooth-images/game01.png#lightbox)

Dispositivos de juego se pueden utilizar para mejorar el comportamiento del juego y proporcionar una idea de inmersión en un juego. También puede utilizar para controlar la interfaz de Apple TV estándar, por lo que el uso no tiene que cambiar entre el servidor remoto y el controlador.

> [!IMPORTANT]
> Dispositivos de juego de Bluetooth son una compra opcional que podría provocar que los usuarios finales, la aplicación no puede obligar al usuario a adquirirlo. Si la aplicación es compatible con dispositivos de juego también debe admitir el mando a distancia Siri para que el juego sea utilizable por todos los usuarios de Apple TV.

Un dispositivo de juego tiene las siguientes características y usos previstos dentro de la aplicación tvOS:

|Característica|Uso de aplicación general|Uso de aplicaciones de juegos|
|---|---|---|
|**D-Pad**|Navega por los elementos de interfaz de usuario (cambia el foco).|Depende de juego.|
|**A**|Activa el elemento (enfocado) seleccionado.|Realiza la función del botón primario y confirma las acciones del cuadro de diálogo.|
|**B**|Vuelve a la pantalla anterior o a la pantalla de inicio se cierra si en la pantalla principal de la aplicación.|Realiza la función del botón secundario o vuelve a la pantalla anterior.|
|**X**|Inicia la reproducción de multimedia o pausa o reanuda la reproducción.|Depende de juego.|
|**Y**|N/D|Depende de juego.|
|**Menu**|Vuelve a la pantalla anterior o a la pantalla de inicio se cierra si en la pantalla principal de la aplicación.|Pausar y reanudar juega, vuelve a la pantalla anterior o a la pantalla de inicio se cierra si en la pantalla principal de la aplicación.|
|**Botón de hombro izquierdo**|Navega izquierda.|Depende de juego.|
|**Disparador izquierdo**|Navega izquierda.|Depende de juego.|
|**Botón de hombro derecho**|Navega derecha.|Depende de juego.|
|**Disparador derecho**|Navega derecha|Depende de juego.|
|**Tecla de navegación izquierda**|Navega por los elementos de interfaz de usuario (cambia el foco).|Depende de juego.|
|**Stick analógico derecho**|N/D|Depende de juego.|

Apple proporciona las siguientes sugerencias para trabajar con dispositivos de juego:

- **Confirme las conexiones del dispositivo de juego** -la aplicación tvOS puede iniciar y detener en cualquier momento por el usuario final. Siempre debe comprobar la presencia de un dispositivo de juego en el inicio de aplicación u horas activos y actuar según sea necesario.
- **Asegúrese de la aplicación funciona en el equipo remoto de Siri y dispositivos de juego** -no requieren que los usuarios alternar entre la instancia remota de Siri y un dispositivo de juego para usar la aplicación. Probar la aplicación a menudo con ambos tipos de controladores de garantizar que todo lo que es fácil navegar y funciona según lo previsto.
- **Proporcionar una manera de volver** -al presionar el botón de menú siempre debe volver a la pantalla anterior. Si el usuario está en la pantalla principal de la aplicación, el botón de menú debe devolver a la pantalla Inicio TV de Apple. Durante el juego, el botón de menú debe mostrar una alerta proporcionar al usuario la capacidad de pausar y reanudar el juego o volver al menú principal.

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>Trabajar con dispositivos de juego

Como se mencionó anteriormente, además de la norma Siri remoto que se incluye con Apple TV, el usuario, opcionalmente, puede adjuntar un 3rd party, dispositivos de juego realizados para iOS (MFI) Bluetooth y usarla para controlar la aplicación Xamarin.tvOS.

Si la aplicación requiere proporcionados por el controlador de bajo nivel, puede usar de Apple [el marco de trabajo de juego Controller](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276) que tiene las siguientes modificaciones para tvOS:

- El perfil de dispositivo de juego Micro (`GCMicroGamepad`) se ha agregado a la instancia remota de Siri de destino.
- El nuevo `GCEventViewController` clase puede usarse para enrutar los eventos de dispositivo de juego a través de la aplicación. Consulte la [determinar la entrada de controlador de juego](#Determining-Game-Controller-Input) sección para obtener más detalles.

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>Requisitos de compatibilidad de dispositivo de juego

Apple tiene varios requisitos específicos que deben cumplirse si la aplicación de Xamarin.tvOS es compatible con dispositivos de juego:

- **Debe ser compatible con la instancia remota de Siri** -siempre debe admitir el control remoto Siri. El juego no requiere una entidad 3rd dispositivo de juego para poder reproducirse.
- **Debe admitir el diseño del Control extendido** -tvOS todos los dispositivos de juego son no formfitting extendidos controladores.
- **Juegos deben ser Playable con controladores Stand-Alone** -si la aplicación es compatible con un dispositivo de juego extendidos, debe poder reproducirse solamente con ese dispositivo de juego.
- **Debe utilizar el botón reproducir/pausa** -durante el juego, si el usuario presiona el botón reproducir/pausa, debe mostrar una alerta proporcionar al usuario la capacidad de pausar/reanudar el juego o volver al menú principal.

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>Habilitar la compatibilidad de dispositivo de juego

Para habilitar la compatibilidad de dispositivos de juego en la aplicación Xamarin.tvOS, haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo:

[![](remote-bluetooth-images/game02.png "El editor de Info.plist")](remote-bluetooth-images/game02.png#lightbox)

En el **dispositivos de juego** sección, colocar una marca de verificación por **habilitar dispositivos de juego**, a continuación, compruebe todos los tipos de dispositivos de juego que serán compatibles con la aplicación.

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>Uso de Siri remoto como un dispositivo de juego

El control remoto Siri que vienen con Apple TV puede usarse como un dispositivo de juego limitado. Al igual que otros dispositivos de juego, se presenta en el marco de trabajo del controlador de juego como un `GCController` objeto y es compatible con la `GCMotion` y `GCMicroGamepad` perfiles.

La instancia remota de Siri tiene las siguientes características cuando se usa como un dispositivo de juego:

- La superficie Touch puede usarse como un relleno de D que proporciona datos de entrada analógicas.
- Puede usar el control remoto en orientación horizontal o vertical y la aplicación decide si el objeto de perfil debe voltear automáticamente los datos de entrada.
- Al hacer clic en la superficie de Touch actúa de forma similar a presionar el botón **A** en un dispositivo de juego.
- El botón reproducir/pausa actúa como botón **X** en un dispositivo de juego.
- El botón de menú debe mostrar una alerta proporcionar al usuario la capacidad de pausar y reanudar el juego o volver al menú principal.

<a name="Summary" />

### <a name="determining-game-controller-input"></a>Determinar la entrada de dispositivo de juego

A diferencia de iOS que se pueden recibir eventos de dispositivo de juego en paralelo con los eventos de toque, tvOS procesa todos los eventos de bajo nivel para ofrecer un alto nivel `UIKit` eventos. Como resultado, si necesita acceso a los eventos de dispositivo de juego de bajo niveles, deberá desactivar `UIKit`del comportamiento predeterminado.

En tvOS, cuando desea procesar la entrada de dispositivo de juego directamente debe usar un `GCEventViewController` (o una subclase) para mostrar el juego de la interfaz de usuario. Cada vez que un `GCEventViewController` es el *primera respuesta*, proporcionados por el dispositivo de juego se capturarán y se entregan a la aplicación a través del marco de controlador de juego.

Puede usar el `UserInteractionEnabled` propiedad de la `GCEventViewController` clase para cambiar cómo se procesan y se controlan los eventos.

Para obtener información acerca de cómo implementar la compatibilidad con dispositivos de juego, vea de Apple [trabajar con dispositivos de juego](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html) sección la [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html) y [dispositivos de juego Guía de programación](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html).

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha cubierto el mando a distancia Siri nueva que se incluye con Apple TV, gestos de toque la superficie y botones de Siri remoto. A continuación, lo cubre trabajar con movimientos y guiones gráficos, gestos y código y eventos de bajo nivel. Por último, si ha comentado trabajar con dispositivos de juego.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
