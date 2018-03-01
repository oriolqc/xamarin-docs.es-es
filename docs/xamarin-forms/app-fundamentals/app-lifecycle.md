---
title: "Ciclo de vida de aplicación"
description: "Cómo responder a las del ciclo de vida de la aplicación"
ms.topic: article
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: c374f261677a5bddc4ea9c73c066b69580ceedd5
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="app-lifecycle"></a>Ciclo de vida de aplicación

La `Application` clase base ofrece las siguientes características:

* [Métodos de ciclo de vida](#Lifecycle_Methods) `OnStart`, `OnSleep`, y `OnResume`.
* [Eventos de navegación modal](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping`, y `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

La `Application` clase contiene tres métodos virtuales que se pueden invalidar para controlar los métodos del ciclo de vida:

* **OnStart** -llama cuando se inicia la aplicación.

* **OnSleep** -llama cada vez que la aplicación entra en segundo plano.

* **OnResume** -se le llama cuando se reanuda la aplicación, después de que se va a enviar al fondo.

Tenga en cuenta que hay *sin* método de finalización de la aplicación.
En circunstancias normales (ie. no es un bloqueo) se realizará la finalización de la aplicación desde el *OnSleep* estado, sin ninguna notificación adicional en el código.

Para observar cuando se llama a estos métodos, implementar un `WriteLine` llamar en cada uno de ellos (como se muestra a continuación) y realiza una prueba en cada plataforma.

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

Al actualizar *anterior* Xamarin.Forms aplicaciones (p. ej. crear con la versión 1.3 Xamarin.Forms o anterior), asegúrese de que incluye la actividad principal Android `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` en el `[Activity()]` atributo. Si no está presente, se observará los `OnStart` método se invoca en rotación, así como cuando la aplicación se inicia por primera vez. Este atributo se incluye automáticamente en las plantillas de aplicación de Xamarin.Forms actuales.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventos de navegación modal

Hay cuatro nuevos eventos en el `Application` clase Xamarin.Forms 1.4, cada uno con sus propios argumentos de evento:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - la `ModalPoppingEventArgs` clase contiene un `Cancel` propiedad. Cuando `Cancel` está establecido en `true` se cancela el emergente modal.
* **ModalPopped** - `ModalPoppedEventArgs`

Estos eventos le ayudará a administrar mejor el ciclo de vida de la aplicación, permitiendo responde a modales páginas que se muestran y se descartará.

> [!NOTE]
> Implementar los métodos del ciclo de vida de aplicación y eventos de navegación modal, todos los anteriores`Application` métodos de creación de una aplicación de Xamarin.Forms (ie. las aplicaciones escritas en la versión 1.2 o anterior que utilizan una variable static `GetMainPage` método) se han actualizado para crear un valor predeterminado `Application` que se establece como el elemento primario de `MainPage`.
>
> Las aplicaciones de Xamarin.Forms que utilizan este comportamiento heredado deben actualizarse a una `Application` subclase tal como se describe en el [clase Application](~/xamarin-forms/app-fundamentals/application-class.md) página.
