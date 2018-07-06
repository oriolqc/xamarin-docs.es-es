---
title: Enlace de bibliotecas de Objective-c.
description: Este documento proporciona información general de cómo crear enlaces de C# para código de Objective-C, que describe cómo enlazar los eventos, métodos, controles personalizados y mucho más.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: 4c414e0e863f44045473a248576a3612b1719559
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854836"
---
# <a name="binding-objective-c-libraries"></a>Enlace de bibliotecas de Objective-c.

Cuando se trabaja con Xamarin.iOS o Xamarin.Mac, pueden producirse los casos donde desee utilizar una biblioteca de Objective-C de terceros. En estos casos, puede usar proyectos de enlace de Xamarin para crear un enlace a las bibliotecas nativas de Objective-C de C#. El proyecto utiliza las mismas herramientas que utilizamos para traer las API de Mac y iOS a C#.

Este documento describe cómo enlazar la API de Objective-C, si va a enlazar sólo las API de C, debe usar el mecanismo estándar de .NET para esto, [el marco de trabajo de P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/).
Obtener más información sobre cómo vincular estáticamente una biblioteca C está disponibles en el [vincular bibliotecas nativas](~/ios/platform/native-interop.md) página.

Consulte nuestro complementaria [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md).
Además, si desea obtener más información sobre lo que sucede en segundo plano, consulte nuestra [Binding Overview](~/cross-platform/macios/binding/overview.md) página.

Los enlaces se pueden crear para iOS y las bibliotecas de Mac.
Esta página describe cómo trabajar en un enlace, sin embargo los enlaces de Mac son muy similares de iOS.

**Código de ejemplo para iOS**

Puede usar el [iOS Binding Sample](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) proyecto para experimentar con los enlaces.

<a name="Getting_Started" />

## <a name="getting-started"></a>Introducción

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para crear un enlace sencillo es crear un proyecto de enlace de Xamarin.iOS.
Puede hacerlo desde Visual Studio para Mac al seleccionar el tipo de proyecto **iOS > biblioteca > biblioteca de enlaces**:

