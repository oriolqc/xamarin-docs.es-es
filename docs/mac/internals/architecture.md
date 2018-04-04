---
title: Arquitectura de Xamarin.Mac
description: Esta guía explora Xamarin.Mac y su relación con objetivo-C en un nivel bajo. Explican los conceptos como la compilación, selectores, registradores, iniciar la aplicación y el generador.
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/12/2017
ms.openlocfilehash: d6d7557fed5ea0ca0719dcbddbda316340645320
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinmac-architecture"></a>Arquitectura de Xamarin.Mac

_Esta guía explora Xamarin.Mac y su relación con objetivo-C en un nivel bajo. Explican los conceptos como la compilación, selectores, registradores, iniciar la aplicación y el generador._

## <a name="overview"></a>Información general

Xamarin.Mac aplicaciones ejecutan dentro del entorno de ejecución Mono y use el compilador de Xamarin para compilar a lenguaje intermedio (IL), que es, a continuación, Just-in-Time (JIT) compilada a código nativo en tiempo de ejecución. Esto se ejecuta en paralelo con el tiempo de ejecución de C de objetivo. Ambos entornos en tiempo de ejecución se ejecutan sobre un núcleo similares a UNIX, específicamente XNU y exponen varias API en el código de usuario que permite a los desarrolladores tener acceso al sistema nativo o administrado subyacente.

El diagrama siguiente muestra una visión general básica de esta arquitectura:

