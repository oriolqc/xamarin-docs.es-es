---
title: Notificaciones
ms.topic: article
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 600d651eae1f470f17511afaf9bc86c80ffc1a75
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="notifications"></a>Notificaciones

Ver aplicaciones pueden recibir notificaciones si la aplicación de iOS que contiene las admite. No hay control de notificación integrado, por lo que no *necesita* para agregar la compatibilidad adicional de notificación descrita más adelante, sin embargo si desea personalizar el comportamiento de notificación y, a continuación, lee apariencia en.

Hacer referencia a la [iOS notificaciones](~/ios/platform/user-notifications/deprecated/index.md) doc. para obtener más información sobre cómo agregar compatibilidad con la notificación a la aplicación de iOS en la solución.

## <a name="creating-notification-controllers"></a>Creación de controladores de notificación

En el guión gráfico los controladores de las notificaciones tienen un tipo especial de desplazará tranquilamente desencadenar ellos. Cuando se arrastra un nuevo **controlador de interfaz de notificación** en un guión gráfico automáticamente tendrán un segue adjunto:

![](notifications-images/notification-storyboard1.png "Un nuevo controlador de interfaz de notificación con un segue adjuntada")

Cuando la notificación desplazará tranquilamente está seleccionado puede modificar sus propiedades:

![](notifications-images/notification-storyboard2.png "La notificación desplazará tranquilamente seleccionado")

Después de que ha personalizado el controlador puede parecer a este ejemplo desde la WatchKitCatalog:

![](notifications-images/notifications-segue.png "Las propiedades de notificación")


Hay dos tipos de notificación:

- **Breve vistazo** -no desplazable vista estática definida por el sistema.

- **Larga. consulte** : desplazable, vista personalizable definida por el usuario. Se pueden especificar una versión más sencilla, estática y una versión dinámica más compleja.

### <a name="short-look-notification-controller"></a>Controlador de notificación. Consulte corta

La interfaz de usuario de la apariencia de corta está formada por el icono de aplicación, nombre de la aplicación y la cadena de título de la notificación.

Si el usuario no omite la notificación, el sistema cambiará automáticamente a una notificación de apariencia prolongada que proporciona más información.


### <a name="long-look-notification-controller"></a>Controlador de notificación. Consulte larga

El sistema operativo decide si se debe mostrar la vista estática o dinámica en función de una serie de factores. Debe proporcionar una interfaz estática y las opciones disponibles son también incluye una interfaz dinámica para las notificaciones.

#### <a name="static"></a>Estático

La vista estática debe ser sencillo y rápido mostrar.

![](notifications-images/notification-static.png "La vista estática")

#### <a name="dynamic"></a>Dinámico

La vista dinámica puede mostrar más datos y proporcionar más interactividad.

![](notifications-images/notification-dynamic.png "La vista dinámica")


## <a name="generating-notifications"></a>Generación de notificaciones

Las notificaciones pueden proceder de un servidor remoto ([servicio de notificaciones Push de Apple](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), o APNS) o se pueden generar localmente en la aplicación de iOS.

Hacer referencia a la [iOS notificaciones tutorial](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) para obtener un ejemplo de cómo generar notificaciones locales y el [WatchNotifications ejemplo](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/) para obtener un ejemplo de trabajo.

Notificaciones locales deben tener la `AlertTitle` establecer que se muestre en el de Apple Watch - la `AlertTitle` cadena se muestra en la interfaz de apariencia corta. Tanto el `AlertTitle` y `AlertBody` se muestran en la lista de notificaciones; y la `AlertBody` se muestra en la interfaz de apariencia larga.

Esta captura de pantalla muestra el `AlertTitle` que se muestran en la lista de notificaciones y el `AlertBody` muestra en la interfaz de apariencia larga (mediante el [código de ejemplo](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)):

![](notifications-images/watch-notificationslist-sml.png "Esta captura de pantalla muestra el AlertTitle que se muestra en la lista de notificaciones") ![ ] (notifications-images/watch-notificationcontroller-sml.png "AlertBody la muestra en la interfaz de apariencia larga")

## <a name="testing-notifications"></a>Notificaciones de pruebas

Las notificaciones (locales y remotas) sólo se pueden correctamente probar en un dispositivo, sin embargo, puede simular mediante un **.json** archivo en el simulador de iOS.

