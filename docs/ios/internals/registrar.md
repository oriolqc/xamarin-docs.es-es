---
title: Tipo de registrador para Xamarin.iOS
description: Este documento describe el registrador de tipo de Xamarin.iOS, lo que hace que C# las clases disponibles para el tiempo de ejecución de C de objetivo.
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 8/29/2018
ms.openlocfilehash: cdd57095b03c24472abec5646ee3a70350770d7c
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2018
ms.locfileid: "34786179"
---
# <a name="type-registrar-for-xamarinios"></a>Registrador de nombres de tipo para Xamarin.iOS

Este documento describe el sistema de registro de tipo utilizado por Xamarin.iOS.

## <a name="registration-of-managed-classes-and-methods"></a>Registro de métodos y clases administradas

Durante el inicio, se registrará de Xamarin.iOS:

- Las clases con un [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo como clases de C de objetivo.
- Las clases con un [[categoría]](https://developer.xamarin.com/api/type/CRuntime.CategoryAttribute) atributo como Objective-C categorías.
- Interactúa con un [[Protocolo]](https://developer.xamarin.com/api/type/Foundation.ProtocolAttribute/) atributo como Objective-C protocolos.
- Los miembros con un [[Exportar]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/), lo que permite para Objective-C acceder a ellos.

Por ejemplo, considere la posibilidad de los recursos administrados `Main` método comunes en aplicaciones de Xamarin.iOS:

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

Este código indica el tiempo de ejecución Objective-C para usar el tipo denominado `AppDelegate` como clase de delegado de la aplicación. Para el tiempo de ejecución Objective-C poder crear una instancia de la C# `AppDelegate` clase, que se debe registrar la clase.

Xamarin.iOS realiza automáticamente, el registro en tiempo de ejecución (registro dinámico) o en tiempo de compilación (registro estático).

Registro dinámico usa la reflexión en el inicio para encontrar todas las clases y métodos para registrar, pasarlos al tiempo de ejecución Objective-C. Registro dinámico se utiliza de forma predeterminada para las compilaciones del simulador.

En tiempo de compilación, registro estático inspecciona los ensamblados utilizados por la aplicación. Determina las clases y métodos para registrar con Objective-C y genera un mapa que se incrusta en el archivo binario.
A continuación, en el inicio, registra el mapa con el tiempo de ejecución Objective-C. Registro estático se usa para las compilaciones de dispositivo.

### <a name="categories"></a>Categorías

Xamarin.iOS 8.10, es posible crear categorías de Objective-C mediante C# sintaxis.

Para crear una categoría, use el `[Category]` atributo y especificar el tipo para ampliar. Por ejemplo, el código siguiente amplía `NSString`:

```csharp
[Category (typeof (NSString))]
```

Cada uno de los métodos de una categoría tiene un `[Export]` atributo, lo que permite el tiempo de ejecución Objective-C:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Todos los métodos de extensión administrados deben ser estáticos, pero es posible crear métodos de instancia de Objective-C con el estándar C# sintaxis de métodos de extensión:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

El primer argumento al método de extensión es la instancia en el que se invocó el método:

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
 }
 ```

Este ejemplo agrega nativo `toUpper` la instancia de método para el `NSString` clase. Este método se puede llamar desde Objective C:

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

### <a name="protocols"></a>Protocolos

A partir de Xamarin.iOS 8.10, interactúa con el `[Protocol]` atributos se exportarán y Objective-C como protocolos:

```csharp
[Protocol ("MyProtocol")]
interface IMyProtocol
{
    [Export ("method")]
    void Method ();
}

class MyClass : IMyProtocol
{
    void Method ()
    {
    }
}
```

Este código exporta `IMyProtocol` y Objective-C como un protocolo llamado `MyProtocol` y una clase denominada `MyClass` que implementa el protocolo.

## <a name="new-registration-system"></a>Nuevo sistema de registro

A partir de la 6.2.6 estable versión y la versión beta 6.3.4, hemos agregado un nuevo registrador estático. En el 7.2.1 versión, hemos realizado el registrador de nuevo el valor predeterminado.

Este nuevo sistema de registro ofrece las siguientes características nuevas:

- Tiempo de compilación la detección de errores de programador:
    - Dos clases que se está registradas con el mismo nombre.
    - Exporta más de un método para responder al mismo selector
- Eliminación de código nativo sin usar:
    - El nuevo sistema de registro agregará las referencias fuertes a código que se usa en las bibliotecas estáticas, lo que permite al enlazador nativo a eliminar sin usar código nativo desde el archivo binario resultante. En los enlaces de ejemplo de Xamarin, mayoría de las aplicaciones se convierten en al menos 300 KB más pequeños.

- Compatibilidad con subclases genéricas de `NSObject`; vea [genéricos de NSObject](~/ios/internals/api-design/nsobject-generics.md) para obtener más información. Además, el nuevo sistema de registro mantendrá construcciones genéricas no admitidas que previamente habría causado un comportamiento aleatorio en tiempo de ejecución.

### <a name="errors-caught-by-the-new-registrar"></a>Errores detectados por el registrador de nuevo

A continuación se muestran algunos ejemplos de los errores capturados por el registrador de nuevo.

- Exportando el mismo selector de más de una vez en la misma clase:

    ```csharp
    [Register]
    class MyDemo : NSObject 
    {
        [Export ("foo:")]
        void Foo (NSString str);
        [Export ("foo:")]
        void Foo (string str)
    }
    ```

- Exportación de más de una clase administrada con el mismo nombre de Objective-C:

    ```csharp
    [Register ("Class")]
    class MyClass : NSObject {}

    [Register ("Class")]
    class YourClass : NSObject {}
    ```

- Exportación de los métodos genéricos:

    ```csharp
    [Register]
    class MyDemo : NSObject
    {
        [Export ("foo")]
        void Foo<T> () {}
    }
    ```

### <a name="limitations-of-the-new-registrar"></a>Limitaciones de la entidad de registro

Algunos aspectos a tener en cuenta sobre el nuevo registrador:

- Algunas bibliotecas de terceros deben actualizarse para que funcione con el nuevo sistema de registro. Consulte [necesarias modificaciones](#required_modifications) a continuación para obtener más detalles.

- La desventaja a corto plazo también es que se debe usar Clang si se usa el marco de trabajo de las cuentas (Esto es porque Apple **accounts.h** encabezado solo se puede compilar si Clang). Agregar `--compiler:clang` a los argumentos mtouch adicionales para usar Clang si usa Xcode 4.6 o una versión anterior (Xamarin.iOS seleccionará automáticamente Clang en Xcode 5.0 o versiones posteriores.)

- Si Xcode 4.6 (o anterior) es usar GCC / G ++ debe seleccionarse si exporta el tipo de los nombres contienen caracteres no ASCII (Esto es porque la versión de Clang enviado con Xcode 4.6 no admite caracteres no ASCII dentro de los identificadores en el código de Objective-C). Agregar `--compiler:gcc` a los argumentos mtouch adicionales para usar GCC.

## <a name="selecting-a-registrar"></a>Seleccionar un registrador

Puede seleccionar un registrador diferente mediante la adición de una de las siguientes opciones para los argumentos mtouch adicionales en el proyecto **compilación de iOS** configuración:

- `--registrar:static` : de manera predeterminada para compilaciones de dispositivo
- `--registrar:dynamic` : de manera predeterminada para las compilaciones del simulador

> [!NOTE]
> API clásica de Xamarin admite otras opciones, como `--registrar:legacystatic` y `--registrar:legacydynamic`. Sin embargo, no se admiten estas opciones mediante la API unificada.

## <a name="shortcomings-in-the-old-registration-system"></a>Deficiencias en el sistema de registro antiguos

El sistema de registro antiguo tiene las siguientes desventajas:

- No hubo ninguna referencia estática (nativa) para Objective-C clases y métodos de bibliotecas nativas de terceros, lo que significaba que no pudimos pedimos al enlazador nativo para quitar el código nativo de otros fabricantes que no se usa realmente (ya que todo se quitaría). Esta es la razón para la `-force_load libNative.a` que todos los enlaces de terceros que tuve que hacer (o su equivalente `ForceLoad=true` en el `[LinkWith]` atributo).
- Se podrían exportar dos tipos administrados con el mismo nombre de Objective-C sin ninguna advertencia. Un escenario poco frecuente era terminará con dos `AppDelegate` clases en diferentes espacios de nombres. En tiempo de ejecución es completamente aleatorio que tomó (de hecho, que varía entre las ejecuciones de una aplicación que aún no se ha recompilado - que se realizan para una experiencia de depuración muy frustrante y desconcierta).
- Se podrían exportar dos métodos que tienen la misma firma de Objective-C. Nuevo aún cuál se llamaría desde Objective-C era aleatorio (pero este problema no era tan común como lo anterior, principalmente porque era la única manera de experimentar realmente este error invalidar el método administrado desafortunado).
- El conjunto de métodos que se exportó era ligeramente diferente entre compilaciones dinámicas y estáticas.
- No funcione correctamente cuando se exportan las clases genéricas (qué implementación genérica exacto que se ejecuta en tiempo de ejecución sería aleatoria, resultante de forma eficaz un comportamiento indeterminado).

## <a name="new-registrar-required-changes-to-bindings"></a>Nuevo registrador: los cambios requeridos para los enlaces

Esta sección describen los cambios de los enlaces que se deben realizar para poder trabajar con el registrador de nuevo.

### <a name="protocols-must-have-the-protocol-attribute"></a>Protocolos deben tener el atributo [Protocolo]

Protocolos ahora deben tener el `[Protocol]` atributo. Si no lo hace, tendrá que, como un error del vinculador nativo:

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>Los selectores de deben tener un número válido de parámetros

Todos los selectores deben indicar el número de parámetros de correctamente. Anteriormente, estos errores se omitieron y podrían causar problemas en tiempo de ejecución.

En resumen, el número de puntos y debe coincidir con el número de parámetros:

- Sin parámetros: `foo`
- Un parámetro: `foo:`
- Dos parámetros: `foo:parameterName2:`

Estos son los usos incorrectos:

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>Utilice el parámetro IsVariadic en exportación

Deben usar las funciones Variádicas el `IsVariadic` argumento para el `[Export]` atributo:

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>Debe vincular a símbolos existentes

Es imposible enlazar las clases que no existen en la biblioteca nativa.
Si se ha quitado o cambiado el nombre de la biblioteca nativa de una clase, asegúrese de actualizar los enlaces para que coincida con.
