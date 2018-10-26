---
title: Arquitectura de aplicación de iOS
description: Este documento describe Xamarin.iOS en un código de modo nativo y administrado de bajo nivel, analizar interactuar, compilación AOT, selectores, registradores, iniciar la aplicación y el generador.
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 106357e9442d51fdd31bb30b4f0342e2b59f67fd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118492"
---
# <a name="ios-app-architecture"></a>Arquitectura de aplicación de iOS

Aplicaciones de Xamarin.iOS ejecutar dentro del entorno de ejecución Mono y utilizar la compilación de adelante of Time (AOT) completo para compilar C# código de lenguaje ensamblador ARM. Esto se ejecuta en paralelo con la [en tiempo de ejecución Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Ambos entornos en tiempo de ejecución se ejecutan en un kernel de similares a UNIX, específicamente [XNU](https://en.wikipedia.org/wiki/XNU)y expone varias API para el código de usuario que permite a los desarrolladores tener acceso al sistema nativo o administrado subyacente.

El diagrama siguiente muestra una visión general básica de esta arquitectura:

[ ![](architecture-images/ios-arch-small.png "Este diagrama muestra una introducción básica a la arquitectura de la compilación con antelación of Time (AOT)")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Nativo y código administrado: una explicación

Al desarrollar para Xamarin los términos *nativo y administrado* usan a menudo el código. [Código administrado](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) es código que tiene su ejecución administrado por el [Common Language Runtime de .NET Framework](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx), o en caso de Xamarin: el tiempo de ejecución Mono. Esto es lo que llamamos un lenguaje intermedio.

Código nativo es el código que se ejecutará de forma nativa en la plataforma concreta (por ejemplo, Objective-C o incluso código compilado de AOT, en un chip ARM). Esta guía explora cómo AOT compila el código administrado a código nativo y se explica cómo el funcionamiento de la aplicación de Xamarin.iOS, hacer un gran uso de las API de iOS de Apple mediante el uso de enlaces, y también disponer de acceso a. BCL de la red y un lenguaje sofisticado como C#.

## <a name="aot"></a>AOT

Al compilar cualquier aplicación de la plataforma Xamarin, el Mono C# (o F#) compilador se ejecutará y se compilará el C# y F# código en lenguaje intermedio de Microsoft (MSIL). Si está ejecutando un Xamarin.Android, una aplicación de Xamarin.Mac o incluso una aplicación de Xamarin.iOS en el simulador, el [.NET Common Language Runtime (CLR)](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx) compila el código MSIL mediante un Just-in compilador Time (JIT). En tiempo de ejecución que esto se compila en un código nativo, lo que puede ejecutar en la arquitectura correcta para su aplicación.

Sin embargo, hay una restricción de seguridad en iOS, establecidas por Apple, que no permite la ejecución del código generado de forma dinámica en un dispositivo.
Para asegurarse de que se cumplan estos protocolos de seguridad, Xamarin.iOS en su lugar, usa un compilador adelante of Time (AOT) para compilar el código administrado. Esto genera un binario, de iOS nativa optimizados, opcionalmente, con funcionalidad LLVM para dispositivos, que se pueden implementar en el procesador de basado en ARM de Apple. Se muestra un diagrama de cómo esto encaja aproximado:

[![](architecture-images/aot.png "Un diagrama de cómo esto encaja aproximado")](architecture-images/aot-large.png#lightbox)

Uso de AOT tiene una serie de limitaciones, que se detallan en el [limitaciones](~/ios/internals/limitations.md) guía. También proporciona una serie de mejoras sobre JIT a través de una reducción en el tiempo de inicio y diversas optimizaciones de rendimiento

Ahora que hemos exploramos cómo se compila el código de origen en código nativo, echemos un vistazo en segundo plano para ver cómo Xamarin.iOS nos permite escribir aplicaciones completamente nativas de iOS

## <a name="selectors"></a>Selectores

Con Xamarin, tenemos dos ecosistemas independientes, .NET y Apple, que es necesario poner juntos a resultar tan simplificada como sea posible, para asegurarse de que el objetivo final es una experiencia de usuario. Hemos visto en la sección sobre cómo se comunican los dos tiempos de ejecución y es muy bien es posible que haya oído del término "enlaces" que permite a las API que se usará en Xamarin nativas para iOS. Los enlaces se explican con más detalle en nuestro [enlace de Objective-C](~/cross-platform/macios/binding/overview.md) documentación, así que por ahora vamos a examinar cómo funciona iOS debajo del capó.

En primer lugar, debe haber una manera de exponer Objective-C a C#, que se realiza a través de los selectores. Un selector es un mensaje que se envía a un objeto o clase. Con Objective-c. Esto se realiza mediante el [objc_msgSend](~/cross-platform/macios/binding/overview.md) funciones.
Para obtener más información sobre el uso de selectores, consulte el [selectores de Objective-C](~/ios/internals/objective-c-selectors.md) guía. También debe haber una manera de exponer el código administrado y Objective-C, que es más complicado, puesto que la Objective-C no sabe nada sobre el código administrado. Para solucionar este problema, usamos *registradores*. Estos se explican con más detalle en la sección siguiente.

## <a name="registrars"></a>Registradores

Como se mencionó anteriormente, el registrador es código que expone el código administrado a Objective-C. Para ello crea una lista de todas las clases administradas que se deriva de NSObject:

- Para todas las clases que no se están ajustando una clase existente de Objective-C, crea una nueva clase de Objective-C con los miembros de Objective-C para la creación de reflejo todos los miembros administrados que tienen una [`Export`] atributo.

- En las implementaciones para cada miembro de Objective-C, el código se agrega automáticamente al llamar al miembro reflejado administrado.

El pseudocódigo siguiente muestra un ejemplo de cómo hacerlo:

**C#(Código administrado)**

```csharp
 class MyViewController : UIViewController{
     [Export ("myFunc")]
     public void MyFunc ()
     {
     }
 }
```

**Objetivo de C:**

```objectivec
@interface MyViewController : UIViewController { }

    -(void)myFunc;
@end

@implementation MyViewController {}

    -(void) myFunc
    {
        /* code to call the managed MyViewController.MyFunc method */
    }
@end

```

El código administrado puede contener los atributos, `[Register]` y `[Export]`, que usa el registrador de saber que el objeto debe exponerse a Objective-C.
El `[Register]` atributo se utiliza para especificar el nombre de la clase generada de Objective-C en caso de que el nombre generado predeterminado no es adecuado. Todas las clases derivadas de NSObject se registran automáticamente con Objective-C.
Necesario `[Export]` atributo contiene una cadena, que es el selector de la clase de Objective-C generada.

Hay dos tipos de registradores que se usa en Xamarin.iOS: estáticas y dinámicas:


- **Registradores dinámicos** : el registrador dinámico el registro de todos los tipos en el ensamblado en tiempo de ejecución. Hace esto mediante el uso de las funciones proporcionadas por [en tiempo de ejecución de Objective-C API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Por lo tanto, el registrador dinámico tiene un inicio más lento, pero más rápido tiempo de compilación. Este es el valor predeterminado para el simulador de iOS. Las funciones nativas (normalmente en C), denominadas camas elásticas, se usan como implementaciones de método cuando se usa al dinámicos registradores. Varían entre arquitecturas diferentes.

- **Registradores estáticos** : el registrador estático genera código de Objective-C durante la compilación, lo que, a continuación, se compila en una biblioteca estática y vinculada en el archivo ejecutable. Esto permite que un inicio más rápido, pero tarda más tiempo durante el tiempo de compilación. Esto se usa de forma predeterminada para compilaciones de dispositivo. El registrador estático también se puede usar con el simulador de iOS pasando `--registrar:static` como un `mtouch` de atributo en las opciones de compilación del proyecto, como se muestra a continuación:

    [![](architecture-images/image1.png "Argumentos mtouch adicionales de configuración")](architecture-images/image1.png#lightbox)

Para obtener más información sobre las características específicas del sistema de registro del tipo utilizado por Xamarin.iOS iOS, consulte el [tipo registrador](~/ios/internals/registrar.md) guía.

## <a name="application-launch"></a>Iniciar la aplicación

El punto de entrada de todos los ejecutables de Xamarin.iOS se proporciona mediante una función denominada `xamarin_main`, que inicializa mono.

Según el tipo de proyecto, se realiza lo siguiente:

- Para iOS normales y las aplicaciones de tvOS, se llama al método administrado de Main, proporcionado por la aplicación de Xamarin. Esto administra el método Main, a continuación, llama a `UIApplication.Main`, que es el punto de entrada para Objective-C. UIApplication.Main es el enlace de Objective-C `UIApplicationMain` método.
- Para las extensiones, la función nativa: `NSExtensionMain` o (`NSExtensionmain` para las extensiones de WatchOS): proporcionadas por Apple se llama a las bibliotecas. Puesto que estos proyectos son bibliotecas de clases y los proyectos no ejecutables, no hay ningún método Main administrado para ejecutar.

Todos los de esta secuencia de inicio se compila en una biblioteca estática, que está vinculada, a continuación, en el archivo ejecutable final para que la aplicación sepa cómo ponerse en marcha.

En este momento se ha iniciado la aplicación, Mono se está ejecutando, se encuentran en código administrado y sabemos cómo llamar a código nativo y volverá a llamar. Lo siguiente que debemos hacer es realmente empezar a agregar controles y poner la aplicación interactiva.


## <a name="generator"></a>Generator

Xamarin.iOS contiene definiciones para cada API de iOS único. Puede examinar a través de cualquiera de estos en el [repositorio de github MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Estas definiciones contienen las interfaces con atributos, así como los métodos y propiedades necesarios. Por ejemplo, es el código siguiente se utiliza para definir un UIToolbar en el UIKit [espacio de nombres](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327). Tenga en cuenta que es una interfaz con un número de métodos y propiedades:

```csharp
[BaseType (typeof (UIView))]
public interface UIToolbar : UIBarPositioning {
    [Export ("initWithFrame:")]
    IntPtr Constructor (CGRect frame);

    [Export ("barStyle")]
    UIBarStyle BarStyle { get; set; }

    [Export ("items", ArgumentSemantic.Copy)][NullAllowed]
    UIBarButtonItem [] Items { get; set; }

    [Export ("translucent", ArgumentSemantic.Assign)]
    bool Translucent { [Bind ("isTranslucent")] get; set; }

    // done manually so we can keep this "in sync" with 'Items' property
    //[Export ("setItems:animated:")][PostGet ("Items")]
    //void SetItems (UIBarButtonItem [] items, bool animated);

    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage ([NullAllowed] UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);

    ...
}
```

El generador, llamado [ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) en Xamarin.iOS, toma estos archivos de definición y usa las herramientas de .NET para [compilarlos en un ensamblado temporal](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). Sin embargo, no es utilizable para llamar a código de Objective-C este ensamblado temporal. El generador, a continuación, lee el ensamblado temporal y genera C# código que se puede usar en tiempo de ejecución.
Esto es, ¿por qué, por ejemplo, si agrega un atributo aleatorio para el archivo de definición. cs, no aparece en el código de salida. El generador no lo sabe y, por tanto, `btouch` no sabe que se busca en el ensamblado temporal para la salida se.

Una vez creada la Xamarin.iOS.dll, mtouch empaquetará todos los componentes entre sí.

En un nivel alto, esto consigue mediante la ejecución de las siguientes tareas:

-   Crear una estructura de paquete de aplicación.
-   Copie los ensamblados administrados.
-   Si está habilitada la vinculación, ejecute el enlazador administrado para optimizar los ensamblados mediante la copia desde CD sin usar de piezas.
-   Compilación de AOT.
-   Crear un ejecutable nativo, lo que da como resultado una serie de bibliotecas estáticas (uno para cada ensamblado) que están vinculados al ejecutable nativo, por lo que el archivo ejecutable nativo está formado por el código del iniciador, el código del registrador (si es estática) y todas las salidas de AOT compilador


Para obtener más información en el vinculador y cómo se utiliza, consulte el [vinculador](~/ios/deploy-test/linker.md) guía.

## <a name="summary"></a>Resumen

Esta guía examinado las compilación de AOT de aplicaciones de Xamarin.iOS y explorado Xamarin.iOS y su relación con Objective-C en profundidad.

## <a name="related-links"></a>Vínculos relacionados

- [Limitaciones](~/ios/internals/limitations.md)
- [Enlace de Objective-C](~/cross-platform/macios/binding/overview.md)
- [Selectores de Objective-c.](~/ios/internals/objective-c-selectors.md)
- [Registrador de tipo](~/ios/internals/registrar.md)
- [Enlazador](~/ios/deploy-test/linker.md)
