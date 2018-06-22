---
title: Bibliotecas de enlace Objective-c.
description: Este documento proporciona una descripción general de cómo crear C# enlaces a código Objective-C, que describe cómo enlazar los eventos, métodos, controles personalizados y mucho más.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: f7c4be4254ce3e3301c0c1e98d37134f5524c23b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782325"
---
# <a name="binding-objective-c-libraries"></a>Bibliotecas de enlace Objective-c.

Cuando se trabaja con Xamarin.iOS o Xamarin.Mac, podría encontrar casos donde probablemente prefiera utilizar una biblioteca de C de objetivo de terceros. En estos casos, puede utilizar proyectos de enlace de Xamarin para crear un enlace de C# para las bibliotecas de Objective-C nativo. El proyecto utiliza las mismas herramientas que se usan para imponer el iOS y Mac API para C#.

Este documento describe cómo enlazar Objective-C API, si va a enlazar solo las API de C, debe utilizar el mecanismo de .NET estándar para ello, [el marco de trabajo de P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/).
Obtener más información sobre cómo vincular estáticamente una biblioteca C está disponibles en la [vincular bibliotecas nativas](~/ios/platform/native-interop.md) página.

Consulte nuestro complementaria [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md).
Además, si desea obtener más información sobre lo que sucede en segundo plano, consulte nuestro [Binding Overview](~/cross-platform/macios/binding/overview.md) página.

Los enlaces se pueden compilar para iOS y las bibliotecas de Mac.
Esta página describe cómo trabajar en un enlace, sin embargo son muy similares enlaces Mac de iOS.

**Código de ejemplo para iOS**

Puede usar el [iOS Binding Sample](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) proyecto para experimentar con los enlaces.

<a name="Getting_Started" />

## <a name="getting-started"></a>Introducción

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Es la manera más fácil de crear un enlace crear un proyecto de enlace de Xamarin.iOS.
Puede hacerlo desde Visual Studio para Mac seleccionando el tipo de proyecto, **iOS > biblioteca > biblioteca de enlaces**:

