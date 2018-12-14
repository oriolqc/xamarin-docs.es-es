---
title: Ciclo de vida de aplicaciones de Xamarin.Forms
description: En este artículo, se explica cómo responder al ciclo de vida de las aplicaciones, incluidos métodos de ciclo de vida, eventos de navegación de página y eventos de navegación modal.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675125"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo de vida de aplicaciones de Xamarin.Forms

La clase base [`Application`](xref:Xamarin.Forms.Application) ofrece las características siguientes:

* [Métodos de ciclo de vida](#Lifecycle_Methods) `OnStart`, `OnSleep` y `OnResume`.
* [Eventos de navegación de página](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing), [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing).
* [Eventos de navegación modal](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping` y `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

La clase [`Application`](xref:Xamarin.Forms.Application) contiene tres métodos virtuales que pueden reemplazarse para controlar métodos de ciclo de vida:

* **OnStart**: se llama cuando se inicia la aplicación.

* **OnSleep**: se llama cada vez que la aplicación pasa a segundo plano.

* **OnResume**: se llama cuando se reanuda la aplicación después de haberla enviado a segundo plano.

Tenga en cuenta que *no* hay ningún método para la finalización de aplicaciones.
En circunstancias normales (es decir, si no se produce un bloqueo), la finalización de la aplicación se producirá desde el estado *OnSleep*, sin que se muestren notificaciones en el código.

Para observar cuándo se llama a estos métodos, implemente una llamada `WriteLine` en cada uno (como se muestra a continuación) y pruébelos en cada plataforma.

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

Al actualizar aplicaciones de Xamarin.Forms *anteriores* (por ejemplo, creadas con Xamarin.Forms 1.3 o versiones anteriores), asegúrese de que, en la actividad principal de Android, se incluya `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` en el atributo `[Activity()]`. Si no está presente, verá que se llama al método `OnStart` en rotación y, además, cuando se inicie por primera vez la aplicación. Este atributo se incluye automáticamente en las plantillas de aplicaciones de Xamarin.Forms actuales.

<a name="page" />

## <a name="page-navigation-events"></a>Eventos de navegación de página

Hay dos eventos en la clase [`Application`](xref:Xamarin.Forms.Application) que proporcionan notificaciones de páginas que aparecen y desaparecen:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing): se genera cuando una página va a aparecer en la pantalla.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing): se genera cuando una página va a desaparecer de la pantalla.

Estos eventos pueden usarse en escenarios en los que quiera realizar un seguimiento de páginas a medida que aparezcan en la pantalla.

> [!NOTE]
> Los eventos [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) y [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) se generan a partir de la clase base [`Page`](xref:Xamarin.Forms.Page) inmediatamente después de los eventos [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) y [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing), respectivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventos de navegación modal

Hay cuatro eventos en la clase [`Application`](xref:Xamarin.Forms.Application), cada uno con sus propios argumentos de evento, que le permiten responder a páginas modales que empiezan a mostrarse y se descartan:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping**: la clase `ModalPoppingEventArgs` contiene una propiedad `Cancel`. Cuando `Cancel` se establece en `true`, se cancela la ventana emergente modal.
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> Para implementar los métodos de ciclo de vida de las aplicaciones y eventos de navegación modal, todos los métodos anteriores a `Application` para crear una aplicación de Xamarin.Forms (es decir, aplicaciones escritas en la versión 1.2 o posteriores que usen un método `GetMainPage` estático) se han actualizado para crear un elemento `Application` predeterminado, que se establece como el elemento principal de `MainPage`.
>
> Las aplicaciones de Xamarin.Forms que usen este comportamiento heredado tienen que actualizarse a una subclase de `Application`, como se describe en la página [Clase de aplicación](~/xamarin-forms/app-fundamentals/application-class.md).
