---
title: macOS API para desarrolladores de Xamarin.Mac
description: Este documento describe cómo leer los selectores Objective-C y cómo encontrar sus correspondientes métodos de C# en una aplicación Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/02/2017
ms.openlocfilehash: cceaa2f6e89b712be5929f7e978663d8c47f18c5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791555"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>macOS API para desarrolladores de Xamarin.Mac

## <a name="overview"></a>Información general

Durante gran parte del tiempo Xamarin.Mac al desarrollo, puede considerar, lectura y escritura en C# sin preocuparse excesivamente con las API subyacentes de C de objetivo. Sin embargo, en ocasiones, la que necesita para leer la documentación de API de Apple, traducir una respuesta de desbordamiento de la pila a una solución para su problema o comparar con un ejemplo existente.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>Leer suficiente Objective-C para ser peligroso

A veces será necesario leer una definición de Objective-C o método llamar y se traducen en el método de C# equivalente. Vamos a echar un vistazo en una definición de función Objective-C y desglosar las partes. Este método (un *selector* en Objective-C) puede encontrarse en `NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

La declaración se puede leer de izquierda a derecha:

- El `-` prefijo significa que es un método de instancia (no estáticos). + significa que es un método de clase (estático)
- `(BOOL)` es el tipo de valor devuelto (bool en C#)
- `canDragRowsWithIndexes` es la primera parte del nombre.
- `(NSIndexSet *)rowIndexes` es el primer parámetro y con el tiene el tipo. El primer parámetro tiene el formato: `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` es el segundo parámetro y su tipo. Cada parámetro después de que el primero es el formato: `selectorPart:(Type) pararmName`
- El nombre completo de este selector de mensajes es: `canDragRowsWithIndexes:atPoint:`. Tenga en cuenta el `:` al final: es importante.
- El enlace real de Xamarin.Mac C# es: `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Esta invocación selector puede leerse de la misma manera:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- La instancia `v` tiene su `canDragRowsWithIndexes:atPoint` selector llamado con dos parámetros, `set` y `point`, que se pasó en.
- En C#, la invocación del método tiene el siguiente aspecto: `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Buscar al miembro de C# para un selector dado

Ahora que ha encontrado el selector de Objective-C que debe invocar, el siguiente paso es asignar para el miembro equivalente de C#. Existen cuatro enfoques que puede intentar (continuar con la `NSTableView CanDragRows` ejemplo):

1. Utilice la lista de finalización automática para buscar rápidamente algo del mismo nombre. Puesto que sabemos es una instancia de `NSTableView` puede escribir:

    - `NSTableView x;`
    - `x.` [ctrl + espacio si no aparece la lista).
    - `CanDrag` [Escriba]
    - Haga clic en el método, ir a declaración para abrir el Explorador de ensamblado en el que puede comparar la `Export` de atributo para el selector en cuestión

2. Busque en el enlace de clase completa. Puesto que sabemos es una instancia de `NSTableView` puede escribir:

    - `NSTableView x;`
    - Haga clic en `NSTableView`, ir a declaración al explorador de ensamblado
    - Busque el selector en cuestión

3. Puede usar el [documentación en línea de la API de Xamarin.Mac](https://developer.xamarin.com/api/root/monomac-lib/) .

4. Miguel proporciona una vista "Rosetta piedra" de las APIs Xamarin.Mac [aquí](http://tirania.org/tmp/rosetta.html) que puede buscar a través de una API determinada. Si la API no es AppKit o macOS específica, es posible no existe.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
