---
title: Resumen de capítulo 20. E/S de archivo y asincrónico
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen de capítulo 20. E/S de archivo y asincrónico'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: fbcf1c9cfb1cd87ea33f47ae61a8ebe233b89736
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241873"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Resumen de capítulo 20. E/S de archivo y asincrónico

 Una interfaz gráfica de usuario debe responder a eventos de entrada del usuario secuencialmente. Esto implica que todo el procesamiento de eventos de entrada de usuario debe estar en un único subproceso, a menudo denominado el *subproceso principal* o *subproceso de interfaz de usuario*.

Los usuarios esperan interfaces gráficas de usuario a la capacidad de respuesta. Esto significa que un programa debe procesar rápidamente los eventos de entrada del usuario. Si esto no es posible, procesamiento, a continuación, debe relegado a subprocesos secundarios de ejecución.

Varios programas de ejemplo de este libro han usado la [ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/) clase. En esta clase la [ `BeginGetReponse` ](https://developer.xamarin.com/api/member/System.Net.WebRequest.BeginGetResponse/p/System.AsyncCallback/System.Object/) método inicia un subproceso de trabajo, que llama a una función de devolución de llamada cuando se complete. Sin embargo, esa función de devolución de llamada se ejecuta en el subproceso de trabajo, por lo que el programa debe llamar a [ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/) método para tener acceso a la interfaz de usuario.

Un enfoque más moderno para el procesamiento asincrónico está disponible en .NET y C#. Esto implica la [ `Task` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.Task/) y [ `Task<TResult>` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.Task%3CTResult%3E/) clases y otros tipos en el [ `System.Threading` ](https://developer.xamarin.com/api/namespace/System.Threading/) y [ `System.Threading.Tasks` ](https://developer.xamarin.com/api/namespace/System.Threading.Tasks/) espacios de nombres, así como el 5.0 de C# `async` y `await` palabras clave. Eso es lo que en este capítulo se centra en.

## <a name="from-callbacks-to-await"></a>En las devoluciones de llamada para esperar

El `Page` propia clase contiene tres métodos asincrónicos para mostrar cuadros de alerta:

- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) Devuelve un `Task` objeto
- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/System.String/) Devuelve un `Task<bool>` objeto
- [`DisplayActionSheet`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet/p/System.String/System.String/System.String/System.String[]/) Devuelve un `Task<string>` objeto

El `Task` objetos indican que estos métodos implementan el basado en tareas modelo asincrónico, conocido como TAP. Estos `Task` se devuelven objetos rápidamente desde el método. El `Task<T>` devolver valores constituyen una "sugerencia" que un valor de tipo `TResult` estará disponible cuando se complete la tarea. El `Task` valor devuelto indica una acción asincrónica que le completo pero no tiene ningún valor devuelto.

En todos estos casos, la `Task` está completa cuando el usuario cierra el cuadro de alerta.  

### <a name="an-alert-with-callbacks"></a>Una alerta con las devoluciones de llamada

El [ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) ejemplo muestra cómo controlar `Task<bool>` devuelven objetos y `Device.BeginInvokeOnMainThread` llamadas utilizando métodos de devolución de llamada.

### <a name="an-alert-with-lambdas"></a>Una alerta con las expresiones lambda

El [ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) ejemplo muestra cómo utilizar las funciones lambda anónima para el control de `Task` y `Device.BeginInvokeOnMainThread` llamadas.  

### <a name="an-alert-with-await"></a>Una alerta con await

Un enfoque más sencillo implica la `async` y `await` palabras clave que se introdujo en C# 5. El [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) muestra su uso.

### <a name="an-alert-with-nothing"></a>Una alerta sin nada

Si el método asincrónico devuelve `Task` en lugar de `Task<TResult>`, a continuación, el programa no necesita usar cualquiera de estas técnicas si no es necesario saber cuando se completa la tarea asincrónica. El [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) ejemplo se muestra cómo hacerlo.

### <a name="saving-program-settings-asynchronously"></a>Guardar los ajustes del programa de forma asincrónica

El [ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) ejemplo muestra el uso de la [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/) método `Application` para guardar los ajustes del programa cuando cambien sin reemplazar el `OnSleep` método.

### <a name="a-platform-independent-timer"></a>Un temporizador de independiente de la plataforma

Es posible utilizar [ `Task.Delay` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.Delay/p/System.Int32/) para crear un temporizador independiente de la plataforma. El [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) ejemplo se muestra cómo hacerlo.

## <a name="file-inputoutput"></a>Archivo de entrada/salida