[![Diagrama que muestra una introducción básica a la arquitectura](architecture-images/mac-arch.png "diagrama que muestra una introducción básica a la arquitectura")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>Código nativo y administrado

Al desarrollar para Xamarin, los términos *nativo* y *administrada* código se utilizan a menudo. El código administrado es código que tiene su ejecución administrado por Common Language Runtime de .NET Framework, o en caso de Xamarin: el tiempo de ejecución de Mono.

Código nativo es el código que se ejecutará de forma nativa en la plataforma concreta (por ejemplo, Objective-C o incluso código AOT compilado, en un chip ARM). Esta guía explora cómo el código administrado se compila a código nativo y se explica cómo un aplicación Xamarin.Mac funciona, realizar un uso completo de API de Mac de Apple mediante el uso de enlaces, al tiempo que también tiene acceso a. BCL de NET y un lenguaje sofisticado, como C#.

## <a name="requirements"></a>Requisitos

Para desarrollar una aplicación maOS con Xamarin.Mac, se requiere lo siguiente:

- Un macOS de ejecución de Mac Sierra (10.12) o superior.
- La versión más reciente de Xcode (instalar desde el [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- La versión más reciente de Xamarin.Mac y Visual Studio para Mac

La ejecución de aplicaciones de Mac creadas con Xamarin.Mac tiene los siguientes requisitos del sistema:

- Un Mac que ejecutan Mac OS X 10.7 o mayor.

## <a name="compilation"></a>Compilación

Al compilar cualquier aplicación de la plataforma Xamarin, el compilador de C# Mono (o F #) se ejecutará y compilará el código de C# y F # en lenguaje intermedio de Microsoft (MSIL o IL). Xamarin.Mac, a continuación, utiliza un *Just in Time (JIT)* compilador en tiempo de ejecución para compilar código nativo, lo que permite la ejecución en la arquitectura correcta según sea necesario.

Esto difiere de Xamarin.iOS que se utiliza la compilación de AOT. Cuando se usa el compilador AOT, todos los ensamblados y todos los métodos dentro de ellos se compilan en tiempo de compilación. Con JIT, la compilación se produce solo para los métodos que se ejecutan a petición.

Con aplicaciones de Xamarin.Mac, Mono normalmente se incrusta en el paquete de aplicación (y se conoce como **Mono incrustado**). Si usa la API Xamarin.Mac clásico, la aplicación en su lugar puede usar **sistema Mono**, sin embargo, esto es no se admiten en la API unificada. Sistema Mono hace referencia a Mono que se ha instalado en el sistema operativo. Al iniciar la aplicación, la aplicación Xamarin.Mac usará.

## <a name="selectors"></a>Selectores

Con Xamarin, tenemos dos ecosistemas independientes, .NET y Apple, que es necesario poner juntos para parece tan sencilla como sea posible, para asegurarse de que el objetivo final es una experiencia de usuario sin problemas. Hemos visto en la sección sobre cómo se comunican los dos tiempos de ejecución y es muy bien haya oído del término 'enlaces', que permite a las API nativas de Mac para su uso en Xamarin. Los enlaces se explican con más detalle en el [documentación de enlace Objective-C](~/mac/platform/binding.md), por lo que, por ahora, veamos cómo funciona Xamarin.Mac en segundo plano.

En primer lugar, tiene que haber una manera de exponer Objective-C a C#, que se realiza a través de selectores. Un selector es un mensaje que se envía a un objeto o una clase. Con Objective-c. Esto se realiza mediante la [objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html) funciones. Para obtener más información sobre el uso de selectores, consulte el archivo iOS [selectores Objective-C](~/ios/internals/objective-c-selectors.md) guía. También debe haber una manera de exponer el código administrado para Objective-C, que es más complicado, puesto que el objetivo de C no sabe nada sobre el código administrado. Para solucionar esto, usamos un [registrador](~/mac/internals/registrar.md). Esto se explica con más detalle en la sección siguiente.

## <a name="registrar"></a>registrador

Como se mencionó anteriormente, el registrador es código que expone el código administrado al objetivo de C. Esto hace mediante la creación de una lista de cada clase administrada que se deriva de NSObject:

- Para todas las clases que no se ajuste una clase existente de Objective-C, crea una nueva clase de objetivo-C con miembros Objective-C creación de reflejo de todos los miembros administrados que tienen un `[Export]` atributo.
- En las implementaciones para cada miembro del objetivo – C, el código se agrega automáticamente para llamar al miembro administrado reflejado.

El pseudocódigo siguiente muestra un ejemplo de cómo hacerlo:

**C# (código administrado):**

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

El código administrado puede contener los atributos, `[Register]` y `[Export]`, que utiliza el registrador de saber que el objeto se debe exponer al objetivo de C. El atributo [registrar] se utiliza para especificar el nombre de la clase Objective-C generada en caso de que el nombre generado de forma predeterminada no es adecuado. Todas las clases derivadas de NSObject se registran automáticamente con el objetivo de C. El atributo [Exportar] necesario contiene una cadena, que es el selector de la clase Objective-C generada.

Hay dos tipos de registradores que se usa en Xamarin.Mac: dinámico y estático:

- Registradores dinámicos: se trata el registrador predeterminado para todas las compilaciones de Xamarin.Mac. El registrador dinámico realiza el registro de todos los tipos en el ensamblado en tiempo de ejecución. Esto lo hace mediante el uso de funciones disponibles en tiempo de ejecución de C objetivo API. Por consiguiente, el registrador dinámico tiene un inicio más lento, pero una más rápida tiempo de compilación. Las funciones nativas (normalmente en C), denominadas camas elásticas, se usan como implementaciones de método cuando se usa al registradores dinámicos. Varían entre diferentes arquitecturas.
- Registradores estáticos: el registrador estático genera código Objective-C durante la compilación, que, a continuación, se compila en una biblioteca estática y vinculada en el archivo ejecutable. Esto permite que un inicio rápido, pero tarda más tiempo durante el tiempo de compilación.

## <a name="application-launch"></a>Iniciar la aplicación

Lógica de inicio de Xamarin.Mac variarán en función de si se incrusta o se utiliza sistema Mono. Para ver el código y los pasos para iniciar la aplicación Xamarin.Mac, consulte el [inicio encabezado](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h) archivo en el repositorio público de macios de xamarin.

## <a name="generator"></a>Generator

Xamarin.Mac contiene definiciones para todas las API Mac. Puede examinar cualquiera de estos en el [repositorio de github de MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Estas definiciones de contengan interfaces con atributos, así como los métodos y propiedades necesarios. Por ejemplo, es el código siguiente se utiliza para definir un NSBox en la [AppKit espacio de nombres](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526). Tenga en cuenta que es una interfaz con una serie de métodos y propiedades:

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

El generador, denominado `bmac` en Xamarin.Mac, toma estos archivos de definición y utiliza herramientas de .NET para compilarlos en un ensamblado temporal. Sin embargo, este ensamblado temporal no es utilizable para llamar a código Objective-C. El generador, a continuación, lee el ensamblado temporal y genera código de C# que se puede utilizar en tiempo de ejecución. Esto es la razón de ello, por ejemplo, si agrega un atributo aleatorio para el archivo de definición de. cs, no se mostrarán en el código de salida. El generador no saberlo y, por tanto, `bmac` no sabe para que busque en el ensamblado temporal para la salida se.

Una vez que la Xamarin.Mac.dll se ha creado, el empaquetador, `mmp`, se agrupar todos los componentes.

En un nivel alto, esto consigue mediante la ejecución de las tareas siguientes:

- Crear una estructura de paquete de aplicación.
- Copiar en los ensamblados administrados.
- Si se habilita la vinculación, ejecute el enlazador administrado para optimizar los ensamblados mediante la eliminación de elementos no utilizados.
- Cree una aplicación de iniciador, vinculación en el código de iniciador hablado junto con el código registar si se encuentra en modo estático.

Esto es, a continuación, el proceso que se compila el código de usuario en un ensamblado de compilación de ejecución como parte del usuario esa referencia Xamarin.Mac.dll y se ejecuta `mmp` para que sea un paquete

Para obtener más información sobre el enlazador y cómo se utiliza, consulte el archivo iOS [vinculador](~/ios/deploy-test/linker.md) guía.

## <a name="summary"></a>Resumen

Esta guía examinando la compilación de aplicaciones de Xamarin.Mac y explorar Xamarin.Mac y su relación con el objetivo de C.
