---
title: Siri Remote y Bluetooth controladores para tvOS en Xamarin
description: En este artículo se describe cómo trabajar con el de Siri Remote y Bluetooth de juego en las aplicaciones de tvOS escritas con Xamarin.
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 79022f7a454ea423fa3112a4c4ade2bcd471fbb8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60933068"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>Siri Remote y Bluetooth controladores para tvOS en Xamarin

Los usuarios de la aplicación Xamarin.tvOS no interactúa con su interfaz directamente como con iOS donde al pulsar imágenes en la pantalla del dispositivo, pero indirectamente desde a través de la sala utilizando el [Siri Remote](#The-Siri-Remote).

Si la aplicación es un juego, puede, opcionalmente, crear en la compatibilidad de parte 3ª, realizados para iOS (MFI) [Bluetooth de juego](#Bluetooth-Game-Controllers) en la aplicación también.

[![](remote-bluetooth-images/intro01.png "El Bluetooth remoto y el dispositivo de juego")](remote-bluetooth-images/intro01.png#lightbox)

Este artículo se describe la [Siri Remote](#The-Siri-Remote), [Touch superficie gestos](#Touch-Surface-Gestures) y [Siri remoto botones](#Siri-Remote-Buttons) y muestra cómo trabajar con ellos a través de [gestos y Guiones gráficos](#Gestures-and-Storyboards), [gestos y código](#Gestures-and-Code) y [control de eventos de bajo nivel](#Low-Level-Event-Handling). Por último, describe [trabajar con dispositivos de juego](#Working-with-Game-Controllers) en una aplicación Xamarin.tvOS.

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>El control remoto de Siri

La principal manera que interactúa con Apple TV y la aplicación Xamarin.tvOS, los usuarios es a través de Siri remoto incluido. Apple ha diseñado el control remoto para salvar la distancia entre el usuario que se encuentra en el sofá y muestra el espacio en la pantalla de TV a través de la interfaz de usuario de Apple TV.

El desafío como desarrollador de la aplicación de tvOS es la interfaz de usuario rápida, fácil de usar y visualmente atractivo que aprovecha la superficie de interacción de Siri Remote, acelerómetro, giroscopio y botones de crear.

[![](remote-bluetooth-images/remote01.png "The Siri Remote")](remote-bluetooth-images/remote01.png#lightbox)

El control remoto de Siri tiene las siguientes características y usos previstos dentro de la aplicación tvOS:

|Característica|Uso de la aplicación general|Uso de las aplicaciones de juegos|
|---|---|---|
|**Superficie de interacción**<br />Deslice el dedo para navegar, presione para seleccionar y se mantiene en los menús contextuales.|**Tap/Swipe**<br />Navegación de la interfaz de usuario entre los elementos de recibir el foco.<br /><br />**Haga clic en**<br />Activa el elemento (enfocado) seleccionado.|**Tap/Swipe**<br />Depende de diseño de juegos y puede usarse como un panel D pulsando en los bordes.<br /><br />**Haga clic en**<br />Ejecutar la función del botón primario.|
|**Menu**<br />Presione para volver a la pantalla anterior o el menú.|Devuelve a la pantalla anterior y se cierra como Apple TV Home screen en la pantalla de la aplicación principal.|Pausar y reanudar el juego, vuelve a la pantalla anterior y se cierra como Apple TV Home screen en la pantalla de la aplicación principal.|
|**Siri/Search**<br />En países con Siri, mantenga presionado para el control de voz, en todos los demás países, muestra la pantalla de búsqueda.|N/D|N/D|
|**Reproducir/pausa**<br />Reproducir y pausar multimedia o proporciona una función secundaria en las aplicaciones.|Inicia la reproducción de medios y pausar y reanudar la reproducción.|Realiza la función del botón secundario o se omite el vídeo de introducción (si existe).|
|**Página principal**<br />Presione para volver a la pantalla Inicio, haga doble clic para mostrar la ejecución de aplicaciones, presione y mantenga presionado para dispositivos de modo de suspensión.|N/D|N/D|
|**Volumen**<br />Controles asociados volumen equipamiento de audio y vídeo.|N/D|N/D|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>Superficies gestos de toque

Superficie de interacción de Siri Remote es capaz de detectar una variedad de movimientos con un solo dedo que se pueden responder en la aplicación Xamarin.tvOS:

|Deslizar rápidamente|Haga clic|Pulsar|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|Mueve la selección (foco) entre los elementos de interfaz de usuario en la pantalla (hacia arriba, abajo a la izquierda, derecha). Gesto de deslizar rápidamente puede utilizarse para desplazarse por las listas grandes de contenido rápidamente con la inercia.|Activa el elemento seleccionado de (enfocado) o actúa como el botón primario en un juego. Pueden activar haciendo clic en y que contiene los menús contextuales o las funciones secundarias.|A la ligera al puntear en la superficie de interacción de los bordes actúa como direccionales botones en un panel D, mover el foco hacia arriba, abajo, izquierda o derecha según el área pulsado. Dependiendo de la aplicación, puede utilizarse para mostrar los controles ocultos.|

Apple proporciona las siguientes sugerencias para trabajar con gestos de toque la superficie:

* **Diferenciar entre los clics y derivaciones de** -haciendo clic es una acción intencionada por el usuario y es ideal para la selección, activación y el botón primario de un juego. Puntear es más sutil y debe usarse con moderación porque el usuario a menudo mantiene el control remoto de Siri en sus manos y accidentalmente puede activar un evento Tap fácilmente.
* **No volver a definir gestos estándar** -el usuario tiene una expectativa de que los gestos específicos llevará a cabo acciones específicas, no debe volver a definir el significado o la función de estos movimientos de la aplicación. La única excepción es una aplicación de juego durante el juego activo.
* **Definir nuevos gestos moderación** -nuevo, el usuario tiene una expectativa de que los gestos específicos llevará a cabo acciones específicas. Debe evitar definir gestos personalizados para llevar a cabo acciones estándar. Y, nuevamente, juegos son la excepción más habitual donde gestos personalizados pueden agregar las play divertida y envolvente para el juego.
* **Si es necesario, responder a Cruceta pulsa** : ligeramente pulsar en la esquina de bordes de la superficie de interacción se reaccionar como un control de dirección en un enfoque móvil del dispositivo de juego o una dirección hacia arriba, abajo, izquierda o derecha. Si es necesario, se debe responder a estos gestos en su aplicación o juego.

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Botones de Siri Remote

Además de gestos en la superficie de interacción, la aplicación puede responder al usuario hacer clic en la superficie de interacción o al presionar el botón Reproducir/pausar. Si tiene acceso remoto Siri con el marco de controlador de juego, también puede detectar se presiona el botón de menú. 

Además, se pueden detectar pulsaciones de botones de menú con un reconocedor de movimiento estándar `UIKit` elementos. Si interceptar se presiona el botón de menú, deberá ser responsable de cerrar la vista actual y el controlador de vista y volver al anterior.

> [!IMPORTANT]
> Debería **siempre** asignar una función al botón Reproducir/pausar en el equipo remoto. Tener un botón que no es funcional puede hacer que la aplicación buscar roto al usuario final. Si no tiene una función válida para este botón, asigne la misma función que el botón primario (Touch superficie Click).

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>Los gestos y guiones gráficos

La manera más fácil trabajar con el control remoto en su aplicación Xamarin.tvOS Siri es agregar los reconocedores de gestos para sus vistas en el Diseñador de la interfaz.

Para agregar un reconocedor de gestos, realice lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo en el Diseñador de la interfaz.
2. Arrastre un **pulse reconocedor de gestos** desde el **biblioteca** y colóquela en la vista: 

    [![](remote-bluetooth-images/storyboard01.png "Un reconocedor de movimiento de Tap")](remote-bluetooth-images/storyboard01.png#lightbox)
3. Comprobar **seleccione** en el **botón** sección de la **Inspector de atributos**: 

    [![](remote-bluetooth-images/storyboard02.png "Compruebe la selección")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **Seleccione** significa que responderá el gesto para el usuario hace clic en el **Touch superficie** en el equipo remoto Siri. También tiene la opción de responder a la **menú**, **Reproducir/pausar**, **seguridad**, **abajo**, **izquierda** y **Derecha** botones.
5. A continuación, conectar un **acción** desde el **pulse reconocedor de gestos** y llámelo `TouchSurfaceClicked`: 

    [![](remote-bluetooth-images/storyboard03.png "Una acción desde el reconocedor de gestos de Tap")](remote-bluetooth-images/storyboard03.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac.

Editar el controlador de vista (ejemplo `FirstViewController.cs`) y agregue el código siguiente para controlar el gesto que se desencadena:

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

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). En concreto la [creación de la interfaz de usuario](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface) y [escribiendo el código con las salidas y acciones](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) secciones.

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>Los gestos y el código

Si lo desea, puede crear los gestos directamente en C# de código y agregarlos a las vistas de la interfaz de usuario. Por ejemplo, para agregar una serie de los reconocedores de gestos de pasar el dedo, edite el controlador de vista y agregue el código siguiente:

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

Si va a crear un tipo personalizado basado en `UIKit` en su aplicación Xamarin.tvOS (por ejemplo `UIView`), también tiene la capacidad para proporcionar control de bajo nivel de presionar el botón a través de `UIPress` eventos. 

Un `UIPress` eventos consiste en tvOS lo que un `UITouch` eventos consiste en iOS, excepto `UIPress` devuelve información sobre el botón se presiona en el control remoto de Siri o de otro conectado los dispositivos Bluetooth (por ejemplo, un dispositivo de juego). `UIPress` describen los eventos se presiona el botón y su estado (Began, cancelado, cambiado o terminado). 

Para los botones analógicos en dispositivos como dispositivos de juego de Bluetooth, `UIPress` también devuelve la cantidad de fuerza que se aplica al botón. El `Type` propiedad de la `UIPress` evento define qué botón físico ha cambiado estado, mientras que el resto de las propiedades describen el cambio que se ha producido.

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

Igual que con `UITouch` eventos, si necesita implementar cualquiera de los `UIPress` invalidaciones de eventos, debe implementar los cuatro.

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>Juego de Bluetooth

Además remoto Siri estándar que se suministra con el Apple TV, 3ª parte, realizados para iOS (MFI) Bluetooth juego puede se empareja con Apple TV y usa para controlar la aplicación Xamarin.tvOS.

[![](remote-bluetooth-images/game01.png "Juego de Bluetooth")](remote-bluetooth-images/game01.png#lightbox)

Dispositivos de juego pueden utilizarse para mejorar el juego y proporcionar una idea de inmersión en un juego. También puede usarse para controlar la interfaz de Apple TV estándar, por lo que el uso no tiene que cambiar entre el servidor remoto y el controlador.

> [!IMPORTANT]
> Juego de Bluetooth son una adquisición opcional que podrían hacer que los usuarios finales, la aplicación no puede obligar al usuario a comprar uno. Si la aplicación es compatible con dispositivos de juego también debe admitir el control remoto de Siri para que el juego sea utilizable por todos los usuarios de Apple TV.

Un dispositivo de juego tiene las siguientes características y usos previstos dentro de la aplicación tvOS:

|Característica|Uso de la aplicación general|Uso de las aplicaciones de juegos|
|---|---|---|
|**D-Pad**|Se desplaza por elementos de interfaz de usuario (cambia el foco).|Depende de juego.|
|**A**|Activa el elemento (enfocado) seleccionado.|Realiza la función del botón primario y confirma las acciones del cuadro de diálogo.|
|**B**|Vuelve a la pantalla anterior o sale a la pantalla de inicio si se encuentra en la pantalla principal de la aplicación.|Realiza la función del botón secundario o vuelve a la pantalla anterior.|
|**X**|Inicia la reproducción multimedia o en pausa o reanuda la reproducción.|Depende de juego.|
|**Y**|N/D|Depende de juego.|
|**Menu**|Vuelve a la pantalla anterior o sale a la pantalla de inicio si se encuentra en la pantalla principal de la aplicación.|Pausar y reanudar juego, vuelve a la pantalla anterior o se cierra a la pantalla de inicio si se encuentra en la pantalla principal de la aplicación.|
|**Botón izquierdo del hombro**|Navega a izquierda.|Depende de juego.|
|**Desencadenador izquierdo**|Navega a izquierda.|Depende de juego.|
|**Botón hombro derecho**|Navega derecha.|Depende de juego.|
|**Desencadenador correcto**|Navega derecha|Depende de juego.|
|**Tecla de navegación izquierda**|Se desplaza por elementos de interfaz de usuario (cambia el foco).|Depende de juego.|
|**Tecla de navegación derecha**|N/D|Depende de juego.|

Apple proporciona las siguientes sugerencias para trabajar con dispositivos de juego:

- **Confirme las conexiones del dispositivo de juego** -la aplicación tvOS puede iniciar y detener en cualquier momento por el usuario final. Siempre debe comprobar la presencia de un controlador de juego al iniciarse la aplicación ni tiempos de activos y actuar según sea necesario.
- **Asegúrese de su aplicación funciona en Siri Remote y dispositivos de juego** -no requieren que los usuarios alternar entre la Siri Remote y un dispositivo de juego para utilizar la aplicación. Pruebe la aplicación a menudo con ambos tipos de controladores al garantizar que todo lo que es fácil de navegar y funciona según lo previsto.
- **Proporcionar una manera de volver** -al presionar el botón de menú siempre debe devolver a la pantalla anterior. Si el usuario está en la pantalla de la aplicación principal, el botón de menú debe devolver a la pantalla Inicio TV de Apple. Durante el juego, el botón de menú debe mostrar una alerta de proporcionar al usuario la capacidad de pausar y reanudar el juego o volver al menú principal.

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>Trabajar con dispositivos de juego

Como se indicó anteriormente, además de la norma Siri Remote que se incluye con Apple TV, el usuario puede adjuntar un 3rd party, realizados para dispositivos de juego de iOS (MFI) Bluetooth y usarla para controlar la aplicación Xamarin.tvOS.

Si la aplicación requiere la entrada del controlador de bajo nivel, puede usa Apple [Game Controller Framework](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276) que tiene las siguientes modificaciones para tvOS:

- El perfil de dispositivo de juego Micro (`GCMicroGamepad`) se ha agregado a Siri remoto de destino.
- El nuevo `GCEventViewController` clase puede usarse para enrutar los eventos de dispositivo de juego a través de la aplicación. Consulte la [determinar Game Controller entrada](#determining-game-controller-input) sección para obtener más detalles.

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>Requisitos de compatibilidad de dispositivo de juego

Apple tiene varios requisitos específicos que deben cumplirse si su aplicación Xamarin.tvOS es compatible con dispositivos de juego:

- **Debe ser compatible con el control remoto de Siri** -siempre debe admitir el control remoto de Siri. El juego no puede requerir una parte 3ª juego para poder reproducirse.
- **Debe admitir el diseño del Control extendido** -tvOS todos los dispositivos de juego son no formfitting extendidos controladores.
- **Juegos deben ser Playable con controladores Stand-Alone** -si la aplicación es compatible con un dispositivo de juego extendidos, debe poder reproducirse únicamente con ese controlador de juego.
- **Debe admitir el botón Reproducir/pausar** -durante el juego, si el usuario presiona el botón Reproducir, pausar, debe mostrar una alerta de proporcionar al usuario la capacidad de pausar o reanudar el juego o volver al menú principal.

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>Habilitar la compatibilidad del dispositivo de juego

Para habilitar la compatibilidad de dispositivos de juego en la aplicación Xamarin.tvOS, haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo:

[![](remote-bluetooth-images/game02.png "El editor Info.plist")](remote-bluetooth-images/game02.png#lightbox)

En el **Game Controller** sección, coloque una marca de verificación por **habilitar dispositivos de juego**, a continuación, compruebe todos los tipos de dispositivos de juego que será compatible con la aplicación.

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>Uso de Siri remoto como un dispositivo de juego

El control remoto de Siri que vienen con Apple TV puede usarse como un dispositivo de juego limitado. Al igual que otros dispositivos de juego, se muestra en el marco de controlador de juego como un `GCController` objeto y es compatible con la `GCMotion` y `GCMicroGamepad` perfiles.

El control remoto de Siri tiene las siguientes características cuando se usa como un dispositivo de juego:

- La superficie de interacción puede usarse como un control que proporciona datos de entrada analógicas.
- Se puede usar el control remoto en orientación horizontal o vertical y la aplicación decide si el objeto de perfil debe voltear automáticamente los datos de entrada.
- Al hacer clic en la superficie de interacción actúa como presionar el botón **A** en un controlador de juego.
- El botón Reproducir/pausar actúa como botón **X** en un controlador de juego.
- El botón de menú debe mostrar una alerta de proporcionar al usuario la capacidad de pausar y reanudar el juego o volver al menú principal.

<a name="Summary" />

### <a name="determining-game-controller-input"></a>Determinar la entrada de dispositivo de juego

A diferencia de que se pueden recibir eventos de dispositivo de juego en paralelo con los eventos de toque de iOS, tvOS procesa todos los eventos de bajo nivel para ofrecer un alto nivel `UIKit` eventos. Como resultado, si necesita acceso a los eventos de dispositivo de juego de bajo niveles, deberá desactivar `UIKit`del comportamiento predeterminado.

En tvOS, cuando desea procesar la entrada de dispositivo de juego directamente debe usar un `GCEventViewController` (o una subclase) para mostrar el juego de la interfaz de usuario. Cada vez que un `GCEventViewController` es el *Respondedor primera*, entrada de dispositivo de juego se capturarán y entregar a la aplicación a través del marco de controlador de juego.

Puede usar el `UserInteractionEnabled` propiedad de la `GCEventViewController` clase para cambiar cómo se procesan y se controlan los eventos.

Para obtener información acerca de cómo implementar la compatibilidad con dispositivos de juego, vea Apple [trabajar con dispositivos de juego](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html) sección la [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html) y [dispositivos de juego Guía de programación](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html).

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha cubierto nuevo Siri Remote que se incluye con Apple TV, gestos de toque la superficie y los botones de Siri Remote. A continuación, se ha descrito cómo trabajar con gestos y los guiones gráficos, gestos y código y eventos de bajo nivel. Por último, si se trata de trabajar con dispositivos de juego.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
