---
title: "6 nuevas características de C# información general"
description: "La versión más reciente del lenguaje de C#, versión 6, continúa evolucionando el idioma para que tenga menos reutilizable, mejorar la claridad y coherencia más. ¿Sintaxis de inicialización de limpieza, la capacidad para usar await en bloques catch/finally y el condicional null? operador son especialmente útiles."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 22b03c43509f3e3a55cd36ead5adef79c9086ba4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="c-6-new-features-overview"></a>6 nuevas características de C# información general

_La versión más reciente del lenguaje de C#, versión 6, continúa evolucionando el idioma para que tenga menos reutilizable, mejorar la claridad y coherencia más. ¿Sintaxis de inicialización de limpieza, la capacidad para usar await en bloques catch/finally y el condicional null? operador son especialmente útiles._

Este documento presentan las nuevas características de C# 6. Es totalmente compatible con el compilador mono y a los desarrolladores pueden empezar a usar las nuevas características en todas las plataformas de destino de Xamarin.

Este artículo incluye fragmentos de código breves del código de C# 6 que ilustran el uso básico.
La aplicación de ejemplo es un programa de línea de comandos que se ejecuta en todas las plataformas de destino de Xamarin y ejercita las distintas características.

# <a name="requirements"></a>Requisitos

## <a name="development-environment"></a>Entorno de desarrollo

### <a name="mac"></a>Mac

* **Visual Studio para Mac** es compatible con C# 6: puede generar y compilar aplicaciones de Xamarin con características de C# 6.
  Obtenga más información sobre [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/).

### <a name="windows"></a>Windows

* **Visual Studio 2015 y 2017** y versiones posteriores tienen compatibilidad completa para C# 6. Versiones anteriores de Visual Studio (p. ej. 2013, 2012) no será compatible con C# 6.

* **Xamarin Studio para Windows** no admite características de C# 6 en el editor.



## <a name="compiler"></a>Compilador

El compilador de C# 6 Mono se incluye en Mono 4.0 y versiones posterior, que es [libremente disponible para su descarga](http://www.mono-project.com/download/).
Visual Studio para Mac actualiza automáticamente la instalación Mono en el sistema.

Los usuarios de Windows deben tener [Visual Studio 2015 o 2017 ^](https://www.visualstudio.com/) instalado para compilar el código de C# 6 (incluso si decide Xamarin Studio para Windows como su IDE).

^ o  *[Microsoft generar herramientas de 2015](http://www.microsoft.com/en-us/download/details.aspx?id=48159)*  de comando de línea compilación o servidores de compilación, por ejemplo.

# <a name="using-c-6"></a>Uso de C# 6

El compilador de C# 6 se utiliza en todas las versiones recientes de Visual Studio para Mac.
Los usuarios que utilicen los compiladores de línea de comandos deben confirmar que `mcs --version` devuelve 4.0 o posterior.
Visual Studio para que los usuarios de Mac puede comprobar si tienen Mono 4 (o posterior) instalado, se hace referencia a **sobre Visual Studio para Mac > Visual Studio para Mac > Mostrar detalles**.

# <a name="less-boilerplate"></a>Menos repetición
## <a name="using-static"></a>uso de versión estática
Enumeraciones y determinadas clases como `System.Math`, son principalmente a los titulares de valores estáticos y funciones. En C# 6, puede importar todos los miembros estáticos de un tipo con una sola `using static` instrucción. Comparar una función trigonométrica típica en C# 5 y 6 de C#:

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

`using static` no hacer público `const` campos, tales como `Math.PI` y `Math.E`, accesibles directamente:

    for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... //PI is const, not static, so requires Math.PI

## <a name="using-static-with-extension-methods"></a>utilizar particiones estáticas con métodos de extensión

El `using static` instalación un poco funciona de forma diferente a los métodos de extensión. Aunque los métodos de extensión se escriben con `static`, no tienen sentido sin una instancia en la que se va a operar. Por lo que cuando `using static` se usa con un tipo que define los métodos de extensión, los métodos de extensión que están disponibles en su tipo de destino (el método `this` tipo). Por ejemplo, `using static System.Linq.Enumerable` puede utilizarse para extender la API de `IEnumerable<T>` objetos sin volver a poner en todos los tipos LINQ:

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

En el ejemplo anterior se muestra la diferencia de comportamiento: el método de extensión `Enumerable.Where` está asociado a la matriz, mientras el método estático `String.Join` se pueda llamar sin referencia a la `String` tipo.

## <a name="nameof-expressions"></a>nombre del tipo de expresiones
A veces, desea hacer referencia al nombre que ha proporcionado una variable o campo. En C# 6, `nameof(someVariableOrFieldOrType)` devolverá la cadena `"someVariableOrFieldOrType"`. Por ejemplo, al producir una `ArgumentException` es muy probable que desea asignar un nombre de argumento que no es válido:

    throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))