[![](objective-c-libraries-images/00-sml.png "Ello desde Visual Studio para Mac, seleccione el tipo de proyecto Biblioteca de enlaces de iOS")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Es la manera más fácil de crear un enlace crear un proyecto de enlace de Xamarin.iOS.
Puede hacerlo desde Visual Studio en Windows, seleccione el tipo de proyecto, **Visual C# > iOS > biblioteca de enlaces (iOS)**:

[![](objective-c-libraries-images/00vs-sml.png "iOS iOS de la biblioteca de enlaces")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Nota: Los proyectos de enlace para **Xamarin.Mac** solo se admiten en Visual Studio para Mac.

-----

El proyecto generado contiene una plantilla pequeña que se puede editar, que contiene dos archivos: `ApiDefinition.cs` y `StructsAndEnums.cs`.

El `ApiDefinition.cs` es donde define el contrato de API, este es el archivo que describe cómo se proyecta la API de C objetivo subyacente en C#. La sintaxis y el contenido de este archivo es los temas principales de las explicaciones de este documento y el contenido del mismo se limitan a interfaces de C# y declaraciones de delegados de C#. El `StructsAndEnums.cs` es el archivo que deberá especificar las definiciones que son necesarios por las interfaces y delegados. Esto incluye valores de enumeración y estructuras que puede utilizar en el código.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Una API de enlace

Para hacer un enlace completa, deberá comprender la definición de la API de C de objetivo y familiarizarse con las directrices de diseño de .NET Framework.

Para enlazar la biblioteca normalmente se iniciará con un archivo de definición de API. Un archivo de definición de API es simplemente un archivo C# origen que contiene interfaces de C# que se han anotado con una serie de atributos que ayudarle a dirigir el enlace.  Este archivo es lo que define el contrato entre C# y Objective-C que es.

Por ejemplo, esto es un archivo de api trivial para una biblioteca:

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

El ejemplo anterior define una clase denominada `Cocos2D.Camera` que se deriva de la `NSObject` tipo base (procede de este tipo de `Foundation.NSObject`) y que define una propiedad estática (`ZEye`), dos métodos que toman ningún argumento y un método que toma tres argumentos.

Se trata una explicación más detallada del formato de archivo de la API y los atributos que puede usar en el [archivo de definición de API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) sección más adelante.

Para generar un enlace completo, por lo general abordará con cuatro componentes:

-  El archivo de definición de API (`ApiDefinition.cs` en la plantilla).
-  Opcional: las enumeraciones, tipos, structs requeridos por el archivo de definición de API (`StructsAndEnums.cs` en la plantilla).
-  Opcionales: orígenes adicionales que pueden expandir el enlace generado, o proporcionar una más descriptiva API de C# (los archivos de C# que agregue al proyecto).
-  La biblioteca nativa que se va a enlazar.

Este gráfico muestra la relación entre los archivos:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "Este gráfico muestra la relación entre los archivos")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

El archivo de definición de la API solo contendrá las definiciones de interfaz y los espacios de nombres (con los miembros que puede contener una interfaz) y no debe contener clases, enumeraciones, delegados o estructuras. El archivo de definición de API es simplemente el contrato que se usarán para generar la API.

Cualquier código adicional que necesita como enumeraciones o clases auxiliares deben hospedarse en un archivo independiente, en el ejemplo anterior, "CameraMode" es un valor de enumeración que no existe en el archivo CS y debe estar hospedado en un archivo independiente, por ejemplo `StructsAndEnums.cs` :

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

El `APIDefinition.cs` archivo se combina con la `StructsAndEnum` clase y se usan para generar el enlace principal de la biblioteca. Puede usar la biblioteca resultante como-es, pero en general, le interesará optimizar la biblioteca resultante para agregar algunas características de C# para el beneficio de los usuarios. Algunos ejemplos incluyen la implementación de un `ToString()` método, proporcionar los indizadores de C#, agregar las conversiones implícitas a y desde algunos tipos nativos o proporcionar versiones fuertemente tipada de algunos métodos. Estas mejoras se almacenan en archivos adicionales de C#. Simplemente agregue los archivos de C# al proyecto y se incluirán en este proceso de compilación.

Esto muestra cómo podría implementar el código en su `Extra.cs` archivo. Tenga en cuenta que va a usar las clases parciales como estos aumentan las clases parciales que se generan a partir de la combinación de la `ApiDefinition.cs` y `StructsAndEnums.cs` principales del enlace:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

Creación de la biblioteca generará su enlace nativo.

Para completar este enlace, debe agregar la biblioteca nativa para el proyecto.  Puede hacerlo mediante la adición de la biblioteca nativa al proyecto, arrastrando y colocando la biblioteca nativa de buscador en el proyecto en el Explorador de soluciones, o haciendo clic en el proyecto y elija **agregar**  >  **Agregar archivos** para seleccionar la biblioteca nativa.
Bibliotecas nativas por convención comienzan con la palabra "lib" y terminan con la extensión ".una". Al hacer esto, Visual Studio para Mac agregará dos archivos: el archivo .una y un archivo rellenado automáticamente en C# que contiene información sobre lo que contiene la biblioteca nativa:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Bibliotecas nativas por convención empezar por la biblioteca de word y terminan con la extensión .una")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

El contenido de la `libMagicChord.linkwith.cs` archivo contiene información acerca de cómo se puede usar esta biblioteca e indica el IDE para empaquetar este binario en el archivo DLL resultante:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Información detallada sobre cómo utilizar el [ `[LinkWith]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) atributo están documentados en la [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md).

Ahora al compilar el proyecto terminará con un `MagicChords.dll` archivo que contiene el enlace y la biblioteca nativa. Puede distribuir este proyecto o usar el archivo DLL resultante a otros desarrolladores para sus propios.

En ocasiones, es posible que necesite unos valores de enumeración, las definiciones de delegado u otros tipos. No coloque los en el archivo de definiciones de API, puesto que éste es simplemente un contrato

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>El archivo de definición de API

El archivo de definición de API se compone de un número de interfaces. Las interfaces en la definición de API se convertirá en una declaración de clase y se deben decorar con el [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) atributo para especificar la clase base para la clase.

Tal vez se pregunte por qué no hemos usado clases en lugar de interfaces para la definición del contrato. Interfaces, seleccionamos porque permite que el contrato para un método de escritura sin tener que proporcionar un cuerpo de método en el archivo de definición de API, ni tener que proporcionar un cuerpo que se debía producir una excepción o devuelve un valor significativo.

Pero, puesto que estamos usando la interfaz como un esqueleto para generar una clase que tuvimos que recurrir a la decoración de distintas partes del contrato con atributos para controlar el enlace.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Métodos de enlace

El enlace más sencillo para ello consiste en enlazar un método. Simplemente declare un método en la interfaz con las convenciones de nomenclatura de C# y decorar el método con el [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo. El [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo es lo que vincula el nombre de C# con el nombre del objetivo-C en el tiempo de ejecución de Xamarin.iOS. El parámetro de la [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo es el nombre del selector de C de objetivo. Algunos ejemplos:

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

Los ejemplos anteriores muestran cómo se pueden enlazar métodos de instancia. Para enlazar métodos estáticos, debe utilizar el [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) atributo, similar al siguiente:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Esto es necesario porque el contrato es parte de una interfaz y interfaces no tienen ninguna noción de las declaraciones de puertos estáticos frente a instancia, por lo que es necesario una vez más que recurrir a atributos. Si desea ocultar un método determinado desde el enlace, puede decorar el método con el [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo.

El `btouch-native` comando presentará comprobaciones para parámetros de referencia no es null. Si desea permitir valores null para un parámetro concreto, use la [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) atributo en el parámetro, del siguiente modo:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Al exportar un tipo de referencia, con la [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) (palabra clave), también puede especificar la semántica de asignación. Esto es necesario para asegurarse de que no se produzca una pérdida ningún dato.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Propiedades de enlace

Como los métodos, propiedades de Objective-C se enlazan mediante el [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo y se asignan directamente a las propiedades de C#. Como los métodos, las propiedades se pueden decorar con el [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) y [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributos.

Cuando se usa el [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo en una propiedad con portadas btouch-nativo enlaza en realidad dos métodos: el captador y el establecedor. El nombre que proporcione exportar es el **basename** y se calcula el establecedor anteponiendo la palabra "set", si la primera letra de la **basename** en mayúsculas y hacer que el selector de tomar un argumento pasado. Esto significa que `[Export ("label")]` aplicado en una propiedad enlaza realmente la "etiqueta" y "setLabel:" métodos de C de objetivo.

En ocasiones, las propiedades de Objective-C no siguen el patrón descrito anteriormente y el nombre se sobrescribe manualmente. En esos casos puede controlar la forma en que el enlace se genera mediante el [ `[Bind]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) atributo al captador o establecedor, por ejemplo:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

A continuación enlaza "isMenuVisible" y "setMenuVisible:". Si lo desea, se puede enlazar una propiedad utilizando la sintaxis siguiente:

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

Donde se definen explícitamente el captador y establecedor que en el `name` y `setName` enlaces anteriores.

Además de soporte técnico para las propiedades estáticas mediante [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), puede decorar las propiedades de subproceso estático con [ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), por ejemplo:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Al igual que los métodos permiten algunos parámetros se marque con [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), puede aplicar [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) a una propiedad para indicar que null es un valor válido para la propiedad, por ejemplo:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

El [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) parámetro también se puede especificar directamente en el establecedor:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Advertencias sobre cómo enlazar controles personalizados

Al configurar el enlace de un control personalizado, deben tener en cuenta las siguientes observaciones:

1. **Propiedades de enlace deben ser estático** : al definir el enlace de propiedades, el [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) atributo debe usarse.
 2. **Los nombres de propiedad deben coincidir exactamente con** -el nombre usado para enlazar la propiedad debe coincidir exactamente con el nombre de la propiedad en el control personalizado.
3. **Tipos de propiedad deben coincidir exactamente con** -el tipo de variable que se usa para enlazar la propiedad debe coincidir exactamente con el tipo de la propiedad en el control personalizado.
4. **Los puntos de interrupción y el captador o establecedor** : puntos de interrupción se colocan en el captador o nunca se alcanzarán los métodos de establecedor de la propiedad.
5. **Observe las devoluciones de llamada** -debe usar devoluciones de llamada de observación para recibir una notificación de cambios en los valores de propiedad de controles personalizados.

Si no se observa cualquiera de las advertencias de la lista anteriores puede producirse en el enlace en modo silencioso un error en tiempo de ejecución.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Propiedades y patrón mutable objective-c.

Marcos de Objective-C usan una locución que algunas clases son inmutables con una subclase mutable. Por ejemplo `NSString` es la versión inmutable, mientras que `NSMutableString` es la subclase que permite mutación.

En esas clases es habitual ver que la clase base inmutable contienen propiedades con un captador, pero ningún establecedor. Y para la versión mutable introducir el establecedor. Puesto que esto no es realmente posible con C#, tuvimos que asignar esta expresión en una locución que funcionará con C#.

La forma que se asigna a C# consiste en agregar el captador y establecedor de la clase base, pero al marcar el establecedor con un [ `[NotImplemented]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute) atributo.

A continuación, en la subclase mutable, utilice la [ `[Override]` ](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) atributo en la propiedad para asegurarse de que la propiedad realmente es invalidar el comportamiento del elemento primario.

Ejemplo:

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>Constructores de enlace

El `btouch-native` herramienta generará automáticamente constructores de cuatros en su clase, para una clase determinada `Foo`, genera:

-  `Foo ()`: el constructor predeterminado (se asigna al constructor de "inicialización" del objetivo-C)
-  `Foo (NSCoder)`: el constructor se usa durante la deserialización de los archivos NIB (se asigna a Objective-C "initWithCoder:" constructor).
-  `Foo (IntPtr handle)`: el constructor para la creación de basado en identificadores, se invoca en tiempo de ejecución cuando el tiempo de ejecución tiene que exponer un objeto administrado desde un objeto no administrado.
-  `Foo (NSEmptyFlag)`: se utiliza por las clases derivadas para evitar la doble inicialización.

Para los constructores que defina, deben declararse con la siguiente firma dentro de la definición de interfaz: debe devolver un `IntPtr` valor y el nombre del método deben ser el Constructor. Por ejemplo, para enlazar el `initWithFrame:` constructor, esto es lo que utiliza:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocolos de enlace

Como se describe en el documento de diseño de la API, en la sección [hablando de modelos y protocolos](~/ios/internals/api-design/index.md#Models), Xamarin.iOS asigna los protocolos Objective-C en clases que se han marcado con el [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) atributo. Normalmente se utiliza al implementar las clases de delegado de C de objetivo.

La gran diferencia entre una clase de enlace normal y una clase de delegado es que la clase de delegado puede tener uno o más métodos opcionales.

Considere, por ejemplo la `UIKit` clase `UIAccelerometerDelegate`, esta es la forma en se enlaza en Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Puesto que éste es un método opcional en la definición de `UIAccelerometerDelegate` hay puede hacer nada más. Pero si se produjo un método requerido por el protocolo, debe agregar el [ `[Abstract]` ](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute) atributo al método. Esto obligará al usuario de la implementación que realmente proporcione un cuerpo para el método.

En general, protocolos se utilizan en las clases que respondan a los mensajes. Esto se hace normalmente en Objective C mediante la asignación a la propiedad "delegate" una instancia de un objeto que responde a los métodos en el protocolo.

La convención de Xamarin.iOS consiste en admitir ambos el objetivo-estilo C con acoplamiento flexible que cualquier instancia de un `NSObject` puede asignarse al delegado y exponer también una versión fuertemente tipada del mismo. Por este motivo, se suelen proporcionan tanto un `Delegate` propiedad fuertemente tipado y un `WeakDelegate` flexible que ha escrito. Normalmente enlazamos la versión imprecisa con [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute), usamos el [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) atributo para proporcionar la versión fuertemente tipada.

Esto muestra la forma en que se enlaza el `UIAccelerometer` clase:

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

<a name="iOS7ProtocolSupport" />

**Nuevo en MonoTouch 7.0**

Se ha incorporado a partir de MonoTouch 7.0 un funcionalidad de enlace de protocolo nuevo y mejorado.  Esta nueva compatibilidad hace más fácil de usar expresiones de C de objetivo para la adopción de uno o más protocolos en una clase determinada.

Para cada definición de protocolo `MyProtocol` en Objective-C, ahora hay un `IMyProtocol` interfaz que muestra todos los métodos necesarios del protocolo, así como una clase de extensión que proporciona todos los métodos opcionales.  Lo anterior, se combinan con nueva en el editor permite a los desarrolladores implementar métodos de protocolo sin tener que usar las subclases independientes de las clases de modelo abstracto anterior de Xamarin Studio.

Cualquier definición que contiene el [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) atributo realmente generará tres clases auxiliares que mejoran considerablemente la forma en que se usan protocolos:

```csharp
    // Full method implementation, contains all methods
    class MyProtocol : IMyProtocol {
        public void Say (string msg);
        public void Listen (string msg);
    }

    // Interface that contains only the required methods
    interface IMyProtocol: INativeObject, IDisposable {
        [Export ("say:")]
        void Say (string msg);
    }

    // Extension methods
    static class IMyProtocol_Extensions {
        public static void Optional (this IMyProtocol this, string msg);
        }
    }
```

El **implementación de la clase** proporciona una clase abstracta completa que puede invalidar los métodos individuales de y obtener la seguridad de tipos completa.  Pero debido a C# no admite herencia múltiple, hay escenarios donde es posible que deba tener una clase base diferente, pero desea implementar una interfaz, que es donde el

Generado **definición de la interfaz** entra en juego.  Es una interfaz que tiene todos los métodos necesarios del protocolo.  Esto permite a los desarrolladores que desean implementar el protocolo para simplemente implementa la interfaz.  El tiempo de ejecución registrará automáticamente el tipo como adoptar el protocolo.

Tenga en cuenta que la interfaz solo muestra los métodos necesarios y exponer los métodos opcionales.  Esto significa que las clases que adopten el protocolo obtendrán completa comprobación de tipos para los métodos necesarios, pero tendrán que recurrir a establecimiento flexible de tipos (manualmente mediante [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributos y que coincide con la firma) para la parte opcional métodos de protocolo.

Para que sea conveniente utilizar una API que usa los protocolos, la herramienta de enlace también generará una clase de método de extensiones que expone todos los métodos opcionales.  Esto significa que siempre que se consume una API, podrá tratar protocolos como si tuviera todos los métodos.

Si desea utilizar las definiciones de protocolo de la API, debe escribir esqueletos interfaces vacías en la definición de API.  Si desea usar el MiProtocolo en una API, deberá hacer esto:

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

Los pasos anteriores es necesaria porque en tiempo de enlace el `IMyProtocol` no existiría, que es por qué necesita proporcionar una interfaz vacía.

#### <a name="adopting-protocol-generated-interfaces"></a>Adopción de interfaces generadas por el protocolo

Cada vez que implemente una de las interfaces que se generan para los protocolos, similar al siguiente:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Automáticamente se exporta la implementación para los métodos de interfaz con el nombre correcto, por lo que es equivalente a esta:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

No importa si se implementa la interfaz de forma implícita o explícita.

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>Extensiones de clase de enlace

En Objective C es posible extender clases con nuevos métodos, en esencia similares a los métodos de extensión de C#. Cuando hay uno de estos métodos, puede usar el [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) atributo para marcar el método como el receptor del mensaje Objective-C.

Por ejemplo, en Xamarin.iOS se enlazan los métodos de extensión que se definen en `NSString` cuando `UIKit` se importa como métodos en el `NSStringDrawingExtensions`, similar a la siguiente:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Enlace de listas de argumentos Objective-c.

Objective-C admite argumentos variádicas. Por ejemplo:

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Para invocar este método de C# que se desea crear una firma similar al siguiente:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Esto declara el método como interno, ocultando la API anterior de los usuarios, pero expone a la biblioteca. A continuación, puede escribir un método similar al siguiente:

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

<a name="Binding_Fields" />

### <a name="binding-fields"></a>Enlazar campos

A veces, desea tener acceso a los campos públicos que se declaran en una biblioteca.

Normalmente estos campos contienen valores de cadenas o enteros que se deben hacer referencia. Se suelen usar como cadena que representa una notificación específica y claves en diccionarios.

Para enlazar un campo, agregar una propiedad a su archivo de definición de interfaz y decorar la propiedad con el [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) atributo. Este atributo toma un parámetro: el nombre de C del símbolo para la búsqueda. Por ejemplo:

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Si desea incluir varios campos en una clase estática que no se deriva de `NSObject`, puede usar el [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) atributo en la clase, similar al siguiente:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

Los pasos anteriores, se generarán una `LonelyClass` que no se deriva de `NSObject` y contendrá un enlace a la `NSSomeEventNotification` 
 `NSString` expone como un `NSString`.

El [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) atributo se puede aplicar a los siguientes tipos de datos:

-  `NSString` referencias (solo propiedades de solo lectura)
-  `NSArray` referencias (solo propiedades de solo lectura)
-  enteros de 32 bits (`System.Int32`)
-  enteros de 64 bits (`System.Int64`)
-  valores de punto flotante de 32 bits (`System.Single`)
-  valores de punto flotante de 64 bits (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

Además del nombre del campo nativo, puede especificar el nombre de la biblioteca donde se encuentra el campo, pasando el nombre de la biblioteca:

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

Si se vincula estáticamente, no hay ninguna biblioteca para enlazar, por lo que deberá utilizar el `__Internal` nombre:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Enumeraciones de enlace

Puede agregar `enum` directamente en el enlace de archivos resulta más fácil usar dentro de las definiciones de API - sin utilizar un archivo de origen diferente (que debe compilarse en los enlaces y el proyecto final).

Ejemplo:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

También es posible crear sus propios enumeraciones para reemplazar `NSString` constantes. En este caso el generador le **automáticamente** crear los métodos para convertir valores de las enumeraciones y constantes NSString para usted.

Ejemplo:

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

En el ejemplo anterior podría decidir decorar `void Perform (NSString mode);` con una [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo. Esto le permitirá **ocultar** la API basada en la constante de los consumidores de enlace.

Sin embargo esto limitaría el tipo de creación de subclases que utilizan alternativas las API más adecuadas una [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) atributo. Los métodos generados no son `virtual`, es decir, que no puedan invalidar ellos - que pueden o no, es una buena opción.

Una alternativa es marcar el original, `NSString`-según, definición como `[Protected]`. Esto le permitirá subclases funcione, cuando sea necesario, y la versión de wrap'ed seguirá trabajar y llame al método reemplazada.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Enlace `NSValue`, `NSNumber`, y `NSString` a un tipo mejor

El [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) atributo permite el enlace `NSNumber`, `NSValue` y `NSString`(enumeraciones) en tipos de C# más precisos. El atributo se puede usar para crear mejor y más precisos, API de .NET a través de la API nativa.

Puede decorar métodos (en el valor devuelto), parámetros y propiedades con [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). La única restricción es que el miembro **no debe** estar dentro de un [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) o [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interfaz.

Por ejemplo:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Generaría:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente, se realizará la `bool?`  <->  `NSNumber` y `CGRect`  <->  `NSValue` conversiones.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) También es compatible con matrices de `NSNumber` `NSValue` y `NSString`(enumeraciones).

Por ejemplo:

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

Generaría:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` es un `NSString` copia enum, se capturará el derecho `NSString` valor y controlar la conversión de tipos.

Vea la [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) documentación para ver los tipos de conversión compatible.

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notificaciones de enlace

Las notificaciones son mensajes que se publican en el `NSNotificationCenter.DefaultCenter` y se utilizan como un mecanismo para difundir mensajes de una parte de la aplicación a otra. Los desarrolladores de suscriben a notificaciones normalmente mediante la [NSNotificationCenter](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/)del [AddObserver](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/M/AddObserver/) método. Cuando una aplicación envía un mensaje en el centro de notificaciones, normalmente contiene una carga que se almacenan en la [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) diccionario. Este diccionario tiene establecimiento flexible y obtener información fuera de ella es propenso a errores, ya que los usuarios normalmente deben leer en la documentación de las claves que están disponibles en el diccionario y los tipos de los valores que se pueden almacenar en el diccionario. La presencia de las claves a veces se utiliza como un valor booleano así.

El generador de enlace de Xamarin.iOS proporciona compatibilidad para desarrolladores enlazar las notificaciones. Para ello, establezca la [ `[Notification]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute) atributo en una propiedad que ha sido también etiquetados con un [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) propiedad (puede ser público o privado).

Este atributo puede utilizarse sin argumentos para las notificaciones que llevar a cabo ninguna carga, o puede especificar un `System.Type` que hace referencia a otra interfaz en la definición de API, normalmente con el nombre termina con "EventArgs". El generador de convertirá la interfaz en una clase que cree subclases `EventArgs` e incluirá todas las propiedades en la lista. El [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo debe usarse en la clase EventArgs para mostrar el nombre de la clave utilizada para buscar el diccionario Objective-C para capturar el valor.

Por ejemplo:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

El código anterior generará una clase anidada `MyClass.Notifications` con los métodos siguientes:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Los usuarios de su código, a continuación, pueden suscribirse fácilmente a las notificaciones que se publican en el [NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/) mediante código similar al siguiente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

El valor devuelto de `ObserveDidStart` puede utilizarse para fácilmente dejar de recibir notificaciones, similar al siguiente:

```csharp
token.Dispose ();
```

O puede llamar a [NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject/) y pasar el token. Si la notificación contiene parámetros, debe especificar una aplicación auxiliar `EventArgs` interfaz como esta:

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

Los pasos anteriores, se generarán una `MyScreenChangedEventArgs` clase con la `ScreenX` y `ScreenY` propiedades que capturará los datos de la [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) diccionario utilizando las claves "ScreenXKey" y "ScreenYKey" respectivamente y aplicar las conversiones apropiadas. El `[ProbePresence]` atributo se usa para el generador para sondear si la clave está establecida el `UserInfo`, en lugar de intentar extraer el valor. Se utiliza para los casos donde la presencia de la clave es el valor (normalmente por valores booleanos).

Esto le permite escribir código similar al siguiente:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Categorías de enlace

Categorías son un mecanismo de Objective-C utilizado para ampliar el conjunto de métodos y propiedades disponibles en una clase.   En la práctica, se utilizan para extender la funcionalidad de una clase base (por ejemplo `NSObject`) cuando se vincula un marco concreto en (por ejemplo `UIKit`), que realiza sus métodos disponibles, pero solo si el nuevo marco de trabajo está vinculado en.   En otros casos, se utilizan para organizar las características en una clase según su funcionalidad.   Únicamente son en esencia similares a los métodos de extensión de C#. Se trata de una categoría de aspecto en C: objetivo

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

El ejemplo anterior si se encuentra en una biblioteca extendería instancias de `UIView` con el método `makeBackgroundRed`.

Para enlazarlas, puede usar el [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) atributo en una definición de interfaz.  Cuando se usa el [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) de atributo, el significado de la [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) atributo cambia desde que se usa para especificar la clase base para extender, para que sea el tipo de extender.

La siguiente muestra cómo el `UIView` extensiones están enlazadas y se convierten en métodos de extensión de C#:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Los pasos anteriores, se creará un `MyUIViewExtension` una clase que contiene el `MakeBackgroundRed` método de extensión.  Esto significa que ahora puede llamar a "MakeBackgroundRed" en cualquier `UIView` subclase, lo que le ofrece la misma funcionalidad que obtendría en objetivo-C. En otros casos, se utilizan categorías no se extiende una clase del sistema, pero para organizar funcionalidad exclusivamente con fines de decoración.  Así:

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Aunque puede usar el [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) atributo también para este estilo de decoración de declaraciones, podría también simplemente agregarlas todas a la definición de clase.  Ambos podrían conseguir el mismo:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

Es simplemente más corto en estos casos para combinar las categorías:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

<a name="Binding_Blocks" />

### <a name="binding-blocks"></a>Bloques de enlace

Los bloques son una estructura nueva presentada por Apple para poner el equivalente funcional de los métodos anónimos de C# al objetivo de C. Por ejemplo, el `NSSet` clase expone este método:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

La descripción anterior declara un método llamado `enumerateObjectsUsingBlock:` que toma un argumento con nombre `block`. Este bloque es similar a un método anónimo de C# tiene compatibilidad para capturar el entorno actual (el puntero "this", el acceso a las variables locales y parámetros). El método anterior en `NSSet` el bloque con dos parámetros, se invoca un `NSObject` (la `id obj` parte) y un puntero a un valor booleano (el `BOOL *stop`) parte.

Para enlazar este tipo de API con btouch, deberá declarar primero la firma de tipo de bloque como C# delegar y, a continuación, hacer referencia a él desde un punto de entrada de API, similar al siguiente:

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

Y ahora el código puede llamar a la función de C#:

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

También puede usar expresiones lambda si prefiere:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>Métodos asincrónicos

El generador de enlace puede convertir una clase determinada de métodos en métodos asincrónicos sencillo (métodos que devuelven una tarea o&lt;T&gt;).

Puede usar el [ `[Async]` ](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) atributo en los métodos que devuelven void y cuyo último argumento es una devolución de llamada.  Cuando esto se aplica a un método, el generador de enlace generará una versión de ese método con el sufijo `Async`.  Si la devolución de llamada no toma ningún parámetro, el valor devuelto será una `Task`, si la devolución de llamada toma un parámetro, el resultado será un `Task<T>`.  Si la devolución de llamada toma varios parámetros, debe establecer el `ResultType` o `ResultTypeName` para especificar el nombre deseado del tipo generado que contendrá todas las propiedades.

Ejemplo:

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

El código anterior generará tanto el método LoadFile, así como:

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Así como la exposición tipos seguros para los parámetros de NSDictionary débiles

En muchos lugares en la API de C de objetivo, los parámetros se pasan débilmente tipada `NSDictionary` API con determinadas claves y valores, pero estas son propenso a errores (que puede pasar claves no válidas y no obtener ninguna advertencia; puede pasar valores no válidos y no obtener ninguna advertencia) y frustrante Para utilizar que necesiten varios viajes a la documentación para buscar los posibles nombres de claves y valores.

La solución consiste en proporcionar una versión fuertemente tipada que proporciona que la versión fuertemente tipada de la API y en segundo plano asigna los distintos subyacente claves y valores.

Así pues, por ejemplo, si la API Objective-C acepta un `NSDictionary` y está documentado que toma la clave `XyzVolumeKey` que adopta un `NSNumber` con un valor de volumen entre 0.0 y 1.0 y un `XyzCaptionKey` que toma una cadena, desearía que los usuarios tengan una API "nice" es similar a esto:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

El `Volume` propiedad se define como float que aceptan valores NULL, como la convención en Objective C no requiere estos diccionarios que tienen el valor, por lo que hay escenarios donde no se puede establecer el valor.

Para ello, debe hacer algunas cosas:

* Cree una clase fuertemente tipada que subclasifique [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) y proporciona diversas captadores y establecedores para cada propiedad.
* Declara sobrecargas de los métodos que toman `NSDictionary` para tomar la nueva versión fuertemente tipada.

Puede crear la clase fuertemente tipada manualmente o utilizar el generador para hacer el trabajo por usted.  En primer lugar, exploramos cómo hacerlo manualmente para que sepan que está sucediendo y, a continuación, la solución automática.

Es necesario crear un archivo auxiliar para este, no entra en su contrato de API.  Esto es lo que tendría que escribir crear la clase XyzOptions:

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

A continuación, debe proporcionar un método contenedor que expone la API de alto nivel sobre la API de bajo nivel.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Si la API no es necesario para que se sobrescriban, sin ningún riesgo puede ocultar la API basada en NSDictionary mediante la [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo.

Como puede ver, usamos el [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) de atributo para obtener un nuevo punto de entrada de API y se expuesta mediante nuestro fuertemente tipado `XyzOptions` clase.  El método de contenedor también se permite si hay valores null que se pasan.

Ahora, algo que no se ha mencionamos es donde el `XyzOptionsKeys` proceden de valores.  Normalmente se pueden agrupar las claves que expone una API en una clase estática como `XyzOptionsKeys`, similar a la siguiente:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Echemos un vistazo a la compatibilidad automática para crear estos diccionarios fuertemente tipada.  Esto evita una gran cantidad de la reutilizable y puede definir el diccionario directamente en su contrato de API, en lugar de usar un archivo externo.

Para crear un diccionario fuertemente tipado, introducir una interfaz en la API y decorarla con el [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) atributo.  Esto indica que el generador que debe crear una clase con el mismo nombre que la interfaz que se deriva de `DictionaryContainer` y le proporcionará los descriptores de acceso con tipo seguros para él.

El [ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) atributo toma un parámetro, que es el nombre de la clase estática que contiene las claves del diccionario.  A continuación, cada propiedad de la interfaz se convertirá en un descriptor de acceso fuertemente tipado.  De forma predeterminada, el código utilizará el nombre de la propiedad con el sufijo "Clave" en la clase estática para crear el descriptor de acceso.

Esto significa que ya no es crear el descriptor de acceso fuertemente tipado requiere un archivo externo, ni tener que crear manualmente los captadores y establecedores para todas las propiedades ni tener que buscar manualmente las claves usted mismo.

Se trata de cuál sería el entero de enlace:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Por si necesita hacer referencia en su `XyzOption` miembros un campo diferente (es decir no el nombre de la propiedad con el sufijo `Key`), puede decorar la propiedad con un [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo con el nombre que usted desea usar.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Asignaciones de tipos

En esta sección se explica cómo se asignan los tipos de Objective-C a tipos de C#.

<a name="Simple_Types" />

### <a name="simple-types"></a>Tipos simples

En la tabla siguiente se muestra cómo debe asignar tipos de C de objetivo y CocoaTouch world a todo el mundo de Xamarin.iOS:

|Nombre de tipo Objective-c.|Tipo de API unificada de Xamarin.iOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([más sobre cómo enlazar NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (Vea también: [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Tipos de CoreFoundation (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|Tipos Foundation (`NS*`)|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>Matrices

El tiempo de ejecución de Xamarin.iOS automáticamente se encarga de convertir las matrices de C# para `NSArrays` y realiza la conversión, por ejemplo el método Objective-C imaginaria que devuelve un `NSArray` de `UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

Se enlaza de forma similar al siguiente:

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

La idea consiste en utilizar una matriz C# fuertemente tipada como esto permitirá que el IDE proporcionar la finalización correcta de código con el tipo real sin obligar al usuario a adivinar o buscar la documentación para averiguar el tipo real de los objetos contenidos en la matriz.

En casos donde no puede realizar seguimiento hacia abajo el tipo más derivado real incluido en la matriz, puede utilizar `NSObject []` como el valor devuelto.

<a name="Selectors" />

### <a name="selectors"></a>Selectores

Selectores aparecen en la API de Objective-C como el tipo especial `SEL`. Al enlazar un selector, podría asignar el tipo a `ObjCRuntime.Selector`.  Normalmente se exponen los selectores en una API con un objeto, el objeto de destino y un selector para invocar en el objeto de destino. Proporcionar ambos básicamente se corresponde con el delegado de C#: algo que encapsula el método que se invoca junto con el objeto que se va a invocar el método en.

Este es el aspecto que tiene el enlace:

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

Y este es cómo el método normalmente se usaría en una aplicación:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

Para realizar el enlace más adecuada para los desarrolladores de C#, normalmente proporcionará un método que toma un `NSAction` parámetro, que permite que los delegados de C# y expresiones lambda que se utilizará en lugar de la `Target+Selector`. Para ello, normalmente debería ocultar el `SetTarget` método marcando con un [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo y, a continuación, se debería mostrar un nuevo método de aplicación auxiliar, similar al siguiente:

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

Ahora se puede escribir el código de usuario similar al siguiente:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

<a name="Strings" />

### <a name="strings"></a>Cadenas

Si va a enlazar un método que toma un `NSString`, puede reemplazar que con un tipo de cadena de C#, ambos en devuelven tipos y parámetros.

El único caso si desea usar un `NSString` directamente es cuando se usa la cadena como un token. Para obtener más información acerca de las cadenas y `NSString`, lea la [diseño de la API en NSString](~/ios/internals/api-design/nsstring.md) documento.

En algunos casos excepcionales, una API podría exponer una cadena de tipo C (`char *`) en lugar de una cadena de C del objetivo (`NSString *`). En esos casos, puede anotar el parámetro con el [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring) atributo.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out / parámetros ref

Algunas API de devolver valores de sus parámetros o pasa parámetros por referencia.

Normalmente la firma se ve así:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

El primer ejemplo muestra una forma habitual de Objective-C para devolver códigos de error, un puntero a un `NSError` pasa el puntero y, cuando se devuelve el valor se establece.   El segundo método muestra cómo un método Objective-C podría tomar un objeto y modificar su contenido.   Esto sería una pasada por referencia, en lugar de un valor de salida puro.

El enlace sería similar al siguiente:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Atributos de la administración de memoria

Cuando se usa el [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo y está pasando los datos que se conservarán por el método llamado, puede especificar la semántica de argumento si se pasa como segundo parámetro, por ejemplo:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

Los pasos anteriores marca el valor que indica que la semántica de "Conservar". La semántica disponible es:

-  Asignar
-  Copiar
-  Conservar

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Directrices de estilo

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>Uso de [interno]

Puede usar el [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo para ocultar un método de la API pública. Puede hacer esto en casos donde la API expuesta es demasiado bajo nivel y desea proporcionar una implementación de nivel superior en un archivo independiente en función de este método.

También puede usar al encontrarse con limitaciones en el generador de enlace, por ejemplo, algunos escenarios avanzados podrían exponer tipos que no están enlazados y que desea enlazar en su propia forma y va a incluir esos tipos de su propia manera.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Las devoluciones de llamada y controladores de eventos

Clases de Objective-C normalmente difusión notificaciones o solicitan información mediante el envío de un mensaje en una clase de delegado (Objective-c. delegado).

Este modelo, mientras que totalmente compatible y obtenidas por Xamarin.iOS a veces puede resultar complicada. Xamarin.iOS expone el modelo de evento de C# y un sistema de devolución de llamada de método en la clase que se puede usar en estas situaciones. Esto permite que el código similar al siguiente para ejecutar:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

El generador de enlace es capaz de reducir la cantidad de escritura necesarios para asignar el patrón Objective-C en el patrón de C#.

A partir de Xamarin.iOS 1.4 se podrán registrarse indicar al generador para producir enlaces para un específico delegados Objective-C y exponer al delegado como C# eventos y propiedades en el tipo de host.

Hay dos clases implicadas en este proceso, la clase de host, lo que es el que actualmente emite eventos y los envía en el `Delegate` o `WeakDelegate` y la clase de delegado real.

Tenga en cuenta la siguiente configuración:

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

Para incluir la clase debe:

-  En la clase de host, agregue a su [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   el tipo que actúa como su delegado y el nombre de C# que expone de la declaración. En nuestro ejemplo anterior son `typeof (MyClassDelegate)` y `WeakDelegate` respectivamente.
-  En la clase de delegado en cada método que tiene más de dos parámetros, debe especificar el tipo que se va a utilizar para la clase EventArgs generada automáticamente.

El generador de enlace no se limita a ajuste solo el destino de un único evento, es posible que algunas clases Objective-C para emitir mensajes a más de un delegado, por lo que tendrá que proporcionar matrices para admitir este programa de instalación. Mayoría de las instalaciones no será necesario, pero el generador está preparado para admitir los casos.

El código resultante será:

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

El `EventArgs` se utiliza para especificar el nombre de la `EventArgs` clase generarse. Debe usar uno por cada firma (en este ejemplo, el `EventArgs` contendrá un `With` propiedad de tipo nint).

Con las definiciones anteriores, el generador producirá el siguiente evento en el MyClass generado:

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

Por lo que ahora puede usar el código similar al siguiente:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

Las devoluciones de llamada son como invocaciones de eventos, la diferencia es que en lugar de tener varios suscriptores posibles (por ejemplo, pueden enlazar varios métodos en un `Clicked` evento o un `DownloadFinished` evento) las devoluciones de llamada solo pueden tener un solo suscriptor.

El proceso es idéntico, la única diferencia es que en lugar de exponer el nombre de la `EventArgs` clase que se generará el parámetro EventArgs realmente se utiliza para denominar el nombre del delegado de C# resultante.

Si el método de la clase de delegado devuelve un valor, el generador de enlace asignará a un método de delegado en la clase primaria en lugar de un evento. En estos casos, debe proporcionar el valor predeterminado que se debe devolver el método si el usuario no enlazar al delegado. Esto se hace mediante el [ `[DefaultValue]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) o [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) atributos.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) le codificar un valor devuelto, mientras que [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) se utiliza para especificar el argumento de entrada que se devolverá.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Enumeraciones y tipos base

También puede hacer referencia enumeraciones o tipos base que no son compatibles directamente con el sistema de definición de interfaz btouch. Para ello, coloque su enumeraciones y tipos principales en un archivo independiente e incluir como parte de uno de los archivos adicionales que se proporcionan a btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>Vincular las dependencias

Si va a enlazar las API que no forman parte de la aplicación, debe asegurarse de que su archivo ejecutable está vinculado con estas bibliotecas.

Deberá informar a Xamarin.iOS cómo vincular las bibliotecas, esto puede hacerse mediante la modificación de la configuración de compilación para invocar la `mtouch` comando con algunos extra compilar argumentos que especifiquen cómo vincular con las nuevas bibliotecas con la "-gcc_flags" opción, seguido de una cadena entrecomillada que contiene todas las bibliotecas adicionales que son necesarias para el programa, como el siguiente:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

El ejemplo anterior se vinculará `libMyLibrary.a`, `libSystemLibrary.dylib` y `CFNetwork` biblioteca del marco de trabajo del archivo ejecutable final.

O puede aprovechar el nivel de ensamblado [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), que se puede incrustar en los archivos de contrato (como `AssemblyInfo.cs`).
Cuando se usa el [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), debe tener la biblioteca nativa disponible en el momento de realizar el enlace, como esta opción incrusta la biblioteca nativa con la aplicación. Por ejemplo:

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Tal vez se pregunte, ¿qué necesita `-force_load` comando y el motivo es que ObjC - marca aunque se compila el código, no conserva los metadatos necesarios para admitir categorías (la eliminación de código muerto del vinculador/compilador elimina lo) que se puede se necesita en tiempo de ejecución para Xamarin.iOS.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Referencias asistidas

Algunos objetos transitorios como las hojas de acción y cuadros de alerta son complicadas de mantener un seguimiento de los desarrolladores y el generador de enlace puede ayudar a un poco aquí.

Por ejemplo, si tiene una clase que se mostró un mensaje y, a continuación, genera un `Done` eventos, la forma tradicional de controlar esto sería:

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

En el escenario anterior, el desarrollador debe mantener la referencia al objeto de sí mismo y cualquier pérdida o activamente borra la referencia para su propio cuadro.  Mientras que admite el código de enlace, el generador para el seguimiento de la referencia que y claro, cuando se invoca un método especial, el código anterior, a continuación, se convertiría en:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Tenga en cuenta cómo ya no es necesario mantener la variable en una instancia, que funciona con una variable local y que no es necesario desactivar la referencia al objeto muere.

Para aprovechar estas características, la clase debe tener un conjunto de propiedades de eventos el [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) declaración y también el `KeepUntilRef` variable se establece en el nombre del método que se invoca cuando el objeto ha completado su trabajo, al igual que Esto:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Herencia de protocolos

A partir de Xamarin.iOS v3.2, se admite la herencia de protocolos que se han marcado con el [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) propiedad. Esto es útil en determinados patrones de API, como in `MapKit` donde el `MKOverlay` de protocolo, que hereda de la `MKAnnotation` protocolo y adoptado por una serie de clases que hereda de `NSObject`.

Históricamente, hemos necesitado copiar el protocolo en todas las implementaciones, pero en estos casos ahora podemos tenemos el `MKShape` clase hereda de la `MKOverlay` protocolo y generarán automáticamente todos los métodos necesarios.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de enlace](https://developer.xamarin.com/samples/BindingSample/)
