---
title: Los libros Xamarin Editor de métodos abreviados de teclado
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 1258a40a527ab47cee78b17454ac818e53c60ad2
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Los libros Xamarin Editor de métodos abreviados de teclado

## <a name="the-return-key-and-its-nuances"></a>La clave devuelto y sus matices

La tabla siguiente describen los distintos enlaces de clave para la ejecución del código y marcado de creación. Le hemos llevado mucho cuidado para elegir razonables y coherentes enlaces de clave que le resulte familiar y fluida.

|Enlace de teclado|Celda de código|Celda de marcado|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>Si el símbolo de intercalación está al final del búfer de celda y la celda se puede analizar correctamente, se ejecutará y resultados se mostrarán a continuación el búfer y una nueva celda de código se insertará y centrada celda después de la celda ejecutada.</p><p>Si el análisis no es correcta, se insertará una nueva línea en el búfer. Diagnóstico de compilador no se producirá si el análisis no es correcta.</p>|<p><kbd>Devolver</kbd> exhibe un comportamiento diferente en función del contexto de marcado en el símbolo de intercalación.</p><ul><li>Si el símbolo de intercalación está en un bloque de código de marcado, se inserta una nueva línea literal.</li><li>Si el símbolo de intercalación está en un bloque de la lista de marcado, crear un nuevo elemento de lista o divida el elemento de lista actual.</li><li>Si el símbolo de intercalación está en cualquier otro tipo de bloque de marcado, crear un nuevo bloque de párrafo o divida el bloque actual.</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Command‑return</kbd></dd><dt>Win</dt><dd><kbd>Control‑Return</kbd></dd></dl>|<p>Siempre intenta analizar y ejecutar el contenido de la celda. Si la compilación se realiza correctamente, se mostrará a continuación el búfer de resultados (incluidas las excepciones de ejecución), y si no hay ninguna celda posterior, una nueva se creará y centrada.</p><p>Si hay algún error de compilación, se mostrarán diagnósticos y el búfer seguirá perteneciendo tiene el foco a la posición del símbolo de intercalación sin cambios.</p>|Inserta y se centra una nueva celda de código después de la celda actual de marcado.|
|<dl><dt>Mac</dt><dd><kbd>Command‑Shift‑return</kbd><dd><dt>Win</dt><dd><kbd>Control‑Shift‑return</kbd></dd></dl>|Inserta y se centra una nueva celda marcado después de la celda actual.|Mismo comportamiento que <kbd>devolver</kbd>|
|<kbd>Shift‑return</kbd>|Siempre inserta una nueva línea, independientemente de la ubicación del símbolo de intercalación o el contenido.|Inserta un salto de línea de duro dentro del bloque actual de marcado.|