La principal ventaja de `nameof` expresiones es que se comprobará el tipo y son compatibles con la refactorización de la herramienta de consumo. La comprobación de tipos de `nameof` expresiones resulta especialmente utilidad en situaciones donde un `string` se utiliza para asociar tipos de forma dinámica. Por ejemplo, en iOS un `string` se utiliza para especificar el tipo utilizado para prototipo `UITableViewCell` objetos en un `UITableView`. `nameof` puede garantizar que esta asociación no se producen errores debido a un error ortográfico o refactorización chapucero:

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
        cell.TextLabel.Text = objects [indexPath.Row].ToString ();
        return cell;
    }

Aunque puede pasar un nombre completo que `nameof`, sólo el último elemento (después de la última `.`) se devuelve. Por ejemplo, puede agregar un enlace de datos de Xamarin.Forms:

    var myReactiveInstance = new ReactiveType ();
    var myLabelOld.BindingContext = myReactiveInstance;
    var myLabelNew.BindingContext = myReactiveInstance;
    var myLabelOld.SetBinding (Label.TextProperty, "StringField");
    var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));

Las dos llamadas a `SetBinding` está pasando valores idénticos: `nameof(ReactiveType.StringField)` es `"StringField"`, no `"ReactiveType.StringField"` como cabría esperar inicialmente.

# <a name="null-conditional-operator"></a>Operador condicional null
Actualizaciones anteriores a C# introdujeron los conceptos de los tipos que aceptan valores NULL y el operador de uso combinado de null `??` para reducir la cantidad de código reutilizable al administrar los valores que aceptan valores NULL. C# 6 sigue este tema con el "operador condicional null" `?.`. Cuando se utiliza en un objeto en el lado derecho de una expresión, el operador condicional null devuelve el valor del miembro, si el objeto no es `null` y `null` en caso contrario:

    var ss = new string[] { "Foo", null };
    var length0 = ss [0]?.Length; // 3
    var length1 = ss [1]?.Length; // null
    var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]

(Ambos `length0` y `length1` se deduzcan del tipo `int?`)

La última línea en el ejemplo anterior muestra la `?` operador condicional null en combinación con la `??` operador de uso combinado de null. Devuelve el nuevo operador condicional null de C# 6 `null` en el elemento 2 de la matriz, momento en que el operador de uso combinado de null, se inicia y proporciona un 0 para el `lengths` matriz (ya que es adecuado o no es, por supuesto, problemas específicos).

El operador condicional null enormemente si debe reducir la cantidad de es necesario comprobar null de reutilizable en muchas aplicaciones.

Existen algunas limitaciones en el operador condicional null debido a ambigüedades. No se puede seguir inmediatamente una `?` con una lista de argumentos entre paréntesis, como se podría esperar que hacer con un delegado:

    SomeDelegate?("Some Argument") // Not allowed

Sin embargo, `Invoke` puede utilizarse para separar el `?` desde la lista de argumentos y sigue siendo una mejora marcada sobre un `null`-comprobación de bloque de código reutilizable:

    public event EventHandler HandoffOccurred;
    public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
    {
        HandoffOccurred?.Invoke (this, userActivity.UserInfo);
        return true;
    }


# <a name="string-interpolation"></a>Interpolación de cadenas
El `String.Format` función utiliza tradicionalmente índices como marcadores de posición en la cadena de formato, por ejemplo, `String.Format("Expected: {0} Received: {1}.", expected, received`). Por supuesto, agregar un nuevo valor ha implicado siempre una tarea poco molesta de recuento de argumentos y cambiar la numeración de los marcadores de posición para insertar el nuevo argumento en la secuencia correcta en la lista de argumentos.

Nueva característica de interpolación de cadena del 6 de C# se mejora en gran medida en `String.Format`. Ahora, puede asignar directamente las variables en una cadena como precedidas un `$`. Por ejemplo:

    $"Expected: {expected} Received: {received}."

Por supuesto, se, comprueba las variables y una variable mal escrita o no disponibilidad provocará un error del compilador.

Los marcadores de posición no es necesario ser variables simples, pueden ser cualquier expresión. Dentro de estos marcadores de posición, puede utilizar las comillas *sin* los presupuestos de secuencias de escape. Por ejemplo, tenga en cuenta el `"s"` en lo siguiente:

    var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"

Interpolación de cadenas es compatible con la alineación y el formato de sintaxis de `String.Format`. Tal y como lo escribió anteriormente `{index, alignment:format}`, en C# 6 escribir `{placeholder, alignment:format}`:

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

