---
title: Introducción a las plantillas de Control de Xamarin.Forms
description: Plantillas de control de Xamarin.Forms proporcionan la capacidad de fácilmente el tema y el tema de nuevo las páginas de aplicación en tiempo de ejecución. Este artículo proporciona una introducción a las plantillas de control.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: a8e5c84bfa2525a28e9af5343be0ee156564bdd6
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242523"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Introducción a las plantillas de Control de Xamarin.Forms

_Plantillas de control de Xamarin.Forms proporcionan la capacidad de fácilmente el tema y el tema de nuevo las páginas de aplicación en tiempo de ejecución. Este artículo proporciona una introducción a las plantillas de control._

Los controles tienen propiedades diferentes, como `BackgroundColor` y `TextColor`, que permite definir aspectos de la apariencia del control. Estas propiedades se pueden establecer mediante [estilos](~/xamarin-forms/user-interface/styles/index.md), que se puede cambiar en tiempo de ejecución para implementar temas básicos. Sin embargo, los estilos no mantienen una separación clara entre la apariencia de una página y su contenido, y los cambios que pueden realizarse mediante el establecimiento de estas propiedades son limitados.

Las plantillas de control proporcionan una separación clara entre la apariencia de una página y su contenido, por lo tanto, lo que permite la creación de páginas que pueden ser fácilmente con temas. Por ejemplo, una aplicación puede contener plantillas de control de nivel de aplicación que proporcionan un tema oscuro y un tema claro. Cada [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) en la aplicación puede ser con temas aplicando una de las plantillas de control sin cambiar el contenido mostrado por cada página. Además, los temas que proporcionan las plantillas de control no están limitados a cambiar las propiedades de controles. También pueden cambiar los controles utilizados para implementar el tema.

## <a name="creating-a-controltemplate"></a>Creación de una plantilla de control

A [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) especifica el aspecto de una página o una vista y contiene un diseño de raíz y en el diseño, los controles que implementan la plantilla. Normalmente, un `ControlTemplate` usará un [ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) para marcar dónde aparecerá el contenido que va a mostrar la página o la vista. La página o la vista que consume el `ControlTemplate` , a continuación, definirá el contenido que va a mostrar el `ContentPresenter`. El diagrama siguiente ilustra un `ControlTemplate` para una página que contiene un número de controles, incluyendo un `ContentPresenter` marcado por un rectángulo azul:

![](introduction-images/control-template.png "Plantilla de control de una página")

A [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) pueden aplicarse a los siguientes tipos estableciendo sus `ControlTemplate` propiedades:

- [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)
- [`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
- [`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/)
- [`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/)

Cuando un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) se crea y asigna a estos tipos, cualquier aspecto existente se reemplaza con el aspecto que se definen en el `ControlTemplate`. Además, así como establecer apariencia utilizando el `ControlTemplate` propiedad, control plantillas también pueden aplicarse mediante estilos para seguir amplían la capacidad de tema.

> [!NOTE]
>  *¿Cuáles son los `TemplatedPage` y `TemplatedView` tipos?* `TemplatedPage` es la clase base para `ContentPage`y es el tipo de página más básico proporcionado por Xamarin.Forms. A diferencia de `ContentPage`, `TemplatedPage` no tiene un `Content` propiedad. Por lo tanto, contenido no se puede agregar directamente a un `TemplatedPage` instancia. En su lugar, se agrega el contenido mediante el establecimiento de la plantilla de control para la `TemplatedPage` instancia. De forma similar, `TemplatedView` es la clase base para `ContentView`. A diferencia de `ContentView`, `TemplatedView` no tiene un `Content` propiedad. Por lo tanto, contenido no se puede agregar directamente a un `TemplatedView` instancia. En su lugar, se agrega el contenido mediante el establecimiento de la plantilla de control para la `TemplatedView` instancia.

Plantillas de control se pueden crear en XAML y en C#:

- Plantillas de control creadas en XAML se definen en un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) que se asigna a la [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) recopilación de una página o, por lo general en el [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/) recolección de la aplicación.
- Plantillas de control creadas en C# normalmente se definen en la clase de la página, o en una clase que se puede acceder de forma global.

Elegir dónde se defina un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instancia impactos dónde pueden utilizarse:

- [`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instancias definidas en el nivel de página solo pueden aplicarse a la página.
- [`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instancias definidas en el nivel de aplicación pueden aplicarse a las páginas a lo largo de la aplicación.

Plantillas de control hacia abajo en la jerarquía de vista tienen prioridad sobre los definidos en cuanto mayor sea la. Por ejemplo, un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) denominado `DarkTheme` que se define en el nivel de página tendrá prioridad sobre una plantilla con el mismo nombre definida en el nivel de aplicación. Por lo tanto, una plantilla de control que define un tema que se aplicará a cada página de una aplicación debe definirse en el nivel de aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Elemento ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentPresenter](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)
