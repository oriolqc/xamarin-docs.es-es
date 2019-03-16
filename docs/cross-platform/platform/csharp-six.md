---
title: C#Información general sobre características nuevas que 6
description: La versión más reciente de la C# , versión 6, idioma sigue evolucionando el lenguaje para tener menos repetitivo, ha mejorado la claridad y coherencia más. ¿Sintaxis de inicialización más limpia, la capacidad de usar await en bloques catch/finally y el condicional de null? operador son especialmente útiles.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: b69fe417bb521781453042269b9b52609d8e00a0
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2019
ms.locfileid: "58070961"
---
# <a name="c-6-new-features-overview"></a>C#Información general sobre características nuevas que 6

_La versión más reciente de la C# , versión 6, idioma sigue evolucionando el lenguaje para tener menos repetitivo, ha mejorado la claridad y coherencia más. ¿Sintaxis de inicialización más limpia, la capacidad de usar await en bloques catch/finally y el condicional de null? operador son especialmente útiles._

Este documento presenta las nuevas características de C# 6. Es totalmente compatible con el compilador mono y los desarrolladores empezar a usar las nuevas características en todas las plataformas de destino de Xamarin.

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**Novedades C# 6, de manera [Xamarin University](https://university.xamarin.com/)**

## <a name="using-c-6"></a>Uso de C# 6

El C# compilador 6 se utiliza en todas las versiones recientes de Visual Studio para Mac.
Los usuarios que usen los compiladores de línea de comandos deben confirmar que `mcs --version` devuelve 4.0 o posterior.
Visual Studio para Mac se puede comprobar si tienen Mono 4 (o posterior) instalado haciendo referencia a **acerca de Visual Studio para Mac > Visual Studio para Mac > Mostrar detalles**.

## <a name="less-boilerplate"></a>Menos texto reutilizable
### <a name="using-static"></a>uso de versión estática
Enumeraciones y ciertas clases como `System.Math`, son principalmente los titulares de funciones y valores estáticos. En C# 6, puede importar todos los miembros estáticos de un tipo con una sola `using static` instrucción. Comparar una típica función trigonométrica en C# 5 y C# 6:

```csharp
// Classic C#
class MyClass
{
    public static Tuple<double,double> SolarAngleOld(double latitude, double declination, double hourAngle)
    {
        var tmp = Math.Sin (latitude) * Math.Sin (declination) + Math.Cos (latitude) * Math.Cos (declination) * Math.Cos (hourAngle);
        return Tuple.Create (Math.Asin (tmp), Math.Acos (tmp));
    }
}

// C# 6
using static System.Math;

class MyClass
{
    public static Tuple<double, double> SolarAngleNew(double latitude, double declination, double hourAngle)
    {
        var tmp = Asin (latitude) * Sin (declination) + Cos (latitude) * Cos (declination) * Cos (hourAngle);
        return Tuple.Create (Asin (tmp), Acos (tmp));
    }
}
```

`using static` no se hace pública `const` campos, tales como `Math.PI` y `Math.E`, accesibles directamente:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>utilización estática con métodos de extensión

El `using static` facility algo funciona de forma diferente con los métodos de extensión. Aunque los métodos de extensión se escriben con `static`, no tienen sentido sin una instancia en la que se va a operar. Por lo que cuando `using static` se usa con un tipo que define los métodos de extensión, los métodos de extensión que están disponibles en su tipo de destino (el método `this` tipo). Por ejemplo, `using static System.Linq.Enumerable` puede usarse para ampliar la API de `IEnumerable<T>` objetos sin poner en todos los tipos LINQ:

```csharp
using static System.Linq.Enumerable;
using static System.String;

class Program
{
    static void Main()
    {
        var values = new int[] { 1, 2, 3, 4 };
        var evenValues = values.Where (i => i % 2 == 0);
        System.Console.WriteLine (Join(",", evenValues));
    }
}
```

El ejemplo anterior muestra la diferencia de comportamiento: el método de extensión `Enumerable.Where` está asociado a la matriz, mientras el método estático `String.Join` puede llamarse sin hacer referencia a la `String` tipo.

### <a name="nameof-expressions"></a>Expresiones nameof
A veces, desea hacer referencia al nombre que ha proporcionado una variable o campo. En C# 6, `nameof(someVariableOrFieldOrType)` devolverá la cadena `"someVariableOrFieldOrType"`. Por ejemplo, cuando se lanza una `ArgumentException` es muy probable que desea asignar un nombre de argumento que no es válido:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