resultado:

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

Interpolación de cadenas es sintáctico para `String.Format`: no se puede usar con `@""` literales de cadena y no es compatible con `const`, incluso si no hay marcadores que se utilizan:

    const string s = $"Foo"; //Error : const requires value

En caso de uso común de la creación de argumentos de función con interpolación de cadenas, deberá tener cuidado con escape, la codificación y problemas de la referencia cultural. Las consultas SQL y la dirección URL son, por supuesto, fundamentales para sanear. Al igual que con `String.Format`, usa la interpolación de cadena del `CultureInfo.CurrentCulture`. Usar `CultureInfo.InvariantCulture` es un poco más farragoso:

    Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
    Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
    Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"

# <a name="initialization"></a>Inicialización
C# 6 proporciona varias maneras de concisa para especificar propiedades, campos y los miembros.

## <a name="auto-property-initialization"></a>Inicialización de propiedades automáticas
Ahora se pueden inicializar propiedades automáticas de la misma manera concisa como campos. Auto-propiedades inmutables se pueden escribir con solo un captador:

    class ToDo
    {
        public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
        public DateTime Created { get; } = DateTime.Now;

En el constructor, puede establecer el valor de una propiedad automática solo captador:

    class ToDo
    {
        public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
        public DateTime Created { get; } = DateTime.Now;
        public string Description { get; }

        public ToDo (string description)
        {
           this.Description = description; //Can assign (only in constructor!)
        }

Esta inicialización de propiedades automáticas es una característica de ahorro de espacio general y una ayuda a los desarrolladores que deseen resaltar inmutabilidad en sus objetos.

## <a name="index-initializers"></a>Inicializadores de índice
C# 6 presenta a inicializadores de índice, lo que permite establecer la clave y el valor en tipos que tienen un indizador. Normalmente, se trata de `Dictionary`-aplicar estilo a las estructuras de datos:

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

## <a name="expression-bodied-function-members"></a>Miembros de la expresión en el cuerpo de función
Las funciones lambda tienen varias ventajas, simplemente uno de los cuales es ahorrar espacio. Del mismo modo, los miembros de clase de expresión en el cuerpo permiten las funciones pequeñas se exprese un poco más sucinta que era posible en versiones anteriores de C# 6.

Los miembros de la expresión en el cuerpo de función utilizar la sintaxis de flecha de la expresión lambda en lugar de la sintaxis de bloque tradicionales:

      public override string ToString () => $"{FirstName} {LastName}";

Observe que la sintaxis de flecha de la expresión lambda no utiliza explícito `return`. Para las funciones que devuelven `void`, la expresión debe ser también una instrucción:

    public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");

En el cuerpo expresión miembros siguen estando sujetas a la regla que `async` es compatible con métodos pero no propiedades:

    //A method, so async is valid
    public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
    //The following property will not compile
    public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;


# <a name="exceptions"></a>Excepciones
No hay ningún dos formas sobre él: control de excepciones es difícil realizar correctamente. Nuevas características de C# 6 que control de excepciones más flexible y coherente.

## <a name="exception-filters"></a>Filtros de excepciones
Por definición, las excepciones se producen en circunstancias inusuales, y puede ser muy difícil de razón y el código sobre *todos los* lo que se podría producir una excepción de un tipo determinado. C# 6 introduce la capacidad de proteger un controlador de ejecución con un filtro evalúa en tiempo de ejecución. Esto se realiza agregando un `when (bool)` patrón después del vector normal `catch(ExceptionType)` declaración. En la siguiente tabla, un filtro distingue un error de análisis relacionados con el `date` parámetro en lugar de otros errores del análisis.

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

## <a name="await-in-catchfinally"></a>await en catch... finally...
El `async` las capacidades introducidas en C# 5 han sido un producto innovador para el idioma. En C# 5, `await` no se permitía en `catch` y `finally` se bloquea, una serie de molestias le asigna el valor de la `async/await` capacidad. C# 6 quita esta limitación, lo que permite resultados asincrónicas que puede esperar de forma coherente a través del programa tal como se muestra en el siguiente fragmento:

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


# <a name="summary"></a>Resumen

El lenguaje C# sigue evolucionando para que los desarrolladores sean más productivos al mismo tiempo promover prácticas recomendadas y compatibilidad con herramientas. Este documento proporciona información general sobre las nuevas características de lenguaje en C# 6 y ha demostrado brevemente cómo se utilizan.

## <a name="related-links"></a>Vínculos relacionados

- [Nuevas características del lenguaje en C# 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)
- [Con C# 6 libro](https://developer.xamarin.com/workbooks/csharp/csharp6/csharp6.workbook)
