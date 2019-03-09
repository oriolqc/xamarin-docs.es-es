---
title: Hacer referencia a bibliotecas nativas de Xamarin.iOS
description: Este documento describe cómo vincular a bibliotecas nativas de C en una aplicación de Xamarin.iOS. Describe cómo crear bibliotecas nativas universales y el acceso a los métodos de C desde C#.
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/28/2016
ms.openlocfilehash: 42cf10e592a37f9a979d05d7784553059923646c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667327"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>Hacer referencia a bibliotecas nativas de Xamarin.iOS

Xamarin.iOS admite la vinculación con las bibliotecas nativas de C y las bibliotecas de Objective-C. Este documento describe cómo vincular las bibliotecas de C nativas con el proyecto de Xamarin.iOS. Para obtener información acerca de cómo realizar el mismo para las bibliotecas de Objective-C, consulte nuestra [tipos de enlace de Objective-C](~/ios/platform/binding-objective-c/index.md) documento.

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Creación de bibliotecas nativas Universal (i386, ARMv7 y ARM64)

A menudo resulta deseable para compilar las bibliotecas nativas para cada una de las plataformas admitidas para el desarrollo de iOS (i386 los propios dispositivos para el simulador y ARMv7/ARM64). Si ya tienes un proyecto de Xcode para la biblioteca, esto es realmente sencillo hacer.

Para compilar la versión i386 de la biblioteca nativa, ejecute el siguiente comando desde un terminal:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Esto dará como resultado una biblioteca estática nativa en `MyProject.xcodeproj/build/Release-iphonesimulator/`. Copiar (o mover) el archivo de almacenamiento de biblioteca (libMyLibrary.a) que en lugar seguro para su uso posterior, dándole un nombre único (como **libMyLibrary i386.a**) para que los no entren en conflicto con las versiones de la misma biblioteca que necesitará arm64 y armv7 crear a continuación.

Para compilar la versión ARM64 de la biblioteca nativa, ejecute el siguiente comando:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Esta vez la biblioteca nativa compilada se ubicará en `MyProject.xcodeproj/build/Release-iphoneos/`. Una vez más, copiar (o mover) este archivo en una ubicación segura, el nombre a algo parecido a **libMyLibrary arm64.a** para que no entren en conflicto.

Ahora, compilar la versión ARMv7 de la biblioteca:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

Copiar (o mover) el archivo de biblioteca resultante en la misma ubicación ha movido el 2 versiones de la biblioteca, el nombre a algo parecido a **libMyLibrary armv7.a**.

Para hacer un universal binario, puede usar el `lipo` herramienta así:

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

Esto crea `libMyLibrary.a` que será una biblioteca (fat) universal que será adecuada que se usará para todos los destinos de desarrollo de iOS.


### <a name="missing-required-architecture-i386"></a>Falta la arquitectura necesaria i386

