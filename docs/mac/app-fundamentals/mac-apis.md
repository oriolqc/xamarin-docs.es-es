---
title: macOS, las API para desarrolladores de Xamarin.Mac
description: Este documento describe cómo se leen los selectores de Objective-C y cómo buscar sus métodos de C# correspondientes en una aplicación de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/02/2017
ms.openlocfilehash: c387bbead1ac56d7f4c4c05a79c430302e50aec1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085287"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>macOS, las API para desarrolladores de Xamarin.Mac

## <a name="overview"></a>Información general

Para gran parte de su tiempo a desarrollar con Xamarin.Mac, puede pensar, leer y escribir en C# sin preocuparse de las API de Objective-C subyacente. Sin embargo, a veces podrá debe leer la documentación de API de Apple, traducir una respuesta de Stack Overflow para una solución para su problema o comparar con un ejemplo existente.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>Leer suficiente Objective-C para que sea peligroso

A veces será necesario leer una definición de Objective-C o método llamar y se traducen en el método equivalente de C#. Vamos a Eche un vistazo a una definición de función de Objective-C y desglosar los elementos. Este método (un *selector* en Objective-C) puede encontrarse en `NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

La declaración se puede leer de izquierda a derecha:

- El `-` prefijo significa que es un método de instancia (no estáticos). + significa que es un método de clase (estático)
- `(BOOL)` es el tipo de valor devuelto (bool en C#)
- `canDragRowsWithIndexes` es la primera parte del nombre.
- `(NSIndexSet *)rowIndexes` es el primer parámetro y con él tiene el tipo. El primer parámetro es el formato: `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` es el segundo parámetro y su tipo. Todos los parámetros después de la primera es el formato: `selectorPart:(Type) pararmName`
- Es el nombre completo de este selector de mensajes: `canDragRowsWithIndexes:atPoint:`. Tenga en cuenta el `:` al final: es importante.
- El enlace real de Xamarin.Mac C# es: `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Esta invocación del selector se puede leer la misma manera:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- La instancia `v` está teniendo su `canDragRowsWithIndexes:atPoint` selector llamado con dos parámetros, `set` y `point`, pasado.
- En C#, la invocación del método tiene este aspecto: `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Buscar al miembro de C# para un determinado selector

Ahora que ha descubierto el selector de Objective-C que se debe invocar, el siguiente paso es asignar el miembro equivalente de C#. Existen cuatro enfoques para probarlo (continuar con la `NSTableView CanDragRows` ejemplo):

1. Use la lista de finalización automática para buscar rápidamente algo del mismo nombre. Puesto que sabemos es una instancia de `NSTableView` puede escribir:

    - `NSTableView x;`
    - `x.` [ctrl + espacio si no aparece la lista).
    - `CanDrag` [Escriba]
    - Haga clic en el método, ir a declaración para abrir el Explorador de ensamblado en el que puede comparar la `Export` el selector en cuestión de atributo

2. Busque en el enlace de la clase completa. Puesto que sabemos es una instancia de `NSTableView` puede escribir:

    - `NSTableView x;`
    - Haga clic en `NSTableView`, vaya a la declaración para el Explorador de ensamblados
    - Busque el selector en cuestión

3. Puede usar el [documentación en línea de API de Xamarin.Mac](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0) .

4. Miguel proporciona una vista de "Rosetta piedra" de las APIs Xamarin.Mac [aquí](https://tirania.org/tmp/rosetta.html) que puede buscar a través de una API determinada. Si la API no es AppKit o específicas de macOS, es posible no existe.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