La principal ventaja de `nameof` expresiones es que son tipos comprobados y son compatibles con basadas en la herramienta de refactorización. La comprobación de tipos de `nameof` expresiones es especialmente bienvenida en situaciones donde un `string` se utiliza para asociar tipos de forma dinámica. Por ejemplo, en iOS un `string` se usa para especificar el tipo utilizado para el prototipo `UITableViewCell` objetos en un `UITableView`. `nameof` puede garantizar que esta asociación no producirá un error debido a un error ortográfico o refactorización descuidado:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Aunque puede pasar un nombre completo que `nameof`, solo el último elemento (después del último `.`) se devuelve. Por ejemplo, puede agregar un enlace de datos en Xamarin.Forms:

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

Las dos llamadas a `SetBinding` se pasan valores idénticos: `nameof(ReactiveType.StringField)` es `"StringField"`, no `"ReactiveType.StringField"` como cabría esperar inicialmente.

## <a name="null-conditional-operator"></a>Operador condicional null
Las actualizaciones anteriormente C# introdujo los conceptos de los tipos que aceptan valores NULL y el operador de uso combinado de null `??` para reducir la cantidad de código reutilizable al controlar los valores que aceptan valores NULL. C#6 sigue este tema con el "operador condicional null" `?.`. Cuando se utiliza en un objeto en el lado derecho de una expresión, el operador condicional null devuelve el valor del miembro si el objeto no es `null` y `null` en caso contrario:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Ambos `length0` y `length1` se infiere para ser de tipo `int?`)

La última línea en el ejemplo anterior muestra la `?` operador condicional null en combinación con el `??` operador de uso combinado de null. El nuevo C# 6 del operador condicional null devuelve `null` en el 2 º elemento en la matriz, momento en que se inicia el operador de uso combinado de null y proporciona un 0 para el `lengths` (ya sea que es adecuado o no es, por supuesto, de matriz problemas específicos).

El operador condicional null enormemente debería reducir la cantidad necesaria de comprobación de null reutilizable en muchas aplicaciones.

Existen algunas limitaciones en el operador condicional null debido a ambigüedades. No se puede seguir inmediatamente un `?` con una lista de argumentos entre paréntesis, como se podría esperar que ver con un delegado:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

Sin embargo, `Invoke` puede usarse para separar el `?` desde la lista de argumentos y sigue siendo una mejora marcada a través de un `null`-bloque de texto reutilizable de comprobación:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Interpolación de cadenas
El `String.Format` función ha utilizan tradicionalmente índices como marcadores de posición en la cadena de formato, por ejemplo, `String.Format("Expected: {0} Received: {1}.", expected, received`). Agregar un nuevo valor, por supuesto, siempre ha implicado una tarea poco molesta de recuento de argumentos y cambiar la numeración de los marcadores de posición para insertar el nuevo argumento en la secuencia correcta en la lista de argumentos.

C#6 de la nueva característica de interpolación de cadena se mejora en gran medida `String.Format`. Ahora, puede asignar variables en una cadena con el prefijo directamente un `$`. Por ejemplo:

```csharp
$"Expected: {expected} Received: {received}."
```

Por supuesto, las variables se comprueban y una variable mal escrita o no disponibles provocará un error del compilador.

Los marcadores de posición no es necesario ser variables simples, pueden ser cualquier expresión. Dentro de estos marcadores de posición, puede usar comillas *sin* los presupuestos de secuencias de escape. Por ejemplo, tenga en cuenta el `"s"` en lo siguiente:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

Interpolación de cadenas es compatible con la alineación y el formato de sintaxis de `String.Format`. Al igual que podía escribió anteriormente `{index, alignment:format}`, en C# 6 escribir `{placeholder, alignment:format}`:

```csharp
using static System.Linq.Enumerable;
using System;

class Program
{
    static void Main ()
    {
        var values = new int[] { 1, 2, 3, 4, 12, 123456 };
        foreach (var s in values.Select (i => $"The value is { i,10:N2}.")) {
            Console.WriteLine (s);
        }
    Console.WriteLine ($"Minimum is { values.Min(i => i):N2}.");
    }
}
```
resultados en:

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

Interpolación de cadenas es azúcar sintáctica para `String.Format`: no se puede usar con `@""` literales de cadena y no es compatible con `const`, incluso si no se utiliza ningún marcador de posición:

```csharp
const string s = $"Foo"; //Error : const requires value
```

En el caso de uso común de la creación de los argumentos de función con la interpolación de cadena, aun así deberá tener cuidado con escape, la codificación y problemas de la referencia cultural. Las consultas SQL y la dirección URL son, por supuesto, fundamentales para sanear. Igual que con `String.Format`, usa la interpolación de cadena del `CultureInfo.CurrentCulture`. Uso de `CultureInfo.InvariantCulture` es un poco más farragoso:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Inicialización

