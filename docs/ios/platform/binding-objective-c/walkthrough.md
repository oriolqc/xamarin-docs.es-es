---
title: 'Tutorial: Enlace de una biblioteca de Objective-C de iOS'
description: En este artículo se proporciona un tutorial práctico de creación de un enlace de Xamarin.iOS para una biblioteca existente de Objective-C, InfColorPicker. Trata temas como compilar una biblioteca estática de Objective-C, enlace y utilizar el enlace en una aplicación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: fcf4e6d9b281eaac4be888c499e537f7397528a0
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669276"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Tutorial: Enlace de una biblioteca de Objective-C de iOS

_En este artículo se proporciona un tutorial práctico de creación de un enlace de Xamarin.iOS para una biblioteca existente de Objective-C, InfColorPicker. Trata temas como compilar una biblioteca estática de Objective-C, enlace y utilizar el enlace en una aplicación de Xamarin.iOS._

Cuando se trabaja en iOS, pueden producirse los casos donde desee utilizar una biblioteca de Objective-C de terceros. En estos casos, puede usar un Xamarin.iOS _enlace proyecto_ para crear un [ C# enlace](~/cross-platform/macios/binding/overview.md) que le permitirá utilizar la biblioteca en las aplicaciones de Xamarin.iOS.

Por lo general en el ecosistema de iOS puede encontrar las bibliotecas de 3 modos:

* Como un archivo de biblioteca estática precompilado con `.a` extensión junto con sus encabezados (archivos. h). Por ejemplo, [Analytics biblioteca de Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* Como un marco de trabajo precompilado. Esto es simplemente una carpeta que contiene la biblioteca estática, encabezados y recursos adicionales a veces con `.framework` extensión. Por ejemplo, [biblioteca AdMob de Google](https://developers.google.com/admob/ios/download).
* Como archivos de código fuente. Por ejemplo, una biblioteca que contiene solo `.m` y `.h` Objective C archivos.

En el primer y segundo escenario ya habrá una biblioteca estática de CocoaTouch precompilado así que en este artículo nos centraremos en el tercer escenario. Recuerde que, antes de empezar a crear un enlace, compruebe siempre el certificado proporcionado con la biblioteca para asegurarse de que esté libre para enlazarlo.

Este artículo proporciona un tutorial paso a paso de creación de un proyecto de enlace con el código abierto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) proyecto Objective-C como por ejemplo, sin embargo, toda la información de esta guía se puede adaptar para su uso con cualquier biblioteca de Objective-C de terceros. La biblioteca de InfColorPicker proporciona un controlador de vista reutilizable que permite al usuario seleccionar un color basado en su representación HSB, lo más fácil de usar la selección de color.

[![](walkthrough-images/run01.png "Ejemplo de la biblioteca InfColorPicker que se ejecutan en iOS")](walkthrough-images/run01.png#lightbox)

Hablaremos sobre todos los pasos necesarios para consumir esta API concreta de Objective-C en Xamarin.iOS:

- En primer lugar, vamos a crear una biblioteca estática de Objective-C con Xcode.
- A continuación, enlazamos la esta biblioteca estática con Xamarin.iOS.
- A continuación, se muestra cómo Sharpie objetivo puede reducir la carga de trabajo mediante la generación automática de algunos (pero no todos) de las definiciones de API necesarias requeridas por el enlace de Xamarin.iOS.
- Por último, vamos a crear una aplicación de Xamarin.iOS que usa el enlace.

La aplicación de ejemplo demuestra cómo utilizar un delegado seguro para la comunicación entre la API InfColorPicker y nuestro C# código. Una vez que hemos visto cómo usar a un delegado seguro, hablaremos sobre cómo usar a delegados débiles para realizar las mismas tareas.

## <a name="requirements"></a>Requisitos

En este artículo se da por supuesto que tiene cierta familiaridad con Xcode y el lenguaje de Objective-C y que haya leído nuestro [Binding Objective-C](~/cross-platform/macios/binding/index.md) documentación. Además, es necesario lo siguiente para completar los pasos que aparecen:

-  **Xcode y el SDK de iOS** -Xcode de Apple y la API de iOS más reciente debe estar instalado y configurado en el equipo del desarrollador.
-  **[Herramientas de línea de comandos de Xcode](#Installing_the_Xcode_Command_Line_Tools)**  -las herramientas de línea de comandos de Xcode debe estar instaladas para la versión actualmente instalada de Xcode (vea los detalles de la instalación a continuación).
-  **Visual Studio para Mac o Visual Studio** -la versión más reciente de Visual Studio para Mac o Visual Studio debe estar instalada y configurada en el equipo de desarrollo. Un Mac de Apple es necesario para desarrollar una aplicación de Xamarin.iOS, y cuando se usa Visual Studio debe estar conectado a [host de compilación de una de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **La versión más reciente de objetivo Sharpie** -descarga una copia actual de la herramienta Sharpie objetivo de [aquí](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Si ya tiene Sharpie objetivo instalado, puede actualizar a la versión más reciente mediante el `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>Instalar las herramientas de línea de comandos de Xcode

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


Como se indicó anteriormente, vamos a usar las herramientas de línea de comandos de Xcode (específicamente `make` y `lipo`) en este tutorial. El `make` comando es una utilidad muy común de Unix que automatizará la compilación de programas ejecutables y bibliotecas mediante el uso de un _archivos MAKE_ que especifica cómo se debe compilar el programa. El `lipo` comando es una utilidad de línea de comandos de OS X para crear la arquitectura de varios archivos; combinará varios `.a` archivos en un archivo que se puede utilizar todas las arquitecturas de hardware.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Como se indicó anteriormente, vamos a usar las herramientas de línea de comandos de Xcode en el **Host de compilación de Mac** (específicamente `make` y `lipo`) en este tutorial. El `make` comando es una utilidad muy común de Unix que automatizará la compilación de programas ejecutables y bibliotecas mediante el uso de un _archivos MAKE_ a especifica la forma de compilar el programa. El `lipo` comando es una utilidad de línea de comandos de OS X para crear la arquitectura de varios archivos; combinará varios `.a` archivos en un archivo que se puede utilizar todas las arquitecturas de hardware.


-----

Según de Apple [compilar desde la línea de comandos con las preguntas más frecuentes de Xcode](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) documentación en OS X 10.9 y versiones posteriores, el **descargas** panel de Xcode **preferencias** cuadro de diálogo no ya es compatible con las herramientas de línea de comandos de descarga.

Necesita usar uno de los métodos siguientes para instalar las herramientas:

- **Instalar Xcode** : al instalar Xcode, está equipado con todas sus herramientas de línea de comandos. En OS X 10.9 las correcciones de compatibilidad (instalado en `/usr/bin`), puede asignar cualquier herramienta que se incluye en `/usr/bin` a la herramienta correspondiente dentro de Xcode. Por ejemplo, el `xcrun` comando, que le permite buscar o ejecute cualquier herramienta dentro de Xcode desde la línea de comandos.
- **La aplicación Terminal** -desde la aplicación Terminal, puede instalar las herramientas de línea de comandos ejecutando el `xcode-select --install` comando:
    - Inicie la aplicación Terminal.
    - Tipo `xcode-select --install` y presione **ENTRAR**, por ejemplo:

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - Le pedirá que instale las herramientas de línea de comandos, haga clic en el **instalar** botón:   [![](walkthrough-images/xcode01.png "Instalar las herramientas de línea de comandos")](walkthrough-images/xcode01.png#lightbox)

    - Las herramientas se descargará y se instala desde servidores de Apple:   [![](walkthrough-images/xcode02.png "Descargar las herramientas")](walkthrough-images/xcode02.png#lightbox)

- **Descargas para desarrolladores de Apple** -paquete de las herramientas de línea de comandos está disponible el [descargas para desarrolladores de Apple](https://developer.apple.com/downloads/index.action) página web. Inicie sesión con su identificador de Apple, a continuación, buscar y descargar las herramientas de línea de comandos: [![](walkthrough-images/xcode03.png "Buscar las herramientas de línea de comandos")](walkthrough-images/xcode03.png#lightbox)

Con las herramientas de línea de comandos instaladas, estamos listos continuar con el tutorial.

## <a name="walkthrough"></a>Tutorial

En este tutorial, describiremos los pasos siguientes:

- **[Crear una biblioteca estática](#Creating_A_Static_Library)**  : este paso implica la creación de una biblioteca estática de la **InfColorPicker** código Objective-C. La biblioteca estática tendrá el `.a` la extensión de archivo y se incrustarán en el ensamblado de .NET del proyecto de biblioteca.
- **[Crear un proyecto de enlace de Xamarin.iOS](#Create_a_Xamarin.iOS_Binding_Project)**  -una vez que tenemos una biblioteca estática, se usará para crear un proyecto de enlace de Xamarin.iOS. El proyecto de enlace consta de la biblioteca estática que acabamos de crear y los metadatos en forma de C# código que se explica cómo se puede usar la API de C de objetivo. Estos metadatos se conocen normalmente como las definiciones de API. Vamos a usar **[objetivo Sharpie](#Using_Objective_Sharpie)** que nos ayudan a crear las definiciones de API.
- **[Normalizar las definiciones de API](#Normalize_the_API_Definitions)**  : Sharpie objetivo hace un gran trabajo ayudarnos, pero no puede hacer todo. Trataremos algunos cambios que se deben realizar en las definiciones de API para que se puedan usar.
- **[Usar la biblioteca de enlace](#Using_the_Binding)**  : por último, vamos a crear una aplicación de Xamarin.iOS para mostrar cómo usar nuestro proyecto de enlace recién creado.

Ahora que sabemos qué pasos, vamos a pasar al resto del tutorial.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Crear una biblioteca estática

Si se inspeccione el código para InfColorPicker en Github:

[![](walkthrough-images/image02.png "Inspeccione el código para InfColorPicker en Github")](walkthrough-images/image02.png#lightbox)

Podemos ver los tres directorios siguientes en el proyecto:

- **InfColorPicker** : este directorio contiene el código de Objective-C para el proyecto.
- **PickerSamplePad** : este directorio contiene un proyecto de ejemplo de iPad.
- **PickerSamplePhone** : este directorio contiene un proyecto de ejemplo de iPhone.

Vamos a descargar el proyecto InfColorPicker desde [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) y descomprímalo en el directorio de nuestra elección. Abriendo el destino de Xcode para `PickerSamplePhone` proyecto, vemos que la siguiente estructura de proyecto en el navegador de Xcode:

[![](walkthrough-images/image03.png "La estructura del proyecto en el navegador de Xcode")](walkthrough-images/image03.png#lightbox)

Este proyecto logra la reutilización del código agregando el código fuente de InfColorPicker (en el cuadro rojo) directamente en cada proyecto de ejemplo. El código del proyecto de ejemplo es dentro del cuadro azul. Dado que este proyecto en particular no proporcionarnos una biblioteca estática, es necesario para nosotros crear un proyecto de Xcode para compilar la biblioteca estática.

Es el primer paso para que agregar el código fuente de InfoColorPicker en la biblioteca estática. Para lograr esto, vamos a hacer lo siguiente:

1. Inicie Xcode.
2. Desde el **archivo** menú, seleccione **New** > **proyecto...** :

    [![](walkthrough-images/image04.png "Iniciar un nuevo proyecto")](walkthrough-images/image04.png#lightbox)
3. Seleccione **Framework & biblioteca**, **biblioteca estática de Cocoa Touch** plantilla y haga clic en el **siguiente** botón:

    [![](walkthrough-images/image05.png "Seleccione la plantilla de biblioteca estática de Cocoa Touch")](walkthrough-images/image05.png#lightbox)

4. Escriba `InfColorPicker` para el **nombre del proyecto** y haga clic en el **siguiente** botón:

    [![](walkthrough-images/image06.png "Escriba InfColorPicker como nombre del proyecto")](walkthrough-images/image06.png#lightbox)
5. Seleccione una ubicación para guardar el proyecto y haga clic en el **Aceptar** botón.
6. Ahora necesitamos agregar el origen desde el proyecto InfColorPicker a nuestro proyecto de biblioteca estática. Dado que el **InfColorPicker.h** archivo ya existe en la biblioteca estática (de forma predeterminada), Xcode no nos permitirá que la sobrescriba. Desde el **buscador**, navegar al código fuente InfColorPicker en el proyecto original que se han descomprimido desde GitHub, copie todos los archivos InfColorPicker y pegarlas en nuestro nuevo proyecto de biblioteca estática:

    [![](walkthrough-images/image12.png "Copiar todos los archivos InfColorPicker")](walkthrough-images/image12.png#lightbox)

7. Vuelva a Xcode, haga clic con el botón derecho en el **InfColorPicker** carpeta y seleccione **agregar archivos a "InfColorPicker..."**:

    [![](walkthrough-images/image08.png "Agregar archivos")](walkthrough-images/image08.png#lightbox)

8. En el cuadro de diálogo Agregar archivos, vaya a los archivos de código fuente de InfColorPicker que acaba de copiar, selecciónelos todos y haga clic en el **agregar** botón:

    [![](walkthrough-images/image09.png "Seleccionar todo y haga clic en el botón Agregar")](walkthrough-images/image09.png#lightbox)

9. El código fuente se copiará en nuestro proyecto:

    [![](walkthrough-images/image10.png "El código fuente se copiará en el proyecto")](walkthrough-images/image10.png#lightbox)

10. En el navegador de proyecto Xcode, seleccione el **InfColorPicker.m** de archivos y marque como comentario las dos últimas líneas (debido a la manera en que se escribió esta biblioteca, no se usa este archivo):

    [![](walkthrough-images/image14.png "Edite el archivo InfColorPicker.m")](walkthrough-images/image14.png#lightbox)

11. Ahora debemos comprobar si hay cualquier marcos requeridos por la biblioteca. Puede encontrar esta información en el archivo Léame, o bien, abra uno de los proyectos de ejemplo proporcionados. Este ejemplo se utiliza `Foundation.framework`, `UIKit.framework`, y `CoreGraphics.framework` así que vamos a agregarlos.

12. Seleccione el **InfColorPicker destino > fases de compilación** y expanda el **vincular binario con bibliotecas** sección:

    [![](walkthrough-images/image16b.png "Expanda la sección Vincular binario con bibliotecas")](walkthrough-images/image16b.png#lightbox)

13. Use la **+** botón para abrir el cuadro de diálogo que le permite agregar los marcos de marcos necesarios mencionados anteriormente:

    [![](walkthrough-images/image16c.png "Agregue que los marcos de marcos necesarios mencionados anteriormente")](walkthrough-images/image16c.png#lightbox)

14. El **vincular binario con bibliotecas** sección debe parecerse a la imagen siguiente:

    [![](walkthrough-images/image16d.png "La sección Vincular binario con bibliotecas")](walkthrough-images/image16d.png#lightbox)

En este momento estamos close, pero no es exactamente eso concluimos. Se ha creado la biblioteca estática, pero es necesario crear para crear una Fat binario que incluye todas las arquitecturas necesarias para el dispositivo iOS y simulador de iOS.

### <a name="creating-a-fat-binary"></a>Creación de un archivo binario Fat

Todos los dispositivos iOS tienen procesadores con tecnología de arquitectura ARM que han desarrollado con el tiempo. Cada nueva arquitectura agrega otras mejoras y nuevas instrucciones mientras todavía mantiene la compatibilidad. En dispositivos iOS, tenemos armv6, armv7, armv7s, conjuntos de instrucciones arm64 – aunque [, ya no usamos armv6](~/ios/deploy-test/compiling-for-different-devices.md). El simulador de iOS no funciona con ARM y es istead un x86 y x86_64 con tecnología de simulador. Nos lo que significa para nosotros es que debemos proporcionar bibliotecas en cada instrucción que establece.

Es una biblioteca Fat `.a` archivo que contiene todas las arquitecturas admitidas.

La creación de un contenido de grasas binario es un proceso de tres pasos:

- Compile una versión 7 de ARM y ARM64 de la biblioteca estática.
- Compile una versión x86 y x84_64 de la biblioteca estática.
- Use el `lipo` herramienta de línea de comandos para combinar las dos bibliotecas estáticas en uno.

Aunque estos tres pasos son bastante sencillos, y puede ser necesario repetirlos en el futuro si la biblioteca de Objective-C recibe las actualizaciones o si se requieren correcciones de errores. Si decide automatizar estos pasos, se simplificará el mantenimiento futuro y soporte técnico del proyecto de enlace de iOS.

Hay muchas herramientas disponibles para automatizar tareas - un script de shell, [rake](http://rake.rubyforge.org/), [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/), y [realizar](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html). Cuando se instalan las herramientas de línea de comandos de Xcode, instalamos make, por lo que es el sistema de compilación que se usará para este tutorial. Este es un **archivos MAKE** que puede usar para crear una biblioteca compartida de arquitectura de múltiples que funcionará en un dispositivo iOS y el simulador para la biblioteca de cualquier:

```bash
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=./YOUR-PROJECT-NAME
PROJECT=$(PROJECT_ROOT)/YOUR-PROJECT-NAME.xcodeproj
TARGET=YOUR-PROJECT-NAME

all: lib$(TARGET).a

lib$(TARGET)-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

lib$(TARGET)-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET)-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET).a: lib$(TARGET)-i386.a lib$(TARGET)-armv7.a lib$(TARGET)-arm64.a
    xcrun -sdk iphoneos lipo -create -output $@ $^

clean:
    -rm -f *.a *.dll
```

Escriba el **archivos MAKE** comandos en el editor de texto sin formato de su elección y actualizar las secciones con **YOUR-PROJECT-NAME** con el nombre del proyecto. También es importante asegurarse de que se pegue las instrucciones anteriores, que se han conservado las pestañas dentro de las instrucciones.

Guarde el archivo con el nombre **archivos MAKE** a la misma ubicación que la biblioteca estática de Xcode de InfColorPicker hemos creado anteriormente:

[![](walkthrough-images/lib00.png "Guarde el archivo con el nombre de archivo MAKE")](walkthrough-images/lib00.png#lightbox)

Abra la aplicación Terminal del Mac y navegue hasta la ubicación de un archivo MAKE. Tipo `make` en el Terminal, presione **ENTRAR** y **archivos MAKE** se va a ejecutar:

[![](walkthrough-images/lib01.png "Salida de ejemplo de archivo MAKE")](walkthrough-images/lib01.png#lightbox)

Al ejecutar make, verá mucho el desplazamiento por el texto. Si todo funcionó correctamente, verá las palabras **compilación correcta** y `libInfColorPicker-armv7.a`, `libInfColorPicker-i386.a` y `libInfColorPickerSDK.a` archivos se copiarán en la misma ubicación que el **archivos MAKE**:

[![](walkthrough-images/lib02.png "Los archivos de libInfColorPicker armv7.a, libInfColorPicker i386.a y libInfColorPickerSDK.a generados por el archivo MAKE")](walkthrough-images/lib02.png#lightbox)

Puede confirmar las arquitecturas dentro de su archivo binario Fat mediante el comando siguiente:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Esto debería mostrar lo siguiente:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

En este momento, nos hemos completado el primer paso de nuestro enlace iOS mediante la creación de una biblioteca estática mediante Xcode y las herramientas de línea de comandos de Xcode `make` y `lipo`. Vamos a continuar con el siguiente paso y usar **objetivo Sharpie** para automatizar la creación de los enlaces de API para nosotros.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Crear un proyecto de enlace de Xamarin.iOS

Para poder usar **objetivo Sharpie** para automatizar el proceso de enlace, es necesario crear un proyecto de enlace de Xamarin.iOS para alojar las definiciones de API (que vamos a usar **objetivo Sharpie** para ayudarnos a compilación) y cree el C# enlace para nosotros.

Vamos a hacer lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie Visual Studio para Mac.
1. Desde el **archivo** menú, seleccione **New** > **solución...** :

    ![](walkthrough-images/bind01.png "A partir de una nueva solución")

1. En el cuadro de diálogo nueva solución, seleccione **biblioteca** > **enlace proyecto iOS**:

    ![](walkthrough-images/bind02.png "Seleccione el proyecto de enlace de iOS")

1. Haga clic en el **siguiente** botón.

1. Escriba "InfColorPickerBinding" como el **nombre del proyecto** y haga clic en el **crear** botón para crear la solución:

    ![](walkthrough-images/bind02a.png "Escriba InfColorPickerBinding como el nombre del proyecto")

Se creará la solución y dos archivos de forma predeterminada se incluyen:

![](walkthrough-images/bind03.png "La estructura de solución en el Explorador de soluciones")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. Inicie Visual Studio.

1. Desde el **archivo** menú, seleccione **New** > **proyecto...** :

    ![Iniciar un nuevo proyecto](walkthrough-images/bind01vs.png "a partir de un nuevo proyecto")

1. En el cuadro de diálogo nuevo proyecto, seleccione **Visual C# > iPhone & iPad > enlaces de biblioteca de iOS (Xamarin)**:

    [![Seleccione la biblioteca de enlaces de iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Escriba "InfColorPickerBinding" como el **nombre** y haga clic en el **Aceptar** botón para crear la solución.

Se creará la solución y dos archivos de forma predeterminada se incluyen:

![](walkthrough-images/bind03vs.png "La estructura de solución en el Explorador de soluciones")

-----

- **ApiDefinition.cs** -este archivo contendrá los contratos que definen cómo se ajustará la API de Objective-C en C#.
- **Structs.cs** : este archivo contendrá las estructuras o valores de enumeración que son necesarios por las interfaces y delegados.

Vamos a trabajar con estos dos archivos más adelante en el tutorial. En primer lugar, tenemos que agregar la biblioteca InfColorPicker al proyecto de enlace.

### <a name="including-the-static-library-in-the-binding-project"></a>Inclusión de la biblioteca estática en el proyecto de enlace

Ahora tenemos nuestro proyecto de enlace, base, en listo, tenemos que agregar la biblioteca Fat binario que hemos creado anteriormente para el **InfColorPicker** biblioteca.

Siga estos pasos para agregar la biblioteca:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el **las referencias nativas** carpeta en el panel de solución y seleccione **agregar las referencias nativas**:

    ![](walkthrough-images/bind04a.png "Agregue las referencias nativas")

1. Navegue hasta la Fat binario hemos realizado anteriormente (`libInfColorPickerSDK.a`) y presione la **abierto** botón:

    ![](walkthrough-images/bind05.png "Seleccione el archivo libInfColorPickerSDK.a")
1. El archivo se incluirán en el proyecto:

    ![](walkthrough-images/bind04.png "Inclusión de un archivo")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Copia el `libInfColorPickerSDK.a` desde su **Host de compilación de Mac** y péguelo en el proyecto de enlace.

1. Haga doble clic en el proyecto y elija **Agregar > elemento existente...** :

    ![](walkthrough-images/bind04vs.png "Agregar un archivo existente")

1. Navegue hasta la `libInfColorPickerSDK.a` y presione la **agregar** botón:

    ![](walkthrough-images/bind05vs.png "Adding libInfColorPickerSDK.a")

1. El archivo se incluirán en el proyecto.

-----

Cuando el **.a** archivo se agrega al proyecto, Xamarin.iOS establecerá automáticamente el **acción de compilación** del archivo a **ObjcBindingNativeLibrary**y cree un archivo especial llama a `libInfColorPickerSDK.linkwith.cs`.


Este archivo contiene el `LinkWith` atributo que indica que Xamarin.iOS cómo agrega la biblioteca estática que acabamos de identificador. El contenido de este archivo se muestra en el siguiente fragmento de código:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

El `LinkWith` atributo identifica la biblioteca estática para el proyecto y algunos indicadores importantes del vinculador.


Lo siguiente que debemos hacer es crear las definiciones de API para el proyecto InfColorPicker. Para los fines de este tutorial, usaremos Sharpie objetivo para generar el archivo **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>Uso de Sharpie objetivo

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


Sharpie objetivo es una línea de comando de herramienta (proporcionado por Xamarin) que puede ayudarle a crear las definiciones necesarias para enlazar una biblioteca de Objective-C parte 3ª a C#. En esta sección, vamos a usar Sharpie objetivo para la creación inicial **ApiDefinition.cs** para el proyecto InfColorPicker.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Sharpie objetivo es una línea de comando de herramienta (proporcionado por Xamarin) que puede ayudarle a crear las definiciones necesarias para enlazar una biblioteca de Objective-C parte 3ª a C#. En esta sección, vamos a usar Sharpie objetivo en nuestra **Host de compilación de Mac** para crear la inicial **ApiDefinition.cs** para el proyecto InfColorPicker.


-----

Para empezar, vamos a descargar archivo de instalador de objetivo Sharpie tal como se detalla en este [guía](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Ejecute al instalador y siga todos los mensajes en la pantalla desde el Asistente para instalación para instalar Sharpie objetivo en nuestro equipo de desarrollo.

Después de que tengamos objetivo Sharpie correctamente instalado, vamos a iniciar la aplicación Terminal y escriba el siguiente comando para obtener ayuda acerca de todas las herramientas que proporciona para ayudar en el enlace:

```bash
sharpie -help
```

Si ejecutamos el comando anterior, se generará el siguiente resultado:

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:

  xcode    Get information about Xcode installations and available SDKs.

  bind     Create a Xamarin C# binding to Objective-C APIs
Europa:Resources kmullins$
```

Con el propósito de este tutorial, vamos a usar las siguientes herramientas Sharpie objetivo:

- **xcode** : herramientas nos da información sobre la instalación de Xcode actual y las versiones de iOS y las API de Mac que hemos instalado. Vamos a usar esta información más adelante cuando se generan nuestro enlaces.
- **enlazar** -vamos a usar esta herramienta para analizar el **.h** los archivos del proyecto InfColorPicker en inicial **ApiDefinition.cs** y **StructsAndEnums.cs** archivos.

Para obtener ayuda sobre una herramienta Sharpie objetivo específica, escriba el nombre de la herramienta y el `-help` opción. Por ejemplo, `sharpie xcode -help` devuelve el siguiente resultado:

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, --help                 Show detailed help
  -v, --verbose              Be verbose with output
      --sdks                 List all available Xcode SDKs. Pass -verbose for
                               more details.
Europa:Resources kmullins$
```

Antes de que podemos empezar el proceso de enlace, es necesario obtener información acerca de nuestro SDK instalados actual escribiendo el comando siguiente en el Terminal `sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

De lo anterior, podemos ver que tenemos el `iphoneos9.3` SDK instalado en la máquina. Con esta información en su lugar, estamos preparados para analizar el proyecto InfColorPicker `.h` archivos a la inicial **ApiDefinition.cs** y `StructsAndEnums.cs` para el proyecto InfColorPicker.

Escriba el siguiente comando en la aplicación Terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Donde `[full-path-to-project]` es la ruta de acceso completa al directorio donde la **InfColorPicker** se encuentra el archivo de proyecto de Xcode en nuestro equipo y [iphone-SO] es el SDK de iOS que hemos instalado, como indica la `sharpie xcode -sdks` comando. Tenga en cuenta que en este ejemplo hemos pasado  **\*.h** como un parámetro, que incluye *todas* los archivos de encabezado en este directorio - normalmente debe no lo hace, pero en su lugar, lea detenidamente a través de la archivos de encabezado para buscar el nivel superior **.h** archivo que hace referencia a todos los archivos pertinentes y solo se pasa al objetivo Sharpie.

La siguiente [salida](walkthrough-images/os05.png) se generarán en el terminal:

```bash
Europa:Resources kmullins$ sharpie bind -output InfColorPicker -namespace InfColorPicker -sdk iphoneos8.1 /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h -unified
Compiler configuration:
    -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk -miphoneos-version-min=8.1 -resource-dir /Library/Frameworks/ObjectiveSharpie.framework/Versions/1.1.1/clang-resources -arch armv7 -ObjC

[  0%] parsing /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h
In file included from /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h:60:
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* sourceColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* resultColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
4 warnings generated.
[100%] parsing complete
[bind] InfColorPicker.cs
Europa:Resources kmullins$
```

Y el **InfColorPicker.enums.cs** y **InfColorPicker.cs** archivos se crearán en el directorio:

[![](walkthrough-images/os06.png "Los archivos InfColorPicker.enums.cs y InfColorPicker.cs")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


Abra ambos archivos en el proyecto de enlace que se crearon anteriormente. Copie el contenido de la **InfColorPicker.cs** de archivo y péguelo en el **ApiDefinition.cs** archivo, reemplazando las existentes `namespace ...` bloque de código con el contenido de la  **InfColorPicker.cs** archivo (dejando el `using` instrucciones intactas):

![](walkthrough-images/os07.png "El archivo InfColorPickerControllerDelegate")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Abra ambos archivos en el proyecto de enlace que se crearon anteriormente. Copie el contenido de la **InfColorPicker.cs** archivo (desde el **Host de compilación de Mac**) y péguelo en el **ApiDefinition.cs** archivo, reemplazando las existentes `namespace ...` bloque de código con el contenido de la **InfColorPicker.cs** archivo (dejando el `using` instrucciones intactas).


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Normalizar las definiciones de API

Objetivo Sharpie a veces, tiene un problema de traducir `Delegates`, por lo que necesitamos modificar la definición de la `InfColorPickerControllerDelegate` interfaz y reemplace el `[Protocol, Model]` línea con lo siguiente:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
Para que la definición tiene el siguiente aspecto:

[![](walkthrough-images/os11.png "La definición")](walkthrough-images/os11.png#lightbox)

A continuación, hacemos lo mismo con el contenido de la `InfColorPicker.enums.cs` archivo, copiar y pegar en el `StructsAndEnums.cs` archivo dejando la `using` intactas las instrucciones:

[![](walkthrough-images/os09.png "El contenido del StructsAndEnums.cs archivo ")](walkthrough-images/os09.png#lightbox)

También es posible que Sharpie objetivo se anota el enlace con `[Verify]` atributos. Estos atributos indican que debe comprobar que el objetivo Sharpie hizo lo correcto comparando el enlace con la declaración de C, Objective-C original (que se proporcionará en un comentario sobre la declaración de enlace). Una vez que haya comprobado los enlaces, debe quitar el atributo compruebe. Para obtener más información, consulte el [compruebe](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) guía.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


En este momento, nuestro proyecto de enlace debe ser completo y listo para compilar. Vamos a crear nuestro proyecto de enlace y asegúrese de que se finalizó sin errores:

[Compile el proyecto de enlace y asegúrese de que no hay errores](walkthrough-images/os12.png)


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


En este momento, nuestro proyecto de enlace debe ser completo y listo para compilar. Vamos a crear nuestro proyecto de enlace y asegúrese de que se finalizó sin errores.


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>Mediante el enlace

Siga estos pasos para crear una aplicación de iPhone de ejemplo para usar la biblioteca de enlace creada anteriormente de iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. **Crear proyecto de Xamarin.iOS** -agregar un nuevo proyecto de Xamarin.iOS denominado **InfColorPickerSample** a la solución, como se muestra en las capturas de pantalla siguiente:

    ![](walkthrough-images/use01.png "Agregar una aplicación de vista única")

    ![](walkthrough-images/use01a.png "Establecimiento del identificador")

1. **Agregar referencia al proyecto de enlace** -actualización de la **InfColorPickerSample** del proyecto para que tenga una referencia a la **InfColorPickerBinding** proyecto:

    ![](walkthrough-images/use02.png "Agregar referencia al proyecto de enlace")

1. **Crear la interfaz de usuario de iPhone** -haga doble clic en el **MainStoryboard.storyboard** de archivos en el **InfColorPickerSample** proyecto para editarlo en el Diseñador de iOS. Agregar un **botón** a la vista y llámelo `ChangeColorButton`, tal y como se muestra en la siguiente:

    ![](walkthrough-images/use03.png "Agregar un botón a la vista")

1. **Agregar el InfColorPickerView.xib** -InfColorPicker Objective-C en la biblioteca incluye un **.xib** archivo. Xamarin.iOS no incluirá este **.xib** en el proyecto de enlace, lo que provocará errores en tiempo de ejecución en nuestra aplicación de ejemplo. Para solucionar esto consiste en agregar el **.xib** archivo a nuestro proyecto de Xamarin.iOS. Seleccione el proyecto de Xamarin.iOS, contextual y seleccione **Agregar > Agregar archivos**y agregue el **.xib** archivo tal como se muestra en la captura de pantalla siguiente:

    ![](walkthrough-images/use04.png "Agregar el InfColorPickerView.xib")

1. Cuando se le pida, copie el **.xib** archivo en el proyecto.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. **Crear proyecto de Xamarin.iOS** -agregar un nuevo proyecto de Xamarin.iOS denominado **InfColorPickerSample** utilizando el **aplicación de vista única** plantilla:

    [![proyecto de aplicación (Xamarin) de iOS](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Seleccione la plantilla](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Agregar referencia al proyecto de enlace** -actualización de la **InfColorPickerSample** del proyecto para que tenga una referencia a la **InfColorPickerBinding** proyecto:

    ![](walkthrough-images/use02vs.png "Agregar referencia al proyecto de enlace")

1. **Crear la interfaz de usuario de iPhone** -haga doble clic en el **MainStoryboard.storyboard** de archivos en el **InfColorPickerSample** proyecto para editarlo en el Diseñador de iOS. Agregar un **botón** a la vista y llámelo `ChangeColorButton`, tal y como se muestra en la siguiente:

    ![](walkthrough-images/use03vs.png "Crear la interfaz de usuario de iPhone")

1. **Agregar el InfColorPickerView.xib** -InfColorPicker Objective-C en la biblioteca incluye un **.xib** archivo. Xamarin.iOS no incluirá este **.xib** en el proyecto de enlace, lo que provocará errores en tiempo de ejecución en nuestra aplicación de ejemplo. Para solucionar esto consiste en agregar el **.xib** archivo a nuestro proyecto de Xamarin.iOS desde nuestro **Host de compilación de Mac**. Seleccione el proyecto de Xamarin.iOS, contextual y seleccione **agregar** > **elemento existente...** y agregue el **.xib** archivo.

-----

A continuación, echemos un vistazo rápido a los protocolos en Objective-C y cómo se controlan en el enlace y C# código.

### <a name="protocols-and-xamarinios"></a>Protocolos y Xamarin.iOS

En Objective-C, un protocolo define los métodos (o mensajes) que se puede utilizar en determinadas circunstancias. Conceptualmente, son muy similares a las interfaces de C#. Una diferencia importante entre un protocolo de Objective-C y un C# interfaz es que los protocolos pueden tener métodos opcionales - métodos que una clase no tiene que implementar. Objective-C usa las @optional palabra clave se utiliza para indicar qué métodos son opcionales. Para obtener más información sobre los protocolos consulte [eventos, protocolos y delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** tiene un protocolo este tipo, se muestra en el siguiente fragmento de código:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Este protocolo se usa por **InfColorPickerController** para informar a los clientes que el usuario ha seleccionado un nuevo color y que la **InfColorPickerController** haya finalizado. Sharpie objetivo había asignado este protocolo, como se muestra en el siguiente fragmento de código:

```csharp
[BaseType(typeof(NSObject))]
[Model]
public partial interface InfColorPickerControllerDelegate {

    [Export ("colorPickerControllerDidFinish:")]
    void ColorPickerControllerDidFinish (InfColorPickerController controller);

    [Export ("colorPickerControllerDidChangeColor:")]
    void ColorPickerControllerDidChangeColor (InfColorPickerController controller);
}

```

Cuando se compila la biblioteca de enlace, Xamarin.iOS creará una clase base abstracta denominada `InfColorPickerControllerDelegate`, que implementa esta interfaz con los métodos virtuales.

Hay dos maneras de que podemos implementar esta interfaz en una aplicación de Xamarin.iOS:

- **Delegado seguro** -mediante un delegado seguro implica la creación de un C# que cree subclases de clases `InfColorPickerControllerDelegate` e invalida los métodos adecuados. **InfColorPickerController** utilizará una instancia de esta clase para comunicarse con sus clientes.
- **Delegado débil** -un delegado débil es una técnica ligeramente diferente que implica la creación de un método público en alguna clase (como `InfColorPickerSampleViewController`) y, a continuación, exponer ese método a la `InfColorPickerDelegate` protocolo a través de un `Export` atributo.

Delegados seguros proporcionan Intellisense, seguridad de tipos y una mejor encapsulación. Por estas razones, debe usar a delegados seguros donde puede, en lugar de un delegado débil.

En este tutorial, veremos dos técnicas: implementar primero un delegado seguro y, a continuación, que se explica cómo implementar un delegado débil.

### <a name="implementing-a-strong-delegate"></a>Implementación de un delegado seguro

Finalizar la aplicación de Xamarin.iOS mediante el uso de un delegado seguro para responder a la `colorPickerControllerDidFinish:` mensaje:

**Subclase InfColorPickerControllerDelegate** -agregar una nueva clase al proyecto denominado `ColorSelectedDelegate`. Modifique la clase para que tenga el código siguiente:

```csharp
using InfColorPickerBinding;
using UIKit;

namespace InfColorPickerSample
{
    public class ColorSelectedDelegate:InfColorPickerControllerDelegate
    {
        readonly UIViewController parent;

        public ColorSelectedDelegate (UIViewController parent)
        {
            this.parent = parent;
        }

        public override void ColorPickerControllerDidFinish (InfColorPickerController controller)
        {
            parent.View.BackgroundColor = controller.ResultColor;
            parent.DismissViewController (false, null);
        }
    }
}
```

Xamarin.iOS se enlazará el delegado de Objective-C mediante la creación de una clase base abstracta denominada `InfColorPickerControllerDelegate`. Subclase este tipo y la invalidación del `ColorPickerControllerDidFinish` método para acceder al valor de la `ResultColor` propiedad de `InfColorPickerController`.

**Crear una instancia de ColorSelectedDelegate** -nuestro controlador de eventos tendrá una instancia de la `ColorSelectedDelegate` tipo que creamos en el paso anterior. Modifique la clase `InfColorPickerSampleViewController` y agregue la siguiente variable de instancia a la clase:

```csharp
ColorSelectedDelegate selector;
```

**Inicialice la variable ColorSelectedDelegate** : para asegurarse de que `selector` es válido de la instancia, el método de actualización `ViewDidLoad` en `ViewController` para que coincida con el siguiente fragmento de código:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**Implemente el método HandleTouchUpInsideWithStrongDelegate** -a continuación, implemente el controlador de eventos cuando el usuario toca **ColorChangeButton**. Editar `ViewController`y agregue el siguiente método:

```csharp
using InfColorPicker;
...

private void HandleTouchUpInsideWithStrongDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.Delegate = selector;
    picker.PresentModallyOverViewController (this);
}

```

Obtenemos primero una instancia de `InfColorPickerController` a través de un método estático y asegúrese de que la instancia consciente de nuestro delegado seguro a través de la propiedad `InfColorPickerController.Delegate`. Esta propiedad se generó automáticamente para que podamos por objetivo Sharpie. Por último, llamamos `PresentModallyOverViewController` para mostrar la vista `InfColorPickerSampleViewController.xib` para que el usuario puede seleccionar un color.

**Ejecute la aplicación** : en este punto hemos terminado con todo nuestro código. Si ejecuta la aplicación, podrá cambiar el color de fondo de la `InfColorColorPickerSampleView` tal como se muestra en las capturas de pantalla siguiente:

[![](walkthrough-images/run01.png "Ejecutar la aplicación")](walkthrough-images/run01.png#lightbox)

¡Enhorabuena! En este punto ha creado correctamente y enlaza una biblioteca de Objective-C para su uso en una aplicación de Xamarin.iOS. A continuación, vamos a aprender sobre el uso de delegados débiles.

### <a name="implementing-a-weak-delegate"></a>Implementación de un delegado débil

En lugar de crear subclases de una clase enlazada con el protocolo de Objective-C para un determinado delegado, Xamarin.iOS también le permite implementar los métodos de protocolo en cualquier clase que derive de `NSObject`, decoración de los métodos con el `ExportAttribute`y, a continuación, se ha proporcionado los selectores adecuados. Al adoptar este enfoque, asigne una instancia de la clase a la `WeakDelegate` propiedad en lugar de a la `Delegate` propiedad. Un delegado débil le ofrece la flexibilidad para tomar la clase delegada hacia abajo de una jerarquía de herencia diferente. Vamos a ver cómo implementar y utilizar a un delegado débil en nuestra aplicación de Xamarin.iOS.

**Crear el controlador de eventos para TouchUpInside** -vamos a crear un nuevo controlador de eventos para el `TouchUpInside` eventos del botón Cambiar Color de fondo. Este controlador llenará la misma función que el `HandleTouchUpInsideWithStrongDelegate` controlador que se creó en la sección anterior, pero utiliza un delegado débil en lugar de un delegado seguro. Modifique la clase `ViewController`y agregue el siguiente método:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**Actualizar ViewDidLoad** -debemos cambiar `ViewDidLoad` para que use el controlador de eventos que acabamos de crear. Editar `ViewController` y cambiar `ViewDidLoad` similar a la de fragmento de código siguiente:


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Controlar la colorPickerControllerDidFinish: Mensaje** : si el `ViewController` está terminado, iOS enviará el mensaje `colorPickerControllerDidFinish:` a la `WeakDelegate`. Es necesario crear un C# método que puede controlar este mensaje. Para ello, creamos un C# método y, a continuación, agregar adornos a él con el `ExportAttribute`. Editar `ViewController`y agregue el siguiente método a la clase:

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Ejecute la aplicación. Ahora debe se comportan exactamente como lo hizo antes, pero usa a un delegado débil en lugar del delegado seguro. En este momento se ha completado correctamente este tutorial. Ahora debe tener una comprensión de cómo crear y consumir un proyecto de enlace de Xamarin.iOS.

## <a name="summary"></a>Resumen

En este artículo ha avanzado a través del proceso de creación y uso de un proyecto de enlace de Xamarin.iOS. Primero analizamos cómo compilar una biblioteca de Objective-C existente en una biblioteca estática. A continuación, hemos tratado la creación de un proyecto de enlace de Xamarin.iOS y cómo usar Sharpie objetivo para generar las definiciones de API de la biblioteca de Objective-C. Analizamos cómo actualizar y ajustar las definiciones de API generadas para que sean adecuados para consumo público. Una vez se ha finalizado el proyecto de enlace de Xamarin.iOS, pasamos a consumir ese enlace en una aplicación de Xamarin.iOS, con un enfoque sobre el uso de delegados fuertes y débiles delegados.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de enlace (ejemplo)](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [Enlace de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalles de enlace](~/cross-platform/macios/binding/overview.md)
- [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desarrolladores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Instrucciones de diseño de .NET Framework](https://msdn.microsoft.com/library/ms229042.aspx)
- [Curso de Xamarin University: Creación de una biblioteca de enlaces de Objective-c.](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Curso de Xamarin University: Generar una biblioteca de enlaces de Objective-C con Sharpie objetivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
