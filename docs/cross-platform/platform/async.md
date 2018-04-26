---
title: Información general de Async
description: 'La versión más reciente del lenguaje de C#: versión 5 – introducido dos nuevas palabras clave para expresar las operaciones asincrónicas: async y await. Estas palabras clave le permiten escribir código simple que utiliza la biblioteca TPL para ejecutar las operaciones de larga duración (por ejemplo, el acceso a la red) en otro subproceso y obtener acceso fácilmente a los resultados de la finalización. Las versiones más recientes de Xamarin.iOS y Xamarin.Android admiten async y await: este documento proporciona explicaciones y un ejemplo de cómo usar la nueva sintaxis con Xamarin.'
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 638e16a47d30e1d4d61fe9e7d19a0245261e5d7f
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
---
# <a name="async-support-overview"></a>Información general sobre la compatibilidad con Async

_La versión más reciente del lenguaje de C#: versión 5 – introducido dos nuevas palabras clave para expresar las operaciones asincrónicas: async y await. Estas palabras clave le permiten escribir código simple que utiliza la biblioteca TPL para ejecutar las operaciones de larga duración (por ejemplo, el acceso a la red) en otro subproceso y obtener acceso fácilmente a los resultados de la finalización. Las versiones más recientes de Xamarin.iOS y Xamarin.Android admiten async y await: este documento proporciona explicaciones y un ejemplo de cómo usar la nueva sintaxis con Xamarin._

Acepta el funcionamiento asincrónico de Xamarin se basa en la base de Mono 3.0 y actualiza el perfil de API desde el que se va a una versión preparadas para dispositivos móviles de Silverlight para ser una versión preparadas para dispositivos móviles de .NET 4.5.

## <a name="overview"></a>Información general

Este documento presenta la nueva async y await palabras clave, a continuación, recorre algunos ejemplos sencillos implementar métodos asincrónicos en Xamarin.iOS y Xamarin.Android.

Para obtener una explicación más completa de las nuevas características asincrónicas de C# 5 (incluida una gran cantidad de ejemplos y escenarios de uso diferentes), consulte la documentación de MSDN [programación asincrónica con Async y Await](http://msdn.microsoft.com/library/vstudio/hh191443.aspx).

