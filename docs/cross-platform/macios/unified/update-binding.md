---
title: Migración de un enlace a Unified API
description: En este artículo se trata los pasos necesarios para actualizar un proyecto de enlace de Xamarin existente para admitir las API unificada para aplicaciones de Xamarin.IOS y Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: f081ccda507fe3fe65af0e2fb50841aecd7b3c23
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830462"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Migración de un enlace a Unified API

_En este artículo se trata los pasos necesarios para actualizar un proyecto de enlace de Xamarin existente para admitir las API unificada para aplicaciones de Xamarin.IOS y Xamarin.Mac._

## <a name="overview"></a>Información general

A partir del 1 de febrero de 2015, Apple requiere que todos los envíos de nuevo a iTunes y el Store de la aplicación Mac deben ser aplicaciones de 64 bits. Como resultado, cualquier nueva aplicación Xamarin.iOS o Xamarin.Mac deberá usar la nueva Unified API en lugar del clásico MonoTouch y MonoMac APIs existentes para admitir de 64 bits.

Además, cualquier proyecto de enlace de Xamarin también debe admitir las nuevas API unificada que se incluirán en un proyecto de Xamarin.Mac o Xamarin.iOS de 64 bits. En este artículo se describen los pasos necesarios para actualizar un proyecto de enlace existente para usar la API unificada.

## <a name="requirements"></a>Requisitos

Para completar los pasos que aparecen en este artículo, se requiere lo siguiente:

- **Visual Studio para Mac** -la versión más reciente de Visual Studio para Mac instalado y configurado en el equipo de desarrollo.
- **Apple Mac** : una Apple mac es necesario para compilar proyectos de enlace para iOS y Mac.

Proyectos de enlace no se admiten en Visual studio en un equipo Windows.

## <a name="modify-the-using-statements"></a>Modificar las instrucciones Using

Las API unificada hace más fácil que nunca para compartir código entre Mac y iOS, así como lo que le permite admitir las aplicaciones de 32 y 64 bits con el mismo binario. Quitando el _MonoMac_ y _MonoTouch_ prefijos de los espacios de nombres, se consigue más sencillo compartir entre los proyectos de aplicación de Xamarin.Mac y Xamarin.iOS.

Como resultado necesitamos modificar cualquiera de nuestros contratos de enlace (y otros `.cs` archivos en nuestro proyecto de enlace) para quitar el _MonoMac_ y _MonoTouch_ prefijos desde nuestro `using` instrucciones.

Por ejemplo, dada la siguiente mediante instrucciones en un contrato de enlace:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

Se podría quitar el `MonoTouch` prefijo resultante en la siguiente:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

Nuevamente, necesitamos hacer esto para cualquier `.cs` archivo en nuestro proyecto de enlace. Con este cambio en su lugar, el siguiente paso es actualizar nuestro proyecto de enlace para usar los nuevos tipos de datos nativos.

Para obtener más información sobre la API unificada, consulte el [Unified API](~/cross-platform/macios/unified/index.md) documentación. Para obtener más información sobre la compatibilidad con 32 y 64 bits aplicaciones e información sobre los marcos de trabajo, consulte el [32 y 64 bits de consideraciones de la plataforma](~/cross-platform/macios/32-and-64/index.md) documentación.

## <a name="update-to-native-data-types"></a>Actualización de tipos de datos nativos

Objective-C se asigna el `NSInteger` tipo de datos que `int32_t` en sistemas de 32 bits y a `int64_t` en sistemas de 64 bits. Para que coincida con este comportamiento, la nueva Unified API reemplaza los usos anteriores de `int` (que en .NET se define como siempre que se va a `System.Int32`) a un nuevo tipo de datos: `System.nint`.

Junto con el nuevo `nint` Unified API de tipo de datos, presenta el `nuint` y `nfloat` tipos para la asignación a la `NSUInteger` y `CGFloat` tipos también.

Dado lo anterior, tenemos que revisar nuestra API y asegúrese de que cualquier instancia de `NSInteger`, `NSUInteger` y `CGFloat` que asignamos anteriormente para `int`, `uint` y `float` se actualizan al nuevo `nint`, `nuint` y `nfloat` tipos.

Por ejemplo, dada una definición de método Objective-C de:

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
Si vamos a asignar a una biblioteca de terceros 3ª de versión más reciente que lo que nos habíamos vinculado inicialmente a, se debe revisar el `.h` archivos de encabezado de la biblioteca y ver si cualquier explícitas existentes, las llamadas a `int`, `int32_t`, `unsigned int`, `uint32_t` o `float` se han actualizado para que sea un `NSInteger`, `NSUInteger` o `CGFloat`. Si es así, las mismas modificaciones en el `nint`, `nuint` y `nfloat` tipos debe establecerse también sus asignaciones.

Para obtener más información sobre estos cambios de tipo de datos, vea la documentación de [tipos nativos](~/cross-platform/macios/nativetypes.md) documento.

