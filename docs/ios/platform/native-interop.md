---
title: Hacer referencia a bibliotecas nativas
ms.topic: article
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/28/2016
ms.openlocfilehash: 99e565c2268bec6d80c4976e604333cbd2f160a3
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="referencing-native-libraries"></a>Hacer referencia a bibliotecas nativas

Xamarin.iOS admite la vinculación con bibliotecas nativas de C y bibliotecas de C de objetivo. Este documento describe cómo vincular sus bibliotecas nativas de C con el proyecto de Xamarin.iOS. Para obtener información sobre hacer lo mismo para las bibliotecas de Objective-C, consulte nuestro [enlace Objective-C tipos](~/ios/platform/binding-objective-c/index.md) documento.

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Creación de bibliotecas nativas Universal (i386, ARMv7 y ARM64)

A menudo es conveniente para crear sus bibliotecas nativas para cada una de las plataformas admitidas para el desarrollo de iOS (i386 los propios dispositivos para el simulador y ARMv7/ARM64). Si ya tiene un proyecto de Xcode de la biblioteca, se trata realmente trivial hacer.

Para compilar la versión de i386 de la biblioteca nativa, ejecute el siguiente comando desde un terminal:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Esto dará como resultado en una biblioteca estática nativa en `MyProject.xcodeproj/build/Release-iphonesimulator/`. Copiar (o mover) el archivo de almacenamiento de biblioteca (libMyLibrary.a) en un lugar seguro para su uso posterior, dándole un nombre único (como **libMyLibrary i386.a**) para que los no entren en conflicto con las versiones de la misma biblioteca que se va a arm64 y armv7 crear a continuación.

Para compilar la versión ARM64 de la biblioteca nativa, ejecute el siguiente comando:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Esta vez se encuentra en la biblioteca nativa generada `MyProject.xcodeproj/build/Release-iphoneos/`. Una vez más, copiar (o mover) este archivo en una ubicación segura, cambiarle el nombre a algo parecido a **libMyLibrary arm64.a** para que no entren en conflicto.

Ahora, compile la versión de ARMv7 de la biblioteca:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

Copiar (o mover) el archivo de biblioteca resultante en la misma ubicación mover las otras 2 versiones de la biblioteca, cambiarle el nombre a algo parecido a **libMyLibrary armv7.a**.

Para realizar un universal binario, puede usar el `lipo` herramienta de este modo:

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

Esto crea `libMyLibrary.a` que será una biblioteca (fat) universal que será adecuada que se utilizará para todos los destinos de desarrollo de iOS.


### <a name="missing-required-architecture-i386"></a>Falta la arquitectura necesaria i386