La aplicación de ejemplo realiza una solicitud web asincrónica simple (sin bloquear el subproceso principal), a continuación, actualiza la interfaz de usuario con el html descargado y el número de caracteres.

 [![](async-images/AsyncAwait_427x368.png "La aplicación de ejemplo realiza una solicitud web asincrónica simple sin bloquear el subproceso principal, a continuación, actualiza la interfaz de usuario con el html descargado y el número de caracteres")](async-images/AsyncAwait.png#lightbox)

Acepta el funcionamiento asincrónico de Xamarin se basa en la base de Mono 3.0 y actualiza el perfil de API desde el que se va a una versión preparadas para dispositivos móviles de Silverlight para ser una versión preparadas para dispositivos móviles de .NET 4.5.

## <a name="requirements"></a>Requisitos

Características de C# 5 requieren 3.0 de Mono que se incluye en 6.4 Xamarin.iOS y Xamarin.Android 4.8. Se le pedirá actualizar Mono, Xamarin.iOS, Xamarin.Android y Xamarin.Mac para aprovechar las ventajas del mismo.

## <a name="using-async-amp-await"></a>Usar async &amp; await

 `async` y `await` son nuevas características lenguaje C# que funcionan en combinación con la biblioteca TPL para que sea fácil de escribir código multiproceso para llevar a cabo tareas de ejecución prolongada sin bloquear el subproceso principal de la aplicación.

## <a name="async"></a>async

### <a name="declaration"></a>Declaración

El `async` palabra clave se coloca en una declaración de método (o en una expresión lambda o un método anónimo) para indicar que contiene código que se puede ejecutar de forma asincrónica, Internet Explorer. no bloquea el subproceso del llamador.

Un método marcado con `async` debe contener al menos un await expresión o instrucción. Si no hay ningún `await`s están presentes en el método, a continuación, se ejecutará de forma sincrónica (igual como si hubiera ninguna `async` modificador). Esto también producirá una advertencia del compilador (pero no un error).

### <a name="return-types"></a>Tipos de valor devueltos

Un método asincrónico debe devolver un `Task`, `Task<TResult>` o `void`.

Especifique el `Task` tipo de valor devuelto si el método no devuelve cualquier otro valor.

Especifique `Task<TResult>` si el método debe devolver un valor, donde `TResult` es el tipo que se devuelve (como un `int`, por ejemplo).

El `void` devolver el tipo se usa principalmente para controladores de eventos que lo requieran. El código que llama a métodos asincrónicos que devuelven void no puede `await` en el resultado.

### <a name="parameters"></a>Parámetros

No se pueden declarar métodos asincrónicos `ref` o `out` parámetros.

## <a name="await"></a>await

El operador await se puede aplicar a una tarea dentro de un método marcado como asincrónicas. Hace que el método detener la ejecución en ese momento y espere a que finalice la tarea.

Usar await no bloquea el subproceso del llamador: en su lugar el control se devuelve al llamador. Esto significa que el subproceso que realiza la llamada no está bloqueado, por lo que para el usuario de ejemplo no se bloquearía previamente subproceso de interfaz al esperar una tarea.

Cuando se complete la tarea, el método reanuda la ejecución en el mismo punto en el código. Esto incluye devolver al ámbito de un bloque try-catch-finally try (si la hay). await no se puede usar en un bloque catch o finally.

Obtenga más información sobre [await en MSDN](http://msdn.microsoft.com/library/vstudio/hh156528.aspx).

## <a name="exception-handling"></a>Control de excepciones

Las excepciones que se producen dentro de un método asincrónico se almacenan en la tarea y se produce cuando finaliza la tarea `await`ed. Estas excepciones se pueden detectar y controlar dentro de un bloque try-catch.

## <a name="cancellation"></a>Cancelación

Los métodos asincrónicos que toman mucho tiempo en completarse deben admitir la cancelación. Normalmente, se invoca de manera cancelación:

- Un `CancellationTokenSource` se crea el objeto.
- El `CancellationTokenSource.Token` instancia se pasa a un método asincrónico cancelable.
- Se solicitó la cancelación mediante una llamada a la `CancellationTokenSource.Cancel` método.

La tarea, a continuación, se cancela y confirma la cancelación.

Para obtener más información sobre la cancelación, consulte [cómo cancelar una tarea asincrónica](http://msdn.microsoft.com/library/vstudio/jj155761.aspx) en MSDN.

## <a name="example"></a>Ejemplo

Descargue el [ejemplo de solución de Xamarin](https://developer.xamarin.com/samples/mobile/AsyncAwait/) (para iOS y Android) para ver un ejemplo de trabajo de `async` y `await` en aplicaciones móviles. El código de ejemplo se describe con más detalle en esta sección.

### <a name="writing-an-async-method"></a>Escribir un método asincrónico

El método siguiente se muestra cómo codificar un `async` método con un `await`tarea ed:

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

-  La declaración de método incluye la `async` palabra clave.
-  El tipo de valor devuelto es `Task<int>` para llamar a código puede tener acceso a la `int` valor calculado en este método.
-  La instrucción return está `return exampleInt;` que es un objeto de entero: el hecho de que el método devuelve `Task<int>` forma parte de las mejoras de lenguaje.


### <a name="calling-an-async-method-1"></a>Llamar a un método asincrónico 1

Este botón click (evento) controlador se encuentra en la aplicación de ejemplo de Android para llamar al método descrito anteriormente:

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
-  El código await en la variable sizeTask.  *Esto* es la ubicación que se suspende el método y el control se devuelve al código de llamada hasta que finaliza la tarea asincrónica en su propio subproceso.
-  Ejecución *no* pausar cuando la tarea se crea en la primera línea del método, a pesar de la tarea que se crea ahí. La palabra clave await indica la ubicación donde se pausó la ejecución.
-  Cuando finaliza la tarea asincrónica, se establece intResult y la ejecución continúa en el subproceso original desde la línea de la instrucción await.


### <a name="calling-an-async-method-2"></a>Llamar a un método asincrónico 2

En la aplicación de ejemplo de iOS en el ejemplo se escribe de forma ligeramente diferente para mostrar un enfoque alternativo. En su lugar de usar un delegado anónimo en este ejemplo declara un `async` controlador de eventos que se asigna como un controlador de eventos normales:

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

-  El método está marcado como `async` pero devuelve `void` . Esto suele hacerse únicamente para controladores de eventos (en caso contrario, devolvería un `Task` o `Task<TResult>` ).
-  El código `await` s en el `DownloadHomepage` (método) directamente en una asignación a una variable ( `intResult` ) a diferencia del ejemplo anterior donde se utilizan intermedios `Task<int>` variable para hacer referencia a la tarea.  *Esto* es la ubicación donde control se devuelve al autor de la llamada hasta que se completa el método asincrónico en otro subproceso.
-  Cuando el método asincrónico completa y devuelve, se reanuda la ejecución en el `await` es lo que significa que el resultado entero devuelto y, a continuación, se representan en un widget de interfaz de usuario.


## <a name="summary"></a>Resumen

Usar async y await simplifica en gran medida el código necesario para generar operaciones de larga duración en subprocesos en segundo plano sin bloquear el subproceso principal. También facilitan tener acceso a los resultados cuando la tarea se ha completado.

Este documento proporciona una visión general de las nuevas palabras clave de lenguaje y ejemplos para Xamarin.iOS y Xamarin.Android.



## <a name="related-links"></a>Vínculos relacionados

- [AsyncAwait (ejemplo)](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [Las devoluciones de llamada como nuestro generaciones ir a declaración](http://tirania.org/blog/archive/2013/Aug-15.html)
- [Datos (iOS) (ejemplo)](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) (ejemplo)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) (ejemplo)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Seminario Web: C# asincrónico en iOS y Android (vídeo)](http://xamarin.wistia.com/medias/k27mc627xz)
- [Programación asincrónica con Async y Await (MSDN)](http://msdn.microsoft.com/library/vstudio/hh191443.aspx)
- [Ajuste de la aplicación de Async (MSDN)](http://msdn.microsoft.com/library/vstudio/jj155761.aspx)
- [Await, interfaz de usuario y y los interbloqueos. ¡Dios mío! (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2011/01/13/10115163.aspx)
- [Las tareas de procesamiento a medida que se completa (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2012/08/02/processing-tasks-as-they-complete.aspx)
- [Modelo asincrónico basado en tareas [TAP]](http://msdn.microsoft.com/library/hh873175.aspx)
- [Asincronía en C# 5 (blog de Eric Lippert): acerca de la introducción de las palabras clave](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)
