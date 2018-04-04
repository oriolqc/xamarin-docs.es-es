---
title: iOS Backgrounding con tareas
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 559c8ccce0ff41dccce0fdc93d6c18a1fee3db7d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="ios-backgrounding-with-tasks"></a>iOS Backgrounding con tareas

La manera más sencilla de realizar backgrounding en iOS es divide los requisitos backgrounding en tareas y se ejecutan las tareas en segundo plano. Las tareas están en un límite de tiempo estricto así como normalmente aproximadamente 600 segundos (10 minutos) del tiempo de procesamiento después de que una aplicación se movió al fondo en iOS 6 y menos de 10 minutos en iOS 7 +.

Tareas en segundo plano se pueden dividir en tres categorías:

1.  **Tareas de seguridad de segundo plano** : se le llama en cualquier parte de la la aplicación que tiene una tarea que no desee interrumpida debe escribir la aplicación de segundo plano.
1.  **Tareas de DidEnterBackground** : se le llama durante la `DidEnterBackground` método de ciclo de vida de la aplicación para ayudar en la limpieza y el almacenamiento de estado.
1.  **(IOS 7 +) de las transferencias en segundo plano** -un tipo especial de tarea en segundo plano que se utiliza para realizar transferencias de red en iOS 7. A diferencia de las tareas habituales, transferencias en segundo plano no tiene un límite de tiempo predeterminado.


Seguridad de segundo plano y `DidEnterBackground` tareas son seguros para usar en iOS 6 y 7, con algunas diferencias menores de iOS. Vamos a investigar estos dos tipos de tareas con más detalle.

## <a name="creating-background-safe-tasks"></a>Crear tareas de seguridad de segundo plano

Algunas aplicaciones contienen las tareas que no deberían interrumpirse por iOS la aplicación debe cambiar de estado. Una manera de proteger estas tareas se interrumpan es regístrelos con iOS como tareas de larga duración. Puede usar este patrón en cualquier lugar en la aplicación donde no desea que una tarea que se interrumpa debe el put de usuario la aplicación en segundo plano. Un excelente candidato para este patrón sería tareas como enviar información de registro de un usuario nuevo al servidor o comprobar la información de inicio de sesión.

El siguiente fragmento de código muestra cómo registrar una tarea se ejecute en segundo plano:

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

El proceso de registro pares una tarea con un identificador único, `taskID`y, a continuación, se ajusta en la coincidencia de `BeginBackgroundTask` y `EndBackgroundTask` llamadas. Para generar el identificador, se hace una llamada a la `BeginBackgroundTask` método en el `UIApplication` de objetos y, a continuación, iniciar la tarea de ejecución prolongada, normalmente en un nuevo subproceso. Una vez completada la tarea, llamamos a `EndBackgroundTask` y pase el mismo identificador. Esto es importante porque iOS cerrará la aplicación si un `BeginBackgroundTask` llamada no tiene una coincidencia `EndBackgroundTask`.

> [!IMPORTANT]
> Pueden ejecutar tareas de seguridad de segundo plano en el subproceso principal o en un subproceso en segundo plano, según las necesidades de la aplicación.


## <a name="performing-tasks-during-didenterbackground"></a>Realización de tareas durante la DidEnterBackground

Además de realizar una tarea de ejecución prolongada segura para el fondo, el registro se puede utilizar para iniciar tareas como una aplicación es que se va a poner en segundo plano. iOS proporciona un método de evento en el *AppDelegate* clase denominada `DidEnterBackground` que se puede utilizar para guardar el estado de aplicación, guardar los datos de usuario y cifrar contenido confidencial antes de una aplicación entre en segundo plano. Una aplicación tiene aproximadamente cinco segundos en volver de este método u obtener terminará. Por lo tanto, pueden llamar a las tareas de limpieza que podrían tardar más de cinco segundos en completarse desde dentro de la `DidEnterBackground` método. Estas tareas se deben invocar en un subproceso independiente.

