---
title: Introducción a los comportamientos
description: Comportamientos le permiten agregar funcionalidad a los controles de interfaz de usuario sin necesidad de subclase de ellos. En su lugar, la funcionalidad se implementa en una clase de comportamiento y se adjunta al control como si fuera parte del propio control. En este artículo se proporciona una introducción a los comportamientos.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 176f41d4b7349af2cf7cc49de8ba0789ad2f8c11
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995819"
---
# <a name="introduction-to-behaviors"></a>Introducción a los comportamientos

_Comportamientos le permiten agregar funcionalidad a los controles de interfaz de usuario sin necesidad de subclase de ellos. En su lugar, la funcionalidad se implementa en una clase de comportamiento y se adjunta al control como si fuera parte del propio control. En este artículo se proporciona una introducción a los comportamientos._

Los comportamientos le permiten implementar el código que normalmente tendría que escribir como código subyacente, ya que interactúa directamente con la API del control de manera que se pueda concisa adjunta al control y empaqueta para su reutilización en más de una aplicación. Que pueden utilizarse para proporcionar un amplio abanico de funcionalidad a los controles, como:

- Agregar un validador de correo electrónico a un [ `Entry` ](xref:Xamarin.Forms.Entry).
- Creación de un control de clasificación mediante un reconocedor de movimiento de tap.
- Controlar una animación.
- Agregar un efecto a un control.

Los comportamientos también permiten escenarios más avanzados. En el contexto de *comandos*, los comportamientos son un enfoque útil para conectar un control a un comando. Además, puede usarse para asociar los comandos a los controles que no se han diseñado para interactuar con los comandos. Por ejemplo, puede usarse para invocar un comando como respuesta a un activación del evento.

Xamarin.Forms es compatible con dos estilos diferentes comportamientos:

- **Comportamientos de Xamarin.Forms** : clases que derivan de la [ `Behavior` ](xref:Xamarin.Forms.Behavior) o [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) (clase), donde `T` es el tipo del control al que el comportamiento debe aplicar. Para obtener más información acerca de los comportamientos de Xamarin.Forms, consulte [comportamientos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) y [comportamientos reutilizables](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Adjunta comportamientos** – `static` clases con una o varias de las propiedades adjuntas. Para obtener más información sobre los comportamientos asociados, consulte [adjunta comportamientos](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Esta guía se centra en los comportamientos de Xamarin.Forms porque son el método preferido para la construcción de comportamiento.



## <a name="related-links"></a>Vínculos relacionados

- [Comportamiento](xref:Xamarin.Forms.Behavior)
- [Comportamiento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
