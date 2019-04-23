---
title: Funcionamiento de Xamarin.Mac
description: Este documento describe el funcionamiento interno de Xamarin.Mac. En concreto, busca constructores, administración de memoria, por delante de la compilación de tiempo y el registrador.
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/25/2017
ms.openlocfilehash: 0635e110cb2aa7bc00234d3d06df57e0fd6f966e
ms.sourcegitcommit: 6f728aa0c1775224e16c0f3e583cf843d34270f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/22/2019
ms.locfileid: "59893236"
---
# <a name="how-xamarinmac-works"></a>Funcionamiento de Xamarin.Mac

La mayoría de las veces que el desarrollador nunca tendrá que preocuparse por la interna "mágicas" de Xamarin.Mac, sin embargo, tener un conocimiento aproximado de cómo funciona cosas en segundo plano le ayudará a ambos interpretación documentación existente con un C# lente y depuración problemas que surjan.

Una aplicación de Xamarin.Mac, puentes de dos mundos: Es el tiempo de ejecución en función de Objective-C que contiene las instancias de clases nativas (`NSString`, `NSApplication`, etcetera) y no hay el C# en tiempo de ejecución que contiene las instancias de clases administradas (`System.String`, `HttpClient`, etcetera). Entre estos dos mundos, Xamarin.Mac crea un puente bidireccional para que una aplicación puede llamar a métodos (selectores) en Objective-C (como `NSApplication.Init`) y Objective-C puede llamar a la aplicación C# hacer una copia de los métodos (como métodos en un delegado de la aplicación). En general, las llamadas en Objective-C se controlan de forma transparente mediante **P/Invokes** y algún código en tiempo de ejecución proporciona Xamarin.

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>Exponer C# clases o métodos a Objective-C

Sin embargo, para Objective-C para volver a llamar en una aplicación C# objetos, que es necesario exponer de forma que pueda entender Objective-C. Esto se realiza mediante el `Register` y `Export` atributos. Considere el ejemplo siguiente:

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

En este ejemplo, el tiempo de ejecución Objective-C ahora sabrá acerca de una clase denominada `MyClass` con selectores llamados `init` y `run`.

En la mayoría de los casos, esto es un detalle de implementación que el desarrollador puede pasar por alto, ya que la mayoría de las devoluciones de llamada recibe una aplicación a través de los métodos invalidados estarán en `base` clases (como `AppDelegate`, `Delegates`, `DataSources`) o en  **Acciones** pasan a las API. En todos esos casos, `Export` atributos no son necesarios en el C# código.

## <a name="constructor-runthrough"></a>Repaso de constructor

En muchos casos, el desarrollador tendrá que exponer la aplicación C# construcción de clases API para el tiempo de ejecución Objective-C, por lo que se pueden crear instancias desde lugares como cuando llama en guión gráfico o XIB archivos. Estos son los cinco constructores más comunes usados en aplicaciones de Xamarin.Mac:

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

En general, debe dejar al desarrollador la `IntPtr` y `NSCoder` constructores que se generan al crear algunos tipos como personalizada `NSViews` por sí solo. Si Xamarin.Mac necesita llamar a uno de estos constructores en respuesta a una solicitud de Objective-C en tiempo de ejecución y se ha quitado, se bloqueará la aplicación dentro de código nativo y puede ser difícil averiguar exactamente el problema.

## <a name="memory-management-and-cycles"></a>Administración de memoria y ciclos

