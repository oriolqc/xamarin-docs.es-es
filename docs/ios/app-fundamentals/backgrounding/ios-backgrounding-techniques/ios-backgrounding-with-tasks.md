---
title: iOS procesamiento en segundo plano con tareas
description: Este documento describe cómo usar tareas en segundo plano para realizar tareas de larga ejecución después de una aplicación se coloca en segundo plano.
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 9d304ee64e7716413febc475e721f5eb39043109
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351543"
---
# <a name="ios-backgrounding-with-tasks"></a>iOS procesamiento en segundo plano con tareas

La manera más sencilla para realizar el procesamiento en segundo plano en iOS es dividir los requisitos de backgrounding en tareas y ejecutar las tareas en segundo plano. Las tareas están en un estricto límite de tiempo y suelen obtención unos 600 segundos (10 minutos) de tiempo de procesamiento después de una aplicación se movió a segundo plano en iOS 6 y menos de 10 minutos en iOS 7 o posterior.

Tareas en segundo plano pueden dividirse en tres categorías:

1.  **Tareas en segundo plano Safe** : se le llama en cualquier parte de la la aplicación donde haya una tarea que no desee interrumpida debe escribir de la aplicación de segundo plano.
1.  **Tareas de DidEnterBackground** : se le llama durante la `DidEnterBackground` método del ciclo de vida de aplicación para ayudar en la limpieza y el almacenamiento de estado.
1.  **(IOS 7 +) de las transferencias en segundo plano** : un tipo especial de tarea en segundo plano que se utiliza para realizar transferencias de red en iOS 7. A diferencia de las tareas periódicas, transferencias en segundo plano no tiene un límite de tiempo determinado previamente.


Seguridad en segundo plano y `DidEnterBackground` tareas son seguras de usar en iOS 6 e iOS 7, con algunas diferencias menores. Vamos a investigar estos dos tipos de tareas con mayor detalle.

## <a name="creating-background-safe-tasks"></a>Creación de tareas en segundo plano con seguridad

Algunas aplicaciones contienen las tareas que no deberían ser interrumpidas por iOS la aplicación debe cambiar de estado. Es una manera de proteger estas tareas se interrumpan registrarlos con iOS como tareas de ejecución prolongada. Puede usar este patrón en cualquier lugar en la aplicación donde no desea que una tarea que se interrumpan debe la put la aplicación de usuario en segundo plano. Un excelente candidato para este patrón sería tareas como enviando información de registro de un usuario nuevo a su servidor o comprobar la información de inicio de sesión.

El fragmento de código siguiente muestra cómo registrar una tarea se ejecute en segundo plano:

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

El proceso de registro de pares de una tarea con un identificador único, `taskID`y, a continuación, se encapsula en la coincidencia de `BeginBackgroundTask` y `EndBackgroundTask` llamadas. Para generar el identificador, hacemos una llamada a la `BeginBackgroundTask` método en el `UIApplication` de objetos y, a continuación, inicie la tarea de ejecución prolongada, normalmente en un nuevo subproceso. Cuando se completa la tarea, llamamos a `EndBackgroundTask` y pase el mismo identificador. Esto es importante porque iOS terminará la aplicación si un `BeginBackgroundTask` llamada no tiene una coincidencia `EndBackgroundTask`.

> [!IMPORTANT]
> Pueden ejecutar tareas en segundo plano con seguridad en el subproceso principal o en un subproceso en segundo plano, según las necesidades de la aplicación.


## <a name="performing-tasks-during-didenterbackground"></a>Realización de tareas durante DidEnterBackground

Además de realizar una tarea de ejecución prolongada en segundo plano con seguridad, registro puede utilizarse para iniciar tareas como una aplicación se coloca en segundo plano. iOS proporciona un método de evento en el *AppDelegate* clase denominada `DidEnterBackground` que puede utilizarse para guardar el estado de la aplicación, guardar los datos de usuario y cifrar contenido confidencial antes de una aplicación entre en segundo plano. Una aplicación tiene aproximadamente cinco segundos en devolver desde este método o se cancelará. Por lo tanto, las tareas de limpieza que pueden tardar más de cinco segundos en completarse pueden llamarse desde dentro de la `DidEnterBackground` método. Estas tareas se deben invocar en un subproceso independiente.

