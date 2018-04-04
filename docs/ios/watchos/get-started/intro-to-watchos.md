---
title: Introducción a watchOS
description: Información general de la estructura de la solución watchOS y limitaciones
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 0a0adbab54fc134eaf2e69cc8088713e54b15d3b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-watchos"></a>Introducción a watchOS

> [!NOTE]
> Extraer del repositorio el [Introducción a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) para obtener información general de las características más recientes.

## <a name="about-watchos"></a>Acerca de watchOS

Una solución de aplicación watchOS tiene 3 proyectos:

- **Ver la extensión** : un proyecto que contiene el código de la aplicación de inspección.
- **Ver aplicación** : contiene el guión gráfico de la interfaz de usuario y los recursos.
- **iOS aplicación primaria** : esta aplicación es una aplicación de iPhone normal. La aplicación de inspección y la extensión están agrupadas en la aplicación de iPhone para su entrega a la inspección del usuario.

En las aplicaciones watchOS 1, se ejecuta el código de la extensión en el iPhone: el Apple Watch es efectivamente una pantalla externa. aplicaciones de watchOS 2 y 3 se ejecutan por completo en el de Apple Watch. En el diagrama siguiente se muestra esta diferencia:

[ ![](intro-to-watchos-images/arch-sml.png "En este diagrama se muestra la diferencia entre watchOS 1 y watchOS 2 (y versiones posteriores)")](intro-to-watchos-images/arch.png#lightbox)

Independientemente de qué versión de watchOS se destina, en Visual Studio para panel del Mac de solución de una solución completa tendrá un aspecto similar al siguiente:

[![](intro-to-watchos-images/projectstructure-sml.png "El panel de la solución")](intro-to-watchos-images/projectstructure.png#lightbox)

El *aplicación primaria* en un watchOS solución es una aplicación de iOS regular. Este es el proyecto en la solución que sea visible solo **en el teléfono**. Casos de uso de esta aplicación incluye tutoriales, pantallas administrativas y filtrado, cacheing, etcetera de nivel intermedio. Sin embargo, es posible que el usuario instalar y ejecutar la aplicación o extensión de inspección sin **alguna vez** tener abierta la aplicación primaria, por lo que si necesita la aplicación principal pueden ejecutarse durante la inicialización única o administración, debe programar el reloj / extensión de la aplicación para indicar al usuario que.

Aunque la aplicación principal ofrece la aplicación de inspección y la extensión, se ejecutan en diferentes espacios aislados.

En watchOS 1 pueden compartir datos a través de un grupo de aplicación compartido o a través de la función estática `WKInterfaceController.OpenParentApplication`, que se desencadenará la `UIApplicationDelegate.HandleWatchKitExtensionRequest` método en la aplicación principal `AppDelegate` (consulte [trabajar con la aplicación primaria](~/ios/watchos/app-fundamentals/parent-app.md)).

En watchOS, 2 o posterior, el marco de trabajo de conectividad de inspección se utiliza para comunicarse con la aplicación primaria, con el `WCSession` clase.

## <a name="application-lifecycle"></a>Ciclo de vida de la aplicación

En la extensión de inspección, una subclase de la `WKInterfaceController` se crea una clase para cada escena de guión gráfico.

Estos `WKInterfaceController` clases son análogas a los `UIViewController` objetos en la programación de iOS, pero no tiene el mismo nivel de acceso a la vista.
Por ejemplo, no se puede agregar controles a dinámicamente o reestructurar la interfaz de usuario.
Puede, sin embargo, ocultar y mostrar controles y, con algunos controles, cambiar su tamaño, transparencia y las opciones de apariencia.

El ciclo de vida de un `WKInterfaceController` objeto implica las siguientes llamadas:

- [Activo](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.Awake/) : debe realizar la mayor parte de la inicialización en este método.
- [WillActivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.WillActivate/) : llama poco antes de que la aplicación de inspección aparece ante el usuario. Utilice este método para realizar la inicialización de último momento, animaciones, etcetera de inicio.
- En este momento, aparece la aplicación de inspección y la extensión empieza a responder a usuario de entrada y actualizar la presentación de la aplicación de inspección por la lógica de aplicación.
- [DidDeactivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.DidDeactivate/) después de la aplicación de inspección se han descartado por el usuario, se llama a este método. Después de que devuelve este método, no se puede modificar los controles de interfaz de usuario hasta la próxima vez `WillActivate` se llama. Este método también se llamará si se interrumpe la conexión con el iPhone.
- Después de que se ha desactivado la extensión, no es accesible para el programa. Las funciones asincrónicas pendientes **no** llamarse. Las extensiones del Kit de inspección no puede usar modos de procesamiento en segundo plano. Si se vuelve a activar el programa por el usuario, pero la aplicación no se terminó con el sistema operativo, será el primer método llamado `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Información general sobre el ciclo de vida de aplicaciones")

## <a name="types-of-user-interface"></a>Tipos de interfaz de usuario

Hay tres tipos de interacción con que el usuario pueda tener con la aplicación de inspección.
Todos se programan mediante subclases personalizados de `WKInterfaceController`, por lo que la secuencia de ciclo de vida descritas anteriormente se aplica universalmente (las notificaciones se programan con subclases de `WKUserNotificationController`, que a su vez es una subclase de `WKInterfaceController`):

### <a name="normal-interaction"></a>Interacción normal

La mayoría de interacción de extensión de la aplicación/inspección serán con subclases de `WKInterfaceController` creado por usted para que correspondan a las escenas de la aplicación de inspección **Interface.storyboard**. Este tema se trata con detalle en la [instalación](~/ios/watchos/get-started/installation.md) y [Introducción](~/ios/watchos/get-started/index.md) artículos.
La siguiente imagen muestra una parte de la [inspección Kit catálogo](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) guión gráfico del ejemplo. Para cada escena que aquí se ha explicado, hay un personalizado correspondiente `WKInterfaceController` (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`, etc.) en el proyecto de extensión.

![](intro-to-watchos-images/scenes.png "Ejemplos de interacción de normal")

### <a name="notifications"></a>Notificaciones

[Notificaciones de](~/ios/watchos/platform/notifications.md) son un caso de uso principal para el Apple Watch. Se admiten las notificaciones locales y remotas. Interacción con las notificaciones se produce en dos fases, denominados aspecto corta y larga.

Parece corta se muestran brevemente y mostrar el icono de la aplicación de inspección, su nombre y el título (según se haya especificado con `WKInterfaceController.SetTitle`).

El aspecto largo combina proporcionada por el sistema **franja** área y el botón Descartar con contenido personalizado basado en el guión gráfico.

`WKUserNotificationInterfaceController` extiende `WKInterfaceController` con los métodos `DidReceiveLocalNotification` y `DidReceiveRemoteNotification`.
Invalidar estos métodos para responder a eventos de notificación.

Para obtener más información sobre el diseño de la interfaz de usuario de notificación, consulte el [directrices de interfaz humana de Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Notificaciones de ejemplo")

## <a name="screen-sizes"></a>Tamaños de pantalla

El Apple Watch tiene dos tamaños de fuente: 38 y 42mm, con una proporción de presentación de 5:4 y una pantalla Retina display. Sus tamaños utilizables son:

- píxeles lógicos 38 136: mm x 170 (272 x 340 píxeles físicos)
- 42 156: mm x 195 lógico píxeles (312 x 390 píxeles físicos).

Utilice `WKInterfaceDevice.ScreenBounds` para determinar en qué pantalla está ejecutando la aplicación de inspección.

Por lo general, resulta más fácil de desarrollar el diseño de texto y el diseño con la presentación de 38mm más restringido y, a continuación, escalar.
Si empieza con el entorno más grande, ajuste de escala hacia abajo puede dar lugar a superposición desagradable o un truncamiento de texto.

Obtenga más información sobre [trabajar con tamaños de pantalla](~/ios/watchos/app-fundamentals/screen-sizes.md).


## <a name="limitations-of-watchos"></a>Limitaciones de watchOS

Existen algunas limitaciones de watchOS a tener en cuenta al desarrollar aplicaciones de watchOS:

- Dispositivos de Apple Watch tienen un almacenamiento limitado: tenga en cuenta el espacio disponible antes de descargar archivos de gran tamaño (p. ej. archivos de audio o película).

- Muchos watchOS [controles](~/ios/watchos/user-interface/index.md) tiene análogos en UIKit, pero varias clases (`WKInterfaceButton` en lugar de `UIButton`, `WKInterfaceSwitch` para `UISwitch`, etc.) y tiene un conjunto limitado de métodos en comparación con su UIKit equivalentes. Además, watchOS tiene algunos controles, como `WKInterfaceDate` (para mostrar una fecha y hora) no tiene que UIKit.

  - No se puede enrutar las notificaciones para la inspección solo o iPhone únicamente (no se ha anunciado el tipo de control que el usuario tiene sobre el enrutamiento por Apple).

Algunas otras limitaciones conocidas / preguntas más frecuentes:

- Apple no permitirá 3rd terceros personalizado inspección caras.

- Las API que permiten la inspección controlar iTunes en el teléfono conectado son privadas.


## <a name="further-reading"></a>Información adicional

Consulte la documentación de Apple:

* [Desarrollar para Kit de inspección](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

* [Vea la Guía de programación de Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

* [Directrices de interfaz humana de Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>Vínculos relacionados

- [watchOS 3 catálogo (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [watchOS 1 catálogo (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [El programa de instalación e instalar](~/ios/watchos/get-started/installation.md)
- [Primer vídeo de aplicación de inspección](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Del desarrollo de Apple para guía del Kit de inspección](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Sugerencias de WatchKit de Apple](https://developer.apple.com/watchkit/tips/)
- [Introducción a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