## <a name="update-the-coregraphics-types"></a>Actualizar los tipos de CoreGraphics

Los tipos de datos punto, el tamaño y el rectángulo que se usan con `CoreGraphics` usar 32 o 64 bits en función del dispositivo que se ejecutan. Cuando Xamarin originalmente enlaza las API de Mac y iOS se usan estructuras de datos existentes que se produjeron para que coincida con los tipos de datos en `System.Drawing` (`RectangleF` por ejemplo).

Debido a los requisitos para admitir los nuevos tipos de datos nativos y de 64 bits, se deben realizarse en el código existente al llamar a los siguientes ajustes `CoreGraphic` métodos:

- **CGRect** -uso `CGRect` en lugar de `RectangleF` al definir flotante puntos regiones rectangulares.
- **CGSize** -uso `CGSize` en lugar de `SizeF` al definir tamaños de punto flotante (ancho y alto).
- **CGPoint** -uso `CGPoint` en lugar de `PointF` al definir un flotante (coordenadas X e Y) de la ubicación del punto.

Dado lo anterior, tendrá que revisar nuestra API y asegúrese de que cualquier instancia de `CGRect`, `CGSize` o `CGPoint` que estaba enlazado anteriormente a `RectangleF`, `SizeF` o `PointF` cambiarse al tipo nativo `CGRect`, `CGSize` o `CGPoint` directamente.

Por ejemplo, con un inicializador de Objective-C de:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Si el enlace anterior incluye el código siguiente:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Se recomienda actualizar dicho código:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Con todos los cambios de código ahora en su lugar, necesitamos modificar nuestro proyecto de enlace o hacer que el archivo para enlazar con las API unificada.

## <a name="modify-the-binding-project"></a>Modificar el proyecto de enlace

Como paso final a la actualización de nuestro proyecto de enlace para usar las API unificada, es necesario modificar el `MakeFile` que utilizamos para crear el proyecto o el tipo de proyecto de Xamarin (si se realiza el enlace desde dentro de Visual Studio para Mac) y dar instrucciones a _btouch_  para enlazar con la API unificada en lugar de las clásica.


### <a name="updating-a-makefile"></a>Actualización de un archivo MAKE

Si estamos usando un archivo MAKE para crear nuestro proyecto de enlace en un Xamarin. Archivo DLL, se deberá incluir el `--new-style` opción de línea de comandos y llamada `btouch-native` en lugar de `btouch`.

Por lo que tenemos la siguiente `MakeFile`:

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

Es necesario cambiar de llamar a `btouch` a `btouch-native`, por lo que se podría ajustar nuestra definición de macro como sigue:

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Se recomienda actualizar la llamada a `btouch` y agregue el `--new-style` opción como sigue:

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

Ahora podemos ejecutar nuestro `MakeFile` como normal para crear la nueva versión de 64 bits de la API.

### <a name="updating-a-binding-project-type"></a>Actualización de un tipo de proyecto de enlace

Si usamos un Visual Studio para Mac enlace de plantilla de proyecto para crear nuestra API, se deberá actualizar a la nueva versión de API unificada de la plantilla de proyecto de enlace. La manera más fácil de hacerlo es empezar un nuevo proyecto de enlace de API unificada y copie todo el código y la configuración existente.

Haga lo siguiente:

1. Inicie Visual Studio para Mac.
2. Seleccione **archivo** > **nueva** > **solución...**
3. En el cuadro de diálogo de solución nueva, seleccione **iOS** > **Unified API** > **enlace proyecto iOS**: 

    [![](update-binding-images/image01new.png "En el cuadro de diálogo de solución nueva, seleccione iOS / Unified API / proyecto de enlace de iOS")](update-binding-images/image01new.png#lightbox)
4. En el cuadro de diálogo 'Configurar el nuevo proyecto' Escriba un **nombre** para el nuevo proyecto de enlace y haga clic en el **Aceptar** botón.
5. Incluyen la versión de 64 bits de la biblioteca de Objective-C que se va a crear los enlaces para.
6. Copiar el código fuente desde el proyecto de enlace de API clásica de 32 bits existente (como el `ApiDefinition.cs` y `StructsAndEnums.cs` archivos).
7. Realice los cambios se indicó anteriormente a los archivos de código fuente.

Con todos estos cambios en su lugar, puede crear la nueva versión de 64 bits de la API como lo haría con la versión de 32 bits.

## <a name="summary"></a>Resumen

En este artículo hemos mostrado los cambios que deben realizarse a un proyecto de enlace de Xamarin existente para admitir las nuevas API unificada y dispositivos de 64 bits y los pasos necesarios para compilar la nueva versión compatible de 64 bits de una API.



## <a name="related-links"></a>Vínculos relacionados

- [IOS y Mac](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/nativetypes.md)
- [Consideraciones de la plataforma de 32 o 64 bits](~/cross-platform/macios/32-and-64/index.md)
- [Actualizar aplicaciones iOS existentes](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://developer.xamarin.com/samples/monotouch/BindingSample/)