### <a name="testing-on-apple-watch"></a>Período de prueba en Apple Watch

Al probar las notificaciones en un Apple Watch, recuerde que [documentación de Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) indica lo siguiente:

> Cuando una de las notificaciones a la aplicación local o remoto llega en iPhone del usuario, iOS decide si debe mostrar esa notificación en el iPhone o en el de Apple Watch.

Esto es alluding al hecho de que iOS decide si aparecerá una notificación en el iPhone o en el reloj. Si el iPhone emparejado está activo cuando se recibe una notificación, la notificación es probable que se muestren en el iPhone y *no* enrutan a la inspección.

Para asegurarse de que aparezca la notificación en el reloj, apagar la pantalla de iPhone (al presionar el botón de encendido una vez) o dejar que entran en suspensión. Si el reloj emparejado esté dentro del alcance, tenga la potencia y se se ha gastado en la muñeca, la notificación se enrutarán existe y aparecen en el reloj (acompañado por un sutiles).

### <a name="testing-on-the-ios-simulator"></a>Período de prueba en el simulador de iOS

Se *debe* proporcionar una carga JSON de prueba al modo de notificación de prueba en el simulador de iOS. Establecer la ruta de acceso en la **personalizado ejecución argumentos** ventana de Visual Studio para Mac.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Visual Studio para Mac mostrará opciones adicionales cuando una extensión de inspección se establece como el **proyecto de inicio**.
Haga doble clic en el proyecto de extensión de inspección y elija **ejecutar con > parámetros personalizados...** :
    
[![](notifications-images/runwith-customparams-sml.png "Ejecución con propiedades personalizadas")](notifications-images/runwith-customparams.png#lightbox)
    
Se abrirá la **ejecución argumentos** ventana que contiene un **WatchKit** ficha. Seleccione **notificación** y proporcionar una carga JSON, a continuación, presione **Execute** para iniciar la aplicación de inspección en el simulador:
    
[![](notifications-images/runwith-execargs-sml.png "Seleccione predeterminado de la carga de notificación")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para establecer la carga de notificación de prueba en el menú contextual de Visual Studio en la extensión de inspección para editar el **propiedades del proyecto**. Vaya a la **depurar** sección y seleccione un archivo JSON de notificaciones en la lista (mostrará automáticamente todos los archivos JSON incluidos en el proyecto).
    
[![](notifications-images/runwith-execargs-sml-vs.png "Seleccione un archivo JSON de notificaciones")](notifications-images/runwith-execargs-vs.png#lightbox)

Cuando la extensión de inspección es la **proyecto de inicio**, Visual Studio mostrará opciones adicionales, tal y como se muestra a continuación. Elija uno de los **notificación** opciones para iniciar la aplicación de inspección **notificación** modo (mediante el archivo JSON seleccionado en la ventana Propiedades):
    
![](notifications-images/runwith-vs.png "El menú del dispositivo")

-----

El controlador de notificación predeterminado aspecto cuando se prueba en el simulador con el archivo de JSON de carga de forma predeterminada:

![](notifications-images/notification-debug-sml.png "Una notificación de ejemplo")

También es posible utilizar el [línea de comandos](~/ios/watchos/troubleshooting.md#command_line) para iniciar el simulador de iOS.

### <a name="example-notification-payload"></a>En el ejemplo se carga de notificaciones

En el [inspección Kit catálogo](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/) ejemplo existe es un ejemplo del archivo de carga JSON **NotificationPayload.json** (incluidos más abajo).

```csharp
{
    "aps": {
        "alert": "Test message content",
        "title": "Optional title",
        "category": "myCategory"
        },

        "WatchKit Simulator Actions": [
        {
            "title": "First Button",
            "identifier": "firstButtonAction"
        }
        ],

        "customKey": "Use this file to define a testing payload for your notifications. The aps dictionary specifies the category, alert text and title. The WatchKit Simulator Actions array can provide info for one or more action buttons in addition to the standard Dismiss button. Any other top level keys are custom payload. If you have multiple such JSON files in your project, you'll be able to choose between them in when selecting to debug the notification interface of your Watch App."
    }
```



## <a name="related-links"></a>Vínculos relacionados

- [WatchNotifications (notificaciones locales) (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)
- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Documentos de inspección Kit notificaciones de Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