El proceso es casi idéntico de registrar una tarea de ejecución prolongada. El siguiente fragmento de código muestra esto en acción:

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Comenzamos invalidando el `DidEnterBackground` método en el `AppDelegate`, donde registramos nuestra tarea a través de `BeginBackgroundTask` como hicimos en el ejemplo anterior. A continuación, se genera un nuevo proceso y realiza la tarea de ejecución prolongada. Tenga en cuenta que el `EndBackgroundTask` es ahora realiza llamada desde dentro de la tarea de ejecución prolongada, ya que el `DidEnterBackground` ya habrá devuelto el método.

> [!IMPORTANT]
> iOS usa una [vigilante mecanismo](http://developer.apple.com/library/ios/qa/qa1693/_index.html) para asegurarse de que la interfaz de usuario de la aplicación sigue respondiendo. Una aplicación que se emplea demasiado tiempo en `DidEnterBackground` deja de responder en la interfaz de usuario. Permite iniciar las tareas se ejecuten en segundo plano `DidEnterBackground` para devolver de manera oportuna, que la interfaz de usuario siga respondiendo y evitando que el guardián de eliminación de la aplicación.


## <a name="handling-background-task-time-limits"></a>Límites de tiempo de tarea en segundo plano de control

iOS coloca límites estrictos sobre cómo se puede ejecutar una tarea en segundo plano de larga y si el `EndBackgroundTask` no se realiza la llamada en el tiempo asignado, se terminará la aplicación. Mediante el seguimiento de los restantes de tiempo de procesamiento en segundo plano y usar controladores de expiración cuando sea necesario, podemos evitar finalizar la aplicación de iOS.

### <a name="accessing-background-time-remaining"></a>Obtener acceso a fondo el tiempo restante

Si mueve una aplicación con tareas registradas para el fondo, las tareas registradas obtendrá unos 600 segundos ejecutar. Podemos comprobar cuánto tiempo tiene la tarea que finalice mediante estático `BackgroundTimeRemaining` propiedad de la `UIApplication` clase. El código siguiente nos proporcionará el tiempo, en segundos, que ha dejado la tarea en segundo plano:

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Evitar la terminación de la aplicación con los controladores de expiración

Además de lo que proporciona acceso a la `BackgroundTimeRemaining` iOS de propiedad, proporciona una forma correcta para controlar la caducidad de tiempo en segundo plano a través de un **caducidad controlador**. Se trata de un bloque de código que se ejecutará cuando el tiempo asignado para una tarea está a punto de expirar opcional. Llama código en el controlador de expiración `EndBackgroundTask` y pasa el Id. de tarea, que indica que la aplicación se comporta bien y evita que finaliza la aplicación incluso si se ejecuta la tarea agotó el tiempo de iOS. `EndBackgroundTask` debe llamarse en el controlador de expiración, así como en el curso normal de ejecución. 

El controlador de expiración se expresa como una función anónima mediante una expresión lambda, como se muestra a continuación:

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.TimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

Mientras que los controladores de expiración no son necesarios para ejecutar el código, siempre debe usar un controlador de expiración con una tarea en segundo plano.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Tareas en segundo plano en iOS 7 o posterior

El cambio más importante en iOS 7 con respecto a tareas en segundo plano no es, cómo se implementan las tareas, pero cuando se ejecutan.

Recuerde que previa iOS 7, una tarea que se ejecuta en segundo plano tenía 600 segundos en completarse. Una razón para este límite es que una tarea que se ejecuta en segundo plano mantendría el dispositivo activo para la duración de la tarea:

 [![](ios-backgrounding-with-tasks-images/ios6.png "Gráfico de la tarea de mantener la aplicación iOS previa activo 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

procesamiento en segundo plano de iOS 7 está optimizado para mayor vida útil de la batería. En iOS 7, procesamiento en segundo plano se convierte en oportunista: en lugar de mantener el dispositivo activo, respetan las tareas cuando el dispositivo entra en modo de suspensión y, en su lugar, realice su procesamiento en fragmentos cuando se activa el dispositivo para controlar las llamadas de teléfono, notificaciones, entrante correos electrónicos y otros interrupciones comunes. El diagrama siguiente proporciona información sobre cómo se puede interrumpir una tarea de copia:

 [![](ios-backgrounding-with-tasks-images/ios7.png "Gráfico de la tarea que se va a dividir en fragmentos posteriores a iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Dado que la tarea en tiempo de ejecución no es ya continua, las tareas que realizan transferencias de red deben administrarse de manera diferente en iOS 7. Se recomienda utilizar los desarrolladores la `NSURlSession` API para controlar las transferencias de red. La siguiente sección es una visión general de transferencias en segundo plano.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Transferencias en segundo plano

La red troncal de transferencias en segundo plano en iOS 7 es la nueva `NSURLSession` API. `NSURLSession` nos permite crear tareas para:

1.  Transferencia de contenido a través de las interrupciones de red y el dispositivo.
1.  Cargar y descargar archivos de gran tamaño ( *el servicio de transferencia en segundo plano* ).


Echemos un vistazo más de cerca a cómo funciona esto.

### <a name="nsurlsession-api"></a>NSURLSession API

 `NSURLSession` es una API eficaz para transferir contenido a través de la red. Proporciona un conjunto de herramientas para controlar la transferencia de datos a través de las interrupciones de red y los cambios en los Estados de la aplicación.

El `NSURLSession` API crea una o varias sesiones, que a su vez generan tareas para avanzar los bloques de datos relacionadas a través de la red. Las tareas se ejecutan de forma asincrónica para transferir datos de manera rápida y confiable. Dado que `NSURLSession` es asincrónica, cada sesión requiere un bloque de controlador de finalización para permitir que el sistema y la aplicación saber cuando se completa una transferencia.

Para realizar una transferencia de red que sea válida en Ios7 previo y posterior de iOS 7, compruebe si un `NSURLSession` está disponible para las transferencias de puesta en cola y utilizar una tarea en segundo plano regular para realizar la transferencia si no es así:

```csharp
if ([NSURLSession class]) {
  // Create a background session and enqueue transfers
}
else {
  // Start a background task and transfer directly
  // Do NOT make calls to update the UI here!
}
```

> [!IMPORTANT]
> Evite realizar llamadas para actualizar la interfaz de usuario desde un segundo plano en el código de 6 compatibles de iOS, como iOS 6 no es compatible con las actualizaciones de la interfaz de usuario en segundo plano y finalizará la aplicación.


El `NSURLSession` API incluye un amplio conjunto de características para controlar la autenticación, administrar las transferencias con errores y notificar los errores de cliente - pero no en el servidor de. Ayuda a salvar distancia que las interrupciones en la tarea ejecutan tiempo que se introdujo en iOS 7 y también proporciona compatibilidad para transferir archivos grandes de manera rápida y confiable. La siguiente sección aborda esta segunda característica.

### <a name="background-transfer-service"></a>Servicio de transferencia en segundo plano

Antes de iOS 7, cargar o descargar archivos en segundo plano no son confiables. Tareas en segundo plano obtención un tiempo limitado para ejecutarse, pero el tiempo necesario para transferir un archivo varía en función de la red y el tamaño del archivo. En iOS 7, podemos usar un `NSURLSession` para cargar y descargar archivos de gran tamaño correctamente. La instancia concreta `NSURLSession` tipo de sesión que controla las transferencias de red de archivos grandes en segundo plano se conoce como el *el servicio de transferencia en segundo plano*.

Las transferencias que se inicia mediante el servicio de transferencia en segundo plano son administradas por el sistema operativo y proporcionan las API para controlar los errores y la autenticación. Dado que no se enlazan las transferencias por un límite de tiempo arbitrario, se puede usar para cargar o descargar archivos de gran tamaño, contenido en segundo plano y mucho más la actualización automática. Hacer referencia a la [tutorial de transferencia en segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) para obtener más información sobre cómo implementar el servicio.

El servicio de transferencia en segundo plano a menudo se empareja con notificaciones remotas para ayudar a las aplicaciones actualizar contenido en segundo plano o capturar en segundo plano. En las dos secciones siguientes, se introduce el concepto de registro de aplicaciones completas para ejecutarse en segundo plano en iOS 6 e iOS 7.