Administración de memoria en Xamarin.Mac es de muchas maneras muy similares a Xamarin.iOS. También es un tema complejo, uno más allá del ámbito de este documento. Lea la [memoria y procedimientos recomendados de rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="ahead-of-time-compilation"></a>Adelante de la compilación de tiempo

Normalmente, las aplicaciones .NET no compilan en código máquina cuando se compilan, en su lugar se compilan en un nivel intermedio, denominado código de IL que obtiene _Just-In-Time_ (JIT) se compila en código máquina cuando se inicia la aplicación.

El tiempo que tarda el tiempo de ejecución mono para la compilación JIT este código máquina puede ralentizar el inicio de una aplicación de Xamarin.Mac hasta un 20%, como puede tardar el código del equipo es necesario que se genere.

Debido a las limitaciones impuestas por Apple en iOS, la compilación JIT del código IL no está disponible para Xamarin.iOS. Como resultado, todas las aplicaciones de Xamarin.iOS están llenos _Ahead Of Time_ (AOT) compilado en código máquina durante el ciclo de compilación.

Nuevo a Xamarin.Mac es la capacidad de AOT el código IL durante el ciclo de compilación de la aplicación, exactamente igual Xamarin.iOS. Xamarin.Mac se usa un _híbrida_ enfoque AOT que compila una mayoría del código máquina necesario, pero permite el tiempo de ejecución compilar camas elásticas necesarios y la flexibilidad necesaria para continuar admitiendo Reflection.Emit (y los casos otro uso actualmente se funciona en Xamarin.Mac).

Hay dos áreas principales que AOT puede ayudar a una aplicación de Xamarin.Mac:

- **Mejor los registros de bloqueo "nativo"** : si se bloquea una aplicación de Xamarin.Mac en código nativo, que es habitual al realizar llamadas no válidas en la API de Cocoa (como enviar un `null` en un método que no acepta) nativa, los registros con JIT de bloqueo los marcos son difíciles de analizar. Puesto que los marcos JIT no tienen información de depuración, habrá varias líneas con desplazamientos hexadecimales y ninguna idea de qué está sucediendo. AOT genera fotogramas "real" con nombre y los seguimientos son mucho más fáciles de leer. Esto también significa que la aplicación de Xamarin.Mac interactúan mejor con las herramientas nativas como **lldb** y **instrumentos**.
- **Mejor rendimiento en tiempo de inicio** : para aplicaciones de Xamarin.Mac grandes, con un tiempo de inicio de segundo múltiples, todo el código de la compilación JIT pueden tardar una cantidad considerable de tiempo. AOT realiza este trabajo por adelantado.

### <a name="enabling-aot-compilation"></a>Activación de la compilación de AOT

AOT está habilitada en Xamarin.Mac haciendo doble clic en el **nombre del proyecto** en el **el Explorador de soluciones**, al ir a **de compilación de Mac** y agregando `--aot:[options]` a la  **Argumentos de mmp adicionales:** campo (donde `[options]` es una o varias opciones para controlar el tipo AOT, consulte más abajo). Por ejemplo:

![Adición de AOT a argumentos de mmp adicionales](how-it-works-images/aot01.png "agregar AOT a argumentos de mmp adicionales")

> [!IMPORTANT]
> Habilitación de AOT compilación aumenta considerablemente el tiempo de compilación, a veces hasta varios minutos, pero puede mejorar los tiempos de inicio de aplicación en un promedio del 20%. Como resultado, compilación AOT sólo debe habilitarse en **versión** la compilación de una aplicación de Xamarin.Mac.

### <a name="aot-compilation-options"></a>Opciones de compilación de AOT

Hay varias opciones que se pueden ajustar al habilitar la compilación de AOT en una aplicación de Xamarin.Mac:

- `none` -Sin compilación AOT. Ésta es la configuración predeterminada.
- `all` -AOT compila todos los ensamblados en el MonoBundle.
- `core` -AOT compila el `Xamarin.Mac`, `System` y `mscorlib` ensamblados.
- `sdk` -AOT compila el `Xamarin.Mac` y ensamblados de las bibliotecas de clases Base (BCL).
- `|hybrid` -Agregar esto a una de las opciones anteriores permite híbrida AOT que permite la eliminación de IL, pero el resultado en ya compilará veces.
- `+` : Incluye un único archivo de compilación de AOT.
- `-` -Quita un solo archivo de compilación de AOT.

Por ejemplo, `--aot:all,-MyAssembly.dll` podían habilitar la compilación de AOT en todos los ensamblados en el MonoBundle _excepto_ `MyAssembly.dll` y `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll` permitiría híbrido, incluya código AOT el `MyOtherAssembly.dll` y excluir el `mscorlib.dll`.

## <a name="partial-static-registrar"></a>Registrador estático parcial

Al desarrollar una aplicación de Xamarin.Mac, puede ser importante para cumplir los requisitos de desarrollo a minimizar el tiempo de finalización de un cambio y probarlo. Las estrategias, como el diseño modular de códigos base y las pruebas unitarias pueden ayudar a disminuir los tiempos de compilación, como reducen el número de veces que una aplicación requerirán una recompilación completa costosa.

Además y nuevos a Xamarin.Mac, _parcial registrador estático_ (como introducidas por primera vez por Xamarin.iOS) puede reducir drásticamente los tiempos de inicio de una aplicación de Xamarin.Mac en la **depurar** configuración. Descripción de cómo mediante el registrador estático parcial puede metido horizontalmente un casi una mejora de 5 veces superior en iniciar la depuración tardará un poco de fondo en el registrador está, ¿qué es la diferencia entre estático y dinámico y lo que hace esta versión de "static parcial".

### <a name="about-the-registrar"></a>Acerca del registrador

Bajo el paraguas de cualquier Xamarin.Mac aplicación encuentra en el marco de cacao de Apple y el tiempo de ejecución de C de objetivo. Creación de un puente entre esta "world nativo" y "world administrado" de C# es la responsabilidad principal de Xamarin.Mac. Parte de esta tarea se controla mediante el registrador, que se ejecuta dentro de `NSApplication.Init ()` método. Se trata de una de las razones que requiere que cualquier uso de API de Cocoa en Xamarin.Mac `NSApplication.Init` se debe llamar primero.

Trabajo del registrador es informar al runtime de la existencia de la aplicación Objective-C C# clases que derivan de clases como `NSApplicationDelegate`, `NSView`, `NSWindow`, y `NSObject`. Esto requiere un examen de todos los tipos en la aplicación para determinar qué elementos de cada tipo de informe y lo que debe registrar.

Este examen puede hacerse **dinámicamente**, en el inicio de la aplicación con la reflexión, o **estáticamente**, como un paso del tiempo de compilación. Al seleccionar un tipo de registro, el desarrollador debe tener en cuenta lo siguiente:

- Registro estático puede reducir significativamente los tiempos de inicio, pero puede ralentizar las compilaciones veces significativamente (normalmente más del doble del tiempo de compilación de depuración). Este será el valor predeterminado para **versión** configuración genera.
- Registro dinámico retrasa este trabajo hasta que la aplicación se inicie y se omite la generación de código, pero este trabajo adicional puede crear una pausa marcada (al menos dos segundos) en el inicio de la aplicación. Esto es especialmente evidente en las compilaciones de configuración de depuración, cuyo valor predeterminado es el registro dinámico y cuyo reflexión es más lento.

Registro estático parcial, se introdujo por primera vez en Xamarin.iOS 8.13, proporciona al desarrollador lo mejor de ambas opciones. Calculando previamente la información de registro de todos los elementos de `Xamarin.Mac.dll` y enviar esta información con Xamarin.Mac en una biblioteca estática (que solo debe vincularse en tiempo de compilación), Microsoft ha quitado la mayor parte del tiempo de reflexión de dinámico registrador y sin afectar a tiempo de compilación.

### <a name="enabling-the-partial-static-registrar"></a>Habilitar al registrador estático parcial

El registrador estático parcial está habilitado en Xamarin.Mac haciendo doble clic en el **nombre del proyecto** en el **el Explorador de soluciones**, al ir a **de compilación de Mac** y agregando `--registrar:static` a la **argumentos de mmp adicionales:** campo. Por ejemplo:

![Agregar el registrador estático parcial a argumentos de mmp adicionales](how-it-works-images/psr01.png "agregar el registrador estático parcial a argumentos de mmp adicionales")

## <a name="additional-resources"></a>Recursos adicionales

Estas son algunas explicaciones más detalladas de cómo funcionan internamente las cosas:

- [Selectores de Objective-c.](~/ios/internals/objective-c-selectors.md)
- [Registrador](~/ios/internals/registrar.md)
- [Unified API de Xamarin para iOS y OS X](~/cross-platform/macios/unified/index.md)
- [Aspectos básicos de theading](~/ios/app-fundamentals/threading.md)
- [Los delegados, protocolos y eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Acerca de `newrefcount`](~/ios/internals/newrefcount.md)

