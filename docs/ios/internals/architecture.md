---
title: Arquitectura de iOS
description: Explorar Xamarin.iOS en un nivel bajo
ms.topic: article
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 732f60a413077bc15018679fe8f8bc0a18227246
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="ios-architecture"></a>Arquitectura de iOS

Aplicaciones de Xamarin.iOS ejecutan dentro del entorno de ejecución Mono y usar completos de la compilación con antelación de tiempo (AOT) para compilar código C# para el lenguaje ensamblador ARM. Esto se ejecuta en paralelo con la [en tiempo de ejecución de C objetivo](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Ambos entornos en tiempo de ejecución se ejecutan sobre un núcleo similares a UNIX, específicamente [XNU](https://en.wikipedia.org/wiki/XNU)y exponer varias API en el código de usuario que permite a los desarrolladores tener acceso al sistema nativo o administrado subyacente.

El diagrama siguiente muestra una visión general básica de esta arquitectura:

[ ![](architecture-images/ios-arch-small.png "Este diagrama muestra una introducción básica a la arquitectura de compilación con antelación de tiempo (AOT)")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Nativo y código administrado: una explicación

Al desarrollar para Xamarin los términos *nativo y administrado* código se utilizan a menudo. [Código administrado](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) es código que tiene su ejecución administrada por el [Common Language Runtime de .NET Framework](https://msdn.microsoft.com/en-us/library/8bs2ecf4(v=vs.110).aspx), o en caso de Xamarin: el tiempo de ejecución de Mono. Esto es lo que llamamos un lenguaje intermedio.

Código nativo es el código que se ejecutará de forma nativa en la plataforma concreta (por ejemplo, Objective-C o incluso código AOT compilado, en un chip ARM). Esta guía explora cómo AOT compila el código administrado a código nativo y explica cómo una aplicación de Xamarin.iOS funciona, realizar un uso completo de las API de iOS de Apple mediante el uso de enlaces, mientras también tenga acceso a. BCL de NET y un lenguaje sofisticado, como C#.


## <a name="aot"></a>AOT

Al compilar cualquier aplicación de la plataforma Xamarin, el compilador de C# Mono (o F #) se ejecutará y compilará el código de C# y F # en lenguaje intermedio de Microsoft (MSIL). Si está ejecutando un Xamarin.Android, una aplicación de Xamarin.Mac o incluso una aplicación de Xamarin.iOS en el simulador, la [.NET Common Language Runtime (CLR)](https://msdn.microsoft.com/en-us/library/8bs2ecf4(v=vs.110).aspx) compila el código de MSIL en un solo compilador Time (JIT). En tiempo de ejecución que esto se compila en un código nativo, que se puede ejecutar en la arquitectura correcta para la aplicación.

Sin embargo, hay una restricción de seguridad en iOS, establecida por Apple, que no permite la ejecución del código generado dinámicamente en un dispositivo.
Para asegurarse de que se adhiere a estos protocolos de seguridad, Xamarin.iOS en su lugar, usa un compilador con antelación de tiempo (AOT) para compilar el código administrado. Esto produce una binaria, nativas para iOS opcionalmente optimizado con LLVM para dispositivos, que se pueden implementar en el procesador de basado en ARM de Apple. Un diagrama de cómo esto encaja aproximado se ilustra a continuación:

[![](architecture-images/aot.png "Un diagrama de cómo esto encaja aproximado")](architecture-images/aot-large.png#lightbox)

El uso de AOT tiene una serie de limitaciones, que se detallan en el [limitaciones](~/ios/internals/limitations.md) guía. También proporciona una serie de mejoras sobre JIT a través de una reducción en el tiempo de inicio y varias optimizaciones de rendimiento

Ahora que se han analizado cómo se compila el código de origen en código nativo, echemos un vistazo en segundo plano para ver cómo Xamarin.iOS nos permite escribir aplicaciones totalmente nativas para iOS

## <a name="selectors"></a>Selectores

Con Xamarin, tenemos dos ecosistemas independientes, .NET y Apple, que es necesario poner juntos para parece tan sencilla como sea posible, para asegurarse de que el objetivo final es una experiencia de usuario sin problemas. Hemos visto en la sección sobre cómo se comunican los dos tiempos de ejecución y es muy bien haya oído del término 'enlaces', que permite a las API que se usará en Xamarin de iOS nativo. Los enlaces se explican con más detalle en nuestro [enlace Objective-C](~/cross-platform/macios/binding/overview.md) documentación, por lo que por ahora vamos a explorar cómo iOS funciona en segundo plano.

En primer lugar, tiene que haber una manera de exponer Objective-C a C#, que se realiza a través de selectores. Un selector es un mensaje que se envía a un objeto o una clase. Con Objective-c. Esto se realiza mediante la [objc_msgSend](~/cross-platform/macios/binding/overview.md) funciones.
Para obtener más información sobre el uso de selectores, consulte la [selectores Objective-C](~/ios/internals/objective-c-selectors.md) guía. También debe haber una manera de exponer el código administrado para Objective-C, que es más complicado, puesto que el objetivo de C no sabe nada sobre el código administrado. Para solucionar esto, usamos *registradores*. Estos se explican con más detalle en la sección siguiente.

## <a name="registrars"></a>Registradores

Como se mencionó anteriormente, el registrador es código que expone el código administrado al objetivo de C. Esto hace mediante la creación de una lista de cada clase administrada que se deriva de NSObject:

- Para todas las clases que no se ajuste una clase existente de Objective-C, crea una nueva clase de objetivo-C con miembros Objective-C creación de reflejo de todos los miembros administrados que tienen una [`Export`] atributo.

- En las implementaciones para cada miembro del objetivo – C, el código se agrega automáticamente para llamar al miembro administrado reflejado.

El pseudocódigo siguiente muestra un ejemplo de cómo hacerlo:

**C# (código administrado)**

```

 class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }


```

**Objective-C:**

```csharp
@interface MyViewController : UIViewController { }

    -(void)myFunc;
@end @implementation

    MyViewController {}
    -(void) myFunc
    {
    /* code to call the managed MyViewController.MyFunc method */
    }
@end

```

El código administrado puede contener los atributos, `[Register]` y `[Export]`, que utiliza el registrador de saber que el objeto se debe exponer al objetivo de C.
El `[Register]` atributo se utiliza para especificar el nombre de la clase Objective-C generada en caso de que el nombre generado de forma predeterminada no es adecuado. Todas las clases derivadas de NSObject se registran automáticamente con el objetivo de C.
Requerido `[Export]` atributo contiene una cadena, que es el selector de la clase Objective-C generada.

Hay dos tipos de registradores que se usa en Xamarin.iOS: dinámico y estático:


- **Registradores dinámicos** : el registrador dinámico el registro de todos los tipos en el ensamblado en tiempo de ejecución. Para ello, mediante el uso de funciones proporcionadas por [en tiempo de ejecución de C objetivo API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Por consiguiente, el registrador dinámico tiene un inicio más lento, pero una más rápida tiempo de compilación. Este es el valor predeterminado para el simulador de iOS. Las funciones nativas (normalmente en C), denominadas camas elásticas, se usan como implementaciones de método cuando se usa al registradores dinámicos. Varían entre diferentes arquitecturas.

- **Registradores estáticos** : el registrador estático genera código Objective-C durante la compilación, que, a continuación, se compila en una biblioteca estática y vinculada en el archivo ejecutable. Esto permite que un inicio rápido, pero tarda más tiempo durante el tiempo de compilación. Se utiliza de forma predeterminada para compilaciones de dispositivo. El registrador estático también puede utilizarse con el simulador de iOS pasando `--registrar:static` como un `mtouch` de atributo en las opciones de compilación del proyecto, tal y como se muestra a continuación:

    [![](architecture-images/image1.png "Definir argumentos de mtouch adicionales")](architecture-images/image1.png#lightbox)

Para obtener más información sobre las características específicas del sistema de registro del tipo utilizado por Xamarin.iOS iOS, consulte el [registrador de tipo](~/ios/internals/registrar.md) guía.

## <a name="application-launch"></a>Iniciar la aplicación

El punto de entrada de todos los ejecutables de Xamarin.iOS proporciona una función denominada `xamarin_main`, que inicializa mono.

Dependiendo del tipo de proyecto, se hace lo siguiente:

- IOS regular y las aplicaciones de tvOS, se llama al método administrado de Main, proporcionado por la aplicación de Xamarin. Esto administra el método Main, a continuación, llama a `UIApplication.Main`, que es el punto de entrada para el objetivo de C. UIApplication.Main es el enlace para Objective-C `UIApplicationMain` método.
- Para las extensiones, la función nativa: `NSExtensionMain` o (`NSExtensionmain` WatchOS extensiones): proporcionado por Apple es llamar a bibliotecas. Dado que estos proyectos son bibliotecas de clases y proyectos no ejecutables, no hay ningún método Main administrado para ejecutar.

Todos los de esta secuencia de inicio se compila en una biblioteca estática, que está vinculada, a continuación, en el archivo ejecutable final para que la aplicación sepa cómo ponerse en marcha.

En este momento se ha iniciado la aplicación, se ejecuta Mono, estamos en código administrado y sabemos cómo llamar a código nativo y volverá a llamar. Lo siguiente que debemos hacer es realmente empezar a agregar controles y hacer que la aplicación interactiva.


## <a name="generator"></a>Generator

Xamarin.iOS contiene definiciones para cada API de iOS único. Puede examinar cualquiera de estos en el [repositorio de github de MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Estas definiciones de contengan interfaces con atributos, así como los métodos y propiedades necesarios. Por ejemplo, es el código siguiente se utiliza para definir un UIToolbar en la UIKit [espacio de nombres](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327). Tenga en cuenta que es una interfaz con una serie de métodos y propiedades:

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

El generador, denominado [ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) en Xamarin.iOS, toma estos archivos de definición y utiliza herramientas de .NET para [compilarlos en un ensamblado temporal](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). Sin embargo, este ensamblado temporal no es utilizable para llamar a código Objective-C. El generador, a continuación, lee el ensamblado temporal y genera código de C# que se puede utilizar en tiempo de ejecución.
Esto es la razón de ello, por ejemplo, si agrega un atributo aleatorio para el archivo de definición de. cs, no se mostrarán en el código de salida. No se conoce el generador y, por tanto, `btouch` no sabe para que busque en el ensamblado temporal para la salida se.

Una vez creada la Xamarin.iOS.dll, mtouch se agrupar todos los componentes.

En un nivel alto, esto consigue mediante la ejecución de las tareas siguientes:

-   Crear una estructura de paquete de aplicación.
-   Copiar en los ensamblados administrados.
-   Si se habilita la vinculación, ejecute el enlazador administrado para optimizar los ensamblados mediante copia desde CD sin usar elementos de salida.
-   Compilación de AOT.
-   Crear un archivo ejecutable nativo, que genera una serie de bibliotecas estáticas (uno para cada ensamblado) que están vinculados en el archivo ejecutable nativo, por lo que el ejecutable nativo está formada por el código de iniciador, el código de registrador (si es estática) y todas las salidas del AOT compilador


Para obtener más información sobre el enlazador y cómo se utiliza, consulte el [vinculador](~/ios/deploy-test/linker.md) guía.

## <a name="summary"></a>Resumen

Esta guía se busca durante la compilación de AOT de aplicaciones de Xamarin.iOS y explorar Xamarin.iOS y su relación con el objetivo de C en profundidad.

## <a name="related-links"></a>Vínculos relacionados

- [Limitaciones](~/ios/internals/limitations.md)
- [Enlace de Objective-C](~/cross-platform/macios/binding/overview.md)
- [Selectores de Objective-c.](~/ios/internals/objective-c-selectors.md)
- [Registrador de tipo](~/ios/internals/registrar.md)
- [Enlazador](~/ios/deploy-test/linker.md)
