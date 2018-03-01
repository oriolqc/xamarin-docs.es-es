---
title: "Los libros Xamarin Editor de métodos abreviados de teclado"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: cbbf070a9c94221d98dedcd1df884a897ff7df3e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Los libros Xamarin Editor de métodos abreviados de teclado

## <a name="the-return-key-and-its-nuances"></a>La clave devuelto y sus matices

La tabla siguiente describen los distintos enlaces de clave para la ejecución del código y marcado de creación. Le hemos llevado mucho cuidado para elegir razonables y coherentes enlaces de clave que le resulte familiar y fluida.

<table>
  <tr>
    <th>Enlace de teclado</th>
    <th>Celda de código</th>
    <th>Markdown Cell</th>
  </tr>
  <tr>
    <td><kbd>Return</kbd></td>
    <td>
      <p>Si el símbolo de intercalación está al final del búfer de celda y la celda se puede analizar correctamente, se ejecutará y resultados se mostrarán a continuación el búfer y una nueva celda de código se insertará y centrada celda después de la celda ejecutada.</p>
      
      <p>Si el análisis no es correcta, se insertará una nueva línea en el búfer. Diagnóstico de compilador no se producirá si el análisis no es correcta.</p>
    </td>
    <td>
      <p>
        <kbd>Devolver</kbd> exhibe un comportamiento diferente en función del contexto de marcado en el símbolo de intercalación.
      </p>
      <ul>
        <li>
Si el símbolo de intercalación está en un bloque de código de marcado, se inserta una nueva línea literal.
        </li>
        <li>
Si el símbolo de intercalación está en un bloque de la lista de marcado, crear un nuevo elemento de lista o divida el elemento de lista actual.
        </li>
        <li>
Si el símbolo de intercalación está en cualquier otro tipo de bloque de marcado, crear un nuevo bloque de párrafo o divida el bloque actual.
        </li>
    </td>
  </tr>
  <tr>
    <td>
      <dl>
        <dt>Mac</dt>
        <dd><kbd>Command‑return</kbd></dd>
        <dt>Win</dt>
        <dd><kbd>Control‑Return</kbd></dd>
      </dl>
    </td>
    <td>
      <p>Siempre intenta analizar y ejecutar el contenido de la celda. Si la compilación se realiza correctamente, se mostrará a continuación el búfer de resultados (incluidas las excepciones de ejecución), y si no hay ninguna celda posterior, una nueva se creará y centrada.</p>
      
      <p>Si hay algún error de compilación, se mostrarán diagnósticos y el búfer seguirá perteneciendo tiene el foco a la posición del símbolo de intercalación sin cambios.</p>
    </td>
    <td>
Inserta y se centra una nueva celda de código después de la celda actual de marcado.
    </td>
  </tr>
  <tr>
    <td>
      <dl>
        <dt>Mac</dt>
        <dd><kbd>Command‑Shift‑Return</kbd></dd>
        <dt>Win</dt>
        <dd><kbd>Control‑Shift‑Return</kbd></dd>
      </dl>
    </td>
    <td>
Inserta y se centra una nueva celda marcado después de la celda actual.
    </td>
    <td>
Mismo comportamiento que <kbd>devolver</kbd>
    </td>
  </tr>
  <tr>
    <td><kbd>Shift‑Return</kbd></td>
    <td>
Siempre inserta una nueva línea, independientemente de la ubicación del símbolo de intercalación o el contenido.
    </td>
    <td>
Inserta un salto de línea de duro dentro del bloque actual de marcado.
    </td>
  </tr>
</table>
