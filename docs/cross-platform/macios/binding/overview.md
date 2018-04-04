---
title: Información general
description: Detalles de cómo funciona el proceso de enlace
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: d1a90934cf7a9a832172f32ed95cf3e254e04385
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="overview"></a>Información general

_Detalles de cómo funciona el proceso de enlace_

Enlace de una biblioteca de C de objetivo para su uso con Xamarin toma de tres pasos:

1. Escribe una "Definición de API" de C#. para describir cómo se expone la API nativa en .NET y cómo se asigna el objetivo subyacente-C. Esto se hace uso estándar de C# de construcciones como `interface` y el enlace de varios **atributos** (verlo [ejemplo sencillo](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Una vez se ha escrito la "definición de API" en C#, compilarlo para generar un ensamblado de "enlace". Esto puede hacerse en el [ **línea de comandos** ](#commandline) o mediante un [ **proyecto enlace** ](#bindingproject) en Visual Studio para Mac o en Visual Studio.

3. Ese ensamblado "enlace", a continuación, se agrega al proyecto de aplicación Xamarin, por lo que puede tener acceso a la funcionalidad nativa mediante la API que se ha definido.
  El proyecto de enlace es completamente independiente de los proyectos de aplicación.

**Nota:** Paso1 puede automatizarse con la Ayuda de [ **objetivo Sharpie**](#objectivesharpie). Examina la API de C de objetivo y genera una propuesta de C# "Definición de API". También puede personalizar los archivos creados por objetivo Sharpie y usarlos en un proyecto de enlace (o en la línea de comandos) para crear el ensamblado de enlace. Sharpie objetivo no crea enlaces por sí solo, es simplemente una parte opcional del proceso mayor.

También puede leer más detalles técnicos de [su funcionamiento](#howitworks), que le ayudará a escribir sus enlaces.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Enlaces de la línea de comandos

Puede usar el `btouch-native` para Xamarin.iOS (o `bmac-native` si usas Xamarin.Mac) para crear enlaces directamente. Funciona pasando las definiciones de API de C# que haya creado manualmente (o mediante objetivo Sharpie) a la herramienta de línea de comandos (`btouch-native` para iOS o `bmac-native` para Mac).


La sintaxis general para llamar a estas herramientas es:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

El comando anterior, genera el archivo `cocos2d.dll` en el directorio actual, y contendrá la biblioteca totalmente dependiente que puede usar en el proyecto. Esta es la herramienta que utiliza Visual Studio para Mac para crear los enlaces si se usa un proyecto de enlace (descrito [a continuación](#bindingproject)).


<a name="bindingproject" />

## <a name="binding-project"></a>Proyecto de enlace

Un proyecto de enlace puede crearse en Visual Studio para Mac o en Visual Studio (Visual Studio solo admite enlaces de iOS) y resulta más fácil editar y crear definiciones de API para el enlace (frente al uso de la línea de comandos).

Siga este [Guía de introducción](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) para ver cómo crear y utilizar un proyecto de enlace para generar un enlace.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Sharpie objetivo

Sharpie objetivo es la herramienta de línea de comandos de otra, independiente que permite realizar las fases iniciales de la creación de un enlace. No crea un enlace por sí solo, en su lugar automatiza el primer paso consiste en generar una definición de la API de la biblioteca nativa de destino.

Lectura del [documentos objetivo Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md) para obtener información sobre cómo analizar bibliotecas nativas y marcos de nativas, CocoaPods en definiciones de API que se pueden integrar en enlaces.

<a name="howitworks" />

## <a name="how-binding-works"></a>Cómo funciona el enlace

Es posible utilizar el [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo, [[Exportar]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) atributo, y [manual invocación del selector de Objective-C](~/ios/internals/objective-c-selectors.md) conjuntamente para enlazar manualmente nuevos (anteriormente tipos de Objective-C sin enlazar).

En primer lugar, encontrar un tipo que se va a enlazar. Para fines de descripción (y simplicidad), se podrá enlazar el [NSEnumerator](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) tipo (que ya se ha enlazado en [Foundation.NSEnumerator](https://developer.xamarin.com/api/type/Foundation.NSEnumerator/); la siguiente implementación es simplemente por ejemplo con fines).

En segundo lugar, es necesario crear el tipo de C#. Es probable que quiera colocarlo en un espacio de nombres; Puesto que el objetivo de C no admite espacios de nombres, se deberá usar el `[Register]` atributo para cambiar el nombre de tipo que se registrarán Xamarin.iOS con el tiempo de ejecución de C de objetivo. El tipo de C# también debe heredar de [Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

En tercer lugar, revise la documentación de C de objetivo y crear [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) instancias para cada selector que se va a usar. Coloque estos dentro del cuerpo de la clase:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

En cuarto lugar, el tipo deberá proporcionar constructores. Se *debe* encadenar la invocación del constructor al constructor de clase base. El `[Export]` atributos permiten código Objective-C para llamar a los constructores con el nombre de selector especificado:

```csharp
[Export("init")]
public NSEnumerator()
    : base(NSObjectFlag.Empty)
{
    Handle = Messaging.IntPtr_objc_msgSend(this.Handle, selInit.Handle);
}
```

```csharp
// This constructor must be present so that Xamarin.iOS
// can create instances of your type from Objective-C code.
public NSEnumerator(IntPtr handle)
    : base(handle)
{
}
```

En quinto lugar, proporcionan métodos para cada uno de los selectores declaran en el paso 3. Estos usarán `objc_msgSend()` para invocar el selector en el objeto nativo. Tenga en cuenta el uso de [Runtime.GetNSObject()](https://developer.xamarin.com/api/member/ObjCRuntime.Runtime.GetNSObject/(System.IntPtr)) para convertir un `IntPtr` en una forma adecuada con tipo `NSObject` (sub) tipo. Si desea que el método se pueda llamar desde código Objective-C, el miembro *debe* ser **virtuales**.

```csharp
[Export("nextObject")]
public virtual NSObject NextObject()
{
    return Runtime.GetNSObject(
        Messaging.IntPtr_objc_msgSend(this.Handle, selNextObject.Handle));
}
```

```csharp
// Note that for properties, [Export] goes on the get/set method:
public virtual NSArray AllObjects {
    [Export("allObjects")]
    get {
        return (NSArray) Runtime.GetNSObject(
            Messaging.IntPtr_objc_msgSend(this.Handle, selAllObjects.Handle));
    }
}
```

Perspectiva general:

```csharp
using System;
using Foundation;
using ObjCRuntime;

namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        static Selector selInit       = new Selector("init");
        static Selector selAllObjects = new Selector("allObjects");
        static Selector selNextObject = new Selector("nextObject");

        [Export("init")]
        public NSEnumerator()
            : base(NSObjectFlag.Empty)
        {
            Handle = Messaging.IntPtr_objc_msgSend(this.Handle,
                selInit.Handle);
        }

        public NSEnumerator(IntPtr handle)
            : base(handle)
        {
        }

        [Export("nextObject")]
        public virtual NSObject NextObject()
        {
            return Runtime.GetNSObject(
                Messaging.IntPtr_objc_msgSend(this.Handle,
                    selNextObject.Handle));
        }

        // Note that for properties, [Export] goes on the get/set method:
        public virtual NSArray AllObjects {
            [Export("allObjects")]
            get {
                return (NSArray) Runtime.GetNSObject(
                    Messaging.IntPtr_objc_msgSend(this.Handle,
                        selAllObjects.Handle));
            }
        }
    }
}
```