Tradicionalmente, .NET [ `System.IO` ](https://developer.xamarin.com/api/namespace/System.IO/) espacio de nombres ha sido la fuente de soporte técnico de E/S de archivos. Aunque algunos métodos de este espacio de nombres son compatibles con operaciones asincrónicas, la mayoría no. El espacio de nombres también admite varias llamadas a métodos simples que realizan funciones de E/S de archivo sofisticadas.

### <a name="good-news-and-bad-news"></a>Buenas y malas noticias

Todas las plataformas compatibles con el almacenamiento local de aplicación de soporte técnico de Xamarin.Forms &mdash; almacenamiento privado a la aplicación.

Las bibliotecas Xamarin.iOS y Xamarin.Android incluyen una versión de .NET que Xamarin expresamente se adapta para estas dos plataformas. Estos incluyen clases de `System.IO` que puede usar para realizar E/S de archivos con almacenamiento local para la aplicación en estas dos plataformas.

Sin embargo, si desea buscar estas `System.IO` clases en una PCL Xamarin.Forms, no encontrarlos. El problema es que E/S de archivos de Microsoft reformado completamente para la API de Windows en tiempo de ejecución. Los programas como destino Windows 8.1 y Windows Phone 8.1, la plataforma Universal de Windows no utilizan `System.IO` para E/S de archivo.

Esto significa que debe usar el [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) (primero se describe en [ **capítulo 9. Llamadas a la API específica de la plataforma** ](chapter09.md) para implementar la E/S de archivos.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Una primera captura en E/S de archivos entre plataformas

El [ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) ejemplo define un [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) interfaz para E/S de archivos y las implementaciones de esta interfaz en todas las plataformas. Sin embargo, las implementaciones en tiempo de ejecución de Windows no funcionan con los métodos de esta interfaz porque los métodos de E/S de archivo en tiempo de ejecución de Windows son asincrónicos.

### <a name="accommodating-windows-runtime-file-io"></a>Ajustando la E/S de archivos en tiempo de ejecución de Windows

Programas que se ejecutan en el tiempo de ejecución de Windows usan clases en el [ `Windows.Storage` ](https://msdn.microsoft.com/library/windows/apps/windows.storage.aspx) y [ `Windows.Storage.Streams` ](https://msdn.microsoft.com/library/windows/apps/windows.storage.streams.aspx) espacios de nombres para E/S de archivos, incluido el almacenamiento local de aplicación. Dado que Microsoft ha determinado que cualquier operación que requiere que más de 50 milisegundos deben ser asincrónicas para evitar el bloqueo del subproceso de interfaz de usuario, estos métodos de E/S de archivo principalmente son asincrónicos.

El código que muestra este nuevo método estará en una biblioteca para que se puede utilizar para otras aplicaciones.

## <a name="platform-specific-libraries"></a>Bibliotecas específicas de plataforma

Esto supone una mejora para almacenar el código reutilizable en bibliotecas. Esto es obviamente más difícil cuando son diferentes partes del código reutilizable para completamente diferentes sistemas operativos.

El [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solución muestra un enfoque. Esta solución contiene siete proyectos diferentes:

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), una PCL Xamarin.Forms normal
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), una biblioteca de clases de iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), una biblioteca de clase Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), una biblioteca de clases de Windows universales
- [**Xamarin.FormsBook.Platform.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Windows), una PCL para Windows 8.1.
- [**Xamarin.FormsBook.Platform.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinPhone), una PCL para Windows Phone 8.1
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), un proyecto compartido para el código que es común a todas las plataformas de Windows

Todos los proyectos de plataforma individuales (con la excepción de **Xamarin.FormsBook.Platform.WinRT**) tienen referencias a **Xamarin.FormsBook.Platform**. Los tres proyectos de Windows tienen una referencia a **Xamarin.FormsBook.Platform.WinRT**.

Todos los proyectos contienen una variable static `Toolkit.Init` método para asegurarse de que se carga la biblioteca si directamente no se hace referencia en un proyecto en una solución de aplicación de Xamarin.Forms.

El **Xamarin.FormsBook.Platform** contiene el nuevo proyecto [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) interfaz. Ahora, todos los métodos tienen nombres con `Async` sufijos y devuelven `Task` objetos.

El **Xamarin.FormsBook.Platform.WinRT** proyecto contiene el [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) clase en tiempo de ejecución de Windows.

