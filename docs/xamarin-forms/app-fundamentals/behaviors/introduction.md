---
title: Introducción a los comportamientos
description: Comportamientos le permiten agregar funcionalidad a los controles de interfaz de usuario sin tener que subclase ellos. En su lugar, la funcionalidad se implementa en una clase de comportamiento y conectada al control como si fuera parte del propio control. Este artículo proporciona una introducción a los comportamientos.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: dc6d8396c2908d251290e4540dbb3cec3344542f
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732793"
---
# <a name="introduction-to-behaviors"></a>Introducción a los comportamientos

_Comportamientos le permiten agregar funcionalidad a los controles de interfaz de usuario sin tener que subclase ellos. En su lugar, la funcionalidad se implementa en una clase de comportamiento y conectada al control como si fuera parte del propio control. Este artículo proporciona una introducción a los comportamientos._

Comportamientos le permiten implementar el código que normalmente tendría que escribir como código subyacente, porque interactúa directamente con la API del control de manera que se pueda concisa adjunta al control y empaqueta para su reutilización en más de una aplicación. Puede utilizar para proporcionar una amplia gama de funcionalidad a los controles, como:

- Agregar un validador de correo electrónico a un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/).
- Crear un control de clasificación mediante un reconocedor de movimiento de derivación.
- Controlar una animación.
- Agregar un efecto a un control.

Comportamientos también permiten escenarios más avanzados. En el contexto de *estableciendo*, los comportamientos son un enfoque útil para conectar un control a un comando. Además, puede utilizarse para asociar comandos a los controles que no se diseñaron para interactuar con los comandos. Por ejemplo, puede utilizarse para invocar un comando como respuesta a un que desencadenó el evento.

Xamarin.Forms admite dos estilos diferentes de comportamientos:

- **Comportamientos de Xamarin.Forms** : las clases que derivan de la [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) (clase), donde `T` es el tipo del control al que el comportamiento debe aplicar. Para obtener más información acerca de los comportamientos de Xamarin.Forms, consulte [comportamientos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) y [reutilizable comportamientos](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Adjunta comportamientos** – `static` clases con una o varias propiedades adjuntas. Para obtener más información acerca de los comportamientos adjuntos, consulte [adjunta comportamientos](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Esta guía se centra en los comportamientos de Xamarin.Forms porque son el método preferido para la construcción de comportamiento.



## <a name="related-links"></a>Vínculos relacionados

- [Comportamiento](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Comportamiento&lt;T&gt;](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
