---
title: Migrar un enlace a la API unificada
description: "Este artículo tratan los pasos necesarios para actualizar un proyecto de enlace de Xamarin existente para admitir las API unificada para aplicaciones Xamarin.IOS y Xamarin.Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: b07a88f25da1ea504785ddc4a0db8db1ed0e2650
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Migrar un enlace a la API unificada

_Este artículo tratan los pasos necesarios para actualizar un proyecto de enlace de Xamarin existente para admitir las API unificada para aplicaciones Xamarin.IOS y Xamarin.Mac._

#<a name="overview"></a>Información general

A partir del 1 de febrero de 2015 Apple requiere que todos los envíos nuevo a iTunes y la tienda de aplicaciones Mac deben ser aplicaciones de 64 bits. Como resultado, cualquier nueva aplicación Xamarin.iOS o Xamarin.Mac necesitará usar la nueva API unificada en lugar del MonoTouch clásico y MonoMac APIs existentes para admitir de 64 bits.

Además, cualquier proyecto de enlace de Xamarin también debe admitir las nuevas API unificada para incluirse en un proyecto de Xamarin.Mac o Xamarin.iOS de 64 bits. En este artículo se tratan los pasos necesarios para actualizar un proyecto de enlace existente para usar la API unificada.

#<a name="requirements"></a>Requisitos

El siguiente es necesario para completar los pasos que aparecen en este artículo:

 -  **Visual Studio para Mac** -la versión más reciente de Visual Studio para Mac instalado y configurado en el equipo de desarrollo.
 -  **Apple Mac** : una Apple mac es necesario para compilar proyectos de enlace para iOS y Mac.

Proyectos de enlace no se admiten en Visual studio en un equipo de Windows.

#<a name="modify-the-using-statements"></a>Modificar las instrucciones Using

Las API unificada resulta más fácil que nunca para compartir código entre Mac y iOS, así como lo que le permite admitir las aplicaciones de 32 y 64 bits con el mismo binario. Quitando la _MonoMac_ y _MonoTouch_ los prefijos de los espacios de nombres, uso compartido más fácil se logra a través de los proyectos de aplicación Xamarin.Mac y Xamarin.iOS.

Como resultado necesitamos modificar cualquiera de nuestros contratos de enlace (y otros `.cs` archivos en el proyecto de enlace) para quitar el _MonoMac_ y _MonoTouch_ prefijos de nuestro `using` instrucciones.

Por ejemplo, dada la siguiente con las instrucciones en un contrato de enlace:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

Se eliminan los `MonoTouch` prefijo resultante en lo siguiente:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

De nuevo, tendrá que hacerlo para cualquier `.cs` archivo nuestro proyecto de enlace. Con este cambio en su lugar, el siguiente paso es actualizar nuestro proyecto de enlace para usar los nuevos tipos de datos nativos.

Para obtener más información sobre la API unificado, vea el [API unificada](~/cross-platform/macios/unified/index.md) documentación. Para obtener más información sobre la compatibilidad de 32 y 64 bits aplicaciones y obtener información sobre marcos, consulte el [32 y 64 bits consideraciones de la plataforma](~/cross-platform/macios/32-and-64.md) documentación.

#<a name="update-to-native-data-types"></a>Actualización de tipos de datos nativos

Objective-C asigna el `NSInteger` tipo de datos que `int32_t` en sistemas de 32 bits y a `int64_t` en sistemas de 64 bits. Para que coincida con este comportamiento, la nueva API unificada reemplaza los usos anteriores de `int` (que en .NET se define como siempre que se va a `System.Int32`) a un nuevo tipo de datos: `System.nint`.

Junto con el nuevo `nint` tipo de datos, la API unificada presenta la `nuint` y `nfloat` tipos para la asignación a la `NSUInteger` y `CGFloat` también tipos.

Proporciona los pasos anteriores, es necesario revisar nuestra API y asegúrese de que cualquier instancia de `NSInteger`, `NSUInteger` y `CGFloat` que asignamos anteriormente a `int`, `uint` y `float` se actualizan al nuevo `nint`, `nuint` y `nfloat` tipos.

