---
title: "Cómo funciona Xamarin.Mac"
description: "Este documento describe el funcionamiento interno de Xamarin.Mac. En concreto, examina constructores, administración de memoria, por delante de la compilación de tiempo y el registrador."
ms.topic: article
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/25/2017
ms.openlocfilehash: 7329e8ddb5b86adcf6e1efaa805149012be8853c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="how-xamarinmac-works"></a>Cómo funciona Xamarin.Mac

La mayoría de las veces que el desarrollador nunca tendrá que preocuparse sobre la interno "mágica" de Xamarin.Mac, sin embargo, tener un conocimiento aproximado de cómo le ayudará cosas funciona en segundo plano en la interpretación de la documentación existente con una lente de C# y depurar problemas cuando que surjan.

En Xamarin.Mac, una aplicación une dos mundos: es el tiempo de ejecución según Objective-C que contiene instancias de clases nativas (`NSString`, `NSApplication`, etcetera) y no hay el runtime de C# que contiene instancias de clases administradas (`System.String`, `HttpClient`, etcetera). Entre estos dos ámbitos, Xamarin.Mac crea un puente de dos direcciones por lo que una aplicación puede llamar a métodos (selectores) en Objective-C (como `NSApplication.Init`) y objetivo de C puede llamar a métodos de la aplicación C# nuevo (por ejemplo, los métodos en un delegado de la aplicación). En general, las llamadas en Objective-C se controlan de forma transparente a través de **P/Invokes** y algún código de Common Language runtime proporciona Xamarin.

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>Exponer clases de C# / métodos para Objective-c.

No obstante, para que Objective-C para volver a llamar en objetos de una aplicación C#, deben exponer de forma que pueda saber Objective-C. Esto se realiza mediante la `Register` y `Export` atributos. Considere el ejemplo siguiente:

```csharp
[Register ("MyClass")]
public class MyClass : NSObject
{
   [Export ("init")]
   public MyClass ()
   {
   }

   [Export ("run")]
   public void Run ()
   {
   }
}
```

En este ejemplo, el tiempo de ejecución de C objetivo ahora sabrá sobre una clase denominada `MyClass` con selectores llama `init` y `run`.

En la mayoría de los casos, esto es un detalle de implementación que el desarrollador puede pasar por alto, ya que la mayoría de las devoluciones de llamada recibe una aplicación ya sea a través de los métodos invalidados estarán en `base` clases (como `AppDelegate`, `Delegates`, `DataSources`) o en  **Acciones** pasan a las API. En todos los casos, `Export` atributos no son necesarios en el código de C#.

## <a name="constructor-runthrough"></a>Repaso de constructor

En muchos casos, el desarrollador necesita exponer C# clases construcción la aplicación API para el tiempo de ejecución de C de objetivo de modo que se pueden crear instancias de lugares como cuando se llama en los archivos de guión gráfico o XIB. Estos son los cinco constructores más comunes que se utiliza en aplicaciones de Xamarin.Mac:

```csharp
// Called when created from unmanaged code
public CustomView (IntPtr handle) : base (handle)
{
   Initialize ();
}

// Called when created directly from a XIB file
[Export ("initWithCoder:")]
public CustomView (NSCoder coder) : base (coder)
{
   Initialize ();
}

// Called from C# to instance NSView with a Frame (initWithFrame)
public CustomView (CGRect frame) : base (frame)
{
}

// Called from C# to instance NSView without setting the frame (init)
public CustomView () : base ()
{
}

// This is a special case constructor that you call on a derived class when the derived called has an [Export] constructor.
// For example, if you call init on NSString then you don’t want to call init on NSObject.
public CustomView () : base (NSObjectFlag.Empty)
{
}
```

En general, debe dejar al desarrollador la `IntPtr` y `NSCoder` constructores que se generan al crear algunos tipos como personalizado `NSViews` independiente. Si Xamarin.Mac necesita llamar a uno de estos constructores en respuesta a una solicitud de tiempo de ejecución de C de objetivo y se han quitado, la aplicación se bloqueará en código nativo y puede ser difícil averiguar exactamente el problema.

## <a name="memory-management-and-cycles"></a>Administración de memoria y ciclos

