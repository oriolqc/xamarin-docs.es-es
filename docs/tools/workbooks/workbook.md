---
title: Libros interactivos
description: "Usar los libros para crear documentos en vivo con código C# para experimentar, enseñanza, entrenamiento o explorar."
ms.topic: article
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: bd53074fcde25736d37544efc719ecef1110c364
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="interactive-workbooks"></a>Libros interactivos

_Usar los libros para crear documentos en vivo con código C# para experimentar, enseñanza, entrenamiento o explorar._

Puede usar los libros como una aplicación independiente, independiente desde el IDE.

Para empezar a crear un nuevo libro, ejecute la aplicación de los libros. Si no ha instalado este ya, visite la [instalación](~/tools/workbooks/install.md#install) página. Se le pedirá que cree un libro en la plataforma de elección, que se conectará automáticamente a una aplicación de agente, lo que le permite visualizar el documento en tiempo real.

Si ya se está ejecutando la aplicación de los libros, puede crear un nuevo documento examinando **archivo > nuevo**.

Los libros se pueden guardar y abrir de nuevo más tarde dentro de la aplicación. A continuación, puede compartirlos con otras personas para mostrar ideas, explorar nuevas API o enseñar a nuevos conceptos.

## <a name="code-editing"></a>Edición de código

La ventana de edición de código proporciona la finalización de código, colorear la sintaxis, alineado live-diagnósticos y compatibilidad de instrucción de varias líneas.

[ ![](workbook-images/inspector-0.6.0-repl-small.png "La ventana de edición de código proporciona la finalización de código, colorear la sintaxis, alineado live-diagnósticos y compatibilidad de instrucción de varias líneas")](workbook-images/inspector-0.6.0-repl.png)

Los libros de Xamarin se guardan en un `.workbook` archivo, que es un archivo CommonMark con algunos metadatos en la parte superior (vea [tipos de archivo de los libros](#Workbooks_Files_Types) para obtener más detalles sobre cómo se pueden guardar libros).

### <a name="nuget-package-support"></a>Compatibilidad con paquetes de NuGet

Muchos paquetes de NuGet populares se admiten directamente en los libros de Xamarin. Puede buscar paquetes a través de **archivo > Agregar paquete**. Adición de un paquete aparecerá automáticamente `#r` las instrucciones que hacen referencia a ensamblados de paquete, lo que le permite utilizarlas inmediatamente.

Cuando se guarda un libro con las referencias del paquete, se guardan también esas referencias. Si el libro se comparte con otra persona, descargará automáticamente los paquetes que se hace referencia.

Existen algunas limitaciones conocidas con compatibilidad con paquetes de NuGet en los libros:

  * Bibliotecas nativas se admiten únicamente en iOS y solo cuando se vinculan a la biblioteca administrada.
  * Paquetes que dependen de `.targets` archivos o secuencias de comandos de PowerShell probablemente no funcionará según lo previsto.
  * Para quitar o modificar una dependencia del paquete, edite el manifiesto del libro con un editor de texto. Administración de paquetes correcta es la manera en que.

### <a name="xamarinforms-support"></a>Compatibilidad con Xamarin.Forms

Si el paquete Xamarin.Forms NuGet hace referencia en el libro, la aplicación de libro cambiará su vista principal para basarse en Xamarin.Forms. Puede obtener acceso a él a través de `Xamarin.Forms.Application.Current.MainPage`.

La ficha Vista Inspector también ofrece una compatibilidad especial para mostrar la jerarquía de vista de Xamarin.Forms para ayudarle a entender los diseños.

## <a name="rich-text-editing"></a>Edición de texto enriquecido

Puede editar el texto en el código mediante el editor de texto enriquecido incluido, como se muestra a continuación:

![](workbook-images/inspector-0.6.2-editing.gif "Editar el texto alrededor del código con el editor de texto enriquecido integrados")

### <a name="markdown-authoring"></a>Creación de marcado

Los autores de libros a veces resultará más fácil editar directamente el CommonMark "origen" del libro con su editor favorito.

Tenga en cuenta que si, a continuación, edite y guarde el libro en el cliente de los libros, el texto de CommonMark puede cambiar el formato.

Tenga en cuenta que debido a la extensión CommonMark utilizamos para habilitar los metadatos YAML en archivos de libro, `---` está reservado para ese fin. Si desea volver a crear [saltos temáticos](http://spec.commonmark.org/0.27/#thematic-break) en el texto, se debe usar `***` o `___` en su lugar. Estos saltos deberían evitarse en los libros 1.2 y versiones anteriores debido a un error durante la guarde.

### <a name="improvements-in-workbooks-13"></a>Mejoras en los libros 1.3

Además, hemos ampliado la sintaxis de oferta de descuento bloque ligeramente para mejorar la presentación. Al agregar un emoji como el primer carácter de la oferta de bloque, puede influir en el color de fondo de la oferta:

- `> [!NOTE]
>' se representará como una nota con un fondo azul
- `> [!IMPORTANT]
>' se representará como una advertencia con un fondo amarillo
- `> [!WARNING]
>' se representarán como un problema con un fondo rojo

También puede vincular a los encabezados en el documento de libro. Delimitadores para cada encabezado, se generan con el identificador de delimitador que se va al texto del encabezado, procesado del siguiente modo:

1. El encabezado es inferior a las mayúsculas y minúsculas.
1. Se quitan todos los caracteres excepto los caracteres alfanuméricos y guiones.
1. Todos los espacios se sustituyen por guiones.

Esto significa que un encabezado como "Encabezado importante" Obtiene un identificador de `important-header` y se pueden vincular a mediante la inserción de un vínculo a `#important-header` en el libro.

## <a name="document-structure"></a>Estructura del documento

### <a name="cell"></a>Celda

Unidad discreta de contenido, que representa código ejecutable o marcado. Una celda de código consta de hasta cuatro componentes secundarios:

- Editor
  - Búfer
- Diagnóstico de compilador
- Salida de la consola
- Resultados de la ejecución

### <a name="editor"></a>Editor

El componente de texto interactivo de una celda. Para las celdas de código, se trata el editor de código real con el resaltado de sintaxis, etcetera. Para las celdas de marcado se trata de un editor de contenido de texto enriquecido con un formato sensible al contexto y barra de herramientas de creación.

### <a name="buffer"></a>Búfer
El contenido de texto real de un editor.

### <a name="compiler-diagnostics"></a>Diagnóstico de compilador

Los diagnósticos se producen cuando se compila código, presentan solo cuando se solicita la ejecución explícita. Aparece inmediatamente debajo del editor de la celda.

### <a name="console-output"></a>Salida de la consola

Ningún resultado escrito para salida normal o de error estándar durante la ejecución de la celda. Salida normal se representará en texto negro y error estándar se representará en texto rojo.

### <a name="execution-results"></a>Resultados de la ejecución

Enriquecida e interactivas potencialmente representaciones de los resultados de una celda se representará en la compilación correcta, siempre que un resultado realmente generado por la ejecución. Las excepciones se consideran resultados en este contexto, ya que se generan como resultado de ejecutar realmente la compilación.

## <a name="workbooks-files-types"></a>Tipos de archivos de libros

### <a name="plain-files"></a>Archivos sin formato

De forma predeterminada, se guarda un libro como texto sin formato `.workbook` archivo que contiene el texto con formato CommonMark.

### <a name="packages"></a>Paquetes

Un paquete de libro es un directorio que se denomina con el `.workbook` extensión.
En el buscador de Mac y en el cuadro de diálogo Abrir los libros de Xamarin y el menú de archivos recientes, este directorio se reconozca como si fuera un archivo.

El directorio debe contener una `index.workbook` archivo, que es el libro de texto sin formato real que se cargará en los libros de Xamarin. El directorio también puede contener recursos necesarios para `index.workbook`, como imágenes u otros archivos.

Si un texto sin formato `.workbook` archivo que haga referencia a los recursos de su mismo directorio se abre en los libros 0.99.3 o versiones posteriores, cuando se guarda, se convertirá en un `.workbook` paquete. Esto es cierto en Mac y Windows.

> [!NOTE]
> **Nota:** los usuarios de Windows se abrirán el `package.workbook\index.workbook` archivo directamente, pero en caso contrario, el paquete comportará igual que en el equipo Mac.

### <a name="archives"></a>Archivos

Paquetes de libro, que se va a directorios, pueden ser difíciles distribuir fácilmente a través de internet. La solución es archivos de libro. Un archivo de libro es un paquete de compresión zip libro, denominado con el `.workbook` extensión.

A partir de los libros 1.1, cuando se guarda un paquete de libro, el cuadro de diálogo Guardar ofrece la posibilidad de guardar como un archivo en su lugar. Los libros 1.0 no tenían ningún medio integrado de crear o guardar archivos.

En los libros 1.0, al abre un archivo de libro, se convirtió de forma transparente en un paquete de libro y se perdió el archivo zip. En los libros 1.1, sigue siendo el archivo zip. Cuando el usuario guarda el archivo, se reemplaza con un nuevo archivo zip.

Puede crear un archivo de libro manualmente haciendo clic en un paquete de libro y seleccionar **comprimir** en Mac, o **enviar a > carpeta comprimida (zip)** en Windows. A continuación, cambiar el nombre del archivo zip para que tenga un `.workbook` la extensión de archivo. Esto solo funciona con paquetes de libro, archivos de libro no sin formato.

## <a name="related-links"></a>Vínculos relacionados

- [Bienvenido a los libros](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
