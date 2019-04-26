---
title: Arquitectura de Xamarin.Mac
description: Esta guía explora Xamarin.Mac y su relación con Objective-C en un nivel bajo. Explican los conceptos como la compilación, los selectores, registradores, iniciar la aplicación y el generador.
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 04/12/2017
ms.openlocfilehash: 1ea38b527acaa89b9f25690de4e55664a7afd9e8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034146"
---
# <a name="xamarinmac-architecture"></a>Arquitectura de Xamarin.Mac

_Esta guía explora Xamarin.Mac y su relación con Objective-C en un nivel bajo. Explican los conceptos como la compilación, los selectores, registradores, iniciar la aplicación y el generador._

## <a name="overview"></a>Información general

Las aplicaciones de Xamarin.Mac ejecutan dentro del entorno de ejecución Mono y usar el compilador de Xamarin para compilar en lenguaje intermedio (IL), que es, a continuación, Just-in-Time (JIT) compilada a código nativo en tiempo de ejecución. Esto se ejecuta en paralelo con el tiempo de ejecución de C de objetivo. Ambos entornos en tiempo de ejecución se ejecutan en un kernel similares a UNIX, específicamente XNU y exponen varias API para el código de usuario que permite a los desarrolladores tener acceso al sistema nativo o administrado subyacente.

El diagrama siguiente muestra una visión general básica de esta arquitectura:

