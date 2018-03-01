---
title: "Configuración e instalación"
description: Configurar para desarrollar para watchOS
ms.topic: article
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/05/2017
ms.openlocfilehash: f7e511d7f0a933ab7f29369e5e5f0aa46607c8f8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="installation"></a>Instalación

watchOS 4 requiere macOS Sierra (10.12) con Xcode 9.

watchOS 1 originalmente requiere OS X Yosemite (10.10) con Xcode 7.

> [!WARNING]
> [no se aceptarán watchOS 1 actualizaciones después del 1 de abril de 2018](https://developer.apple.com/news/?id=11162017a). Las actualizaciones futuras deben usar watchOS 2 SDK o posterior; compilar con la watchOS se recomienda 4 SDK.

## <a name="project-structure"></a>Estructura del proyecto

Una aplicación watch consta de tres proyectos:

- **Proyecto de aplicación de iPhone de Xamarin.iOS** -se trata de un proyecto de iPhone normal, puede ser cualquiera de las plantillas de Xamarin.iOS. La aplicación de inspección y su extensión se incluirá en este proyecto principal.

- **Proyecto de extensión de inspección** -contiene el código (por ejemplo, las clases de controlador) para la aplicación de inspección.

- **Proyecto de aplicación de inspección** -contiene el archivo de guión gráfico de la interfaz de usuario con todos los recursos de la interfaz de usuario para la aplicación de inspección.

El [ejemplo inspección Kit Catalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) solución este aspecto en Xamarin.Studio:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](installation-images/catalog-solution.png "La solución en Visual Studio")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](installation-images/catalog-solution-vs.png "La solución en Visual Studio")

-----

Descargue y ejecute la [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/) ejemplo para empezar a trabajar.
Pantallas de la muestra pueden encontrarse en el [controles](~/ios/watchos/user-interface/index.md) página.


## <a name="creating-a-new-project"></a>Crear un proyecto nuevo

No se puede crear una nueva "inspección solución" … en su lugar puede agregar una aplicación de inspección para una aplicación iOS existente. Siga estos pasos para crear una aplicación de inspección:

1. Si no tienes un proyecto existente, elija primero **archivo > nueva solución** y crear una aplicación de iOS (por ejemplo, un **ver solo la aplicación**):

    [ ![](installation-images/cycle8-2-sml.png "Seleccione Archivo > nueva solución y crear una aplicación de iOS")](installation-images/cycle8-2.png)

2. Una vez creada la aplicación de iOS (o tiene previsto usar la aplicación iOS existente), haga doble clic en la solución y elija **Agregar > Agregar nuevo proyecto...** . En el **nuevo proyecto** ventana Seleccione **watchOS > aplicaciones > WatchKit App**:

    [ ![](installation-images/cycle8-6-sml.png "Seleccione watchOS > aplicaciones > WatchKit App")](installation-images/cycle8-6.png)

3. La siguiente pantalla le permite elegir qué proyecto de aplicación de iOS debe incluir la aplicación de inspección:

    [ ![](installation-images/cycle8-7-sml.png "Elija el proyecto de aplicación de iOS debe incluir la aplicación de inspección")](installation-images/cycle8-7.png)

4. Por último, elija la ubicación para guardar el proyecto (y, opcionalmente, tiene habilitado el control de origen):

    [ ![](installation-images/cycle8-8-sml.png "Elija la ubicación para guardar el proyecto")](installation-images/cycle8-8.png)

5. Visual Studio para Mac configura automáticamente [referencias del proyecto y **Info.plist** configuración](~/ios/watchos/get-started/project-references.md) para usted.

## <a name="creating-the-watch-user-interface"></a>Creación de la interfaz de usuario de inspección

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>Usar el Diseñador de iOS de Xamarin

Haga doble clic en la aplicación de inspección **Interface.storyboard** para editar con el Diseñador de iOS. Puede arrastrar los controladores de interfaz y los controles de interfaz de usuario en el guión gráfico de la **cuadro de herramientas** y configurarlos mediante la **propiedades** panel:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](installation-images/iosdesigner-sml.png "El guión gráfico en el diseñador")](installation-images/iosdesigner.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](installation-images/iosdesigner-sml-vs.png "El guión gráfico en el diseñador")](installation-images/iosdesigner-vs.png)

