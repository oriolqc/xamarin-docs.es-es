---
title: Compatibilidad de 2.0 estándar de .NET en Xamarin.Forms
description: En este artículo se explica cómo convertir una aplicación de Xamarin.Forms para usar .NET 2.0 estándar.
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 8685f1e10b5094e6f58e8efea51e6dd216dfa000
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="net-standard-20-support-in-xamarinforms"></a>Compatibilidad de 2.0 estándar de .NET en Xamarin.Forms

_En este artículo se explica cómo convertir una aplicación de Xamarin.Forms para usar .NET 2.0 estándar._

Estándar de .NET es una especificación de API de .NET que se van a estar disponible en todas las implementaciones. NET. Resulta más fácil compartir código entre aplicaciones de escritorio, aplicaciones móviles y juegos y servicios de nube, aunando las API idénticas a las diferentes plataformas. Para obtener información acerca de las plataformas compatibles con el estándar. NET, vea [soporte de implementación de .NET](/dotnet/standard/net-standard#net-implementation-support/).

Bibliotecas de .NET estándar son la sustitución de las bibliotecas de clases portables (PCL). Sin embargo, una biblioteca que tenga como destino .NET estándar sigue siendo una PCL y se conoce como una PCL basado en .NET estándar. Ciertos perfiles PCL se asignan a versiones de .NET estándar y para los perfiles que tienen una asignación, los tipos de dos biblioteca podrán hacer referencia a entre sí. Para obtener más información, consulte [compatibilidad PCL](/dotnet/standard/net-standard#pcl-compatibility).

Xamarin.Forms 2.4 permite a las aplicaciones Xamarin.Forms destino .NET estándar 2.0 si se reemplaza la PCL con una biblioteca estándar de .NET 2.0. Esto puede lograrse como sigue:

- Asegúrese de [.NET Core 2.0](https://www.microsoft.com/net/download/core) está instalado.
- Actualizar la solución de Xamarin.Forms para uso de Xamarin.Forms 2.4 o superior.
- Agregar una biblioteca estándar de .NET a la solución, que tiene como destino .NET 2.0 estándar.
- Eliminar la clase que se agrega a la biblioteca estándar. NET.
- Agregue el paquete de NuGet Xamarin.Forms 2,4 (o posterior) a la biblioteca estándar. NET.
- En los proyectos de plataforma, agregue una referencia a la biblioteca estándar de .NET y quite la referencia al proyecto de PCL que contiene la lógica de interfaz de usuario de Xamarin.Forms.
- Copie los archivos del proyecto PCL a la biblioteca estándar. NET.
- Quitar el proyecto PCL que contiene la lógica de interfaz de usuario de Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md)