[![Diagrama que muestra una introducción básica a la arquitectura](architecture-images/mac-arch.png "diagrama que muestra una introducción básica a la arquitectura")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>Código nativo y administrado

Al desarrollar para Xamarin, los términos *nativo* y *administrada* usan a menudo el código. Código administrado es código que tiene su ejecución administrada por Common Language Runtime de .NET Framework, o en caso de Xamarin: el tiempo de ejecución Mono.

Código nativo es el código que se ejecutará de forma nativa en la plataforma concreta (por ejemplo, Objective-C o incluso código compilado de AOT, en un chip ARM). Esta guía explora cómo el código administrado se compila a código nativo y se explica cómo el funcionamiento de la aplicación de Xamarin.Mac, hacer un gran uso de las API de Mac de Apple mediante el uso de enlaces, y también disponer de acceso a. BCL de la red y un lenguaje sofisticado como C#.

## <a name="requirements"></a>Requisitos

Para desarrollar una aplicación maOS con Xamarin.Mac, se requiere lo siguiente:

- Un equipo Mac con macOS Sierra (10.12) o superior.
- La versión más reciente de Xcode (instalado desde la [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- La última versión de Xamarin.Mac y Visual Studio para Mac

La ejecución de aplicaciones de Mac creadas con Xamarin.Mac tiene los siguientes requisitos del sistema:

- Mac ejecutando Mac OS X 10.7 o superior.

## <a name="compilation"></a>Compilación

Al compilar cualquier aplicación de la plataforma Xamarin, el Mono C# (o F#) compilador se ejecutará y se compilará el C# y F# código en lenguaje intermedio de Microsoft (MSIL o IL). Xamarin.Mac, a continuación, usa un *Just in Time (JIT)* compilador en tiempo de ejecución para compilar el código nativo, lo que permite la ejecución en la arquitectura correcta según sea necesario.

Esto difiere de Xamarin.iOS que se utiliza la compilación de AOT. Cuando se usa el compilador AOT, todos los ensamblados y todos los métodos dentro de ellos se compilan en tiempo de compilación. Con JIT, compilación produce a petición solo para los métodos que se ejecutan.

Con las aplicaciones de Xamarin.Mac, Mono normalmente se incrusta en el lote de aplicaciones (y se conoce como **Mono incrustado**). Cuando se usa la API clásica de Xamarin.Mac, la aplicación podría usar en su lugar **sistema Mono**, sin embargo, esto no es compatible con la API unificada. Sistema Mono se refiere a Mono que se ha instalado en el sistema operativo. En el inicio de la aplicación, la aplicación de Xamarin.Mac lo usará.

## <a name="selectors"></a>Selectores

Con Xamarin, tenemos dos ecosistemas independientes, .NET y Apple, que es necesario poner juntos a resultar tan simplificada como sea posible, para asegurarse de que el objetivo final es una experiencia de usuario. Hemos visto en la sección sobre cómo se comunican los dos tiempos de ejecución y es muy bien es posible que haya oído del término "enlaces" que permite a las API nativas de Mac que se usará en Xamarin. Los enlaces se explican con más detalle en la [documentación de enlace de Objective-C](~/mac/platform/binding.md), por lo que por ahora, vamos a examinar el funcionamiento de Xamarin.Mac en segundo plano.

En primer lugar, debe haber una manera de exponer Objective-C a C#, que se realiza a través de los selectores. Un selector es un mensaje que se envía a un objeto o clase. Con Objective-c. Esto se realiza mediante el [objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html) funciones. Para obtener más información sobre el uso de selectores, hacen referencia a iOS [selectores de Objective-C](~/ios/internals/objective-c-selectors.md) guía. También debe haber una manera de exponer el código administrado y Objective-C, que es más complicado, puesto que la Objective-C no sabe nada sobre el código administrado. Para solucionar este problema, usamos un [registrador](~/mac/internals/registrar.md). Esto se explica con más detalle en la sección siguiente.

## <a name="registrar"></a>registrador

Como se mencionó anteriormente, el registrador es código que expone el código administrado a Objective-C. Para ello crea una lista de todas las clases administradas que se deriva de NSObject:

- Para todas las clases que no se están ajustando una clase existente de Objective-C, crea una nueva clase de Objective-C con los miembros de Objective-C para la creación de reflejo todos los miembros administrados que tienen un `[Export]` atributo.
- En las implementaciones para cada miembro de Objective-C, el código se agrega automáticamente al llamar al miembro reflejado administrado.

El pseudocódigo siguiente muestra un ejemplo de cómo hacerlo:

**C#(código administrado):**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**Objective-C (código nativo):**

```objc
@interface MyViewController : UIViewController
 - (void)myFunc;
@end 

@implementation MyViewController
- (void)myFunc {
    // Code to call the managed C# MyFunc method in MyViewController
}
@end
```

El código administrado puede contener los atributos, `[Register]` y `[Export]`, que usa el registrador de saber que el objeto debe exponerse a Objective-C. El atributo [registrar] se usa para especificar el nombre de la clase generada de Objective-C en caso de que el nombre generado predeterminado no es adecuado. Todas las clases derivadas de NSObject se registran automáticamente con Objective-C. El atributo required de [Export] contiene una cadena, que es el selector de la clase de Objective-C generada.

Hay dos tipos de registradores que se usa en Xamarin.Mac: estáticas y dinámicas:

- Registradores dinámicos: se trata el registrador predeterminado para todas las compilaciones de Xamarin.Mac. El registrador dinámico realiza el registro de todos los tipos en el ensamblado en tiempo de ejecución. Esto lo hace mediante el uso de las funciones proporcionadas por el tiempo de ejecución de Objective-C API. Por lo tanto, el registrador dinámico tiene un inicio más lento, pero más rápido tiempo de compilación. Las funciones nativas (normalmente en C), denominadas camas elásticas, se usan como implementaciones de método cuando se usa al dinámicos registradores. Varían entre arquitecturas diferentes.
- Registradores estáticos: el registrador estático genera código de Objective-C durante la compilación, lo que, a continuación, se compila en una biblioteca estática y vinculada en el archivo ejecutable. Esto permite que un inicio más rápido, pero tarda más tiempo durante el tiempo de compilación.

## <a name="application-launch"></a>Iniciar la aplicación

Lógica de inicio de Xamarin.Mac variarán en función de si incrustados o sistema de Mono se usa. Para ver el código y los pasos para iniciar la aplicación Xamarin.Mac, consulte el [inicio encabezado](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h) archivo en el repositorio público de xamarin macios.

## <a name="generator"></a>Generator

Xamarin.Mac contiene definiciones para todas las API de Mac. Puede examinar a través de cualquiera de estos en el [repositorio de github MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Estas definiciones contienen las interfaces con atributos, así como los métodos y propiedades necesarios. Por ejemplo, el código siguiente se utiliza para definir un NSBox en el [AppKit espacio de nombres](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526). Tenga en cuenta que es una interfaz con un número de métodos y propiedades:

```csharp
[BaseType (typeof (NSView))]
public interface NSBox {

        …

        [Export ("borderRect")]
        CGRect BorderRect { get; }

        [Export ("titleRect")]
        CGRect TitleRect { get; }

        [Export ("titleCell")]
        NSObject TitleCell { get; }

        [Export ("sizeToFit")]
        void SizeToFit ();

        [Export ("contentViewMargins")]
        CGSize ContentViewMargins { get; set; }

        [Export ("setFrameFromContentFrame:")]
        void SetFrameFromContentFrame (CGRect contentFrame);

        …

}
```

El generador, llamado `bmac` en Xamarin.Mac, toma estos archivos de definición y usa las herramientas de .NET para compilarlos en un ensamblado temporal. Sin embargo, no es utilizable para llamar a código de Objective-C este ensamblado temporal. El generador, a continuación, lee el ensamblado temporal y genera C# código que se puede usar en tiempo de ejecución. Esto es, ¿por qué, por ejemplo, si agrega un atributo aleatorio para el archivo de definición. cs, no aparece en el código de salida. El generador no lo sabe y por lo tanto, `bmac` no sabe que se busca en el ensamblado temporal para la salida se.

Una vez que el Xamarin.Mac.dll se ha creado, el empaquetador, `mmp`, empaquetará todos los componentes entre sí.

En un nivel alto, esto consigue mediante la ejecución de las siguientes tareas:

- Crear una estructura de paquete de aplicación.
- Copie los ensamblados administrados.
- Si está habilitada la vinculación, ejecute el enlazador administrado para optimizar los ensamblados mediante la eliminación de elementos no utilizados.
- Crear una aplicación de iniciador, vinculación en el código del iniciador hablado junto con el código de registrador si se encuentra en modo estático.

Esto es, a continuación, el proceso que se compila el código de usuario en un ensamblado de la compilación de ejecución como parte del usuario esa referencia Xamarin.Mac.dll y se ejecuta `mmp` para que sea un paquete

Para obtener más información en el vinculador y cómo se utiliza, consulte el iOS [vinculador](~/ios/deploy-test/linker.md) guía.

## <a name="summary"></a>Resumen

Esta guía examinado la compilación de aplicaciones de Xamarin.Mac y Xamarin.Mac explorados y su relación con Objective-C.
