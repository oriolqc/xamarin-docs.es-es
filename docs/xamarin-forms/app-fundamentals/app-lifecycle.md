---
title: Ciclo de vida de aplicación de Xamarin.Forms
description: Este artículo explica cómo responder al ciclo de vida de aplicación, incluidos los métodos de ciclo de vida, los eventos de navegación de página y eventos de navegación modal.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: fb651494b63a77ede47dd246ee054b5c67af2a35
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240274"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo de vida de aplicación de Xamarin.Forms

El [ `Application` ](xref:Xamarin.Forms.Application) clase base ofrece las siguientes características:

* [Métodos de ciclo de vida](#Lifecycle_Methods) `OnStart`, `OnSleep`, y `OnResume`.
* [Página eventos de navegación](#page) [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing), [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing).
* [Eventos de navegación modal](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping`, y `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

El [ `Application` ](xref:Xamarin.Forms.Application) clase contiene tres métodos virtuales que se pueden invalidar para controlar los métodos del ciclo de vida:

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

<a name="page" />

## <a name="page-navigation-events"></a>Eventos de navegación de página

Hay dos eventos en el [ `Application` ](xref:Xamarin.Forms.Application) clase que proporcionar la notificación de páginas que aparecen y desaparecen:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) -se produce cuando una página está a punto de aparecen en la pantalla.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) -se produce cuando una página está a punto de desaparecen de la pantalla.

Estos eventos se pueden usar en escenarios donde quiera hacer un seguimiento páginas a medida que aparecen en pantalla.

> [!NOTE]
> El [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing) y [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing) se generan eventos desde el [ `Page` ](xref:Xamarin.Forms.Page) clase base inmediatamente después de la [ `Page.Appearing` ](xref:Xamarin.Forms.Page.Appearing) y [ `Page.Disappearing` ](xref:Xamarin.Forms.Page.Disappearing) eventos, respectivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventos de navegación modal

Hay cuatro eventos en el [ `Application` ](xref:Xamarin.Forms.Application) (clase), cada uno con sus propios argumentos de evento, que le permiten responder a modales páginas que se muestran y se descartará:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - la `ModalPoppingEventArgs` clase contiene un `Cancel` propiedad. Cuando `Cancel` está establecido en `true` se cancela el emergente modal.
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> Implementar los métodos del ciclo de vida de aplicación y eventos de navegación modal, todos los anteriores`Application` métodos de creación de una aplicación de Xamarin.Forms (ie. las aplicaciones escritas en la versión 1.2 o anterior que utilizan una variable static `GetMainPage` método) se han actualizado para crear un valor predeterminado `Application` que se establece como el elemento primario de `MainPage`.
>
> Las aplicaciones de Xamarin.Forms que utilizan este comportamiento heredado deben actualizarse a una `Application` subclase tal como se describe en el [clase Application](~/xamarin-forms/app-fundamentals/application-class.md) página.
