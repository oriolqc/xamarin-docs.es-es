---
title: Información general sobre la compatibilidad con Async
description: Este documento se describe la programación con async y await, los conceptos presentados en C# 5 para que resulte más fácil escribir código asincrónico.
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0a72dead1b6c001f1514f1a089df9b407eb90644
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671887"
---
# <a name="async-support-overview"></a>Información general sobre la compatibilidad con Async

_C#5 introdujo dos palabras clave para simplificar la programación asincrónica: async y await. Estas palabras clave le permiten escribir código simple que usa la biblioteca TPL para ejecutar operaciones de larga ejecución (por ejemplo, el acceso a la red) en otro subproceso y acceder fácilmente a los resultados al finalizar. Las últimas versiones de Xamarin.iOS y Xamarin.Android, compatibilidad con async y await: este documento proporciona explicaciones y un ejemplo del uso de la nueva sintaxis con Xamarin._

Soporte para asincronía de Xamarin se basa en la base de Mono 3.0 y actualiza el perfil de API desde el que se va a una versión móvil de Silverlight que sea una versión móvil de .NET 4.5.

## <a name="overview"></a>Información general

Este documento presenta el nuevas async y await palabras clave, le guía a través de algunos ejemplos sencillos de implementar los métodos asincrónicos en Xamarin.iOS y Xamarin.Android.

Para obtener una explicación más completa de las nuevas características asincrónicas de C# 5 (incluido muchos ejemplos y escenarios de uso diferentes), consulte la documentación de MSDN [programación asincrónica con Async y Await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx).