Administración de memoria en Xamarin.Mac es muy similar a Xamarin.iOS de muchas maneras. También es un tema complejo, uno más allá del ámbito de este documento. Lea la [memoria y prácticas recomendadas de rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="ahead-of-time-compilation"></a>Hacia delante de la compilación de tiempo

Por lo general, las aplicaciones .NET no compilan a código máquina que se generan, en su lugar compilan en un nivel intermedio que llama a código IL que obtenga _Just-In-Time_ (JIT) se compila en código máquina cuando se inicie la aplicación.

El tiempo que tarda el tiempo de ejecución de mono para la compilación JIT este código máquina puede ralentizar el inicio de una aplicación Xamarin.Mac hasta 20%, tal y como se consume tiempo para el código máquina es necesario que se genere.

Debido a las limitaciones impuestas por Apple IOS, la compilación JIT del código IL no está disponible para Xamarin.iOS. Como resultado, todas las aplicaciones de Xamarin.iOS están llenos _Ahead de tiempo_ (AOT) compilados con código máquina durante el ciclo de compilación.

Nuevo a Xamarin.Mac es la capacidad de AOT el código IL durante el ciclo de compilación de la aplicación, exactamente igual Xamarin.iOS. Xamarin.Mac utiliza un _híbrida_ enfoque AOT que compila una mayoría del código máquina necesaria, pero permite el tiempo de ejecución compilar camas elásticas necesarios y la flexibilidad de continúan admitiendo Reflection.Emit (y otros usos de los casos, que actualmente se funcionen en Xamarin.Mac).

Hay dos áreas principales donde AOT puede ayudar a una aplicación Xamarin.Mac:

- **Mejor registros de bloqueo "nativo"** : si se bloquea una aplicación Xamarin.Mac en código nativo, que es típico al realizar llamadas no válidas a las API de cacao (como el envío de un `null` a un método que no lo acepta) nativo registros con JIT de bloqueo marcos son difíciles de analizar. Puesto que los marcos JIT no tiene información de depuración, habrá varias líneas con desplazamientos hexadecimales y ninguna pista para saber qué estaba sucediendo. AOT genera fotogramas "real" con nombre y los seguimientos son mucho más fáciles de leer. Esto también significa Xamarin.Mac aplicación interactuará mejor con las herramientas nativas como **lldb** y **instrumentos**.
- **Iniciar el mejor rendimiento en tiempo de** : para las aplicaciones de Xamarin.Mac grandes, con una hora de inicio de segundo varios, JIT compila todo el código pueden tardar una cantidad considerable de tiempo. AOT realiza este trabajo por adelantado.

### <a name="enabling-aot-compilation"></a>Habilitar la compilación de AOT

AOT está habilitada en Xamarin.Mac haciendo doble clic en el **nombre del proyecto** en el **el Explorador de soluciones**, navegación a **Mac compilar** y agregar `--aot:[options]` a la  **Argumentos adicionales mmp:** campo (donde `[options]` es una o varias opciones para controlar el tipo AOT, véase más abajo). Por ejemplo:

![Agregar AOT a argumentos adicionales mmp](how-it-works-images/aot01.png "agregar AOT a argumentos de mmp adicionales")

> [!IMPORTANT]
> ¡¡ADVERTENCIA! Habilitar AOT compilación aumenta en gran medida el tiempo de compilación, a veces hasta varios minutos, pero puede mejorar los tiempos de inicio de aplicaciones por un promedio del 20%. Como resultado, compilación AOT sólo debe habilitarse en **versión** la compilación de una aplicación Xamarin.Mac.

### <a name="aot-compilation-options"></a>Opciones de compilación de AOT

Hay varias opciones diferentes que se pueden ajustar al habilitar la compilación de AOT en una aplicación Xamarin.Mac:

- `none` -Ninguna compilación AOT. Ésta es la configuración predeterminada.
- `all` -AOT compila todos los ensamblados en el MonoBundle.
- `core` -AOT compila el `Xamarin.Mac`, `System` y `mscorlib` ensamblados.
- `sdk` -AOT compila el `Xamarin.Mac` y ensamblados de bibliotecas de clases Base (BCL).
- `|hybrid` -Agregar esto a una de las opciones anteriores permite híbrida AOT que permite la eliminación de IL, sino que generan se compilarán veces.
- `+` -Incluye una única para a la compilación de AOT.
- `-` -Quita un único archivo de compilación de AOT.

Por ejemplo, `--aot:all,-MyAssembly.dll` permitiría compilación AOT en todos los ensamblados de la MonoBundle _excepto_ `MyAssembly.dll` y `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll` permitiría híbrida, incluya código AOT la `MyOtherAssembly.dll` y sin incluir el `mscorlib.dll`.

## <a name="partial-static-registrar"></a>Registrador estático parcial

Al desarrollar una aplicación Xamarin.Mac, minimizar el tiempo entre completar un cambio y probarla, puede ser importante para cumplir los requisitos de desarrollo. Estrategias como diseño modular de códigos base y pruebas unitarias pueden ayudar a reducir los tiempos de compilación, tal y como se reducen el número de veces que una aplicación requerirá una reconstrucción completa costosa.

Además y nuevos para Xamarin.Mac, _parcial registrador estático_ (tal y como se introdujeron Xamarin.iOS) puede reducir drásticamente los tiempos de inicio de una aplicación de Xamarin.Mac en el **depurar** configuración. Descripción de cómo el registrador estático parcial permite comprimido fuera un casi una mejora de 5 x en el inicio de depuración tardará un poco de información general sobre qué es el registrador, ¿cuál es la diferencia entre estático y dinámico y lo que hace esta versión de "static parcial".

### <a name="about-the-registrar"></a>Sobre el registrador

Bajo el paraguas de cualquier Xamarin.Mac aplicación encuentra en el marco de trabajo de cacao de Apple y el tiempo de ejecución de C de objetivo. Construcción de un puente entre este "mundo nativo" y "administrada world" de C# es la responsabilidad primaria de Xamarin.Mac. Parte de esta tarea se controla mediante el registrador, que se ejecuta dentro de `NSApplication.Init ()` método. Se trata de una de las razones que el uso de las API de cacao en Xamarin.Mac requiere `NSApplication.Init` llamar primero.

Trabajo del registrador es informar el tiempo de ejecución de C de objetivo de la existencia de C# clases la aplicación que derivan de clases como `NSApplicationDelegate`, `NSView`, `NSWindow`, y `NSObject`. Esto requiere un recorrido de todos los tipos de la aplicación para determinar qué elementos de cada tipo de informe y lo que debe registrar.

Este examen puede hacerse **dinámicamente**, en el inicio de la aplicación mediante la reflexión, o **estáticamente**, como un paso de tiempo de compilación. Al seleccionar un tipo de registro, el desarrollador debe ser consciente de las acciones siguientes:

- Registro estático puede reducir drásticamente los tiempos de inicio, pero puede ralentizar compilaciones veces considerablemente (normalmente más del doble del tiempo de compilación de depuración). Este es el valor predeterminado de **versión** compilaciones de configuración.
- El registro dinámico retrasa este trabajo hasta que la aplicación se inicie y se omite la generación de código, pero este trabajo adicional puede crear una pausa apreciable (al menos dos segundos) en el inicio de la aplicación. Esto es especialmente notable en las compilaciones de la configuración de depuración, que de forma predeterminada el registro dinámico y cuyo reflexión es más lenta.

Registro estático parcial, se introdujo por primera vez en el punto 8.13 Xamarin.iOS, proporciona al desarrollador lo mejor de ambas opciones. Calculando previamente la información de registro de todos los elementos de `Xamarin.Mac.dll` y enviar esta información con Xamarin.Mac en una biblioteca estática (que solo debe estar relacionado con en tiempo de compilación), Microsoft ha quitado la mayor parte del tiempo de reflexión de dinámico registrador y sin afectar a tiempo de compilación.

### <a name="enabling-the-partial-static-registrar"></a>Habilitar al registrador estático parcial

El registrador de estático parcial está habilitado en Xamarin.Mac haciendo doble clic en el **nombre del proyecto** en el **el Explorador de soluciones**, navegación a **Mac compilar** y agregando `--registrar:static` a la **argumentos adicionales mmp:** campo. Por ejemplo:

![Agregar el registrador estático parcial a los argumentos adicionales mmp](how-it-works-images/psr01.png "agregando el registrador estático parcial a argumentos de mmp adicionales")

## <a name="additional-resources"></a>Recursos adicionales

Estas son algunas explicaciones más detalladas de cómo funcionan internamente las cosas:

- [Selectores de Objective-c.](~/ios/internals/objective-c-selectors.md)
- [Registrar](~/ios/internals/registrar.md)
- [API unificada de Xamarin para iOS y OS X](~/cross-platform/macios/unified/index.md)
- [Conceptos básicos de theading](~/ios/app-fundamentals/threading.md)
- [Delegados, protocolos y eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Acerca de `newrefcount`](~/ios/internals/newrefcount.md)

