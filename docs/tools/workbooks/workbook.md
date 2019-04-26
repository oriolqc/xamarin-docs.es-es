---
title: Libros interactivos
description: Este documento describe cómo usar Xamarin Workbooks para crear documentos en vivo que contengan C# código para experimentar, enseñanza, aprendizaje o exploración.
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 71f46535ffd0a99ad78acb8f0e3bbc5870abf33e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61197961"
---
# <a name="interactive-workbooks"></a>Libros interactivos

Puede usar los libros como una aplicación independiente, separada de su IDE.

Para empezar a crear un nuevo libro, ejecute la aplicación Workbooks. Si no ha instalado esto ya, visite la [instalación](~/tools/workbooks/install.md#install) página. Se le pedirá que cree un libro en la plataforma de elección, que se conectará automáticamente a una aplicación de agente, lo que le permite visualizar el documento en tiempo real.

Si ya se está ejecutando la aplicación Workbooks, puede crear un nuevo documento examinando **archivo > nuevo**.

Los libros se pueden guardar y abrir de nuevo más tarde dentro de la aplicación. A continuación, puede compartirlos con otras personas para demostrar ideas, explore las nuevas API o enseñar a los nuevos conceptos.

## <a name="code-editing"></a>Edición de código

La ventana de edición de código proporciona la finalización de código, colores de sintaxis, inline live-diagnostics y compatibilidad de las instrucciones de varias líneas.

[ ![](workbook-images/inspector-0.6.0-repl-small.png "La ventana de edición de código proporciona la finalización de código, colores de sintaxis, inline live-diagnostics y compatibilidad de las instrucciones de varias líneas")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin Workbooks se guardan en un `.workbook` archivo, que es un archivo CommonMark con algunos metadatos en la parte superior (consulte [tipos de archivos de libros](#workbooks-files-types) para obtener más detalles sobre cómo se pueden guardar libros).

### <a name="nuget-package-support"></a>Compatibilidad con paquetes de NuGet

Se admiten muchos paquetes conocidos de NuGet directamente en Xamarin Workbooks. Puede buscar paquetes examinando **archivo > Agregar paquete**. Adición de un paquete introducirá automáticamente `#r` las instrucciones que hacen referencia a los ensamblados del paquete, lo que le permite usar de inmediato.

Cuando se guarda un libro con las referencias de paquete, se guardan también esas referencias. Si el libro se comparte con otra persona, se descargarán automáticamente los paquetes que se hace referencia.

Hay algunas limitaciones conocidas con compatibilidad con paquete de NuGet en los libros:

- Las bibliotecas nativas se admiten solo en iOS y solo cuando se vincula con la biblioteca administrada.
- Los paquetes que dependen de `.targets` archivos o secuencias de comandos de PowerShell probablemente no funcionará según lo previsto.
- Para quitar o modificar una dependencia del paquete, edite el manifiesto del libro con un editor de texto. Administración de paquetes correcto está en camino.

### <a name="xamarinforms-support"></a>Compatibilidad de Xamarin.Forms

Si hace referencia el paquete Xamarin.Forms NuGet en su libro, la aplicación de libro cambiará su vista principal para que esté basado en Xamarin.Forms. Puede tener acceso a él a través de `Xamarin.Forms.Application.Current.MainPage`.

La ficha Vista Inspector también tiene compatibilidad especial para mostrar la jerarquía de vistas de Xamarin.Forms que le ayudarán a comprender sus diseños.

## <a name="rich-text-editing"></a>Rich Text Editing

Puede editar el texto en torno a su código mediante el editor de texto enriquecido que incluye, como se muestra a continuación:

![](workbook-images/inspector-0.6.2-editing.gif "Editar el texto en torno al código mediante el editor de texto enriquecido integrados")

### <a name="markdown-authoring"></a>Creación de markdown

Los autores de libros a veces, resultará más fácil editar directamente el CommonMark "origen" del libro con su editor favorito.

Tenga en cuenta que si, a continuación, edite y guarde el libro en el cliente de los libros, el texto CommonMark puede volver a formatear.

Tenga en cuenta que debido a la extensión CommonMark se usa para habilitar los metadatos YAML en archivos de libro, `---` está reservado para ese propósito. Si desea crear [saltos temáticos](http://spec.commonmark.org/0.27/#thematic-break) en el texto, se debe usar `***` o `___` en su lugar. Estos saltos deben evitarse en los libros 1.2 y anteriormente debido a un error durante la guarde.

### <a name="improvements-in-workbooks-13"></a>Mejoras en los libros 1.3

Hemos ampliado también la sintaxis de comillas de bloque de Markdown ligeramente para mejorar la presentación. Al agregar un emoji como el primer carácter de la cita en bloque, puede influir en el color de fondo de la oferta:

- `> [!NOTE]`
    > se representará como una nota con un fondo azul
- `> [!IMPORTANT]`
    > se representará como una advertencia con un fondo amarillo
- `> [!WARNING]`
    > se representará como un problema con un fondo rojo

También puede vincular a los encabezados del documento del libro. Delimitadores para cada encabezado, se generan con el identificador de delimitador está el texto del encabezado, procesado como sigue:

1. El encabezado está en minúsculas.
1. Se quitan todos los caracteres excepto los caracteres alfanuméricos y guiones.
1. Todos los espacios se sustituyen por guiones.

Esto significa que un encabezado, como "Importante Header" Obtiene un identificador de `important-header` y se pueden vincular mediante la inserción de un vínculo a `#important-header` en el libro.

## <a name="document-structure"></a>Estructura del documento

### <a name="cell"></a>celda

Unidad discreta de contenido, que representa el código ejecutable o markdown. Una celda de código está formada por un máximo de cuatro subcomponentes:

- Editor
  - Búfer
- Diagnósticos de compilador
- Salida de la consola
- Resultados de la ejecución

### <a name="editor"></a>Editor

El componente de texto interactivo de una celda. Para las celdas de código, este es el editor de código real con el resaltado de sintaxis, etcetera. Para las celdas de markdown es un editor de texto enriquecido contenido con un formato sensible al contexto y la barra de herramientas de creación.

### <a name="buffer"></a>Búfer

El contenido de un editor de texto reales.

### <a name="compiler-diagnostics"></a>Diagnósticos de compilador

Los diagnósticos se generan al compilar el código, presentan solo cuando se solicita la ejecución explícita. Se muestra inmediatamente debajo del editor de la celda.

### <a name="console-output"></a>Salida de la consola

Cualquier salida se escriben en la salida estándar o un error estándar durante la ejecución de la celda. Salida estándar que se representará en texto negro y error estándar se representará en texto rojo.

### <a name="execution-results"></a>Resultados de la ejecución

Enriquecida e interactivas potencialmente representaciones de los resultados de una celda se representará tras la compilación se realizó correctamente, siempre que un resultado realmente generado por la ejecución. Las excepciones se consideran los resultados en este contexto, ya que se producen como resultado de ejecutar realmente la compilación.

## <a name="workbooks-files-types"></a>Tipos de archivos de libros

### <a name="plain-files"></a>Archivos sin formato

De forma predeterminada, se guarda un libro como texto sin formato `.workbook` archivo que contiene el texto con formato CommonMark.

### <a name="packages"></a>Paquetes

Un paquete de libro es un directorio que se denomina con el `.workbook` extensión.
En Finder de Mac y en el cuadro de diálogo Abrir Xamarin Workbooks y el menú de archivos recientes, este directorio se reconocerá como si fuese un archivo.

El directorio debe contener un `index.workbook` archivo, que es el libro de texto sin formato real que se cargarán en Xamarin Workbooks. El directorio también puede contener recursos requeridos por `index.workbook`, incluidas imágenes u otros archivos.

Si un texto sin formato `.workbook` se abre el archivo que hace referencia a los recursos desde el mismo directorio en los libros 0.99.3 o versiones posteriores, cuando se guarda, se convertirá en un `.workbook` paquete. Esto es cierto en Mac y Windows.

> [!NOTE]
> Los usuarios de Windows se abrirán el `package.workbook\index.workbook` archivo directamente, pero en caso contrario, el paquete comportará igual que en el equipo Mac.

### <a name="archives"></a>Archivos de almacenamiento

Paquetes de libro, que se va a directorios, pueden ser difíciles distribuir fácilmente a través de internet. La solución es archivos de libro. Un archivo de libro es un paquete comprimido en zip libro, denominado con el `.workbook` extensión.

A partir de los libros 1.1, al guardar un paquete de libro, el cuadro de diálogo Guardar ofrece la posibilidad de guardar como un archivo en su lugar. Libros 1.0 tenían ninguna manera integrada de crear o guardar archivos.

En los libros 1.0, cuando se abrió un archivo de libro, se convirtió en forma transparente en un paquete de libro y se ha perdido el archivo zip. En los libros 1.1, sigue siendo el archivo zip. Cuando el usuario guarda el archivo, se reemplaza con un nuevo archivo zip.

Puede crear un archivo de libro manualmente haciendo clic en un paquete de libro y seleccionar **comprimir** en Mac, o **enviar a > carpeta comprimida (zip)** en Windows. A continuación, cambie el nombre el archivo zip para que tenga un `.workbook` la extensión de archivo. Esto solo funciona con los paquetes de libro, archivos de libro no estándar.

## <a name="related-links"></a>Vínculos relacionados

- [Bienvenido a los libros](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
