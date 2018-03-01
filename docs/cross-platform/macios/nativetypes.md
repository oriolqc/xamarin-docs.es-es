---
title: Tipos nativos
ms.topic: article
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: b78ade19efed92ab3b2d8ba790f2d7334472bab4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="native-types"></a>Tipos nativos

En el núcleo de la diferencia, Mac y API de iOS usan un tipo de datos específicos de la arquitectura que siempre es de 32 bits en plataformas de 32 bits y 64 bits en plataformas de 64 bits.

Por ejemplo, asigna Objective-c. el `NSInteger` tipo de datos que `int32_t` en sistemas de 32 bits y a `int64_t` en sistemas de 64 bits.

Para que coincida con este comportamiento, en nuestra API unificada, sustituiremos los usos anteriores de `int` (que en .NET se define como siempre que se va a `System.Int32`) a un nuevo tipo de datos: `System.nint`.  Se puede considerar "n" como "nativos", para escribir el tipo entero nativo de la plataforma.

Con estos nuevos tipos de datos, el mismo código fuente se compila para 32 bits, 32 bits y 64 bits o 64 bits, dependiendo de los marcadores de compilación.

## <a name="new-data-types"></a>Nuevos tipos de datos

La siguiente tabla muestra los cambios en nuestros tipos de datos para que coincida con este nuevo mundo de 32 o 64 bits:

<table>
        <tr>
            <th>Tipo nativo</th>
            <th>tipo de respaldo de 32 bits</th> 
            <th>tipo de respaldo de 64 bits</th>
        </tr>
        <tr>
            <td><code>System.nint</code></td>
        <td><code>System.Int32</code> (<code>int</code>)</td>
        <td><code>System.Int64</code> (<code>long</code>)</td>
        </tr>
        <tr>
            <td><code>System.nuint</code></td>
        <td><code>System.UInt32</code> (<code>uint</code>)</td>
        <td><code>System.UInt64</code> (<code>ulong</code>)</td>
        </tr>
        <tr>
            <td><code>System.nfloat</code></td>
        <td><code>System.Single</code> (<code>float</code>)</td>
        <td><code>System.Double</code> (<code>double</code>)</td>
        </tr>
    </table>

Elegimos esos nombres para permitir que el código de C# buscar más o menos la misma manera que tendría el aspecto hoy en día.

### <a name="implicit-and-explicit-conversions"></a>Conversiones implícitas y explícitas

El diseño de los nuevos tipos de datos está diseñado para permitir que un único archivo de origen C# natural usar el almacenamiento de bits de 32 ó 64 según la plataforma de host y la configuración de compilación.

Esto requiere que nos permite diseñar un conjunto de conversiones implícitas y explícitas a y desde los tipos de datos específicos de la plataforma para los tipos de datos enteros y de punto flotante. NET.

Operadores de las conversiones implícitas se proporcionan cuando no hay ninguna posibilidad de pérdida de datos (valores de 32 bits que se almacena en un espacio de 64 bits).

Operadores de las conversiones explícitas se proporcionan cuando hay un posible pérdida de datos (valor de 64 bits se almacena en una ubicación de almacenamiento de 32 o potencialmente 32).

 `int`, `uint` y `float` todas son implícitamente convertible a `nint`, `nuint` y `nfloat` como 32 bits siempre quepan en 32 o 64 bits.

 `nint`, `nuint` y `nfloat` todas son implícitamente convertible a `long`, `ulong` y `double` como valores de bit de 32 ó 64 siempre quepa en el almacenamiento de 64 bits.

Debe utilizar conversiones explícitas de `nint`, `nuint` y `nfloat` en `int`, `uint` y `float` porque los tipos nativos pueden retener 64 bits del almacenamiento.

Debe utilizar conversiones explícitas de `long`, `ulong` y `double` en `nint`, `nuint` y `nfloat` porque los tipos nativos solo puede contener 32 bits de almacenamiento.

## <a name="coregraphics-types"></a>Tipos de CoreGraphics

Los tipos de datos de punto, el tamaño y el rectángulo que se usan con CoreGraphics usan 32 o 64 bits en función del dispositivo que se ejecutan en.  Cuando se enlazan originalmente iOS y Mac API se usan estructuras de datos existentes que se produjeron para que coincida con los tamaños de la plataforma de host (los tipos de datos en `System.Drawing`).

Al mover a **unificado**, deberá reemplazar instancias de `System.Drawing` con sus `CoreGraphics` homólogos tal como se muestra en la tabla siguiente:

<table>
        <tr>
            <th>Tipo anterior en System.Drawing</th>
            <th>CoreGraphics de tipo de datos nuevo</th> 
            <th>Descripción</th>
        </tr>
        <tr>
        <td><code>RectangleF</code></td>
        <td><code>CGRect</code></td>
        <td>Contiene flotante información del punto de rectángulo.  </td>
        </tr>
        <tr>
        <td><code>SizeF</code></td>
        <td><code>CGSize</code></td>
        <td>Punto de suspensiones flotante información de tamaño (ancho, alto)</td>
        </tr>
        <tr>
        <td><code>PointF</code></td>
        <td><code>CGPoint</code></td>
        <td>Contiene un punto flotante, seleccione información (X, Y)</td>
        </tr>
    </table>

El antiguo flotantes de tipos que se utilizan datos para almacenar los elementos de las estructuras de datos, mientras el nuevo uno usa `System.nfloat`.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Diferencias de API unificada de vs clásicos](http://developer.xamarin.comhttps://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
