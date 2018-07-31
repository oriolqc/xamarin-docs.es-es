---
title: Métodos abreviados de teclado del Editor de libros de Xamarin
description: Este documento describe los métodos abreviados de teclado disponibles para su uso en el editor de Xamarin Workbooks. En concreto, examina varias formas, que se utiliza la tecla ENTRAR.
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: c2b4a8c1bcb8f7b88ab2ae1e2906b1c9c702b76a
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351683"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Métodos abreviados de teclado del Editor de libros de Xamarin

## <a name="the-return-key-and-its-nuances"></a>Devolver clave y sus matices

La tabla siguiente describe los distintos enlaces de teclado para ejecutar el código y la creación de markdown. Hemos tomado mucho cuidado para elegir los enlaces de teclado coherentes y razonables que están familiarizados y fluida.

|Enlace de teclado|Celda de código|Celda de markdown|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>Si el símbolo de intercalación está al final del búfer de celda y se puede analizar correctamente la celda, se ejecutará y los resultados se mostrarán a continuación el búfer y una nueva celda de código se insertará y centra la celda después de la celda ejecutada.</p><p>Si el análisis no es correcta, se insertará una nueva línea en el búfer. Diagnósticos de compilador no se producirá si el análisis no es correcta.</p>|<p><kbd>Devolver</kbd> exhibe un comportamiento diferente según el contexto de Markdown en el símbolo de intercalación.</p><ul><li>Si el símbolo de intercalación está en un bloque de código de Markdown, se inserta una nueva línea literal.</li><li>Si el símbolo de intercalación está en un bloque de la lista de Markdown, crear un nuevo elemento de lista o dividir el elemento de lista actual.</li><li>Si el símbolo de intercalación está en cualquier otro tipo de bloque de Markdown, crear un nuevo bloque de párrafo o divida el bloque actual.</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Command‑return</kbd></dd><dt>Win</dt><dd><kbd>Control‑Return</kbd></dd></dl>|<p>Siempre intenta analizar y ejecutar el contenido de la celda. Si la compilación es correcta, se mostrará a continuación el búfer de resultados (incluidas las excepciones de ejecución), y si no hay ninguna celda posteriores, se creará uno nuevo y se centra.</p><p>Si hay errores de compilación, se mostrará el diagnóstico y seguiremos enfocado el búfer con la posición del símbolo de intercalación sin cambios.</p>|Inserta y se centra una nueva celda de código después de la celda actual de markdown.|
|<dl><dt>Mac</dt><dd><kbd>Command‑Shift‑return</kbd><dd><dt>Win</dt><dd><kbd>Control‑Shift‑return</kbd></dd></dl>|Inserta y se centra una nueva celda de markdown después de la celda actual.|Mismo comportamiento que <kbd>devolver</kbd>|
|<kbd>Shift‑return</kbd>|Siempre inserta una nueva línea, independientemente de la ubicación del símbolo de intercalación o contenido.|Inserta un salto de línea de duro dentro del bloque de marcado actual.|