Por ejemplo, dada una definición de método de C de objetivo de:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Si el contrato de enlace anterior tenía la siguiente definición:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Se recomienda actualizar el nuevo enlace sea:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```
Si vamos a asignar a una biblioteca de entidad 3rd de versión más reciente que lo que nos habíamos vinculado inicialmente a, es necesario revisar la `.h` archivos de encabezado de la biblioteca y vea si cualquier existentes, explícitas las llamadas a `int`, `int32_t`, `unsigned int`, `uint32_t` o `float` se han actualizado para que sea un `NSInteger`, `NSUInteger` o `CGFloat`. Si es así, las mismas modificaciones en el `nint`, `nuint` y `nfloat` tipos deberá realizarse en sus asignaciones así.

Para obtener más información sobre estos cambios de tipo de datos, vea el [tipos nativos](~/cross-platform/macios/nativetypes.md) documento.

#<a name="update-the-coregraphics-types"></a>Actualizar los tipos de CoreGraphics

Los tipos de datos de punto, el tamaño y el rectángulo que se usan con `CoreGraphics` usar 32 o 64 bits en función del dispositivo que se ejecutan en. Cuando Xamarin originalmente enlaza iOS y Mac API se usan estructuras de datos existentes que se produjeron para que coincida con los tipos de datos en `System.Drawing` (`RectangleF` por ejemplo).

Debido a los requisitos para admitir los nuevos tipos de datos nativos y de 64 bits, necesitará los siguientes ajustes que se realizan en código existente al llamar a `CoreGraphic` métodos:

- **CGRect** -uso `CGRect` en lugar de `RectangleF` al definir flotante puntos regiones rectangulares.
- **CGSize** -uso `CGSize` en lugar de `SizeF` al definir tamaños de punto flotante (ancho y alto).
- **CGPoint** -uso `CGPoint` en lugar de `PointF` al definir un flotante señalar la ubicación (coordenadas X e Y).

Proporciona los pasos anteriores, tendrá que revisar nuestra API y asegúrese de que cualquier instancia de `CGRect`, `CGSize` o `CGPoint` que estaba enlazado anteriormente a `RectangleF`, `SizeF` o `PointF` cambiarse en el tipo nativo `CGRect`, `CGSize` o `CGPoint` directamente.

Por ejemplo, dado a un inicializador Objective-C de:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Si el enlace anterior incluye el código siguiente:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Se recomienda actualizar el código para:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Con todos los cambios de código ahora en su lugar, es necesario modificar el proyecto de enlace o defina los archivos para enlazar con las API unificada.

#<a name="modify-the-binding-project"></a>Modificar el proyecto de enlace

Como último paso a la actualización de nuestro proyecto de enlace para que utilicen las API unificada, es necesario modificar el `MakeFile` que se usan para compilar el proyecto o el tipo de proyecto de Xamarin (Si estamos enlazando desde dentro de Visual Studio para Mac) y dar instrucciones a _btouch_  para enlazar con las API unificada en lugar de la clásica los.


##<a name="updating-a-makefile"></a>Actualizar un archivo MAKE

Si se está usando un archivo MAKE para compilar el proyecto de enlace en un Xamarin. DLL, se deberá incluir el `--new-style` opción de línea de comandos y llame al método `btouch-native` en lugar de `btouch`.

Por lo que tiene los siguientes `MakeFile`:

```csharp
BINDDIR=/src/binding
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=XMBindingLibrarySample
PROJECT=$(PROJECT_ROOT)/XMBindingLibrarySample.xcodeproj
TARGET=XMBindingLibrarySample
BTOUCH=/Developer/MonoTouch/usr/bin/btouch


all: XMBindingLibrary.dll

libXMBindingLibrarySample-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

libXMBindingLibrarySample-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySample-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySampleUniversal.a: libXMBindingLibrarySample-armv7.a libXMBindingLibrarySample-i386.a libXMBindingLibrarySample-arm64.a
    lipo -create -output $@ $^

XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a

clean:
    -rm -f *.a *.dll
```

Tenemos que cambiar de llamar al método `btouch` a `btouch-native`, por lo que se ajuste el nuestra definición de macro de la siguiente manera:

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Se recomienda actualizar la llamada a `btouch` y agregue el `--new-style` opción como sigue:

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

Ahora podemos ejecutar nuestro `MakeFile` como normal para generar la nueva versión de 64 bits de la API.

##<a name="updating-a-binding-project-type"></a>Actualizar un tipo de proyecto de enlace

Si usamos un en Visual Studio para Mac enlace de plantilla de proyecto para crear nuestra API, necesitamos actualizar a la nueva versión de API unificada de la plantilla de proyecto de enlace. La manera más fácil de hacerlo es iniciar un nuevo proyecto de enlace de API unificada y copie sobre todo del código y la configuración existente.

Haga lo siguiente:

1. Inicie Visual Studio para Mac.
2. Seleccione **archivo** > **nueva** > **solución...**
3. En el cuadro de diálogo de solución nueva, seleccione **iOS** > **API unificada** > **iOS enlace proyecto**: 

    [ ![](update-binding-images/image01new.png "En el cuadro de diálogo de solución nueva, seleccione iOS / API unificada / proyecto de enlace de iOS")](update-binding-images/image01new.png)
4. En el diálogo 'Configurar el proyecto nuevo' Escriba un **nombre** para el nuevo proyecto de enlace y haga clic en el **Aceptar** botón.
5. Incluya la versión de 64 bits de biblioteca de C de objetivo que se va a crear enlaces para.
6. Copiar a través del código fuente desde el proyecto de enlace de API clásico existente de 32 bits (como el `ApiDefinition.cs` y `StructsAndEnums.cs` archivos).
7. Realice los cambios anotados anteriores a los archivos de código fuente.

Con todos estos cambios en su lugar, puede crear la nueva versión de 64 bits de la API como lo haría con la versión de 32 bits.

#<a name="summary"></a>Resumen

En este artículo hemos mostrado los cambios que deben realizarse en un proyecto de enlace de Xamarin existente para admitir las nuevas API unificada y dispositivos de 64 bits y los pasos necesarios para compilar la nueva versión compatible de 64 bits de una API.



## <a name="related-links"></a>Vínculos relacionados

- [IOS y Mac](~/cross-platform/macios/index.md)
- [API unificada](~/cross-platform/macios/nativetypes.md)
- [Consideraciones de la plataforma de 32 o 64 bits](~/cross-platform/macios/32-and-64.md)
- [Actualizar aplicaciones de iOS existente](~/cross-platform/macios/unified/updating-ios-apps.md)
- [API unificada](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://developer.xamarin.com/samples/monotouch/BindingSample/)