-----

Debe conceder a cada nuevo controlador de interfaz un **clase** , selecciónela y, a continuación, escriba el nombre de la **propiedades** pad (Esto creará los archivos de código subyacente de C# necesarios automáticamente):

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](installation-images/iosdesigner-classname.png "Proporcionar a cada nuevo controlador de interfaz en una clase")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](installation-images/iosdesigner-classname-vs.png "Proporcionar a cada nuevo controlador de interfaz en una clase")

-----

Crear segues por **Ctrl + arrastrar** desde un controlador de botón, la tabla o la interfaz a otro controlador de interfaz.


### <a name="using-xcode-on-the-mac"></a>Mediante Xcode en el equipo Mac

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Aún puede usar Xcode para crear la interfaz de usuario con el botón secundario en el archivo Interface.storyboard y seleccionando **abrir con > Generador de interfaz de Xcode**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Usuarios de Visual Studio también pueden utilizar Xcode para generar la interfaz de usuario cambiando a usar directamente el Host de compilación de Mac.
Abra la solución en Visual Studio para Mac y, a continuación, haga doble clic en el archivo Interface.storyboard y seleccione **abrir con > Generador de interfaz de Xcode**:

-----

![](installation-images/openwith-xcode.png "Abra el Interface.storyboard en el generador de interfaz de Xcode")

Si usa Xcode, debe seguir los mismos pasos para las aplicaciones de inspección para normal [guiones gráficos de aplicación de iOS](~/ios/user-interface/storyboards/index.md) (como la creación de las salidas y las acciones por **Ctrl + arrastrar** en el **.h**archivo de encabezado).

Al guardar el guión gráfico en el generador de interfaz de Xcode, se agregará automáticamente las salidas y las acciones que se crea en C# **. designer.cs** archivos en el proyecto de extensión de inspección.


### <a name="adding-additional-screens-in-xcode"></a>Agregar pantallas adicionales en Xcode.

Al agregar pantallas adicionales (más allá de lo que se incluye en la plantilla de forma predeterminada) en el guión gráfico con el generador de interfaz de Xcode **debe agregar manualmente los archivos de código de C#** para cada nuevo controlador de interfaz.

