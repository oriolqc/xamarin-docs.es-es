---
title: Demostración de ciclo de vida de la aplicación de Xamarin.iOS
description: Este documento examina varios eventos de ciclo de vida controlados por el delegado de la aplicación en una aplicación de iOS que muestra cuándo y cómo controlan estos eventos.
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/17/2018
ms.openlocfilehash: 3beb511c03b328ecea824bf89355d056df003f3e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102703"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>Demostración de ciclo de vida de la aplicación de Xamarin.iOS

En este artículo y [código de ejemplo](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/) muestra los Estados de cuatro aplicaciones en iOS y el rol de la `AppDelegate` métodos en la notificación a la aplicación de cambios cuando los Estados. La aplicación imprimirá las actualizaciones en la consola cada vez que la aplicación cambia su estado:

[![](application-lifecycle-demo-images/image3-sml.png "La aplicación de ejemplo")](application-lifecycle-demo-images/image3.png#lightbox)

[![](application-lifecycle-demo-images/image4.png "La aplicación imprimirá las actualizaciones en la consola cada vez que la aplicación cambia su estado")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Tutorial

1. Abra el **ciclo de vida** del proyecto en el **LifecycleDemo** solución.
1. Abra el `AppDelegate` clase. Se ha agregado a los métodos del ciclo de vida para indicar cuando la aplicación ha cambiado el estado de registro:

    ```csharp
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
1. Presione el botón de inicio en el simulador o dispositivo para ponerla en segundo plano. `OnResignActivation` y `DidEnterBackground` llamará como las transiciones de la aplicación desde `Active` a `Inactive` y en el `Backgrounded` estado. Puesto que no hay ningún conjunto de código de aplicación para ejecutar en segundo plano, la aplicación se considera _suspendido_ en memoria.
1. Vaya a la aplicación para que vuelva a primer plano. `WillEnterForeground` y `OnActivated` ambos se llamará:

    ![](application-lifecycle-demo-images/image4.png "Cambios de estado se imprimen en la consola")

    La siguiente línea de código en el controlador de vista se ejecuta cuando la aplicación ha entrado en primer plano en segundo plano y cambia el texto mostrado en la pantalla:

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. Presione el **inicio** botón para poner la aplicación en segundo plano. A continuación, puntee el **inicio** botón para que aparezca el selector de la aplicación. En el iPhone X, deslícese hacia arriba desde la parte inferior de la pantalla:

    [![El conmutador de aplicaciones](application-lifecycle-demo-images/app-switcher-sml.png "el conmutador de aplicaciones")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. Busque la aplicación en el selector de la aplicación y deslícese hacia arriba para quitarlo (en iOS 11, presión prolongada hasta que aparezcan los iconos de color rojo en la esquina):

    [![Deslice el dedo hasta quitar una aplicación en ejecución](application-lifecycle-demo-images/app-switcher-swipe-sml.png "pasar el dedo hasta quitar una aplicación en ejecución")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

iOS finalizará la aplicación. Tenga en cuenta que `WillTerminate` no se llama porque la aplicación ya está _suspendido_ en segundo plano.

## <a name="related-links"></a>Vínculos relacionados

- [LifecycleDemo (ejemplo)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