C#6 proporciona varias maneras de concisa para especificar las propiedades, campos y los miembros.

### <a name="auto-property-initialization"></a>Inicialización de propiedades automáticas

Ahora se pueden inicializar propiedades automáticas de la misma manera concisa como campos. Pueden escribirse inmutable propiedades automáticas con solo un captador:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

En el constructor, puede establecer el valor de una propiedad automática solo captador:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
    public string Description { get; }

    public ToDo (string description)
    {
        this.Description = description; //Can assign (only in constructor!)
    }
```

Esta inicialización de propiedades automáticas es una característica de ahorro de espacio en general y es una ventaja para los desarrolladores que desean hacer hincapié en la inmutabilidad en sus objetos.

### <a name="index-initializers"></a>Inicializadores de índice

C#6 introduce a inicializadores de índice, lo que permite establecer la clave y el valor de tipos que tienen un indizador. Normalmente, esto es para `Dictionary`-estructuras de datos de estilo:

```csharp
partial void ActivateHandoffClicked (WatchKit.WKInterfaceButton sender)
{
    var userInfo = new NSMutableDictionary {
        ["Created"] = NSDate.Now,
        ["Due"] = NSDate.Now.AddSeconds(60 * 60 * 24),
        ["Task"] = Description
    };
    UpdateUserActivity ("com.xamarin.ToDo.edit", userInfo, null);
    statusLabel.SetText ("Check phone");
}
```

### <a name="expression-bodied-function-members"></a>Miembros con forma de expresión de función

Las funciones lambda tienen varias ventajas, simplemente uno de los cuales es ahorrar espacio. De forma similar, los miembros de clase con forma de expresión permiten las funciones pequeñas se expresen de forma un poco más sucinta que era posible en versiones anteriores de C# 6.

Los miembros con forma de expresión de función use la sintaxis de flecha lambda en lugar de la sintaxis tradicional de bloque:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Tenga en cuenta que la sintaxis de flecha lambda no usa explícita `return`. Para las funciones que devuelven `void`, la expresión debe ser también una instrucción:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

Los miembros con forma de expresión siguen estando sujetas a la regla que `async` es compatible con métodos, pero no las propiedades:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Excepciones

Hay no hay dos maneras de sobre ella: control de excepciones es difícil realizar correctamente. Las nuevas características de C# 6 que control de excepciones más flexible y coherente.

### <a name="exception-filters"></a>Filtros de excepciones

Por definición, se producen excepciones en circunstancias inusuales, y puede ser muy difícil de motivo y código sobre *todas* las formas en que podría producirse una excepción de un tipo determinado. C#6 se introduce la capacidad de proteger un controlador de ejecución con un filtro evalúa en tiempo de ejecución. Esto se hace agregando un `when (bool)` patrón después de la normal `catch(ExceptionType)` declaración. En la siguiente tabla, un filtro distingue un error de análisis relacionados con el `date` parámetro en lugar de otros errores de análisis.

```csharp
public void ExceptionFilters(string aFloat, string date, string anInt)
{
    try
    {
        var f = Double.Parse(aFloat);
        var d = DateTime.Parse(date);
        var n = Int32.Parse(anInt);
    } catch (FormatException e) when (e.Message.IndexOf("DateTime") > -1) {
        Console.WriteLine ($"Problem parsing \"{nameof(date)}\" argument");
    } catch (FormatException x) {
        Console.WriteLine ("Problem parsing some other argument");
    }
}
```

### <a name="await-in-catchfinally"></a>await en catch... finally...

El `async` las capacidades introducidas en C# 5 han sido un producto innovador para el idioma. En C# 5, `await` no se permitía en `catch` y `finally` bloquea una molestia dada el valor de la `async/await` capacidad. C#6 elimina esta limitación, lo que permite resultados asincrónicos que se espere coherente a través del programa, como se muestra en el siguiente fragmento de código:

```csharp
async void SomeMethod()
{
    try {
        //...etc...
    } catch (Exception x) {
        var diagnosticData = await GenerateDiagnosticsAsync (x);
        Logger.log (diagnosticData);
    } finally {
        await someObject.FinalizeAsync ();
    }
}
```

## <a name="summary"></a>Resumen

El C# idioma sigue evolucionando para que los desarrolladores sean más productivos al mismo tiempo la promoción de prácticas recomendadas y compatibilidad con las herramientas. Este documento ha proporcionado una visión general de las nuevas características de lenguaje de C# 6 y ha demostrado brevemente cómo se usan.

## <a name="related-links"></a>Vínculos relacionados

- [Características del nuevo lenguaje de C# 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)