El **Xamarin.FormsBook.Platform.iOS** proyecto contiene el [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) clase para iOS. Estos métodos ahora deben ser asincrónicos. Algunos de los métodos utilizan las versiones asincrónicas de los métodos definidos en `StreamWriter` y `StreamReader`: [ `WriteAsync` ](https://developer.xamarin.com/api/member/System.IO.StreamWriter.WriteAsync/p/System.String/) y [ `ReadToEndAsync` ](https://developer.xamarin.com/api/member/System.IO.StreamReader.ReadToEndAsync()/). Otras convierten un resultado para un `Task` objeto mediante la [ `FromResult` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.FromResult%7BTResult%7D/p/TResult/) método.

El **Xamarin.FormsBook.Platform.Android** proyecto contiene similares [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) clase para Android.

El **Xamarin.FormsBook.Platform** proyecto también contiene un [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) clase que facilita el uso de la `DependencyService` objeto.

Para usar estas bibliotecas, una solución de aplicación debe incluir todos los proyectos en el **Xamarin.FormsBook.Platform** solución y cada uno de los proyectos de aplicación deben tener una referencia a la biblioteca correspondiente en  **Xamarin.FormsBook.Platform**.

El [ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) solución muestra cómo utilizar el **Xamarin.FormsBook.Platform** bibliotecas. Cada uno de los proyectos tiene una llamada a `Toolkit.Init`. La aplicación hace uso de la E/S de archivos asincrónica funciones.

### <a name="keeping-it-in-the-background"></a>Mantener en segundo plano

Métodos de bibliotecas que realicen llamadas a varios métodos asincrónicos &mdash; como el `WriteFileAsync` y `ReadFileASync` métodos en el tiempo de ejecución de Windows [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) clase &mdash; pueden estar ligeramente más eficaz mediante el uso de la [ `ConfigureAwait` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task%3CTResult%3E.ConfigureAwait/p/System.Boolean/) método para evitar el cambio hacia el subproceso de interfaz de usuario.

### <a name="dont-block-the-ui-thread"></a>No bloquea el subproceso de interfaz de usuario.

A veces es tentador para evitar el uso de `ContinueWith` o `await` mediante el uso de la [ `Result` ](https://developer.xamarin.com/api/property/System.Threading.Tasks.Task%3CTResult%3E.Result/) propiedad en los métodos. Éste debería evitarse para que pueda bloquear el subproceso de interfaz de usuario o incluso bloquear la aplicación.

## <a name="your-own-awaitable-methods"></a>Sus propios métodos que admite await y

Puede ejecutar algún código de forma asincrónica al pasarlo a uno de los [ `Task.Run` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.Run/p/System.Action/) métodos. Puede llamar a `Task.Run` dentro de un método asincrónico que controla algunos de la sobrecarga.

Los distintos `Task.Run` patrones se describen a continuación.

### <a name="the-basic-mandelbrot-set"></a>El conjunto básico de Mandelbrot

Para dibujar el Mandelbrot establecido en tiempo real, el [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca tiene un [ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) estructura similar a la de la `System.Numerics` espacio de nombres.

El [ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) ejemplo tiene un `CalculateMandeblotAsync` método en su archivo de código subyacente que calcula el conjunto básico de Mandelbrot en blanco y negro y lo usa [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)lo coloque en un mapa de bits.

### <a name="marking-progress"></a>Marcar el progreso

Para notificar el progreso de un método asincrónico, puede crear instancias de un [ `Progress<T>` ](https://developer.xamarin.com/api/type/System.Progress%3CT%3E/) clase y defina su método asincrónico para tener un argumento de tipo [ `IProgress<T>` ](https://developer.xamarin.com/api/type/System.IProgress%3CT%3E/). Esto se muestra en el [ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) ejemplo.

### <a name="cancelling-the-job"></a>Al cancelar el trabajo

También puede escribir un método asincrónico para que sea cancelable. Comenzar con una clase denominada [ `CancellationTokenSource` ](https://developer.xamarin.com/api/type/System.Threading.CancellationTokenSource/). El [ `Token` ](https://developer.xamarin.com/api/property/System.Threading.CancellationTokenSource.Token/) propiedad es un valor de tipo [ `CancellationToken` ](https://developer.xamarin.com/api/type/System.Threading.CancellationToken/). Este argumento se pasa a la función asincrónica. Un programa llama a la [ `Cancel` ](https://developer.xamarin.com/api/member/System.Threading.CancellationTokenSource.Cancel()/) método `CancellationTokenSource` (normalmente en respuesta a una acción por el usuario) para cancelar la función asincrónica.

El método asincrónico puede comprobar periódicamente la [ `IsCancellationRequested` ](https://developer.xamarin.com/api/property/System.Threading.CancellationToken.IsCancellationRequested/) propiedad de `CancellationToken` y se cierra si se establece la propiedad `true`, o simplemente llamar a la [ `ThrowIfCancellationRequested` ](https://developer.xamarin.com/api/member/System.Threading.CancellationToken.ThrowIfCancellationRequested()/) método, en cuyo caso el método termina con un [ `OperationCancelledException` ](https://developer.xamarin.com/api/type/System.OperationCanceledException/).

El [ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) ejemplo muestra el uso de una función cancelable.

### <a name="an-mvvm-mandelbrot"></a>Un Mandelbrot MVVM

El [ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) ejemplo tiene una interfaz de usuario más amplia y se basa principalmente en un [ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) y [ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)clases:

[![Captura de pantalla triple de Mandelbrot X F](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>Volver a la web

El [ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/) clase usada en algunos ejemplos utiliza un protocolo asincrónico antigua denominado APM o modelo de programación asincrónica. Puede convertir esta clase en el protocolo TAP moderno mediante uno de los `FromAsync` métodos en el [ `TaskFactory` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.TaskFactory%3CTResult%3E/) clase. El [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) ejemplo se muestra cómo hacerlo.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 20 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Ejemplos de capítulo 20](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Working with Files (Trabajo con archivos)](~/xamarin-forms/app-fundamentals/files.md)