[![](objective-c-libraries-images/00-sml.png "Ello desde Visual Studio para Mac, seleccione el tipo de proyecto Biblioteca de enlaces de la biblioteca de iOS")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para crear un enlace sencillo es crear un proyecto de enlace de Xamarin.iOS.
Puede hacerlo desde Visual Studio en Windows, seleccione el tipo de proyecto, **Visual C# > iOS > biblioteca de enlaces (iOS)**:

[![](objective-c-libraries-images/00vs-sml.png "Biblioteca de enlaces de iOS de iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Nota: Los proyectos de enlace para **Xamarin.Mac** solo se admiten en Visual Studio para Mac.

-----

El proyecto generado contiene una plantilla pequeño que puede editar, que contiene dos archivos: `ApiDefinition.cs` y `StructsAndEnums.cs`.

El `ApiDefinition.cs` es donde va a definir el contrato de API, este es el archivo que se describe cómo la API de Objective-C subyacente se proyectan en C#. La sintaxis y el contenido de este archivo es el tema principal de la explicación de este documento y el contenido del mismo se limitan a las interfaces de C# y declaraciones de delegados de C#. El `StructsAndEnums.cs` es el archivo que se escriben las definiciones que son necesarios por las interfaces y delegados. Esto incluye valores de enumeración y estructuras que puede usar el código.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Una API de enlace

Para realizar un enlace completa, deseará comprender la definición de API de Objective-C y familiarizarse con las directrices de diseño de .NET Framework.

Para enlazar la biblioteca que normalmente empezará con un archivo de definición de API. Un archivo de definición de API es simplemente un archivo C# origen que contiene interfaces de C# que se han anotado con un puñado de atributos que facilitan el enlace.  Este archivo es lo que define el contrato entre C# y Objective-C What ' s.

Por ejemplo, se trata de un archivo de api trivial para una biblioteca:

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

El ejemplo anterior define una clase denominada `Cocos2D.Camera` que se deriva el `NSObject` tipo base (procede de este tipo de `Foundation.NSObject`) y que define una propiedad estática (`ZEye`), dos métodos que toman ningún argumento y un método que toma tres argumentos.

Se trata una explicación detallada del formato de archivo de la API y los atributos que puede usar en el [archivo de definición de API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) sección más adelante.

Para generar un enlace completo, normalmente se ocupará con cuatro componentes:

-  El archivo de definición de API (`ApiDefinition.cs` en la plantilla).
-  Opcional: las enumeraciones, tipos, structs requeridos por el archivo de definición de API (`StructsAndEnums.cs` en la plantilla).
-  Opcionales: orígenes adicionales que pueden expandir el enlace generado, o proporcionar una más descriptiva API de C# (los archivos C# que agregue al proyecto).
-  La biblioteca nativa que se va a enlazar.

Este gráfico muestra la relación entre los archivos:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "Este gráfico muestra la relación entre los archivos")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

El archivo de definición de API solo contendrá las definiciones de interfaz y los espacios de nombres (con los miembros que puede contener una interfaz) y no debe contener las clases, enumeraciones, delegados o structs. El archivo de definición de API es simplemente el contrato que se usará para generar la API.

Cualquier código adicional que necesita, como enumeraciones o clases auxiliares deben hospedarse en un archivo independiente, en el ejemplo anterior, el "CameraMode" es un valor de enumeración que no existe en el archivo CS y debe estar hospedado en un archivo independiente, por ejemplo `StructsAndEnums.cs` :

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

El `APIDefinition.cs` archivo se combina con la `StructsAndEnum` clase y se usan para generar el enlace de núcleo de la biblioteca. Puede usar la biblioteca resultante como-es, pero en general, desea optimizar la biblioteca resultante para agregar algunas características de C# para el beneficio de los usuarios. Algunos ejemplos incluyen la implementación de un `ToString()` método, proporcionar los indizadores de C#, agregue las conversiones implícitas a y desde algunos tipos nativos o proporcionan versiones fuertemente tipada de algunos métodos. Estas mejoras se almacenan en archivos adicionales de C#. Simplemente agregue los archivos de C# al proyecto y se incluirá en este proceso de compilación.

Esto muestra cómo podría implementar el código en su `Extra.cs` archivo. Tenga en cuenta que va a usar las clases parciales como estos aumentan las clases parciales que se generan a partir de la combinación de la `ApiDefinition.cs` y `StructsAndEnums.cs` principales de enlace:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

Creación de la biblioteca producirá el enlace nativo.

Para completar este enlace, debe agregar la biblioteca nativa para el proyecto.  Puede hacerlo mediante la adición de la biblioteca nativa para el proyecto, ya sea arrastrando y colocando la biblioteca nativa desde el Finder hasta el proyecto en el Explorador de soluciones, o haciendo clic en el proyecto y elegir **agregar**  >  **Agregar archivos** para seleccionar la biblioteca nativa.
Las bibliotecas nativas por convención, comienzan con la palabra "lib" y terminan con la extensión ".a". Al hacer esto, Visual Studio para Mac agregará dos archivos: el archivo .a y un archivo rellenado de forma automática en C# que contiene información sobre lo que contiene la biblioteca nativa:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Las bibliotecas nativas por convención, comience con la biblioteca de word y terminan con la extensión .a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

El contenido de la `libMagicChord.linkwith.cs` archivo tiene información sobre cómo se puede usar esta biblioteca e indica a su IDE para empaquetar este archivo binario en el archivo DLL resultante:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Información detallada sobre cómo usar el [ `[LinkWith]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) atributo se documentan en el [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md).

Ahora cuando se compila el proyecto terminará con un `MagicChords.dll` archivo que contiene el enlace y la biblioteca nativa. Puede distribuir este proyecto o usar el archivo DLL resultante a otros desarrolladores para sus propios.

A veces es posible que necesite unos valores de enumeración, definiciones de delegados u otros tipos. No coloque la del archivo de definiciones de API, ya que esto es simplemente un contrato

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>El archivo de definición de API

El archivo de definición de API consta de un número de interfaces. Las interfaces en la definición de API se convertirá en una declaración de clase y se deben señalar con el [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) atributo para especificar la clase base para la clase.

Tal vez se pregunte por qué no hemos usado las clases en lugar de interfaces para la definición del contrato. Elegimos interfaces porque nos permitía escribir el contrato para un método sin tener que proporcionar un cuerpo de método en el archivo de definición de API, ni tener que proporcionar un cuerpo que se debía producir una excepción o devuelve un valor significativo.

Pero, puesto que estamos usando la interfaz como un esqueleto para generar una clase tenido que recurrir a la decoración de distintas partes del contrato con atributos para controlar el enlace.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Métodos de enlace

El enlace más sencillo que puede hacer es enlazar un método. Simplemente declare un método en la interfaz con las convenciones de nomenclatura de C# y decore el método con el [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo. El [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo es lo que vincula el nombre de C# con el nombre de Objective-C en el tiempo de ejecución de Xamarin.iOS. El parámetro de la [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo es el nombre del selector de Objective-C. Algunos ejemplos:

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

Los ejemplos anteriores muestran cómo se pueden enlazar métodos de instancia. Para enlazar métodos estáticos, se debe utilizar el [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) atributo similar al siguiente:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Esto es necesario porque el contrato es parte de una interfaz y las interfaces no tienen ninguna noción de las declaraciones de la instancia de puertos estáticos frente a, por lo que es necesario una vez más que recurrir a los atributos. Si desea ocultar un método concreto desde el enlace, puede decorar el método con el [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo.

El `btouch-native` comando presentará comprobaciones para los parámetros de referencia no es null. Si desea permitir valores null para un parámetro concreto, utilice el [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) atributo en el parámetro, similar al siguiente:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Al exportar un tipo de referencia, con el [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) palabra clave también puede especificar la semántica de asignación. Esto es necesario para asegurarse de que no se pierde ningún dato.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Propiedades de enlace

Al igual que los métodos, propiedades de Objective-C se enlazan mediante el [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo y se asignan directamente a las propiedades de C#. Al igual que los métodos, las propiedades se pueden decorar con el [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) y [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributos.

Cuando se usa el [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo en una propiedad en el plano btouch nativo enlaza en realidad dos métodos: el captador y establecedor. El nombre que proporcione exportar es el **basename** y el establecedor se calcula mediante la anteposición de la palabra "set" activación de la primera letra de la **basename** en mayúscula y hacer que el selector de tomar una argumento. Esto significa que `[Export ("label")]` aplicado en una propiedad enlaza en realidad la "etiqueta" y "setLabel:" métodos de Objective-C.

A veces, las propiedades de Objective-C no siguen el patrón descrito anteriormente y el nombre se sobrescribe manualmente. En esos casos puede controlar la forma en que el enlace se genera mediante el uso de la [ `[Bind]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) atributo en el captador o establecedor, por ejemplo:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

A continuación enlaza "isMenuVisible" y "setMenuVisible:". Opcionalmente, se puede enlazar una propiedad utilizando la sintaxis siguiente:

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

Donde se definen explícitamente el captador y establecedor como en el `name` y `setName` enlaces anteriores.

Además de soporte técnico para las propiedades estáticas mediante [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), puede decorar las propiedades de subproceso estático con [ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), por ejemplo:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Al igual que métodos permiten algunos parámetros que se marcará con [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), puede aplicar [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) a una propiedad para indicar que null es un valor válido para la propiedad, por ejemplo:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

El [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) parámetro también se puede especificar directamente en el establecedor:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Advertencias de enlazar controles personalizados

Al configurar el enlace para un control personalizado, se deben considerar las siguientes observaciones:

1. **Propiedades de enlace deben ser estáticas** : al definir el enlace de propiedades, el [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) se debe usar el atributo.
 2. **Los nombres de propiedad deben coincidir exactamente con** -el nombre usado para enlazar la propiedad debe coincidir exactamente con el nombre de la propiedad en el control personalizado.
3. **Tipos de propiedad deben coincidir exactamente** -el tipo de variable que se usa para enlazar la propiedad debe coincidir exactamente con el tipo de la propiedad en el control personalizado.
4. **Puntos de interrupción y el captador o establecedor** : puntos de interrupción se colocan en el captador o nunca se alcanzarán los métodos de establecedor de la propiedad.
5. **Observe las devoluciones de llamada** -deberá utilizar las devoluciones de llamada de observación para recibir notificaciones de cambios en los valores de propiedad de controles personalizados.

En el enlace en tiempo de ejecución en modo silencioso, no pueden dar lugar a errores para observar cualquiera de las advertencias de la lista anteriores.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Las propiedades y el patrón mutable objective-c.

Marcos de Objective-C usan un modismo donde algunas clases son inmutables con una subclase mutable. Por ejemplo `NSString` es la versión inmutable, mientras que `NSMutableString` es la subclase que permite la mutación.

En esas clases es habitual ver la clase base inmutable contienen propiedades con un captador pero ningún establecedor. Y para la versión introducir el establecedor mutable. Puesto que esto no es realmente posible con C#, tuvimos que asignar esta expresión en una locución que funcionaría con C#.

La forma que se asigna a C# consiste en agregar el captador y establecedor de la clase base, pero al marcar el establecedor con un [ `[NotImplemented]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute) atributo.

A continuación, en la subclase mutable, utilice el [ `[Override]` ](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) atributo en la propiedad para asegurarse de que la propiedad realmente es invalidar el comportamiento de los padres.

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

El `btouch-native` herramienta generará automáticamente constructores de cuatros en su clase para una clase determinada `Foo`, genera:

-  `Foo ()`: el constructor predeterminado (se asigna al constructor de "inicialización" de Objective-C)
-  `Foo (NSCoder)`: el constructor usado durante la deserialización de los archivos NIB (se asigna a Objective-C "initWithCoder:" constructor).
-  `Foo (IntPtr handle)`: el constructor para la creación de identificador, se invoca el tiempo de ejecución cuando el tiempo de ejecución debe exponer un objeto administrado desde un objeto no administrado.
-  `Foo (NSEmptyFlag)`: se usa las clases derivadas para evitar la inicialización doble.

Para los constructores que defina, debe declararse con la siguiente firma dentro de la definición de interfaz: debe devolver un `IntPtr` valor y el nombre del método deben ser el Constructor. Por ejemplo, para enlazar el `initWithFrame:` constructor, esto es lo que podría usar:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocolos de enlace

Como se describe en el documento de diseño de API, en la sección [tratan los protocolos y los modelos](~/ios/internals/api-design/index.md#Models), Xamarin.iOS asigna los protocolos de Objective-C en clases que se han marcado con el [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) atributo. Esto normalmente se usa al implementar las clases de delegado de Objective-C.

La gran diferencia entre una clase enlazada normal y una clase de delegado es que la clase de delegado puede tener uno o más métodos opcionales.

Por ejemplo, tenga en cuenta la `UIKit` clase `UIAccelerometerDelegate`, se trata de cómo está enlazado en Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Puesto que este es un método opcional en la definición para `UIAccelerometerDelegate` hay puede hacer nada más. Pero si se produjo un método necesario en el protocolo, debe agregar el [ `[Abstract]` ](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute) al método. Esto obligará al usuario de la implementación para proporcionar un cuerpo del método.

En general, protocolos se utilizan en las clases que responden a los mensajes. Normalmente esto se hace en Objective-C, se asigna a la propiedad "delegate" una instancia de un objeto que responde a los métodos en el protocolo.

La convención en Xamarin.iOS es admitir tanto el Objective-C acoplamiento estilo donde cualquier instancia de un `NSObject` puede asignarse al delegado y a exponer también una versión fuertemente tipada del mismo. Por este motivo, se suele proporcionar ambos un `Delegate` propiedad fuertemente tipado y una `WeakDelegate` flexible que se escribe. Normalmente, enlazamos la versión fuertemente tipado con [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute), y usamos el [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) atributo para proporcionar la versión fuertemente tipada.

Esto muestra cómo se enlaza el `UIAccelerometer` clase:

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

Se ha incorporado a partir de MonoTouch 7.0, un funcionalidad de enlace de protocolo nuevo y mejorado.  Esta nueva compatibilidad, es más fácil usar expresiones de Objective-C para la adopción de uno o más protocolos en una clase determinada.

Para cada definición de protocolo `MyProtocol` en Objective-C, ahora hay un `IMyProtocol` interfaz que muestra todos los métodos necesarios del protocolo, así como una clase de extensión que proporciona todos los métodos opcionales.  Lo anterior, puede combinar con la nueva compatibilidad en el editor permite a los desarrolladores implementar métodos de protocolo sin tener que usar las subclases independientes de las clases de modelo abstracto anterior de Xamarin Studio.

Cualquier definición que contiene el [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) atributo realmente generará tres clases auxiliares que mejoran considerablemente la forma en que consume protocolos:

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

El **implementación de la clase** proporciona una clase abstracta completa que puede invalidar los métodos individuales de y obtenga seguridad de tipos completa.  Sin embargo, debido a C# no admite herencia múltiple, hay escenarios donde es posible que deba tener una clase base diferente, pero desea implementar una interfaz, que es donde el

Generado **definición de interfaz** entra en juego.  Es una interfaz que tiene todos los métodos necesarios del protocolo.  Esto permite que los desarrolladores que desean implementar el protocolo para implementar simplemente la interfaz.  El tiempo de ejecución registrará automáticamente el tipo como adoptar el protocolo.

Tenga en cuenta que la interfaz sólo se enumera los métodos necesarios y exponer los métodos opcionales.  Esto significa que las clases que adopten el protocolo obtendrán completa comprobación de tipos para los métodos necesarios, pero tendrán que recurrir a establecimiento flexible de tipos (manualmente mediante [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributos y que coincide con la firma) para el elemento opcional métodos de protocolo.

Para que sea conveniente usar una API que usa los protocolos, la herramienta de enlace también generará una clase de método de extensiones que expone todos los métodos opcionales.  Esto significa que, siempre que consume una API, podrá tratar protocolos como si tuviera todos los métodos.

Si desea utilizar las definiciones de protocolo de la API, deberá escribir esqueletos interfaces vacías en la definición de API.  Si desea usar el MiProtocolo en una API, deberá hacer esto:

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

El anterior es necesario porque en tiempo de enlace el `IMyProtocol` no existiría, que es ¿por qué tiene que proporcionar una interfaz vacía.

#### <a name="adopting-protocol-generated-interfaces"></a>Adoptar interfaces generadas por el protocolo

Cada vez que implemente una de las interfaces que se generan para los protocolos, similar al siguiente:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

La implementación para los métodos de interfaz obtiene exportan automáticamente con el nombre correcto, por lo que es equivalente a esta:

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

En Objective-C es posible extender clases con nuevos métodos, de forma similares en espíritu a los métodos de extensión de C#. Cuando hay uno de estos métodos, puede usar el [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) atributo para marcar el método como el receptor del mensaje de Objective-C.

Por ejemplo, en Xamarin.iOS, enlazamos los métodos de extensión que se definen en `NSString` cuando `UIKit` se importa como métodos en el `NSStringDrawingExtensions`, similar al siguiente:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Listas de argumentos de Objective-C de enlace

Objective-C admite argumentos variádicos. Por ejemplo:

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Para invocar este método de C# que se desea crear una firma similar al siguiente:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Esto declara el método como interno, ocultando la API anterior de los usuarios, pero exponerlo a la biblioteca. A continuación, puede escribir un método similar al siguiente:

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

### <a name="binding-fields"></a>Campos de enlace

A veces desea tener acceso a los campos públicos que se declaran en una biblioteca.

Normalmente, estos campos contienen valores de cadenas o enteros que se deben hacer referencia. Se suelen usar como cadena que representa una notificación específica y como claves en diccionarios.

Para enlazar un campo, agregue una propiedad al archivo de definición de interfaz y decorar la propiedad con el [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) atributo. Este atributo toma un parámetro: el nombre de C del símbolo para la búsqueda. Por ejemplo:

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Si desea ajustar varios campos en una clase estática que no se deriva de `NSObject`, puede usar el [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) atributo en la clase, similar al siguiente:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

Los pasos anteriores, se generarán un `LonelyClass` que no se deriva de `NSObject` y contendrá un enlace a la `NSSomeEventNotification` 
 `NSString` expone como un `NSString`.

El [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) atributo puede aplicarse a los siguientes tipos de datos:

-  `NSString` referencias (solo propiedades de solo lectura)
-  `NSArray` referencias (solo propiedades de solo lectura)
-  enteros de 32 bits (`System.Int32`)
-  enteros de 64 bits (`System.Int64`)
-  valores de punto flotante de 32 bits (`System.Single`)
-  valores de punto flotante de 64 bits (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

Además del nombre de campo nativo, puede especificar el nombre de la biblioteca donde se encuentra el campo, pasando el nombre de la biblioteca:

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

Si vincula estáticamente, no hay ninguna biblioteca para enlazar, por lo que deberá usar el `__Internal` nombre:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Enumeraciones de enlace

Puede agregar `enum` directamente en el enlace de archivos a resulta más fácil de usar dentro de las definiciones de API - sin utilizar un archivo de origen diferente (que debe compilarse en los enlaces y el proyecto final).

Ejemplo:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

También es posible crear sus propias enumeraciones para reemplazar `NSString` constantes. En este caso el generador le **automáticamente** crear los métodos para convertir los valores de las enumeraciones y constantes NSString para usted.

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

En el ejemplo anterior podría decidir decorar `void Perform (NSString mode);` con un [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo. Esto le permitirá **ocultar** la API basada en la constante de los consumidores de enlace.

Sin embargo esto limitaría el tipo de creación de subclases que utilizan alternativas las API más bonito un [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) atributo. Esos métodos generados no son `virtual`, es decir, no podrá invalidar ellos - puede o no, ser una buena elección.

Una alternativa consiste en marcar el original, `NSString`-según la definición como `[Protected]`. Esto permitirá la creación de subclases funcione, cuando sea necesario, y la versión wrap'ed seguirá trabajar y llame al método invalidado.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Enlace `NSValue`, `NSNumber`, y `NSString` a un tipo mejor

El [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) atributo permite enlazar `NSNumber`, `NSValue` y `NSString`(enumeraciones) en tipos de C# más precisos. El atributo se puede usar para crear mejores y más precisos, API de .NET a través de la API nativa.

Puede decorar métodos (en el valor devuelto), parámetros y propiedades con [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). La única restricción es que el miembro **no debe** estar dentro de un [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) o [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interfaz.

Por ejemplo:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Salida sería:

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

Salida sería:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` es un `NSString` respaldado enum, se capturará el derecho `NSString` valor y controlar la conversión de tipos.

Consulte la [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) documentación para ver los tipos de conversión compatibles.

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notificaciones de enlace

Las notificaciones son mensajes que se publican en el `NSNotificationCenter.DefaultCenter` y sirven como mecanismo para difundir mensajes de una parte de la aplicación a otra. Los desarrolladores se suscriben a las notificaciones normalmente mediante la [NSNotificationCenter](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/)del [AddObserver](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/M/AddObserver/) método. Cuando una aplicación envía un mensaje en el centro de notificaciones, normalmente contiene una carga que se almacenan en el [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) diccionario. Este diccionario tiene establecimiento flexible y obtención de información fuera de ella es propenso a errores, como los usuarios normalmente necesitan leer en la documentación de qué claves están disponibles en el diccionario y los tipos de los valores que se pueden almacenar en el diccionario. La presencia de las claves a veces se utiliza como también un valor booleano.

El generador de enlace de Xamarin.iOS proporciona compatibilidad para los desarrolladores enlazar las notificaciones. Para ello, Establece la [ `[Notification]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute) atributo en una propiedad que se han también se ha etiquetado con un [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) propiedad (puede ser público o privado).

Este atributo se puede usar sin argumentos para las notificaciones que llevar a cabo ninguna carga, o puede especificar un `System.Type` que hace referencia a otra interfaz en la definición de API, normalmente con el nombre termina con "EventArgs". El generador convertirá la interfaz en una clase que cree subclases `EventArgs` e incluirá todas las propiedades en la lista. El [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo debería usarse en la clase EventArgs para mostrar el nombre de la clave utilizada para buscar el diccionario de Objective-C para capturar el valor.

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

Los usuarios de su código, a continuación, pueden suscribirse fácilmente a las notificaciones que se registran en el [NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/) mediante código similar al siguiente:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

El valor devuelto de `ObserveDidStart` puede usarse fácilmente dejar de recibir notificaciones, similar al siguiente:

```csharp
token.Dispose ();
```

O bien puede llamar [NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject/) y pasar el token. Si la notificación contiene parámetros, debe especificar una aplicación auxiliar `EventArgs` interfaz como esta:

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

Lo anterior, se generará un `MyScreenChangedEventArgs` clase con el `ScreenX` y `ScreenY` propiedades que se capturará los datos desde el [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) diccionario utilizando las claves "ScreenXKey" y "ScreenYKey" respectivamente y se aplican las conversiones adecuadas. El `[ProbePresence]` atributo se utiliza para el generador para sondear si la clave se establece el `UserInfo`, en lugar de intentar extraer el valor. Esto se usa para los casos donde la presencia de la clave es el valor (normalmente por los valores booleanos).

Esto le permite escribir código como este:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Categorías de enlace

Categorías son un mecanismo de Objective-C que se usa para extender el conjunto de métodos y propiedades disponibles en una clase.   En la práctica, se usan para extender la funcionalidad de una clase base (por ejemplo `NSObject`) cuando está vinculado a un marco concreto (por ejemplo `UIKit`), hacer que sus métodos disponibles, pero solo si está vinculada a la nueva plataforma.   En otros casos, se utilizan para organizar las características en una clase mediante la funcionalidad.   Son similares en espíritu a métodos de extensión de C#. Se trata de una categoría de aspecto en Objective C:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

El ejemplo anterior si se encuentra en una biblioteca se extendería instancias de `UIView` con el método `makeBackgroundRed`.

Para enlazarlas, puede usar el [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) atributo en una definición de interfaz.  Cuando se usa el [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) atributo, el significado de la [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) atributo cambia desde que se usa para especificar la clase base para extender, para que sea el tipo para ampliar.

El siguiente se muestra cómo el `UIView` extensiones son enlazadas y se convierten en métodos de extensión de C#:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Lo anterior creará un `MyUIViewExtension` una clase que contiene el `MakeBackgroundRed` método de extensión.  Esto significa que ahora puede llamar a "MakeBackgroundRed" en cualquier `UIView` subclase, lo que le ofrece la misma funcionalidad que se obtendrá en Objective-C. En otros casos, se usan las categorías no extender una clase del sistema, sino para organizar funciones puramente para fines de decoración.  Así:

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Aunque puede usar el [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) atributo también para este estilo de decoración de las declaraciones, es posible que también solo tiene que agregarlos todos a la definición de clase.  Ambas opciones conseguiría el mismo:

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

Los bloques son una estructura nueva presentada por Apple para poner el equivalente funcional de los métodos anónimos de C# a Objective-C. Por ejemplo, el `NSSet` clase expone ahora este método:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

La descripción anterior declara un método llamado `enumerateObjectsUsingBlock:` que toma un argumento con nombre `block`. Este bloque es similar a un método anónimo de C# porque tiene compatibilidad para capturar el entorno actual (el puntero "this", el acceso a variables locales y parámetros). El método anterior en `NSSet` invoca el bloque con dos parámetros de un `NSObject` (el `id obj` parte) y un puntero a un valor booleano (el `BOOL *stop`) parte.

Para enlazar este tipo de API con btouch, debe declarar primero la firma del tipo de bloque como C# delegar y, a continuación, haga referencia a él desde un punto de entrada de API, similar al siguiente:

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

También puede usar las expresiones lambda si lo prefiere:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>Métodos asincrónicos

El generador de enlace puede convertir una clase determinada de métodos en los métodos de asincronía (los métodos que devuelven Task o Task&lt;T&gt;).

Puede usar el [ `[Async]` ](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) atributo en los métodos que devuelven void y cuyo último argumento es una devolución de llamada.  Cuando esto se aplica a un método, el generador de enlace generará una versión de ese método con el sufijo `Async`.  Si la devolución de llamada no toma ningún parámetro, el valor devuelto será una `Task`, si la devolución de llamada toma un parámetro, el resultado será un `Task<T>`.  Si la devolución de llamada toma varios parámetros, debe establecer el `ResultType` o `ResultTypeName` para especificar el nombre del tipo generado que contendrá todas las propiedades deseado.

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

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Exponer tipos seguros para los parámetros de NSDictionary débiles

En muchos lugares de la API de Objective-C, los parámetros se pasan débilmente tipada `NSDictionary` API con claves específicas y los valores, pero estos son propensas a errores (puede pasar las claves no válidas y no obtener ninguna advertencia; puede pasar valores no válidos y no obtener advertencias) y frustrante Para utilizar como necesiten varios viajes a la documentación para buscar los posibles nombres de claves y valores.

La solución es proporcionar una versión fuertemente tipada que proporciona que la versión fuertemente tipada de la API y en segundo plano asigna las diferentes subyacente claves y valores.

Así, por ejemplo, si la API de Objective-C acepta un `NSDictionary` y se documenta que toma la clave `XyzVolumeKey` que adopta un `NSNumber` con un valor de volumen entre 0.0 y 1.0 y una `XyzCaptionKey` que toma una cadena, ¿desea que los usuarios tengan una buena API es similar a esto:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

El `Volume` propiedad se define como float que acepta valores NULL, como la convención en Objective-C no requiere estos diccionarios tiene el valor, por lo que hay escenarios donde no se puede establecer el valor.

Para ello, deberá hacer algunas cosas:

* Crear una clase fuertemente tipada que cree subclases [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) y proporciona los captadores y establecedores distintos para cada propiedad.
* Declarar las sobrecargas de los métodos que toman `NSDictionary` para aprovechar la nueva versión fuertemente tipada.

Puede crear la clase fuertemente tipada manualmente o usar el generador para hacer el trabajo por usted.  En primer lugar, exploramos cómo hacer esto manualmente para que sepan qué está sucediendo y, a continuación, el enfoque automático.

Deberá crear un archivo auxiliar para esto, no entra en su contrato de API.  Esto es lo tendría que escribir para crear la clase XyzOptions:

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

Si no tiene que sobrescribir la API, puede ocultar con seguridad la API basada en NSDictionary utilizando el [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo.

Como puede ver, usamos el [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) de atributo para un nuevo punto de entrada de API de superficie y emergemos mediante nuestro fuertemente tipado `XyzOptions` clase.  El método contenedor también permite valores null que se va a pasar.

Ahora, algo que no se mencionado es dónde la `XyzOptionsKeys` proceden de los valores.  Normalmente, se pueden agrupar las claves que expone una API en una clase estática como `XyzOptionsKeys`, similar al siguiente:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Examinemos la compatibilidad automática para crear estos diccionarios fuertemente tipado.  Esto evita una gran cantidad del código reutilizable, y puede definir el diccionario directamente en su contrato de API, en lugar de usar un archivo externo.

Para crear un diccionario fuertemente tipado, introducir una interfaz en la API y decorarla con el [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) atributo.  Esto indica que el generador que debe crear una clase con el mismo nombre que la interfaz que se derivará de `DictionaryContainer` y le proporcionará los descriptores de acceso con tipo seguros para él.

El [ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) atributo toma un parámetro, que es el nombre de la clase estática que contiene las claves del diccionario.  A continuación, cada propiedad de la interfaz se convertirá en un descriptor de acceso fuertemente tipado.  De forma predeterminada, el código usará el nombre de la propiedad con el sufijo "Key" en la clase estática para crear el descriptor de acceso.

Esto significa que crear el descriptor de acceso fuertemente tipado ya no requiere un archivo externo, ni tener que crear manualmente los captadores y establecedores para cada propiedad, ni tener que buscar las claves manualmente usted mismo.

Esto es lo que tendría el aspecto de su enlace completo:

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

En caso de que debe hacer referencia en su `XyzOption` miembros un campo diferente (es decir no el nombre de la propiedad con el sufijo `Key`), puede decorar la propiedad con un [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo con el nombre que desea usar.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Asignaciones de tipos

Esta sección describen cómo se asignan los tipos de Objective-C a tipos de C#.

<a name="Simple_Types" />

### <a name="simple-types"></a>Tipos simples

En la tabla siguiente se muestra cómo debe asignar los tipos del mundo CocoaTouch y Objective-C para el mundo de Xamarin.iOS:

|Nombre del tipo de Objective-c.|Tipo de Unified API de Xamarin.iOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([más en enlace NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (Vea también: [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Tipos de CoreFoundation (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|Tipos base (`NS*`)|`Foundation.NS*`|
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

El tiempo de ejecución de Xamarin.iOS se encarga automáticamente de la conversión de matrices de C# para `NSArrays` y realizar la conversión, por ejemplo el método Objective-C imaginaria que devuelve un `NSArray` de `UIViews`:

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

La idea es usar una matriz C# fuertemente tipado que esto permitirá que el IDE para proporcionar la finalización correcta de código con el tipo real sin obligar al usuario que adivinar o buscar la documentación para averiguar el tipo real de los objetos contenidos en la matriz.

En casos donde no puede seguir hacia abajo el tipo más derivado real contenido en la matriz, puede usar `NSObject []` como el valor devuelto.

<a name="Selectors" />

### <a name="selectors"></a>Selectores

Los selectores de aparecen en la API de Objective-C como el tipo especial `SEL`. Al enlazar un selector, podría asignar el tipo a `ObjCRuntime.Selector`.  Normalmente, los selectores se exponen en una API con un objeto, el objeto de destino y un selector para invocar el objeto de destino. Proporcionar ambos básicamente se corresponde con el delegado de C#: algo que encapsula el método que se invoca junto con el objeto para invocar el método.

Este es el aspecto que tiene el enlace:

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

Y esto es cómo el método normalmente se usaría en una aplicación:

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

Para facilitar el enlace mejor a los desarrolladores de C#, normalmente proporcionará un método que toma un `NSAction` parámetro, que permite a los delegados de C# y las expresiones lambda que se utilizará en lugar de la `Target+Selector`. Para ello, normalmente ocultaría la `SetTarget` método marcando con un [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo y, a continuación, podría exponer un nuevo método auxiliar, similar al siguiente:

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

Así que ahora se puede escribir el código de usuario similar al siguiente:

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

Al enlazar un método que toma un `NSString`, puede reemplazar que con un tipo de cadena de C#, tanto en tipos de devolución y parámetros.

El único caso cuando desee usar un `NSString` directamente es cuando se utiliza la cadena como un token. Para obtener más información acerca de las cadenas y `NSString`, lea el [diseño de API en NSString](~/ios/internals/api-design/nsstring.md) documento.

En algunos casos excepcionales, una API podría exponer una cadena de tipo C (`char *`) en lugar de una cadena de Objective-C (`NSString *`). En esos casos, puede anotar el parámetro con el [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring) atributo.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out / parámetros ref

Algunas API devolver valores de sus parámetros, o pasa parámetros por referencia.

Normalmente, la firma tiene este aspecto:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

El primer ejemplo muestra una forma habitual de Objective-C para devolver los códigos de error, un puntero a un `NSError` se pasa el puntero y, cuando se devuelve el valor está establecido.   El segundo método muestra cómo un método de Objective-C podría tomar un objeto y modificar su contenido.   Esto sería una pasada por referencia, en lugar de un valor de salida sin cambios.

El enlace tendría este aspecto:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Atributos de la administración de memoria

Cuando se usa el [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo y se pasa los datos que se conservarán el método llamado, puede especificar la semántica de argumento si se pasa como segundo parámetro, por ejemplo:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

Lo anterior sería marcar el valor como si tuviera la semántica "Conservar". La semántica disponible es:

-  Asignar
-  Copiar
-  Conservar

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Directrices de estilo

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>Uso de [interno]

Puede usar el [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo para ocultar un método de la API pública. Es posible que desee hacer esto en casos donde la API expuesta es demasiado bajo nivel y desea proporcionar una implementación de alto nivel en un archivo independiente según este método.

También puede utilizar cuando surgen las limitaciones en el generador de enlace, por ejemplo, algunos escenarios avanzados podrían exponer tipos que no están enlazados y que desea enlazar en su propia manera y va a incluir esos tipos en su propia manera.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Las devoluciones de llamada y controladores de eventos

Las clases de Objective-C normalmente difusión notificaciones o solicitar información mediante el envío de un mensaje en una clase de delegado (delegado Objective-C).

Este modelo, mientras totalmente compatible y obtenidas por Xamarin.iOS a veces puede resultar complicado. Xamarin.iOS expone el modelo de evento de C# y un sistema de devolución de llamada de método en la clase que se puede usar en estas situaciones. Esto permite que el código similar al siguiente para ejecutar:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

El generador de enlace es capaz de reducir la cantidad de escritura necesarios para asignar el patrón de Objective-C en el patrón de C#.

Comenzando con Xamarin.iOS 1.4 será posible indicar también el generador para generar enlaces para un específico delegados de Objective-C y exponer al delegado como eventos de C# y las propiedades en el tipo de host.

Hay dos clases implicadas en este proceso, la clase de host, lo que es lo que emite eventos actualmente y los envía en el `Delegate` o `WeakDelegate` y la clase de delegado real.

Teniendo en cuenta la siguiente configuración:

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
   el tipo que actúa como su delegado y el nombre de C# que se expuso la declaración. En nuestro ejemplo anterior son `typeof (MyClassDelegate)` y `WeakDelegate` respectivamente.
-  En la clase de delegado, en cada método que tiene más de dos parámetros, deberá especificar el tipo que desea usar para la clase EventArgs generada automáticamente.

El generador de enlace no se limita a ajuste solo el destino de un único evento, es posible que algunas clases de Objective-C para emitir mensajes a más de un delegación, por lo que tendrá que proporcionan matrices para admitir esta configuración. La mayoría de las instalaciones no será necesario, pero el generador está preparado para admitir esos casos.

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

El `EventArgs` se usa para especificar el nombre de la `EventArgs` clase va a generar. Debe usar uno por cada firma (en este ejemplo, el `EventArgs` contendrá un `With` propiedad de tipo nint).

Con las definiciones anteriores, el generador creará el siguiente evento en el MyClass generado:

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

Así que ahora puede usar el código similar al siguiente:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

Las devoluciones de llamada son igual que las invocaciones de eventos, la diferencia es que, en lugar de tener varios suscriptores posibles (por ejemplo, pueden colocar varios métodos en un `Clicked` eventos o un `DownloadFinished` eventos) las devoluciones de llamada solo pueden tener un solo suscriptor.

El proceso es idéntico, la única diferencia es que, en lugar de exponer el nombre de la `EventArgs` clase que se van a generar el EventArgs realmente se utiliza para denominar el nombre del delegado de C# resultante.

Si el método en la clase de delegado devuelve un valor, el generador de enlace asignará a un método delegado en la clase principal en lugar de un evento. En estos casos, deberá proporcionar el valor predeterminado que se debe devolver el método si el usuario no enlazar al delegado. Esto se hace mediante la [ `[DefaultValue]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) o [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) atributos.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) a codificar un valor devuelto, mientras que [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) se usa para especificar el argumento de entrada que se devolverá.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Enumeraciones y tipos base

También puede hacer referencia enumeraciones o tipos base que no son compatibles directamente con el sistema de definición de interfaz btouch. Para ello, coloque su enumeraciones y tipos principales en un archivo independiente e incluirlo como parte de uno de los archivos adicionales que se proporcionan a btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>Vinculación de las dependencias

Si va a enlazar la API que no forman parte de la aplicación, deberá asegurarse de que el archivo ejecutable está vinculado con estas bibliotecas.

Deberá informar a Xamarin.iOS cómo vincular las bibliotecas, esto se puede hacer modificando la configuración de compilación para invocar el `mtouch` comando con algunos extra para compilar los argumentos que especifiquen cómo vincular con las nuevas bibliotecas mediante la "-gcc_flags" opción, seguido de una cadena entre comillas que contiene todas las bibliotecas adicionales que son necesarias para el programa, similar al siguiente:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

El ejemplo anterior se vinculará `libMyLibrary.a`, `libSystemLibrary.dylib` y `CFNetwork` biblioteca del marco de trabajo en el archivo ejecutable final.

O puede aprovechar el nivel de ensamblado [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), que se puede incrustar en los archivos de contrato (como `AssemblyInfo.cs`).
Cuando se usa el [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), deberá tener la biblioteca nativa disponible en el momento de realizar el enlace, tal y como se insertará la biblioteca nativa con la aplicación. Por ejemplo:

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Tal vez se pregunte, ¿por qué necesita `-force_load` comando y el motivo es que ObjC - marca aunque se compila el código, no conserva los metadatos necesarios para admitir las categorías (la eliminación de código no alcanzado del vinculador o compilador elimina) que se necesita en tiempo de ejecución para Xamarin.iOS.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Referencias asistidas

Algunos objetos transitorios, como hojas de acción y los cuadros de alerta son complicados para realizar un seguimiento de para los desarrolladores y el generador de enlace puede ayudar a un poco aquí.

Por ejemplo, si tenía una clase que se ha mostrado un mensaje y, a continuación, genera un `Done` eventos, la manera tradicional de controlar esto sería:

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

En el escenario anterior, el desarrollador debe mantener la referencia al objeto a sí mismo y cualquier pérdida o borrar activamente la referencia para su propio cuadro.  Aunque el código de enlace, el generador es compatible con seguimiento de la referencia para usted y claro que cuando se invoca un método especial, el código anterior se convierte entonces en:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Tenga en cuenta que ya no es necesario mantener la variable en una instancia, que funciona con una variable local y que no es necesario borrar la referencia cuando se bloquea el objeto.

Para aprovechar las ventajas de esto, la clase debe tener un conjunto de propiedades de eventos el [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) declaración y también el `KeepUntilRef` variable se establece en el nombre del método que se invoca cuando el objeto ha finalizado su trabajo, como Esto:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Heredar de protocolos

A partir de Xamarin.iOS v3.2, se admite la herencia de los protocolos que se han marcado con el [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) propiedad. Esto es útil en determinados patrones de API, como en `MapKit` donde el `MKOverlay` protocolo, hereda la `MKAnnotation` de protocolo y adoptada por una serie de clases que heredan de `NSObject`.

Históricamente hemos necesitado copiando el protocolo en cada implementación, pero en estos casos ahora podemos tenemos el `MKShape` clase hereda de la `MKOverlay` protocolo y generarán automáticamente todos los métodos necesarios.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de enlace](https://developer.xamarin.com/samples/BindingSample/)
- [Curso de Xamarin University: Creación de una biblioteca de enlaces de Objective-c.](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Curso de Xamarin University: Compilar una biblioteca de enlaces de Objective-C con Sharpie objetivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