La aplicación de ejemplo realiza una solicitud web asincrónica simple (sin bloquear el subproceso principal), a continuación, actualiza la interfaz de usuario con el html descargado y el recuento de caracteres.

 [![](async-images/AsyncAwait_427x368.png "La aplicación de ejemplo realiza una solicitud web asincrónica simple sin bloquear el subproceso principal, a continuación, actualiza la interfaz de usuario con el html descargado y el recuento de caracteres")](async-images/AsyncAwait.png#lightbox)

Soporte para asincronía de Xamarin se basa en la base de Mono 3.0 y actualiza el perfil de API desde el que se va a una versión móvil de Silverlight que sea una versión móvil de .NET 4.5.

## <a name="requirements"></a>Requisitos

C#las 5 características requieren 3.0 de Mono que se incluye en 6.4 Xamarin.iOS y Xamarin.Android 4.8. Se le pedirá para actualizar su Mono, Xamarin.iOS, Xamarin.Android y Xamarin.Mac para aprovecharlo.

## <a name="using-async-amp-await"></a>Usar async &amp; await

 `async` y `await` nuevas C# características del lenguaje que funcionan en conjunción con la biblioteca TPL para que sea fácil escribir código multiproceso para llevar a cabo tareas de ejecución prolongada sin bloquear el subproceso principal de la aplicación.

## <a name="async"></a>async

### <a name="declaration"></a>Declaración

El `async` palabra clave se coloca en una declaración de método (o en una expresión lambda o método anónimo) para indicar que contiene código que se puede ejecutar de forma asincrónica, ie. no bloquear el subproceso del llamador.

Un método marcado con `async` debe contener al menos una expresión o instrucción await. Si no hay ningún `await`s están presentes en el método se ejecutará de forma sincrónica (el mismo como si hubiera ninguna `async` modificador). También se generará una advertencia del compilador (pero no un error).

### <a name="return-types"></a>Tipos de valor devueltos

Un método asincrónico debe devolver un `Task`, `Task<TResult>` o `void`.

Especifique el `Task` tipo de valor devuelto si el método no devuelve cualquier otro valor.

Especificar `Task<TResult>` si el método debe devolver un valor, donde `TResult` es el tipo que se devuelve (como un `int`, por ejemplo).

El `void` devolver el tipo se usa principalmente para controladores de eventos que lo requieren. Código que llama a métodos asincrónicos que devuelven void no `await` en el resultado.

### <a name="parameters"></a>Parámetros

No se pueden declarar los métodos asincrónicos `ref` o `out` parámetros.

## <a name="await"></a>await

El operador await se puede aplicar a una tarea dentro de un método marcado como async. Hace que el método para detener la ejecución en ese momento y espere hasta que finalice la tarea.

Uso de await no bloquea el subproceso del llamador: en su lugar el control se devuelve al llamador. Esto significa que no se bloquea el subproceso de llamada, por lo que por ejemplo, el usuario subproceso de interfaz no quedará bloqueado cuando se espera una tarea.

Cuando se complete la tarea, el método reanuda la ejecución en el mismo punto en el código. Esto incluye devolver al ámbito vuelva de un bloque try-catch-finally (si hay alguno). await no se puede usar en un bloque catch o finally.

Obtenga más información sobre [await en MSDN](https://msdn.microsoft.com/library/vstudio/hh156528.aspx).

## <a name="exception-handling"></a>Control de excepciones

Las excepciones que se producen dentro de un método asincrónico se almacena en la tarea y se produce cuando la tarea es `await`ed. Estas excepciones se pueden detectar y tratar dentro de un bloque try-catch.

## <a name="cancellation"></a>Cancelación

Los métodos asincrónicos que toman mucho tiempo en completarse deben admitir la cancelación. Normalmente, cancelación se invoca como sigue:

- Un `CancellationTokenSource` se crea el objeto.
- El `CancellationTokenSource.Token` instancia se pasa a un método asincrónico puede cancelar.
- Se solicita la cancelación mediante una llamada a la `CancellationTokenSource.Cancel` método.

La tarea, a continuación, se cancela y confirma la cancelación.

Para obtener más información sobre la cancelación, vea [cómo cancelar una tarea asincrónica](https://msdn.microsoft.com/library/vstudio/jj155761.aspx) en MSDN.

## <a name="example"></a>Ejemplo

Descargue el [ejemplo de solución de Xamarin](https://developer.xamarin.com/samples/mobile/AsyncAwait/) (para iOS y Android) para ver un ejemplo práctico de `async` y `await` en aplicaciones móviles. El código de ejemplo se explica con más detalle en esta sección.

### <a name="writing-an-async-method"></a>Escribir un método asincrónico

El método siguiente muestra cómo codificar un `async` método con un `await`tarea ed:

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("http://xamarin.com"); // async method!

    // await! control returns to the caller and the task continues to run on another thread
    string contents = await contentsTask;

    ResultEditText.Text += "DownloadHomepage method continues after async call. . . . .\n";

    // After contentTask completes, you can calculate the length of the string.
    int exampleInt = contents.Length;

    ResultEditText.Text += "Downloaded the html and found out the length.\n\n\n";

    ResultEditText.Text += contents; // just dump the entire HTML

    return exampleInt; // Task<TResult> returns an object of type TResult, in this case int
}
```

Tenga en cuenta estos puntos:

-  La declaración del método incluye el `async` palabra clave.
-  El tipo de valor devuelto es `Task<int>` para llamar a código pueda acceder a la `int` valor calculado en este método.
-  La instrucción return está `return exampleInt;` que es un objeto entero: el hecho de que el método devuelve `Task<int>` forma parte de las mejoras de lenguaje.


### <a name="calling-an-async-method-1"></a>Una llamada a un método asincrónico 1

Evento click del botón este controlador se encuentra en la aplicación de Android de ejemplo para llamar al método descrito anteriormente:

```csharp
GetButton.Click += async (sender, e) => {

    Task<int> sizeTask = DownloadHomepage();

    ResultTextView.Text = "loading...";
    ResultEditText.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await sizeTask;

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultTextView.Text = "Length: " + intResult ;
    // "returns" void, since it's an event handler
};
```

Notas:

-  El delegado anónimo tiene el prefijo de palabra clave async.
-  El método asincrónico DownloadHomepage devuelve una tarea <int> que se almacena en la variable sizeTask.
-  El código espera en la variable sizeTask.  *Esto* es la ubicación que se suspende el método y el control se devuelve al código de llamada hasta que finalice la tarea asincrónica en su propio subproceso.
-  Ejecución hace *no* pausar cuando la tarea se crea en la primera línea del método, a pesar de la tarea que se va a crear no existe. La palabra clave await indica la ubicación donde se pausa la ejecución.
-  Cuando finalice la tarea asincrónica, se establece intResult y la ejecución continúa en el subproceso original desde la línea de await.


### <a name="calling-an-async-method-2"></a>Una llamada a un método asincrónico 2

En la aplicación de ejemplo de iOS en el ejemplo se escribe de forma ligeramente diferente para demostrar un enfoque alternativo. En lugar de utilizar un delegado anónimo en este ejemplo declara un `async` controlador de eventos que se asigna como un controlador de eventos normales:

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

El método de controlador de eventos, a continuación, se define como se muestra aquí:

```csharp
async void HandleTouchUpInside (object sender, EventArgs e)
{
    ResultLabel.Text = "loading...";
    ResultTextView.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await DownloadHomepage();

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultLabel.Text = "Length: " + intResult ;
}
```

Algunos puntos importantes:

-  El método está marcado como `async` pero devuelve `void` . Esto suele hacerse únicamente para los controladores de eventos (en caso contrario, devolvería un `Task` o `Task<TResult>` ).
-  El código `await` s en el `DownloadHomepage` método directamente en una asignación a una variable ( `intResult` ) a diferencia del ejemplo anterior donde se utiliza un intermedio `Task<int>` variable para hacer referencia a la tarea.  *Esto* es la ubicación donde control se devuelve al llamador hasta que finalice el método asincrónico en otro subproceso.
-  Cuando el método asincrónico se complete y devuelve, se reanuda la ejecución en el `await` lo que significa que el resultado entero devuelto y, a continuación, se representan en un widget de interfaz de usuario.


## <a name="summary"></a>Resumen

Usar async y await simplifica en gran medida el código necesario para generar operaciones de larga duración en subprocesos en segundo plano sin bloquear el subproceso principal. También facilitan tener acceso a los resultados cuando se ha completado la tarea.

Este documento ha proporcionado una visión general de las nuevas palabras clave del lenguaje y ejemplos para Xamarin.iOS y Xamarin.Android.



## <a name="related-links"></a>Vínculos relacionados

- [AsyncAwait (ejemplo)](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [Las devoluciones de llamada como nuestro las generaciones que se vaya a la instrucción](https://tirania.org/blog/archive/2013/Aug-15.html)
- [Datos (iOS) (ejemplo)](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) (ejemplo)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) (ejemplo)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Webinar: C#Async en iOS y Android (vídeo)](http://xamarin.wistia.com/medias/k27mc627xz)
- [Programación asincrónica con Async y Await (MSDN)](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)
- [Ajustar la aplicación de Async (MSDN)](https://msdn.microsoft.com/library/vstudio/jj155761.aspx)
- [Await y la interfaz de usuario y los interbloqueos. ¡Dios mío! (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2011/01/13/10115163.aspx)
- [Tareas de procesamiento a medida que se completa (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2012/08/02/processing-tasks-as-they-complete.aspx)
- [Task-based Asynchronous Pattern (TAP)](https://msdn.microsoft.com/library/hh873175.aspx) (Modelo asincrónico basado en tareas [TAP])
- [La asincronía en C# 5 (blog de Eric Lippert) – sobre la introducción de las palabras clave](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)