Si recibe un `does not implement methodSignatureForSelector` o `does not implement doesNotRecognizeSelector` mensaje en la salida en tiempo de ejecución al intentar utilizar una biblioteca de Objective-C en el simulador, la biblioteca de iOS probablemente no se compiló para la arquitectura i386 (consulte la [creación universales Las bibliotecas nativas](#building_native) sección anterior).

Para comprobar las arquitecturas compatibles con una biblioteca concreta, use el siguiente comando en el Terminal:

```bash
lipo -info /full/path/to/libraryname.a
```

Donde `/full/path/to/` es la ruta de acceso completa a la biblioteca que se consume y `libraryname.a` es el nombre de la biblioteca en cuestión.

Si tiene el origen a la biblioteca, deberá compilar y empaquetarla para la arquitectura de i386 también, si desea probar la aplicación en el simulador de iOS.

### <a name="linking-your-library"></a>Vinculación de la biblioteca

Cualquier biblioteca de terceros que consume debe vincularse estáticamente con la aplicación. 

Si desea vincular estáticamente la biblioteca "libMyLibrary.a" que obtuvo desde Internet o una compilación con Xcode, deberá hacer algunas cosas:

-  Incluya la biblioteca en su proyecto
-  Configurar Xamarin.iOS para vincular la biblioteca
-  Acceso a los métodos de la biblioteca.


Para **incluya la biblioteca en su proyecto**, seleccione el proyecto en el Explorador de soluciones y presione **opción + comando + a**. Vaya a la libMyLibrary.a y agréguelo al proyecto. Cuando se le solicite, indicar a Visual Studio para Mac o Visual Studio copiarlo en el proyecto. Después de agregarlo, busque el libFoo.a en el proyecto, haga clic con el botón derecho en él y establezca el **acción de compilación** a **ninguno**.

Para **configurar Xamarin.iOS para vincular la biblioteca**, en las opciones del proyecto para el ejecutable final (no la propia biblioteca, pero el final del programa) deberá agregar en **compilación de iOS**del argumento adicional (son parte de las opciones del proyecto) el "-gcc_flags" opción seguida de una cadena entre comillas que contiene todas las bibliotecas adicionales que son necesarias para el programa, por ejemplo:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

El ejemplo anterior se vinculará **libMyLibrary.a**

Puede usar `-gcc_flags` para especificar cualquier conjunto de argumentos de línea de comandos para pasar al compilador GCC que se usa para realizar el vínculo del archivo ejecutable final. Por ejemplo, esta línea de comandos, también hace referencia a la plataforma CFNetwork:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Si la biblioteca nativa contiene código de C++ también se debe pasar la marca - cxx en "Argumentos adicionales" para que sepa de Xamarin.iOS para usar el compilador correcto. Para C++ las opciones anteriores sería:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>Acceso a los métodos de C desde C&#35;

Hay dos tipos de bibliotecas nativas en iOS:

-  Compartir bibliotecas que forman parte del sistema operativo.

-  Bibliotecas estáticas que se envían con la aplicación.


Para tener acceso a los métodos definidos en cualquiera de ellas, usa [funcionalidad de P/Invoke de Mono](https://www.mono-project.com/docs/advanced/pinvoke/) que es la misma tecnología que usaría en. NET, que es aproximadamente:

-  Determinar qué función de C que desea invocar
-  Determinar su firma
-  Determinar qué biblioteca se encuentra en
-  Escribir la declaración P/Invoke adecuada


Al usar P/Invoke deberá especificar la ruta de acceso de la biblioteca que desea vincular con. Al usar iOS bibliotecas compartidas, puede codificar la ruta de acceso o puede usar las constantes de conveniencia que hemos definido en nuestra [clase constantes](https://developer.xamarin.com/api/type/Constants/), estas constantes deberían cubrir las bibliotecas compartidas de iOS.

Por ejemplo, si deseara invocar el método UIRectFrameUsingBlendMode de biblioteca de UIKit de Apple que tiene esta firma en C:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

La declaración P/Invoke tendría este aspecto:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

El Constants.UIKitLibrary es simplemente una constante definida como "/ System/Library/Frameworks/UIKit.framework/UIKit", el punto de entrada nos permite especificar opcionalmente el nombre externo (UIRectFramUsingBlendMode) al exponer un nombre diferente en C#, el RectFrameUsingBlendMode más corto.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>Acceso a Dylibs C

Si tiene la necesidad de consumir un Dylib C en su aplicación de Xamarin.iOS, hay un poco de configuración adicional que se requiere antes de llamar a la `DllImport` atributo.

Por ejemplo, si tenemos un `Animal.dylib` con un `Animal_Version` método que se advertirá en nuestra aplicación, es necesario informar a Xamarin.iOS de la ubicación de la biblioteca antes de intentar usarla.

Para ello, edite el `Main.CS` de archivo y dele un aspecto similar al siguiente:

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

Donde `/full/path/to/` es la ruta de acceso completa a la Dylib consumido. Con este código en su lugar, a continuación, podemos vincular a la `Animal_Version` método como sigue:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Bibliotecas estáticas

Dado que solo puede usar las bibliotecas estáticas en iOS, no hay ninguna biblioteca compartida externa para vincular con, por lo que el parámetro de ruta de acceso en el atributo DllImport se debe usar el nombre especial `__Internal` (tenga en cuenta los caracteres de subrayado doble al principio del nombre) en contraposición a el nombre de ruta de acceso.

Esto obliga DllImport para buscar el símbolo del método que se hace referencia en el programa actual, en lugar de intentar realizar la carga desde una biblioteca compartida.

