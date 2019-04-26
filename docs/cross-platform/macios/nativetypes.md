---
title: Tipos nativos para iOS y macOS
description: Este documento describe cómo Xamarin Unified API asigna tipos de .NET a tipos nativos 32 bits y 64 bits, según sea necesario según la arquitectura de destino de compilación.
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: fc2b91a9265fcf09e4f58d5de27a1fdef9350b2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199643"
---
# <a name="native-types-for-ios-and-macos"></a>Tipos nativos para iOS y macOS

Mac y API de iOS usan tipos de datos específicos de la arquitectura que siempre son de 32 bits en plataformas de 32 bits y 64 bits en plataformas de 64 bits.

Por ejemplo, Objective-C se asigna el `NSInteger` tipo de datos que `int32_t` en sistemas de 32 bits y a `int64_t` en sistemas de 64 bits.

Para que coincida con este comportamiento, en nuestra API unificada, sustituiremos los usos anteriores de `int` (que en .NET se define como siempre que se va a `System.Int32`) a un nuevo tipo de datos: `System.nint`. Se puede considerar de las "n" significado "nativo", por lo que escriba el entero nativo de la plataforma.

Con estos nuevos tipos de datos, el mismo código fuente se compila para las arquitecturas de 32 bits y 64 bits, dependiendo de los marcadores de compilación.

## <a name="new-data-types"></a>Nuevos tipos de datos

La siguiente tabla muestra los cambios en nuestros tipos de datos para que coincida con este nuevo mundo de 32 o 64 bits:

|Tipo nativo|tipo de respaldo de 32 bits|tipo de respaldo de 64 bits|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

Elegimos esos nombres para permitir que su C# código para buscar más o menos la misma manera que tendría el aspecto hoy en día.

### <a name="implicit-and-explicit-conversions"></a>Conversiones implícitas y explícitas

El diseño de los nuevos tipos de datos está diseñado para permitir que una sola C# archivo de código fuente naturalmente uso del almacenamiento de bits 32 o 64 según la plataforma de host y la configuración de compilación.

Esto requería diseñar un conjunto de conversiones implícitas y explícitas a y desde los tipos de datos específicos de la plataforma para los tipos de datos enteros y de punto flotante. NET.

Se proporcionan los operadores de las conversiones implícitas cuando no hay ninguna posibilidad de pérdida de datos (valores de 32 bits que se almacenan en un espacio de 64 bits).

Operadores de las conversiones explícitas se proporcionan cuando hay un posible pérdida de datos (el valor de 64 bits se almacena en una ubicación de almacenamiento de 32 o potencialmente 32).

 `int`, `uint` y `float` son implícitamente convertible a `nint`, `nuint` y `nfloat` como 32 bits siempre quepan en 32 o 64 bits.

 `nint`, `nuint` y `nfloat` son implícitamente convertible a `long`, `ulong` y `double` como valores de bits 32 o 64 cabrán siempre en el almacenamiento de 64 bits.

Debe usar conversiones explícitas de `nint`, `nuint` y `nfloat` en `int`, `uint` y `float` puesto que los tipos nativos pueden contener de 64 bits de almacenamiento.

Debe usar conversiones explícitas de `long`, `ulong` y `double` en `nint`, `nuint` y `nfloat` puesto que los tipos nativos solo podrían ser capaz de contener 32 bits de almacenamiento.

## <a name="coregraphics-types"></a>Tipos de CoreGraphics

Los tipos de datos punto, el tamaño y el rectángulo que se usan con CoreGraphics usan 32 o 64 bits en función del dispositivo que se ejecutan.  Cuando se enlazan originalmente las API de Mac y iOS se usan estructuras de datos existentes que se produjeron para que coincida con los tamaños de la plataforma de host (los tipos de datos en `System.Drawing`).

Al pasar a **unificados**, deberá reemplazar las instancias de `System.Drawing` con sus `CoreGraphics` equivalentes tal como se muestra en la tabla siguiente:

|Tipo anterior en System.Drawing|Nuevo CoreGraphics del tipo de datos|Descripción|
|--- |--- |--- |
|`RectangleF`|`CGRect`|Contiene información del rectángulo de punto de flotante.|
|`SizeF`|`CGSize`|Información de tamaño (width, height) de puntos de suspensiones flotante|
|`PointF`|`CGPoint`|Contiene un punto flotante, seleccione información (X, Y)|

Los valores float antiguo utiliza los tipos de datos para almacenar los elementos de las estructuras de datos, mientras que uno usa el nuevo `System.nfloat`.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
