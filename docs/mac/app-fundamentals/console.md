---
title: Uso de enlaces de Xamarin.Mac para las aplicaciones de consola
description: Crear una aplicación de consola sin periféricos con Xamarin.Mac para tener acceso a las API de macOS nativo.
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: 135ef06cd044235023c3acc970c8e7a33f144b47
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378281"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>Enlaces de Xamarin.Mac en aplicaciones de consola

Hay algunos escenarios donde desea usar algunas de las API nativas de Apple en C# para compilar una aplicación sin periféricos &ndash; uno que no tiene una interfaz de usuario &ndash; con C#.

Las plantillas de proyecto para las aplicaciones de Mac incluyen una llamada a `NSApplication.Init()` seguido por una llamada a `NSApplication.Main(args)`, normalmente este aspecto:

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

La llamada a `Init` prepara el tiempo de ejecución de Xamarin.Mac, la llamada a `Main(args)` se inicia el bucle principal de aplicación Cocoa, que prepara la aplicación para recibir eventos de teclado y mouse (ratón) y mostrar la ventana principal de la aplicación.   La llamada a `Main` también intentará localizar recursos de Cocoa, prepare una ventana de nivel superior y espera que el programa para formar parte de un conjunto de aplicaciones (programas distribuidos en un directorio con el `.app` extensión y un diseño muy específico).

Equipos sin periféricos de aplicaciones no necesitan un usuario de la interfaz y no es necesario ejecutar como parte de un conjunto de aplicaciones.

## <a name="creating-the-console-app"></a>Creación de la aplicación de consola

Por lo que es mejor empezar con un tipo de proyecto de consola de .NET normal.

Deberá hacer algunas cosas:

- Crear un proyecto vacío.
- Referencia a la biblioteca de Xamarin.Mac.dll.
- Ponga la dependencia no administrada al proyecto.

Estos pasos se explican más detalladamente a continuación:

### <a name="create-an-empty-console-project"></a>Crear un proyecto vacío de la consola

Cree un nuevo proyecto de consola .NET, asegúrese de que es .NET y no de .NET Core, como Xamarin.Mac.dll no se ejecuta en el tiempo de ejecución de .NET Core, solo se ejecuta con el tiempo de ejecución Mono.

### <a name="reference-the-xamarinmac-library"></a>Referencia a la biblioteca de Xamarin.Mac

Para compilar el código, desea hacer referencia a la `Xamarin.Mac.dll` ensamblado desde este directorio: `/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

Para ello, vaya a las referencias del proyecto, seleccione el **ensamblado .NET** ficha y haga clic en el **examinar** para buscar el archivo en el sistema de archivos.  Vaya a la ruta de acceso anterior y, a continuación, seleccione el **Xamarin.Mac.dll** desde ese directorio.

Esto le dará acceso a las API de Cocoa en tiempo de compilación.   En este momento, puede agregar `using AppKit` a la parte superior del archivo y llamada la `NSApplication.Init()` método.   Hay solo un paso más para poder ejecutar la aplicación.

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>Incluya la biblioteca de compatibilidad no administrada en su proyecto

Antes de que se ejecutará la aplicación, debe poner el `Xamarin.Mac` biblioteca de compatibilidad en el proyecto.   Para ello, agregue un nuevo archivo al proyecto (en Opciones de proyecto, seleccione **agregar**y, a continuación, **Agregar archivo existente**) y vaya a este directorio:

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

En este caso, seleccione el archivo **libxammac.dylib**.   Se le ofrecerá una opción de copiar, vinculación o mover.   Personalmente gusta la vinculación, pero también funciona con la copia.    Deberá seleccionar el archivo y en el panel de propiedades (seleccione **Vista > paneles > propiedades** si no está visible el panel de propiedades), vaya a la **compilar** sección y establezca el **copiar en la salida Directorio** si se establece en **copiar si es posterior**.

Ahora puede ejecutar la aplicación de Xamarin.Mac.

El resultado en el directorio bin tendrá este aspecto:

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

Para ejecutar esta aplicación, necesitará todos estos archivos en el mismo directorio.

## <a name="building-a-standalone-application-for-distribution"></a>Creación de una aplicación independiente para su distribución

Es posible que desee distribuir un archivo ejecutable único a los usuarios.  Para ello, puede usar el `mkbundle` herramienta para convertir los distintos archivos en un archivo ejecutable independiente.

En primer lugar, asegúrese de que la aplicación se compila y ejecuta.   Una vez que esté satisfecho con los resultados, puede ejecutar el siguiente comando desde la línea de comandos:

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

En la invocación de línea de comandos anterior, la opción `-o` es usa para especificar la salida generada, en este caso, pasamos `/tmp/consoleapp`.   Ahora es una aplicación independiente que se puede distribuir y no tiene dependencias externas en Mono o Xamarin.Mac, es un archivo ejecutable independiente totalmente.

La línea de comandos especificada manualmente el **machine.config** archivo que se usará y un archivo de configuración de la asignación de biblioteca de todo el sistema.   No son necesarios para todas las aplicaciones, pero es conveniente agruparlos, ya que se usan cuando se usa con más funcionalidades de .NET

## <a name="project-less-builds"></a>Compilaciones de proyecto menos

No es necesario un proyecto completo para crear una aplicación de Xamarin.Mac independiente, también puede usar archivos MAKE de Unix simple para realizar el trabajo.   El ejemplo siguiente muestra cómo se puede configurar un archivo MAKE para una aplicación de línea de comandos simple:

```
XAMMAC_PATH=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full/
DYLD=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib
MONODIR=/Library/Frameworks/Mono.framework/Versions/Current/etc/mono

all: consoleapp.exe

consoelapp.exe: consoleapp.cs Makefile
    mcs -g -r:$(XAMMAC_PATH)/Xamarin.Mac.dll consoleapp.cs
    
run: consoleapp.exe
    MONO_PATH=$(XAMMAC_PATH) DYLD_LIBRARY_PATH=$(DYLD) mono --debug consoleapp.exe $(COMMAND)


bundle: consoleapp.exe
    mkbundle --simple consoleapp.exe -o ncsharp -L $(XAMMAC_PATH) --library $(DYLD)/libxammac.dylib --config $(MONODIR)/config --machine-config $(MONODIR)/4.5/machine.config
```

Los pasos anteriores `Makefile` proporciona tres destinos:

- `make` creará el programa
- `make run` compilará y ejecutará el programa en el directorio actual
- `make bundle` creará un archivo ejecutable independiente