Si va a obtener un `does not implement methodSignatureForSelector` o `does not implement doesNotRecognizeSelector` mensaje en la salida en tiempo de ejecución al intentar usar una biblioteca de Objective-C en el simulador, la biblioteca de iOS probablemente no se compiló con la arquitectura de i386 (vea la [creación Universal Bibliotecas nativas](#building_native) sección anterior).

Para comprobar las arquitecturas compatibles con una biblioteca concreta, utilice el siguiente comando en el Terminal:

```bash
lipo -info /full/path/to/libraryname.a
```

Donde `/full/path/to/` es la ruta de acceso completa a la biblioteca consumida y `libraryname.a` es el nombre de la biblioteca en cuestión.

Si tiene el origen a la biblioteca, debe compilar y agrupar de la arquitectura de i386 así, si desea probar la aplicación en el simulador de iOS.

### <a name="linking-your-library"></a>La biblioteca de vinculación

Cualquier biblioteca de otro fabricante que utilizas debe vincularse estáticamente a la aplicación. 

Si desea vincular estáticamente la biblioteca "libMyLibrary.a" que obtuvo en Internet o una compilación con Xcode, deberá hacer algunas cosas:

-  Poner la biblioteca en el proyecto
-  Configurar Xamarin.iOS para vincular la biblioteca
-  Acceso a los métodos de la biblioteca.


Para **poner la biblioteca en el proyecto**, seleccione el proyecto en el Explorador de soluciones y presione **comando + opción + a**. Navegue hasta la libMyLibrary.a y agregarlo al proyecto. Cuando se le solicite, indicar a Visual Studio para Mac o en Visual Studio copiarlo en el proyecto. Después de agregarlo, buscar el libFoo.a en el proyecto, haga clic con el botón secundario en él y establecer el **acción de compilación** a **ninguno**.

Para **Xamarin.iOS configurar para vincular la biblioteca**, acerca de las opciones de proyecto para una aplicación ejecutable final (no la propia biblioteca, pero el final del programa) necesite agregar en **iOS compilación**del argumento adicional (son forma parte de las opciones del proyecto) el "-gcc_flags" opción seguido por una cadena entre comillas que contiene todas las bibliotecas adicionales que son necesarias para el programa, por ejemplo:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

El ejemplo anterior se vinculará **libMyLibrary.a**

Puede usar `-gcc_flags` para especificar cualquier conjunto de argumentos de línea de comandos para pasar al compilador GCC que se use para realizar el enlace de su archivo ejecutable final. Por ejemplo, esta línea de comandos, también hace referencia el marco de trabajo de CFNetwork:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Si la biblioteca nativa contiene código de C++, también deberá pasar la marca - cxx en "Argumentos adicionales" para que Xamarin.iOS sepa que puede para usar el compilador correcto. Para C++ las opciones anteriores sería:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>Obtener acceso a los métodos de C de C&#35;

Hay dos tipos de bibliotecas nativas en iOS:

-  Bibliotecas compartidas que forman parte del sistema operativo.

-  Bibliotecas estáticas que se incluyen con la aplicación.


Para obtener acceso a los métodos definidos en cualquiera de ellas, utilice [funcionalidad de P/Invoke de Mono](http://www.mono-project.com/docs/advanced/pinvoke/) que es la misma tecnología que le gustaría usar en. NET, que es aproximadamente:

-  Determinar a qué función de C que desea invocar
-  Determinar la firma
-  Determinar la biblioteca en la que vive en
-  Escribir la correspondiente declaración P/Invoke


Cuando se utiliza P/Invoke debe especificar la ruta de acceso de la biblioteca que se está estableciendo vínculos. Al usar iOS bibliotecas compartidas, puede codificar la ruta de acceso o puede usar las constantes de conveniencia que hemos definido en nuestro [clase constantes](https://developer.xamarin.com/api/type/Constants/), estas constantes deberían cubrir las bibliotecas compartidas de iOS.

Por ejemplo, si desea invocar el método UIRectFrameUsingBlendMode de biblioteca de UIKit de Apple que tiene esta firma en C:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

La declaración de P/Invoke sería similar al siguiente:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

El Constants.UIKitLibrary es simplemente una constante definida como "/ System/Library/Frameworks/UIKit.framework/UIKit", el punto de entrada nos permite especificar opcionalmente el nombre externo (UIRectFramUsingBlendMode) al exponer un nombre diferente en C#, la cadena más corta RectFrameUsingBlendMode.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>Obtener acceso a Dylibs C

Si tiene la necesidad de utilizar un Dylib C en su aplicación Xamarin.iOS, hay cierta configuración adicional necesaria antes de llamar a la `DllImport` atributo.

Por ejemplo, si tenemos un `Animal.dylib` con un `Animal_Version` método que se llamará a en nuestra aplicación, es necesario informar a Xamarin.iOS de la ubicación de la biblioteca antes de intentar usarlo.

Para ello, edite la `Main.CS` de archivos y darle un aspecto similar al siguiente:

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

Donde `/full/path/to/` es la ruta de acceso completa a la Dylib consumido. Con este código en su lugar, a continuación, podemos vincular a la `Animal_Version` método tal como se indica a continuación:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Bibliotecas estáticas

Puesto que sólo se pueden utilizar las bibliotecas estáticas en iOS, no hay ninguna biblioteca compartida externa para vincular con, por lo que el parámetro de ruta de acceso en el atributo DllImport se debe usar el nombre especial `__Internal` (tenga en cuenta los caracteres de subrayado doble al principio del nombre) en contraposición a el nombre de ruta de acceso.

Esto obliga a DllImport para buscar el símbolo del método al que hace referencia en el programa actual, en lugar de intentar cargarlo desde una biblioteca compartida.

