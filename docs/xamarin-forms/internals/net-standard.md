---
title: 2.0 compatibilidad con .NET standard en Xamarin.Forms
description: En este artículo se explica cómo convertir una aplicación de Xamarin.Forms para usar .NET Standard 2.0. Estándar de .NET es una especificación de API de .NET que va a estar disponible en todas las implementaciones. NET.
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: c3e46592bb8760ff85eaeb5dce119897a97dfe89
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293292"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>2.0 compatibilidad con .NET standard en Xamarin.Forms

_En este artículo se explica cómo convertir una aplicación de Xamarin.Forms para usar .NET Standard 2.0._

Estándar de .NET es una especificación de API de .NET que va a estar disponible en todas las implementaciones. NET. Resulta más fácil compartir código entre aplicaciones de escritorio, aplicaciones móviles, juegos y servicios de nube aunando las API idénticas a las diferentes plataformas. Para obtener información acerca de las plataformas compatibles con el estándar. NET, vea [soporte de implementación de .NET](/dotnet/standard/net-standard#net-implementation-support).

Bibliotecas de .NET standard son el reemplazo de las bibliotecas de clases portables (PCL). Sin embargo, una biblioteca que tenga como destino .NET Standard sigue siendo una PCL y se conoce como una PCL basadas en .NET Standard. Algunos perfiles de PCL se asignan a las versiones de .NET Standard, y para los perfiles que tienen una asignación, los tipos de dos biblioteca podrán hacen referencia entre sí. Para obtener más información, consulte [compatibilidad con PCL](/dotnet/standard/net-standard#pcl-compatibility).

2.4 de Xamarin.Forms permite que las aplicaciones de Xamarin.Forms para el destino .NET Standard 2.0 reemplazando la PCL con una biblioteca .NET Standard 2.0. Esto puede lograrse como sigue:

- Asegúrese de [.NET Core 2.0](https://www.microsoft.com/net/download/core) está instalado.
- Actualización de la solución de Xamarin.Forms para usar Xamarin.Forms 2.4 o superior.
- Agregar una biblioteca .NET Standard a la solución, que tiene como destino .NET Standard 2.0.
- Eliminar la clase que se agrega a la biblioteca .NET Standard.
- Agregue el paquete de NuGet Xamarin.Forms 2,4 (o superior) a la biblioteca .NET Standard.
- En los proyectos de plataforma, agregue una referencia a la biblioteca estándar de .NET y quite la referencia al proyecto PCL que contiene la lógica de interfaz de usuario de Xamarin.Forms.
- Copie los archivos desde el proyecto PCL en la biblioteca .NET Standard.
- Quitar el proyecto PCL que contiene la lógica de interfaz de usuario de Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md)
