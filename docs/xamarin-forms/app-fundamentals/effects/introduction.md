---
title: Introducción a los efectos
description: Los efectos permiten personalizar los controles nativos de cada plataforma y se suelen usar para pequeños cambios de estilo. En este artículo proporciona una introducción a los efectos, se describe el límite entre los representadores personalizados y de efectos y describe la clase PlatformEffect.
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: d3fa958e999a10832d5fa15e4190077955b0e6df
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997389"
---
# <a name="introduction-to-effects"></a>Introducción a los efectos

_Los efectos permiten a los controles nativos en cada plataforma para personalizarse y se utilizan normalmente para los cambios de estilo pequeño. En este artículo proporciona una introducción a los efectos, se describe el límite entre los representadores personalizados y de efectos y describe la clase PlatformEffect._

Xamarin.Forms [páginas, diseños y controles](~/xamarin-forms/user-interface/controls/index.md) presenta una API común para describir interfaces de usuario móviles multiplataforma. Cada página, el diseño y el control se representan de manera diferente en cada plataforma mediante una `Renderer` clase que a su vez crea un control nativo (que corresponde a la representación de Xamarin.Forms), lo organiza en la pantalla y agrega el comportamiento especificado en el recurso compartido código.

Los desarrolladores pueden implementar sus propias clases `Renderer` personalizadas para personalizar la apariencia o el comportamiento de un control. Sin embargo, a menudo es una respuesta exhaustiva implementar una clase de representador personalizado para llevar a cabo una personalización de controles simples. Efectos de simplifican este proceso, lo que permite a los controles nativos en cada plataforma para personalizar más fácilmente.

Los efectos se crean en proyectos específicos de plataforma mediante la creación de subclases el `PlatformEffect` control y, a continuación, los efectos se consumen adjuntándolos a un control adecuado en un proyecto de biblioteca compartida o la biblioteca estándar de .NET de Xamarin.Forms.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>¿Por qué usar un efecto a través de un representador personalizado?

Efectos simplifican la personalización de un control, sean reutilizables y se pueden parametrizar para aumentar aún más la reutilización.

Todo lo que se puede lograr con un efecto también se puede lograr con un representador personalizado. Sin embargo, los representadores personalizados ofrecen más flexibilidad y personalización que los efectos de. Las instrucciones siguientes enumeran las circunstancias en que se va a elegir un efecto a través de un representador personalizado:

- Se recomienda un efecto al cambiar las propiedades de un control específico de la plataforma proporcionará el resultado deseado.
- Un representador personalizado es necesario cuando es necesario para invalidar los métodos de un control específico de la plataforma.
- Un representador personalizado es necesario cuando es necesario reemplazar el control específico de la plataforma que implementa un control de Xamarin.Forms.

## <a name="subclassing-the-platformeffect-class"></a>Creación de subclases de la clase PlatformEffect

La tabla siguiente muestra el espacio de nombres para el `PlatformEffect` clase en cada plataforma y los tipos de sus propiedades:

|Plataforma|Espacio de nombres|Contenedor|Control|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|Grupo de vista|Ver|
|Plataforma universal de Windows (UWP)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

Cada uno específico de plataforma `PlatformEffect` clase expone las propiedades siguientes:

- `Container` : hace referencia al control específico de la plataforma que se usa para implementar el diseño.
- `Control` : hace referencia al control específico de la plataforma que se usa para implementar el control de Xamarin.Forms.
- `Element` : hace referencia al control de Xamarin.Forms que se va a representar.

Efectos no tiene información de tipo sobre el contenedor, el control o el elemento que se adjunten a, ya que pueden asociarse a cualquier elemento. Por lo tanto, cuando se adjunta un efecto a un elemento que no es compatible con deben rebajarse o producir una excepción. Sin embargo, el `Container`, `Control`, y `Element` propiedades pueden convertirse a su tipo de implementación. Para obtener más información acerca de estos tipos vea [clases Base del representador y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Cada uno específico de plataforma `PlatformEffect` clase expone los métodos siguientes, que se deben invalidar para implementar un efecto:

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached) : se le llama cuando se adjunta un efecto a un control de Xamarin.Forms. Una versión reemplazada de este método en cada clase efecto Frameworks específica, es el lugar para realizar la personalización del control, junto con el control de excepciones en caso de que no se puede aplicar el efecto para el control especificado de Xamarin.Forms.
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached) : se le llama cuando se desasocia un efecto de un control de Xamarin.Forms. Una versión reemplazada de este método en cada clase efecto específico de la plataforma, es el lugar para realizar cualquier limpieza efecto como anular el registro de un controlador de eventos.

Además, el `PlatformEffect` expone el [ `OnElementPropertyChanged` ](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) método, que también se puede invalidar. Este método se llama cuando ha cambiado una propiedad del elemento. Una versión reemplazada de este método en cada clase efecto específico de la plataforma, es el lugar para responder a los cambios de propiedad enlazable en el control de Xamarin.Forms. Siempre se debe realizar una comprobación para la propiedad que se ha modificado, como esta invalidación puede llamarse varias veces.


## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
