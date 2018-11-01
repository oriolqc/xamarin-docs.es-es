---
title: Ciclo de vida de aplicación de Xamarin.Forms
description: En este artículo se explica cómo responder al ciclo de vida de la aplicación, incluidos los métodos del ciclo de vida, los eventos de navegación de página y eventos de navegación modal.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675125"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo de vida de aplicación de Xamarin.Forms

El [ `Application` ](xref:Xamarin.Forms.Application) clase base ofrece las siguientes características:

* [Métodos de ciclo de vida](#Lifecycle_Methods) `OnStart`, `OnSleep`, y `OnResume`.
* [Página eventos de navegación](#page) [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing), [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing).
* [Los eventos de navegación modal](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping`, y `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

El [ `Application` ](xref:Xamarin.Forms.Application) clase contiene tres métodos virtuales que se pueden invalidar para controlar los métodos del ciclo de vida:

* **OnStart** -se le llama cuando se inicia la aplicación.

* **OnSleep** -llama cada vez que la aplicación entra en segundo plano.

* **OnResume** -se le llama cuando se reanuda la aplicación, después de que se va a enviar al fondo.

Tenga en cuenta que hay *ningún* método de finalización de la aplicación.
En circunstancias normales (es decir, no un bloqueo) se realizará la finalización de la aplicación desde el *OnSleep* state, sin ninguna notificación adicional a su código.

Para observar cuando se llama a estos métodos, implemente un `WriteLine` llamar en cada uno de ellos (como se muestra a continuación) y realizar pruebas en cada plataforma.

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

Al actualizar *anteriores* (p ej. las aplicaciones de Xamarin.Forms. crear con Xamarin.Forms 1.3 o una versión anterior), asegúrese de que incluye la actividad principal Android `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` en el `[Activity()]` atributo. Si no está presente, se observará los `OnStart` obtiene llama al método en rotación, así como cuando la aplicación se inicia por primera vez. Este atributo se incluye automáticamente en las plantillas de aplicación de Xamarin.Forms actuales.

<a name="page" />

## <a name="page-navigation-events"></a>Eventos de navegación de página

Hay dos eventos en el [ `Application` ](xref:Xamarin.Forms.Application) clases que proporcionan notificaciones de páginas que aparecen y desaparecen:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) -se genera cuando una página está a punto de mostrarse en la pantalla.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) -se genera cuando una página se va a desaparecer de la pantalla.

Estos eventos se pueden usar en escenarios donde desea realizar un seguimiento de las páginas como estuvieran colocadas en pantalla.

> [!NOTE]
> El [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing) y [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing) eventos se generan desde el [ `Page` ](xref:Xamarin.Forms.Page) clase base inmediatamente después de la [ `Page.Appearing` ](xref:Xamarin.Forms.Page.Appearing) y [ `Page.Disappearing` ](xref:Xamarin.Forms.Page.Disappearing) eventos, respectivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventos de navegación modal

Hay cuatro eventos en el [ `Application` ](xref:Xamarin.Forms.Application) (clase), cada uno con sus propios argumentos de evento, que le permiten responder a las páginas modales se muestran y descartar:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** : la `ModalPoppingEventArgs` clase contiene un `Cancel` propiedad. Cuando `Cancel` está establecido en `true` pop modal se ha cancelado.
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> Implementar los métodos de ciclo de vida de aplicaciones y los eventos de navegación modal, todos los anteriores`Application` métodos de creación de una aplicación de Xamarin.Forms (es decir, las aplicaciones escritas en la versión 1.2 o anterior que utilizan estático `GetMainPage` método) se han actualizado para crear un valor predeterminado `Application` que se establece como el elemento primario de `MainPage`.
>
> Las aplicaciones de Xamarin.Forms que usan este comportamiento heredado se deben actualizar a una `Application` subclase tal como se describe en el [clase Application](~/xamarin-forms/app-fundamentals/application-class.md) página.
