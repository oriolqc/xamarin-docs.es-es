---
title: Introducción a las plantillas de Control de Xamarin.Forms
description: Plantillas de controles de Xamarin.Forms proporcionan la capacidad tema fácilmente y re-tema de las páginas de aplicación en tiempo de ejecución. En este artículo se proporciona una introducción a las plantillas de control.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 6b7a6c6d9c9c541e1d5e821fc2dac202e98bec62
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994430"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Introducción a las plantillas de Control de Xamarin.Forms

_Plantillas de controles de Xamarin.Forms proporcionan la capacidad tema fácilmente y re-tema de las páginas de aplicación en tiempo de ejecución. En este artículo se proporciona una introducción a las plantillas de control._

Los controles tienen propiedades diferentes, como `BackgroundColor` y `TextColor`, que puede definir los aspectos de la apariencia del control. Estas propiedades se pueden establecer mediante [estilos](~/xamarin-forms/user-interface/styles/index.md), que se puede cambiar en tiempo de ejecución para implementar la creación de temas básico. Sin embargo, los estilos no mantienen una separación clara entre la apariencia de una página y su contenido y los cambios que pueden realizarse mediante el establecimiento de estas propiedades son limitados.

Las plantillas de control proporcionan una separación clara entre la apariencia de una página y su contenido, por lo tanto, lo que permite la creación de páginas que fácilmente se pueden aplicar un tema. Por ejemplo, una aplicación puede contener plantillas de control de nivel de aplicación que proporcionan un tema oscuro y un tema claro. Cada [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) en la aplicación puede ser temáticas al aplicar una de las plantillas de control sin cambiar el contenido mostrado por cada página. Además, los temas que proporcionan las plantillas de control no están limitados a cambiar las propiedades de controles. También pueden cambiar los controles utilizados para implementar el tema.

## <a name="creating-a-controltemplate"></a>Creación de una plantilla de control

Un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) especifica la apariencia de una página o vista y contiene un diseño de raíz y en el diseño, los controles que implementan la plantilla. Normalmente, un `ControlTemplate` usará un [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) para marcar dónde aparecerá el contenido que va a mostrar la página o vista. La página o vista que consume el `ControlTemplate` , a continuación, definirá el contenido que va a mostrar el `ContentPresenter`. El siguiente diagrama ilustra una `ControlTemplate` para una página que contiene un número de controles, incluyendo un `ContentPresenter` marcado por un rectángulo azul:

![](introduction-images/control-template.png "Plantilla de control de una página")

Un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) se pueden aplicar a los siguientes tipos estableciendo sus `ControlTemplate` propiedades:

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

Cuando un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) se crean y se asignan a estos tipos, cualquier aspecto existente se reemplaza con el aspecto que se definen en el `ControlTemplate`. Además, así como establecer apariencia utilizando el `ControlTemplate` propiedad, también se pueden aplicar plantillas mediante estilos aún más el control expandir la capacidad de tema.

> [!NOTE]
>  *¿Cuáles son los `TemplatedPage` y `TemplatedView` tipos?* `TemplatedPage` es la clase base para `ContentPage`y es el tipo de página más básico proporcionado Xamarin.Forms. A diferencia de `ContentPage`, `TemplatedPage` no tiene un `Content` propiedad. Por lo tanto, contenido no se puede agregar directamente a un `TemplatedPage` instancia. En su lugar, se agrega el contenido mediante el establecimiento de la plantilla de control para el `TemplatedPage` instancia. De forma similar, `TemplatedView` es la clase base para `ContentView`. A diferencia de `ContentView`, `TemplatedView` no tiene un `Content` propiedad. Por lo tanto, contenido no se puede agregar directamente a un `TemplatedView` instancia. En su lugar, se agrega el contenido mediante el establecimiento de la plantilla de control para el `TemplatedView` instancia.

Plantillas de control se pueden crear en XAML y en C#:

- Plantillas de control creadas en XAML se definen en un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) que se asigna a la [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) colección de una página o, por lo general a la [ `Resources` ](xref:Xamarin.Forms.Application.Resources) recolección de la aplicación.
- Plantillas de control creadas en C# normalmente se definen en la clase de página o en una clase que se puede acceder de forma global.

Elegir dónde se puede definir un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) impactos en el que se puede usar la instancia:

- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) las instancias definidas en el nivel de página sólo pueden aplicarse a la página.
- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) las instancias definidas en el nivel de aplicación pueden aplicarse a las páginas en toda la aplicación.

Plantillas de control más abajo en la jerarquía de vistas tienen prioridad sobre las define mayor seguridad. Por ejemplo, un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) denominado `DarkTheme` que se define en el nivel de página tendrá prioridad sobre una plantilla con el mismo nombre definida en el nivel de aplicación. Por lo tanto, una plantilla de control que define un tema que se aplicará a cada página en una aplicación debe definirse en el nivel de aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