Hacer referencia a la [avanzada instrucciones sobre cómo agregar nuevos controladores de interfaz a un guión gráfico](~/ios/watchos/troubleshooting.md#add).

*El Diseñador de iOS de Xamarin hace esto automáticamente, se requiere ningún paso manual.*


## <a name="building"></a>Compilación

Crea un proyecto que incluya una aplicación de inspección al igual que otros proyectos de iOS. El proceso de generación se producirá en una aplicación de iPhone (.app) que contiene una extensión de inspección (.appex), que a su vez contiene la aplicación de inspección de código de menor (.app).


## <a name="launching"></a>Iniciar

Puede iniciar aplicaciones de inspección en el simulador mediante Visual Studio para Mac o en Visual Studio (se inicia en el Host de compilación de Mac).

Hay dos modos para iniciar una aplicación WatchKit:

 - modo de aplicación normal (predeterminado), y
- [Las notificaciones](~/ios/watchos/platform/notifications.md) (lo que requiere una carga de notificación de prueba en formato JSON).

### <a name="xcode-8-support"></a>Compatibilidad con Xcode 8

Una vez instalado Xcode 8 (o posterior), simuladores de Apple Watch son independientes de los simuladores de iOS (a diferencia de [Xcode 6](#xcode6), donde aparecían como un *pantalla externa*).
Al seleccionar el proyecto de aplicación de inspección y asegúrese de el proyecto de inicio, se mostrará la lista del simulador *simuladores de iOS* para elegir (tal y como se muestra a continuación).

[ ![](installation-images/xs-xcode8-watchos3-sml.png "Seleccionar el tipo de simulador")](installation-images/xs-xcode8-watchos3.png)

Al iniciar la depuración, *dos* debe iniciar simuladores: el simulador de iOS *y* el simulador de Apple Watch. Usar **comando + Mayús + H** para navegar hasta la pantalla de menú y de reloj watch; y use la **Hardware** menú para establecer el **Force Touch presión**. Desplazamiento en el panel de seguimiento o el mouse para simular mediante la copa Digital.

#### <a name="troubleshooting"></a>Solución de problemas

Aparecerá el siguiente error en la **salida de la aplicación** si intenta iniciar en un simulador que no tiene una inspección emparejada:

```csharp
error MT0000: Unexpected error - Please file a bug report at http://bugzilla.xamarin.com
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Hacer referencia a [foros de Apple](https://forums.developer.apple.com/thread/7783) para obtener instrucciones acerca de cómo configurar los simuladores, si los valores predeterminados no funcionan.


<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 y watchOS 1

Debe realizar la *proyecto de extensión de inspección* el **proyecto de inicio** antes de ejecutar o depurar la aplicación. No se puede "iniciar" la propia aplicación de inspección y, si elige la aplicación de iOS, a continuación, se iniciará como normal en el simulador de iOS.

De forma predeterminada se inicia una aplicación de la inspección en normal **aplicación** mode (modo de vista no o notificaciones) desde Visual Studio para Mac **ejecutar** o **depurar** comandos.

Cuando se usa Xcode 6, solo el iPhone 5, 5 s iPhone, iPhone 6 y iPhone 6 Plus puede activar la pantalla externa para cada uno **Apple Watch - 38mm** o **Apple Watch - 42mm** dónde estarán las aplicaciones de inspección muestra.

**Nota:** recordar que la pantalla de inspección no aparecen automáticamente en el simulador de iOS cuando se usa Xcode 6.
Use la **Hardware > pantallas externas** menú para mostrar la pantalla de inspección.

<a name="custommodes" />

## <a name="launching-notification-mode"></a>Iniciar modo de notificación

Hacer referencia a la [página notificaciones](~/ios/watchos/platform/notifications.md) para obtener información cómo controlar las notificaciones en el código.


Visual Studio para Mac puede iniciar la aplicación de inspección con una notificación _modos de inicio_ para las notificaciones:



Haga doble clic en el proyecto de aplicación de inspección y elija **ejecutar con > configuración personalizada...** :


[![](installation-images/runwith-customparams-sml.png "Ejecuta una configuración personalizada")](installation-images/runwith-customparams.png)


Se abrirá la **Custom Parameters** ventana donde puede seleccionar **notificación** (y proporcionar una carga JSON), a continuación, presione **ejecutar** para iniciar la aplicación de inspección en el simulador:


[![](installation-images/runwith-execargs-sml.png "Establecer la notificación y la carga")](installation-images/runwith-execargs.png)



## <a name="debugging"></a>Depuración

Se admite la depuración en Visual Studio para Mac y Visual Studio.
Recuerde que debe proporcionar un archivo JSON de notificación durante la depuración en modo de notificaciones. Esta captura de pantalla muestra un punto de interrupción de depuración en una aplicación de inspección de llamadas:

![](installation-images/debug-sml.png "Esta captura de pantalla muestra un punto de interrupción de depuración que se alcanzaron en una aplicación de inspección")

Después de seguir las instrucciones de inicio terminará con su aplicación de inspección que se ejecuta el **iOS Simulator (Watch)**.
Para el modo de notificación puede seleccionar **Depurar > Abrir registro del sistema** (**CMD + /**) y usar `Console.WriteLine` en el código.

### <a name="debugging-lifecycle-event-handlers"></a>Depuración de controladores de eventos de ciclo de vida

<!--
To test the functionality in your  and 
    methods, use the **Hardware > Lock** command in the iOS Simulator.
    Locking will trigger the `DidDeactivate` method and the watch simulator
    will indicate that it has been locked. Swipe the iOS Simulator to unlock,
    which triggers the `WillActivate` method of the watch app.
-->

Los archivos de plantilla watchOS (como `InterfaceController`, `ExtensionDelegate`, `NotificationController`, y `ComplicationController`) vienen con sus métodos de ciclo de vida necesarios ya implementados. Agregar `Console.WriteLine` llamadas y leer el **salida de la aplicación** para comprender mejor el ciclo de vida de eventos.



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Primer vídeo de aplicación de inspección](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Sugerencias de WatchKit de Apple](https://developer.apple.com/watchkit/tips/)
