---
title: "Introducción a los efectos"
description: "Efectos de permitir que los controles nativos en cada plataforma para personalizarse y se utilizan normalmente para los cambios de estilo pequeño. Este artículo proporciona una introducción a los efectos, describe el límite entre los representadores personalizados y efectos y describe la clase PlatformEffect."
ms.topic: article
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 037c82aa31c167e44a88619cba91a5be8035d0fa
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="introduction-to-effects"></a>Introducción a los efectos

_Efectos de permitir que los controles nativos en cada plataforma para personalizarse y se utilizan normalmente para los cambios de estilo pequeño. Este artículo proporciona una introducción a los efectos, describe el límite entre los representadores personalizados y efectos y describe la clase PlatformEffect._

Xamarin.Forms [páginas, diseños y controles](~/xamarin-forms/user-interface/controls/index.md) presenta una API común para describir las interfaces de usuario móviles entre plataformas. Cada página, el diseño y el control se representan de manera diferente en cada plataforma mediante un `Renderer` clase que a su vez crea un control nativo (que corresponde a la representación de Xamarin.Forms), lo organiza en la pantalla y agrega el comportamiento especificado en el recurso compartido código.

Los desarrolladores pueden implementar sus propias clases `Renderer` personalizadas para personalizar la apariencia o el comportamiento de un control. Sin embargo, a menudo es una respuesta exhaustiva implementar una clase de representador personalizado para llevar a cabo una personalización de control simple. Efectos simplifican este proceso, lo que permite a los controles nativos en cada plataforma para personalizar más fácilmente.

Efectos se crean en proyectos específicos de plataforma de creación de subclases el `PlatformEffect` control y, a continuación, los efectos se consumen adjuntándolos a un control adecuado en un proyecto de biblioteca de clases portables (PCL) de Xamarin.Forms o biblioteca compartida.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>¿Por qué usar un efecto sobre un representador personalizado?

Efectos simplifican la personalización de un control, son reutilizables y se pueden parametrizar para aumentar aún más la reutilización.

Todo lo que se puede lograr con un efecto también se puede conseguir con un representador personalizado. Sin embargo, los representadores personalizados proporcionan más flexibilidad y personalización de efectos. Las instrucciones siguientes enumeran las circunstancias en que se va a elegir un efecto en un representador personalizado:

- Se recomienda un efecto al cambiar las propiedades de un control específico de la plataforma va a obtener el resultado deseado.
- Un representador personalizado es necesario cuando hay una necesidad para invalidar los métodos de un control específico de la plataforma.
- Un representador personalizado es necesario cuando hay una necesidad para reemplazar el control específico de la plataforma que implementa un control de Xamarin.Forms.

## <a name="subclassing-the-platformeffect-class"></a>Creación de subclases de la clase PlatformEffect

La tabla siguiente muestra el espacio de nombres para el `PlatformEffect` clase en cada plataforma y los tipos de sus propiedades:

|Plataforma|Espacio de nombres|contenedor|Control|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|Vista ViewGroup|Ver|
|Windows Phone 8,1|Xamarin.Forms.Platform.WinRT|FrameworkElement|FrameworkElement|
|Plataforma universal de Windows (UWP)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

Cada específica de la plataforma `PlatformEffect` clase expone las propiedades siguientes:

- `Container` : hace referencia al control específico de la plataforma que se usa para implementar el diseño.
- `Control` : hace referencia al control específico de la plataforma que se usa para implementar el control de Xamarin.Forms.
- `Element` : hace referencia al control de Xamarin.Forms que se va a representar.

Efectos no tiene información de tipos sobre el contenedor, el control o el elemento que se adjuntan a ya que pueden asociarse a cualquier elemento. Por lo tanto, cuando un efecto se adjunta a un elemento que no es compatible con deben rebajarse o se produzca una excepción. Sin embargo, el `Container`, `Control`, y `Element` propiedades pueden convertirse a su tipo de implementación. Para obtener más información acerca de estos tipos de vea [clases del representador Base y los controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Cada específica de la plataforma `PlatformEffect` clase expone los métodos siguientes, que se deben invalidar para implementar un efecto:

- [`OnAttached`](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.OnAttached()/) : se le llama cuando un efecto se adjunta a un control de Xamarin.Forms. Una versión reemplazada de este método en cada clase específica de la plataforma efecto, es el lugar para realizar la personalización del control, junto con el control de excepciones en caso de que no se puede aplicar el efecto en el control de Xamarin.Forms especificado.
- [`OnDetached`](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.OnDetached()/) : se le llama cuando se desasocia un efecto de un control de Xamarin.Forms. Una versión reemplazada de este método en cada clase específica de la plataforma efecto, es el lugar para realizar cualquier limpieza efecto como anular registro de un controlador de eventos.

Además, el `PlatformEffect` expone la [ `OnElementPropertyChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E.OnElementPropertyChanged/p/System.ComponentModel.PropertyChangedEventArgs/) método, que también se puede invalidar. Este método se llama cuando ha cambiado una propiedad del elemento. Una versión reemplazada de este método en cada clase específica de la plataforma efecto, es el lugar para responder a los cambios de propiedad enlazable en el control de Xamarin.Forms. Siempre se debe realizar una comprobación de la propiedad que se ha modificado, como esta invalidación puede llamarse muchas veces.


## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
