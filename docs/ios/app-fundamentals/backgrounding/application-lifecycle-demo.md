---
title: Demostración de ciclo de vida de la aplicación
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: f03cd7d74d09fc40b88cfbf44bf54c6da5f4c149
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="application-lifecycle-demo"></a>Demostración de ciclo de vida de la aplicación

En esta sección, vamos a examinar una aplicación que muestra los cuatro estados de aplicación y el rol de la `AppDelegate` métodos de notificación de la aplicación de cambios cuando los Estados. La aplicación imprimirá actualizaciones en la consola cuando la aplicación cambia el estado:

 [![](application-lifecycle-demo-images/image3.png "La aplicación de ejemplo")](application-lifecycle-demo-images/image3.png#lightbox)

 [![](application-lifecycle-demo-images/image4.png "La aplicación imprimirá las actualizaciones en la consola cuando la aplicación cambia el estado")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Tutorial


  1. Abra la _ciclo de vida de_ del proyecto en el _LifecycleDemo_ solución.
  1. Abra la `AppDelegate` clase. Tenga en cuenta que hemos agregado el registro a los métodos del ciclo de vida para hacernos saber cuando la aplicación ha cambiado de estado:

            ```chsarp
                public override void OnActivated(UIApplication application)
                {
                    Console.WriteLine("OnActivated called, App is active.");
                }
                public override void WillEnterForeground(UIApplication application)
                {
                    Console.WriteLine("App will enter foreground");
                }
                public override void OnResignActivation(UIApplication application)
                {
                    Console.WriteLine("OnResignActivation called, App moving to inactive state.");
                }
                public override void DidEnterBackground(UIApplication application)
                {
                    Console.WriteLine("App entering background state.");
                }
                // not guaranteed that this will run
                public override void WillTerminate(UIApplication application)
                {
                    Console.WriteLine("App is terminating.");
                }
            ```

  1. Inicie la aplicación en el simulador o en el dispositivo. `OnActivated` se llamará cuando se inicia la aplicación. La aplicación está ahora en el _Active_ estado.
  1. Pulse el botón de inicio en el simulador o el dispositivo para poner la aplicación en segundo plano. `OnResignActivation` y `DidEnterBackground` llamará como las transiciones de la aplicación desde `Active` a `Inactive` y en el `Backgrounded` estado. Puesto que no le hemos dado a nuestra aplicación cualquier código para ejecutar en segundo plano, la aplicación se considera _Suspended_ en la memoria.
  1. Navegue a la aplicación para poner en primer plano. `WillEnterForeground` y `OnActivated` ambos se llamará:

        ![](application-lifecycle-demo-images/image4.png "Cambios de estado se imprimen en la consola")

    Tenga en cuenta que agregamos la siguiente línea de código a nuestro controlador de vista que se nos informaba de que la aplicación ha entrado en el primer plano del fondo:

        ```csharp
            UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
                    label.Text = "Welcome back!";
                });
        ```

1. Presione el **inicio** botón para poner la aplicación en segundo plano. A continuación, puntee el **inicio** botón para abrir el selector de la aplicación:
    
    ![](application-lifecycle-demo-images/app-switcher-.png "El conmutador de aplicaciones")
  
1. Ponga la aplicación en el selector de la aplicación y deslícese hacia arriba para quitar el equipo:
    
    ![](application-lifecycle-demo-images/app-switcher-swipe-.png "Deslice el dedo hasta quitar una aplicación en ejecución") 
    
iOS cerrará la aplicación. Tenga en cuenta que `WillTerminate` no se llama porque se está finalizando una aplicación que está _Suspended_ en segundo plano.

Ahora que sabemos iOS aplicación Estados y transiciones, echaremos un vistazo en las distintas opciones disponibles para backgrounding en iOS.



## <a name="related-links"></a>Vínculos relacionados

- [LifecycleDemo(Part2) (ejemplo)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
