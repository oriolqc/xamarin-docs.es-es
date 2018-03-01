---
title: Registrador de tipo
ms.topic: article
ms.prod: xamarin
ms.assetid: 4C1669A4-C12B-9C49-4A39-9046576D10DC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 32d29456b9880914f728b9e24fc0bfadb2f5efde
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="type-registrar"></a>Registrador de tipo

Este documento describe el sistema de registro de tipo utilizado por Xamarin.iOS.

## <a name="registration-of-managed-classes-and-methods"></a>Registro de las clases administradas y métodos

Durante el inicio, se registrará Xamarin.iOS:

  - Las clases con un [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo como clases de C de objetivo.
  - Las clases con un [[categoría]](https://developer.xamarin.com/api/type/CRuntime.CategoryAttribute) atributo como Objective-C categorías.
  - Se comunica con un [[Protocol]](https://developer.xamarin.com/api/type/Foundation.ProtocolAttribute/) atributo como Objective-C protocolos.

y en cada miembros de los casos con una [[Exportar]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) atributo se exportan a objetivo-C. Esto permite que las clases administradas que crea y administra los métodos que se llame desde Objective-C y es la forma en que los métodos y propiedades están vinculadas entre el mundo de C# y el objetivo-C uno.

Un ejemplo muy sencillo es la clase de AppDelegate que tiene todas las aplicaciones. Recordará que el método Main administrado tiene una línea como ésta:

    UIApplication.Main (args, null, "AppDelegate");

Esto indica el tiempo de ejecución de C de objetivo para crear el tipo denominado "AppDelegate" como la clase de delegado de la aplicación.  Para que Objective-C en tiempo de ejecución sabe cómo crear una instancia de la clase "AppDelegate" escrita en C#, esta clase debe estar registrado.

En tiempo de ejecución de Xamarin.iOS se encargará del registro para que, internamente, este registro se puede realizar por completo en tiempo de ejecución (el registro dinámico) o se puede realizar en tiempo de compilación (registro estático).  El enfoque dinámico implica el uso de reflexión en el inicio para buscar todas las clases y métodos para registrar y pasar los empleados para el tiempo de ejecución de C de objetivo.  El método estático inspecciona los ensamblados utilizados por la aplicación en tiempo de compilación.  Determina las clases y métodos para registrar con Objective-C y genera un mapa que se incrusta en el archivo binario.  A continuación, en el inicio, registramos el mapa con el tiempo de ejecución de C de objetivo.

### <a name="categories"></a>Categorías

A partir de Xamarin.iOS 8.10, será posible crear categorías Objective-C mediante la sintaxis de C#.

Esto se realiza mediante el atributo [categoría], especificar el tipo de extensión como un argumento para el atributo.
Por ejemplo, en el ejemplo siguiente se extenderá NSString:

    [Category (typeof (NSString))]

Cada método de categoría es mediante el mecanismo normal para exportar los métodos a Objective-C con el atributo [Exportar]:

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

Todos los métodos de extensión administrados deben ser estáticos, pero es posible crear métodos de instancia Objective-C mediante la sintaxis estándar de métodos de extensión en C#:

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

y el primer argumento para el método de extensión será la instancia en la que se invocó el método.

Ejemplo completo:

    [Category (typeof (NSString))]
    public static class MyStringCategory
    {
        [Export ("toUpper")]
        static string ToUpper (this NSString self)
        {
            return self.ToString ().ToUpper ();
        }
    }

En este ejemplo agregará un método de instancia nativo toUpper a la clase NSString, que se puede invocar desde el objetivo de C.

    [Category (typeof (UIViewController))]
    public static class MyViewControllerCategory
    {
        [Export ("shouldAutoRotate")]
        static bool GlobalRotate ()
        {
            return true;
        }
    }

### <a name="protocols"></a>Protocolos

A partir de Xamarin.iOS 8.10 interfaces con el atributo [Protocol] se exportará a Objective-C como protocolos.

Ejemplo:

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

Esto se exportarán a Objective-C como un protocolo (MiProtocolo) y una clase (MyClass) que implementa el protocolo.

 **Registro dinámico**

se utiliza para las compilaciones del simulador, ya que acelera el ciclo de compilación/debug.  Esto es el resultado de la eliminación de los pasos que genera la asignación de clase y la compilación de esta tabla de asignación en el iniciador de la aplicación cada vez que inicia la aplicación y en su lugar un selector de propósito general se usa cada vez.  El equipo de escritorio tiene una gran cantidad de energía para realizar el tiempo de ejecución el examen de las clases rápidamente, por lo que el rendimiento no es un problema.

 **Registro estático**

está diseñado para las compilaciones de dispositivo como dispositivos móviles son más lentos que los equipos de escritorio y en tiempo de ejecución de realizar análisis es lenta.  Puesto que genera el dispositivo siempre será necesario crear un nuevo valor binario, el ciclo de compilación/debug no se ve afectado por la creación de la asignación de registro.

## <a name="new-registration-system"></a>Nuevo sistema de registro

A partir de la 6.2.6 estable versión y la versión beta 6.3.4 hemos agregado un nuevo registrador estático. En el 7.2.1 versión hemos realizado el registrador de nuevo el valor predeterminado.

Este nuevo sistema de registro ofrece las siguientes características nuevas:

- Compile la detección en tiempo de errores de programador:
    - Dos clases que se está registradas con el mismo nombre.
    - Más de un método que se exporta a responder en el mismo selector



- Puede quitar el código nativo no utilizado
    - El nuevo sistema de registro agregará referencias fuertes al código que se emplea en las bibliotecas estáticas, lo que permite al vinculador nativo que quiten las código nativo no utilizado del archivo binario resultante.
      En los enlaces de ejemplo de Xamarin, mayoría de las aplicaciones se convierten en 300k más pequeños.

- Compatibilidad con genéricas subclases de NSObject. Vea [NSObject genéricos](~/ios/internals/api-design/nsobject-generics.md) para obtener más información. Además, el nuevo sistema de registro detectará construcciones no admitidas de genéricas que previamente habría causado un comportamiento aleatorio en tiempo de ejecución.

Estos son algunos ejemplos de los errores capturados por el nuevo registar:

Exportando el mismo selector de más de una vez en la misma clase.

```csharp
[Register]
class MyDemo : NSObject {
    [Export ("foo:")]
    void Foo (NSString str);
    [Export ("foo:")]
    void Foo (string str)
}
```

Exportación de más de una clase administrada con el mismo nombre de C de objetivo.

```csharp
[Register ("Class")]
class MyClass : NSObject {}

[Register ("Class")]
class YourClass : NSObject {}
```

Exportar métodos genéricos.

```csharp
[Register]
class MyDemo : NSObject {
    [Export ("foo")]
    void Foo<T> () {}
}
```



Algunos aspectos que tener en cuenta sobre el registrador de nuevo:
- Algunos terceros bibliotecas deben actualizarse para que funcione con el nuevo sistema de registro, vea la sección [requiere modificaciones siguientes](#required_modifications) para obtener más detalles.
- Un inconveniente a corto plazo también es que Clang debe usarse si se utiliza el marco de trabajo de cuentas (Esto es porque solo se puede compilar el encabezado de accounts.h de Apple si Clang). Agregar <code>--compiler:clang</code> a los argumentos de mtouch adicionales que se usa Clang si la está usando Xcode 4.6 o una versión anterior (Xamarin.iOS se seleccionarán automáticamente Clang en Xcode 5.0 o posterior.)

    <li>Si Xcode 4.6 (o versiones anterior) es usada, GCC / G ++ debe seleccionarse si el tipo exportado nombres contienen caracteres no ascii (Esto es porque la versión de Clang enviado con Xcode 4.6 no admite caracteres no ascii en identificadores en el código Objective-C). Agregar <code>--compiler:gcc</code> a los argumentos adicionales mtouch usar GCC.


## <a name="selecting-a-registrar"></a>Al seleccionar un registrador

Puede seleccionar a un registrador diferente mediante la adición de una de las siguientes opciones a los argumentos adicionales mtouch en Opciones de compilación de iOS del proyecto:

-  `--registrar:static` : el valor predeterminado de compilaciones de dispositivo
-  `--registrar:dynamic` : el valor predeterminado de simulador de compilaciones
-  `--registrar:legacystatic` : el valor predeterminado de dispositivo se compila hasta que Xamarin.iOS 7.2.1
-  `--registrar:legacydynamic` : el valor predeterminado de simulador compilaciones hasta Xamarin.iOS 7.2.1


## <a name="shortcomings-in-the-old-registration-system"></a>Deficiencias en el sistema de registro antiguo

El sistema de registro antiguo tiene las siguientes desventajas:

-  No hubo ninguna referencia estática (nativa) para Objective-C clases y métodos de bibliotecas nativas de terceros, lo que significa que no pudimos pedimos al vinculador nativo que quite el código nativo de terceros que no se usaron realmente (porque se quitaría todo). Esta es la razón para la "-force_load libNative.a" que cada enlace de terceros que tuve que hacer (o el equivalente ForceLoad = true en el atributo [LinkWith]).
-  Puede exportar dos tipos administrados con el mismo nombre Objective-C, sin ninguna advertencia. Un escenario poco frecuente era acabará con dos clases de AppDelegate (en diferentes espacios de nombres). En tiempo de ejecución sería completamente aleatorias que se ha seleccionado (de hecho que varía entre las ejecuciones de una aplicación que no se vuelven a generar incluso - que ha realizado para una experiencia de depuración muy un tanto confuso y frustrante).
-  Puede exportar dos métodos que tienen la misma firma de C de objetivo. Todavía nuevo cuál se denominaría del objetivo de C se aleatorio (pero este problema no estaba tan común como lo anterior, principalmente porque la única manera realmente experimentando este error era invalidar el método administrado suerte).
-  El conjunto de métodos que se exportó era ligeramente diferente entre compilaciones dinámicas y estáticas.
-  No funcione correctamente cuando se exportan las clases genéricas (qué implementación genérica exacto que se ejecuta en tiempo de ejecución sería aleatoria, produciendo eficazmente un comportamiento indeterminado).


 <a name="required_modifications" />


## <a name="new-registrar-required-changes-to-bindings"></a>Registrar nuevo: Los cambios necesarios a los enlaces


Los enlaces existentes Objective-C deba actualizarse para que funcione con la nueva registar.

Esta es una lista de cambios que deben realizarse.

### <a name="protocols-must-have-the-protocol-attribute"></a>Protocolos deben tener el atributo [Protocol]

Las implementaciones de protocolos deben tener ahora el atributo [Protocol] aplicado a ellos.  Si no lo hace, aparecerá un error del vinculador nativo como este:

```csharp
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>Selectores deben tener un número válido de parámetros

Todos los selectores deben indicar un número de parámetros correctamente.  Anteriormente, estos errores se ignoraron y pudieron causar problemas en tiempo de ejecución.

En resumen, el número de caracteres de dos puntos debe coincidir con el número de parámetros.

Por ejemplo:

-  Sin parámetros: 'foo'
-  Un parámetro: ' foo:'
-  Dos parámetros: ' foo:parameterName2:'


A continuación se indican algunos usos incorrectos:

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>Utilice el parámetro de IsVariadic de exportación

Las funciones Variádicas deben decir esto en su atributo de exportación (Esto es porque el selector es incorrecto sobre el número de argumentos, es decir, el punto 2. desde arriba se infringe):

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>Debe vincular a símbolos existentes

Es posible enlazar las clases que no existen en la biblioteca nativa.

Obtendrá un error nativo del vinculador si intenta enlazar clases no existente.

Esto suele ocurrir cuando existe un enlace durante algún tiempo y se ha modificado el código nativo durante ese tiempo para que una clase nativa determinada se ha quitado o cambiado el nombre, mientras que no se ha actualizado el enlace.