El proceso es casi idéntico del registro de una tarea de ejecución prolongada. El fragmento de código siguiente muestra esto en acción:

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Empezaremos invalidando el `DidEnterBackground` método en el `AppDelegate`, donde registramos nuestra tarea a través de `BeginBackgroundTask` como hicimos en el ejemplo anterior. A continuación, generar un nuevo subproceso y realizar la tarea de ejecución prolongada. Tenga en cuenta que la `EndBackgroundTask` es ahora realiza llamada desde dentro de la tarea de ejecución prolongada, ya que el `DidEnterBackground` ya habrá devuelto el método.

> [!IMPORTANT]
> iOS usa un [vigilante mecanismo](http://developer.apple.com/library/ios/qa/qa1693/_index.html) para asegurarse de que la interfaz de usuario de la aplicación sigue respondiendo. Una aplicación que emplea demasiado tiempo en `DidEnterBackground` dejará de responder en la interfaz de usuario. Permite que las tareas se ejecuten en segundo plano, que comenzó `DidEnterBackground` para devolver de manera oportuna, que la interfaz de usuario siga respondiendo e impide la vigilancia de la terminación de la aplicación.


## <a name="handling-background-task-time-limits"></a>Límites de tiempo de tarea de segundo plano de control

iOS coloca límites estrictos en el que puede ejecutar una tarea en segundo plano de la larga y si la `EndBackgroundTask` no se realiza la llamada en el tiempo asignado, se terminará la aplicación. Mediante el seguimiento de los restantes backgrounding tiempo y utilizar controladores de expiración cuando sea necesario, podemos evitar iOS terminar la aplicación.

### <a name="accessing-background-time-remaining"></a>Obtener acceso a la hora en segundo plano restantes

Si una aplicación con tareas registradas obtiene mueve al fondo, las tareas registradas obtendrá unos 600 segundos para que se ejecute. Podemos comprobar cuánto tiempo tiene la tarea para completarse utilizando el método estático `BackgroundTimeRemaining` propiedad de la `UIApplication` clase. El código siguiente nos proporcionará el tiempo, en segundos, que ha dejado la tarea en segundo plano:

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Evitar la terminación de la aplicación con los controladores de expiración

Además de lo que proporciona acceso a la `BackgroundTimeRemaining` propiedad, iOS proporciona una manera estable para controlar la expiración del tiempo de fondo a través de un **caducidad controlador**. Se trata de un bloque de código que se ejecutará cuando el tiempo asignado para una tarea está a punto de expirar opcional. Llama a código en el controlador de expiración `EndBackgroundTask` y pasa el identificador de tarea, lo que indica que la aplicación se comporta correctamente e impide que iOS finaliza la aplicación incluso si la tarea se ejecuta fuera del tiempo. `EndBackgroundTask` se debe llamar en el controlador de expiración, así como en el curso normal de ejecución. 

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

Aunque los controladores de expiración no son necesarios ejecutar el código, debe utilizar siempre un controlador de expiración con una tarea en segundo plano.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Tareas en segundo plano en iOS 7 +

El cambio más importante en iOS 7 con respecto a tareas en segundo plano no es cómo se implementan las tareas, pero cuando ejecuta.

Recuerde que previa iOS 7, una tarea que se ejecuta en segundo plano tenía 600 segundos en completarse. Una razón para este límite es que una tarea que se ejecuta en segundo plano mantendría el dispositivo activo para la duración de la tarea:

 [![](ios-backgrounding-with-tasks-images/ios6.png "Gráfico de la tarea de mantener la aplicación iOS previa activos 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

procesamiento en segundo plano iOS 7 está optimizado para mayor duración de la batería. En iOS 7, backgrounding se convierte en oportunista: en lugar de mantener activo el dispositivo, las tareas respetan cuando el dispositivo pasa al modo de suspensión y, en su lugar, realice su procesamiento en fragmentos cuando el dispositivo se reactiva para controlar llamadas de teléfono, notificaciones, entrante mensajes de correo electrónico y otros interrupciones comunes. El siguiente diagrama proporciona una visión general de cómo esté rota una tarea de seguridad:

 [![](ios-backgrounding-with-tasks-images/ios7.png "Gráfico de la tarea que se va a dividir en fragmentos posterior a la de iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Dado que la tarea en tiempo de ejecución no es ya continua, las tareas que realizan transferencias de red deben administrarse de manera diferente en iOS 7. Los desarrolladores pueden usar el `NSURlSession` API para controlar las transferencias de red. La siguiente sección es una visión general de transferencias en segundo plano.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Transferencias en segundo plano

La red troncal de transferencias en segundo plano en iOS 7 es el nuevo `NSURLSession` API. `NSURLSession` permite crear tareas para:

1.  Transferencia de contenido a través de las interrupciones de red y el dispositivo.
1.  Cargar y descargar archivos de gran tamaño ( *servicio de transferencia en segundo plano* ).


Echemos un vistazo más de cerca de su funcionamiento.

### <a name="nsurlsession-api"></a>NSURLSession API

 `NSURLSession` es una API eficaz para transferir contenido a través de la red. Proporciona un conjunto de herramientas para administrar a la transferencia de datos a través de las interrupciones y los cambios en los Estados de aplicación.

El `NSURLSession` API crea una o varias sesiones, que a su vez generan tareas para avanzar los bloques de datos relacionadas a través de la red. Las tareas se ejecutan de forma asincrónica para transferir datos de manera rápida y confiable. Dado que `NSURLSession` es asincrónica, cada sesión requiere un bloque de controlador de finalización para permitir que el sistema y la aplicación saber si se completa una transferencia.

Para realizar una transferencia de red que sea válida en previa iOS 7 y posterior a la de iOS 7, compruebe si un `NSURLSession` está disponible para las transferencias de poner en cola y utilizar una tarea en segundo plano regular para realizar la transferencia si no lo es:

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
> Evitar la realización de llamadas para actualizar la interfaz de usuario desde el fondo en el código de 6 compatible de iOS, iOS 6 no admite actualizaciones de la interfaz de usuario de fondo y cerrará la aplicación.


El `NSURLSession` API incluye un amplio conjunto de características para controlar la autenticación, administrar las transferencias de errores y notificar los errores de cliente - pero no en el servidor:. Ayuda a puente que las interrupciones en la tarea ejecutan hora introducido en iOS 7 y también proporciona compatibilidad para transferir archivos grandes de manera rápida y confiable. La siguiente sección explora esta característica de segundo.

### <a name="background-transfer-service"></a>Servicio de transferencia de segundo plano

Antes de iOS 7, cargar o descargar archivos en segundo plano fue no confiable. Tareas en segundo plano obtener un tiempo limitado para que se ejecute, pero el tiempo necesario para transferir un archivo varía en función de la red y el tamaño del archivo. En iOS 7, podemos usar un `NSURLSession` para cargar y descargar archivos de gran tamaño correctamente. Ese `NSURLSession` tipo de sesión que controla las transferencias de red de archivos grandes en segundo plano se conoce como el *el servicio de transferencia de fondo*.

Las transferencias que se inicia utilizando el servicio de transferencia en segundo plano administradas por el sistema operativo y proporcionan las API para controlar los errores y la autenticación. Dado que las transferencias de no estarán enlazadas por un límite de tiempo arbitrario, puede utilizar para cargar o descargar archivos de gran tamaño, contenido en el fondo y mucho más la actualización automática. Hacer referencia a la [tutorial Transfer de fondo](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) para obtener más información sobre cómo implementar el servicio.

El servicio de transferencia en segundo plano a menudo se empareja con captura de fondo o notificaciones remoto para ayudar a las aplicaciones actualizar contenido en segundo plano. En las dos secciones siguientes, se introduce el concepto de registro de aplicaciones completas para ejecutar en segundo plano en iOS 6 e iOS 7.

