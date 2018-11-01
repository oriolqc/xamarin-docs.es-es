---
title: Android ejecutar servicios en procesos remotos
description: Por lo general, todos los componentes de una aplicación de Android se ejecutarán en el mismo proceso. Servicios de Android son una excepción notable en que se pueden configurar para ejecutarse en sus propios procesos y compartir con otras aplicaciones, las de otros desarrolladores de Android incluidas. Esta guía describirá cómo crear y utilizar un servicio remoto Android con Xamarin.
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 8514d3b2c423e524d03a800f5f56359f3aee4b75
ms.sourcegitcommit: 650fd5813e243d67eea13c4bc76683c0f8134123
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2018
ms.locfileid: "50737198"
---
# <a name="running-android-services-in-remote-processes"></a>Android ejecutar servicios en procesos remotos

_Por lo general, todos los componentes de una aplicación de Android se ejecutarán en el mismo proceso. Servicios de Android son una excepción notable en que se pueden configurar para ejecutarse en sus propios procesos y compartir con otras aplicaciones, las de otros desarrolladores de Android incluidas. Esta guía describirá cómo crear y utilizar un servicio remoto Android con Xamarin._

## <a name="out-of-process-services-overview"></a>Fuera de la información general de servicios de proceso

Cuando se inicia una aplicación, Android crea un proceso en el que se va a ejecutar la aplicación. Por lo general, todos los componentes de la aplicación se ejecutará en este uno proceso. Servicios de Android son una excepción notable en que se pueden configurar para ejecutarse en sus propios procesos y compartir con otras aplicaciones, las de otros desarrolladores de Android incluidas. Estos tipos de servicios se conocen como _servicios remotos_ o _servicios fuera de proceso_. El código para estos servicios se incluirá en el mismo APK que la aplicación principal; Sin embargo, cuando se inicia el servicio de Android creará un nuevo proceso para solo ese servicio. En cambio, un servicio que se ejecuta en el mismo proceso que el resto de la aplicación se denomina a veces un _servicio local_.

En general, no es necesario para una aplicación implementar un servicio remoto. Un servicio local es suficiente (y deseable) para conocer los requisitos de una aplicación en muchos casos. Un fuera de proceso tiene su propio espacio de memoria que debe estar administrado por Android. Aunque esto introduce más sobrecarga a la aplicación general, hay algunos escenarios donde puede ser conveniente para ejecutar un servicio en su propio proceso:

1. **Compartir la funcionalidad** &ndash; algunos desarrolladores de aplicaciones pueden tener varias aplicaciones y funcionalidad que se comparte entre todas las aplicaciones. Empaquetar esa funcionalidad en un servicio de Android que se ejecuta en su propio proceso puede simplificar el mantenimiento de la aplicación. También es posible empaquetar el servicio en su propio APK independiente e implementarlo por separado del resto de la aplicación.
2. **Mejorar la experiencia del usuario** &ndash; hay dos formas posibles de que un servicio fuera de proceso puede mejorar la experiencia del usuario de la aplicación. La primera manera relacionado con la administración de memoria. Cuando una colección de elementos no utilizados (GC) se produce ciclo Android pausará todas las actividades en el proceso hasta que se complete el GC. El usuario puede percibir esta pausa como "entrecortarse" o "jank". Cuando se ejecuta un servicio en es propio proceso, es el proceso de servicio que está en pausa, no el proceso de aplicación. Esta pausa será mucho menos evidente para el usuario ya no está en pausa el proceso de aplicación (y, por tanto, la interfaz de usuario).

    En segundo lugar, si los requisitos de memoria de un proceso es demasiado grande, Android puede terminar ese proceso para liberar recursos para el dispositivo. Si un servicio tiene una superficie de memoria grande y se ejecuta en el mismo proceso que la interfaz de usuario, a continuación, cuando Android forzosamente reclama los recursos de la interfaz de usuario se apagará, hace que el usuario para iniciar la aplicación. Sin embargo, si un servicio, que se ejecuta en su propio proceso se cierra por Android, el proceso de interfaz de usuario queda intacto. La interfaz de usuario puede enlazar (y reinicie) el servicio, de forma transparente para el usuario y reanudar el funcionamiento normal.

3. **Mejorar el rendimiento de la aplicación** &ndash; el proceso de la interfaz de usuario puede finalizar o apagar independiente del proceso del servicio. Al mover las tareas de inicio largos a un servicio fuera de proceso, es posible que el tiempo de inicio de la interfaz de usuario mejorado quizás (suponiendo que el proceso del servicio se mantiene activo entre las horas que se inicia la interfaz de usuario).

En muchos sentidos, enlace a un servicio que se ejecuta en otro proceso es el mismo que [enlazar a un servicio local](~/android/app-fundamentals/services/creating-a-service/bound-services.md). El cliente invocará `BindService` para enlazar (e iniciar, si es necesario) el servicio. Un `Android.OS.IServiceConnection` se creará el objeto para administrar la conexión entre el cliente y el servicio. Si el cliente correctamente se enlaza con el servicio, Android, devolverá un objeto a través de la `IServiceConnection` que puede usarse para invocar métodos en el servicio. El cliente interactúa con el servicio con el objeto. Para revisar, estos son los pasos para enlazar a un servicio:

* **Cree una intención** &ndash; una intención explícita debe usarse para enlazar con el servicio.
* **Implemente y Instantiate un `IServiceConnection` objeto** &ndash; el `IServiceConnection` objeto actúa como intermediario entre el cliente y el servicio.  Es responsable de supervisar la conexión entre cliente y servidor.
* **Invocar el `BindService` método** &ndash; llamada `BindService` enviará la intención y la conexión de servicio creada en los pasos anteriores para Android, que se encargará de iniciar el servicio y establecer la comunicación entre cliente y servicio.

La necesidad de cruzar los límites del proceso introduce una complejidad adicional: la comunicación es unidireccional (cliente a servidor) y el cliente no puede invocar directamente los métodos de la clase de servicio. Recuerde que cuando un servicio se está ejecutando el mismo proceso que el cliente, Android proporciona un `IBinder` objeto que puede permitir la comunicación bidireccional. Esto no es el caso con el servicio que se ejecuta en su propio proceso. Un cliente se comunica con un servicio remoto con la Ayuda de la `Android.OS.Messenger` clase.

Cuando un cliente solicita para enlazar con el servicio remoto, Android invocará el `Service.OnBind` método del ciclo de vida, lo cual devolverá interno `IBinder` objeto encapsulado por el `Messenger`. El `Messenger` es un contenedor fino sobre un especial `IBinder` implementación proporcionada por el SDK de Android. El `Messenger` se encarga de la comunicación entre los dos procesos diferentes. El desarrollador es indiferente con los detalles de serializar un mensaje, el mensaje de cálculo de referencias a través del límite de proceso y, a continuación, se deserializa en el cliente. Este trabajo se controla mediante el `Messenger` objeto. Este diagrama muestra los componentes de cliente Android que intervienen cuando un cliente inicia el enlace a un servicio fuera de proceso:

![Diagrama que muestra los componentes y pasos para un enlace a un servicio de cliente](out-of-process-services-images/ipc-01.png "diagrama que muestra los componentes y pasos para un enlace a un servicio de cliente.")

La `Service` clase en el proceso remoto pasará a través de las devoluciones de llamada del ciclo de vida mismo recorrerá un servicio en un proceso local enlazado y muchas de las API implicadas son los mismos. `Service.OnCreate` se usa para inicializar un `Handler` e inyectar a `Messenger` objeto. Del mismo modo, `OnBind` es invalidado, pero en lugar de devolver un `IBinder` objeto, el servicio devolverá el `Messenger`.  Este diagrama muestra lo que sucede en el servicio cuando un cliente se enlaza a ella:

![Diagrama que muestra los pasos y los componentes de un servicio se pasa cuando se enlaza a un cliente remoto](out-of-process-services-images/ipc-02.png "recorre diagrama que muestra los pasos y los componentes de un servicio al que se enlaza a un cliente remoto.")

Cuando un `Message` es recibido por un servicio, lo procesa en la instancia de `Android.OS.Handler`. El servicio va a implementar su propio `Handler` subclase que debe invalidar el `HandleMessage` método. Este método es invocado por el `Messenger` y recibe el `Message` como un parámetro. El `Handler` inspeccionará el `Message` metadatos y utilizar esa información para invocar métodos en el servicio.

Comunicación unidireccional se produce cuando un cliente crea un `Message` objeto y lo envía al servicio utilizando el `Messenger.Send` método. `Messenger.Send` se serializará el `Message` y entregue datos serializados desactivado para Android, que se enrutará el mensaje a través del límite de proceso y el servicio.  El `Messenger` hospedada por el servicio creará un `Message` objeto de los datos entrantes. Esto `Message` se coloca en una cola, donde los mensajes son uno enviado a la vez a la `Handler`. El `Handler` inspeccionará los metadatos contenidos en el `Message` e invocar los métodos adecuados en el `Service`. El siguiente diagrama ilustra estos distintos conceptos en acción:

![Diagrama que muestra cómo se pasan mensajes entre procesos](out-of-process-services-images/ipc-03.png "diagrama que muestra cómo se pasan mensajes entre procesos.")

Esta guía tratan los detalles de la implementación de un servicio fuera de proceso. Veremos cómo implementar un servicio que está pensado para ejecutarse en su propio proceso y cómo un cliente puede comunicarse con ese servicio mediante el `Messenger` framework. En ella se describe también brevemente una comunicación bidireccional: el cliente que envía un mensaje a un servicio y el servicio envía un mensaje al cliente. Dado que los servicios se pueden compartir entre diferentes aplicaciones, esta guía describirá también una técnica para limitar el acceso de cliente al servicio mediante el uso de los permisos de Android.

> [!IMPORTANT]
> [Bugzilla 1950 51940/GitHub - servicios con procesos aislados y clase de aplicación personalizada no se resuelve correctamente sobrecargas](https://github.com/xamarin/xamarin-android/issues/1950) informes que un servicio de Xamarin.Android no se iniciará correctamente cuando el `IsolatedProcess` está establecido en `true`. Esta guía se proporciona como una referencia. Una aplicación de Xamarin.Android todavía debe ser capaz de comunicarse con un servicio de fuera de proceso que está escrito en Java.

## <a name="requirements"></a>Requisitos

Esta guía asume que está familiarizado con la creación de servicios.

Aunque es posible usar intenciones implícitas con las aplicaciones que tienen como destino anterior API de Android, esta guía se centrará exclusivamente en el uso de intenciones explícitas. Aplicaciones dirigidas a Android 5.0 (API nivel 21) o superior debe usar una intención explícita para enlazar con un servicio. Ésta es la técnica que se mostrarán en esta guía...

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Crear un servicio que se ejecuta en un proceso independiente

Como se describió anteriormente, el hecho de que un servicio se está ejecutando en su propio proceso significa que algunas API diferentes está implicado. Como una introducción rápida, estos son los pasos para enlazar con y consumir un servicio remoto:  

* **Crear el `Service` subclase** &ndash; subclase la `Service` escribir e implementar los métodos del ciclo de vida de un servicio dependiente. También es necesario establecer los metadatos que le informará de Android que el servicio se ejecuta en su propio proceso.
* **Implemente un `Handler`**  &ndash; el `Handler` es responsable de analizar las solicitudes de cliente, los parámetros que se han pasado desde el cliente de extracción e invocar los métodos adecuados en el servicio.
* **Crear una instancia de un `Messenger`**  &ndash; como se describió anteriormente, cada uno `Service` debe mantener una instancia de la `Messenger` clase que se usará para enrutar las solicitudes de cliente para el `Handler` que se creó en el paso anterior.

Un servicio que está pensado para ejecutarse en su propio proceso es, básicamente, siendo un servicio dependiente. La clase de servicio extenderá la base de `Service` clase y se decora con el `ServiceAttribute` que contiene los metadatos que necesita de Android para incluir en el manifiesto de Android. Para comenzar con las siguientes propiedades de la `ServiceAttribute` que son importantes para un servicio fuera de proceso:

1. `Exported` &ndash; Esta propiedad debe establecerse en `true` para permitir que otras aplicaciones interactúen con el servicio. El valor predeterminado de esta propiedad es `false`.
2. `Process` &ndash; Esta propiedad debe establecerse. Sirve para especificar el nombre del proceso que el servicio se ejecutará en.
3. `IsolatedProcess` &ndash; Esta propiedad habilitará la seguridad adicional, que le indica a Android para ejecutar el servicio en un espacio aislado con el permiso mínimo para iteract con el resto del sistema. Consulte [Bugzilla 51940 - servicios con procesos aislados y clase de aplicación personalizada no se pudo resolver correctamente las sobrecargas](https://bugzilla.xamarin.com/show_bug.cgi?id=51940).
4. `Permission` &ndash; Es posible controlar el acceso de cliente al servicio mediante la especificación de un permiso que los clientes deben solicitar (y recibir).

Para ejecutar un servicio en su propio proceso, el `Process` propiedad en el `ServiceAttribute` debe establecerse en el nombre del servicio. Para interactuar con las aplicaciones externas, la `Exported` propiedad debe establecerse en `true`. Si `Exported` es `false`, solo los clientes en el mismo APK (es decir, la misma aplicación) y en ejecución en el mismo proceso podrán interactuar con el servicio.

¿Qué tipo de proceso que el servicio se ejecutará en depende del valor de la `Process` propiedad. Android identifica tres tipos diferentes de procesos:

-   **Proceso privado** &ndash; un proceso privado es aquel que solo está disponible para la aplicación que fue iniciado. Para identificar un proceso como privado, su nombre debe comenzar con un **:** (coma). El servicio que se muestra en el fragmento de código anterior y la captura de pantalla es un proceso privado. El fragmento de código siguiente es un ejemplo de la `ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **Proceso global** &ndash; un servicio que se ejecuta en un proceso global es accesible para todas las aplicaciones que se ejecutan en el dispositivo. Un proceso global debe ser un nombre de clase completo que comienza con un carácter en minúscula.
    (A menos que se tomen medidas para proteger el servicio, otras aplicaciones pueden enlazar e interactuar con él. Proteger el servicio frente al uso no autorizado se tratará más adelante en esta guía.)

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **Aislamiento de proceso** &ndash; un proceso aislado es un proceso que se ejecuta en su propio espacio aislado, aislado del resto del sistema y sin ningún permiso especial de su propio. Para ejecutar un servicio en un proceso aislado, el `IsolatedProcess` propiedad de la `ServiceAttribute` está establecido en `true` tal como se muestra en este fragmento de código:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Consulte [Bugzilla 51940 - servicios con procesos aislados y clase de aplicación personalizada no se pudo resolver las sobrecargas correctamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

Un servicio aislado es una manera sencilla de proteger una aplicación y el dispositivo frente a código no seguro. Por ejemplo, una aplicación puede descargar y ejecutar un script desde un sitio Web. En este caso, proporciona una capa adicional de seguridad frente a código no seguro, poner en peligro el dispositivo Android a realizar en un proceso aislado.

> [!IMPORTANT]
> Una vez que se ha exportado un servicio, no debe cambiar el nombre del servicio. Cambiar el nombre del servicio puede interrumpir otras aplicaciones que usan el servicio.

Para ver el efecto que el `Process` tiene la propiedad, la captura de pantalla siguiente muestra un servicio que se ejecuta en su propio proceso privada:

![Captura de pantalla que muestra un servicio que se ejecuta en un proceso privado](out-of-process-services-images/ipc-04.png "captura de pantalla que muestra un servicio que se ejecuta en un proceso privado.")

Esta captura de pantalla siguiente muestra `Process="com.xamarin.xample.messengerservice.timestampservice_process"` y el servicio se ejecuta en un proceso global:

![Captura de pantalla de un servicio que se ejecuta en un proceso global](out-of-process-services-images/ipc-05.png "captura de pantalla de un servicio que se ejecuta en un proceso global.")

Una vez el `ServiceAttribute` se ha establecido, las necesidades de servicio para implementar un `Handler`.

### <a name="implementing-a-handler"></a>Implementación de un controlador

Para procesar las solicitudes de cliente, el servicio debe implementar un `Handler` e invalidar la `HandleMessage` methodThis es el método toma un `Message` de instancia que encapsula la llamada al método desde el cliente y convierte esa llamada en alguna acción o tarea que se llevará a cabo el servicio. El `Message` objeto expone una propiedad denominada `What` que es un valor entero, el significado de los cuales se comparte entre el cliente y el servicio y se relaciona con alguna tarea que el servicio debe realizar para el cliente.

El siguiente fragmento de código de la aplicación de ejemplo muestra un ejemplo de `HandleMessage`. En este ejemplo, hay dos acciones que puede solicitar un cliente del servicio:

* La primera acción es un _Hello, World_ mensaje, el cliente ha enviado un mensaje simple al servicio.
* La segunda acción se invoca un método en el servicio y recuperar una cadena, en este caso, la cadena es un mensaje que devuelve el tiempo en los que el servicio que se inició y cuánto tiempo lleva ejecutándose:

```csharp
public class TimestampRequestHandler : Android.OS.Handler
{
    // other code omitted for clarity

    public override void HandleMessage(Message msg)
    {
        int messageType = msg.What;
        Log.Debug(TAG, $"Message type: {messageType}.");

        switch (messageType)
        {
            case Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE:
                // The client as sent a simple Hello, say in the Android Log.
                break;

            case Constants.GET_UTC_TIMESTAMP:
                // Call methods on the service to retrive a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

También es posible a los parámetros de paquete para el servicio en la `Message`. Esto se explicará más adelante en esta guía. Está creando el tema siguiente a tener en cuenta la `Messenger` objeto para procesar la entrada `Message`s.

### <a name="instantiating-the-messenger"></a>Crear instancias de Messenger

Como se explicó anteriormente, al deserializar el `Message` objeto e invoca `Handler.HandleMessage` es la responsabilidad de la `Messenger` objeto. El `Messenger` clase también proporciona un `IBinder` objeto que el cliente usará para enviar mensajes al servicio.  

Cuando se inicia el servicio, creará una instancia de la `Messenger` e insertar el `Handler`. Un buen lugar para realizar esta inicialización se encuentra en la `OnCreate` método del servicio. Este fragmento de código es un ejemplo de un servicio que inicializa su propia `Handler` y `Messenger`:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

En este momento, el paso final es el `Service` reemplazar `OnBind`.

### <a name="implementing-serviceonbind"></a>Implementar Service.OnBind

Todos los servicios dependientes, tanto si se ejecutan en su propio proceso o no, deben implementar la `OnBind` método. El valor devuelto de este método es un objeto que el cliente puede usar para interactuar con el servicio. ¿Qué es ese objeto depende de si el servicio es un servicio local o un servicio remoto. Mientras que un servicio local devolverá un personalizado `IBinder` implementación, un servicio remoto devolverá el `IBinder` que está encapsulado pero el `Messenger` que se creó en la sección anterior:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Una vez que se llevan a cabo estos tres pasos, el servicio remoto puede considerarse como completado.

## <a name="consuming-the-service"></a>Consumo del servicio

Todos los clientes deben implementar algo de código para poder enlazar y consumir el servicio remoto. Conceptualmente, desde el punto de vista del cliente, hay muy pocas diferencias entre el enlace de servicio local o un servicio remoto. El cliente invoca el `BindService` método, pasando una intención explícita para identificar el servicio y un `IServiceConnection` que ayuda a administrar la conexión entre el cliente y el servicio.

Este fragmento de código es un ejemplo de cómo crear un **intención explícita** para enlazarlo a un servicio remoto. La intención debe identificar el paquete que contiene el servicio y el nombre del servicio. Una manera de definir esta información es usar un `Android.Content.ComponentName` objeto y que lo proporcione a la intención. Este fragmento de código es un ejemplo:  

```csharp
// This is the package name of the APK, set in the Android manifest
const string REMOTE_SERVICE_COMPONENT_NAME = "com.xamarin.TimestampService";
// This is the name of the service, according the value of ServiceAttribute.Name
const string REMOTE_SERVICE_PACKAGE_NAME   = "com.xamarin.xample.messengerservice";

// Provide the package name and the name of the service with a ComponentName object.
ComponentName cn = new ComponentName(REMOTE_SERVICE_PACKAGE_NAME, REMOTE_SERVICE_COMPONENT_NAME);
Intent serviceToStart = new Intent();
serviceToStart.SetComponent(cn);
```

Cuando se enlaza el servicio, el `IServiceConnection.OnServiceConnected` método se invoca y proporciona un `IBinder` a un cliente. Sin embargo, el cliente no utilizarán directamente el `IBinder`. En su lugar, creará una instancia de un `Messenger` objeto desde el que `IBinder`. Se trata de la `Messenger` que el cliente usará para interactuar con el servicio remoto.

El siguiente es un ejemplo de una forma muy básica `IServiceConnection` implementación que se muestra cómo un cliente controlaría la conexión y desconexión de un servicio. Tenga en cuenta que el `OnServiceConnected` método recibe y `IBinder`, y el cliente crea un `Messenger` desde que `IBinder`:

```csharp
public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection
{
    static readonly string TAG = typeof(TimestampServiceConnection).FullName;

    MainActivity mainActivity;
    Messenger messenger;

    public TimestampServiceConnection(MainActivity activity)
    {
        IsConnected = false;
        mainActivity = activity;
    }

    public bool IsConnected { get; private set; }
    public Messenger Messenger { get; private set; }

    public void OnServiceConnected(ComponentName name, IBinder service)
    {
        Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

        IsConnected = service != null
        Messenger = new Messenger(service);

        if (IsConnected)
        {
            // things to do when the connection is successful. perhaps notify the client? enable UI features?
        }
        else
        {
            // things to do when the connection isn't successful.
        }
    }

    public void OnServiceDisconnected(ComponentName name)
    {
        Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
        IsConnected = false;
        Messenger = null;

        // Things to do when the service disconnects. perhaps notify the client? disable UI features?

    }
}
```

Una vez que se crean la conexión de servicio y la intención, es posible que el cliente llame a `BindService` e iniciar el proceso de enlace:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Después de que el cliente ha enlazado correctamente al servicio y el `Messenger` está disponible, es posible que el cliente envíe `Messages` al servicio.

## <a name="sending-messages-to-the-service"></a>Envío de mensajes al servicio

Una vez que el cliente está conectado y tiene un `Messenger` objeto, es posible comunicarse con el servicio al distribuirse `Message` objetos a través de la `Messenger`. Esta comunicación es unidireccional, el cliente envía el mensaje pero no hay ningún mensaje de vuelta desde el servicio al cliente. En este sentido, el `Message` es un mecanismo de fire and forget.

La mejor manera de crear un `Message` objeto consiste en usar el [ `Message.Obtain` ](https://developer.xamarin.com/api/type/Android.OS.Message/#Public%20Methods) método de fábrica. Este método se extraerá un `Message` objeto a partir de un grupo global que se mantiene por Android. `Message.Obtain` También tiene algunos métodos sobrecargados que permiten la `Message` objetos se inicialicen con los valores y los parámetros requeridos por el servicio.  Una vez el `Message` es crear una instancia, envía al servicio mediante una llamada a `Messenger.Send`. Este fragmento de código es un ejemplo de creación y envío de un `Message` al proceso del servicio:

```csharp
Message msg = Message.Obtain(null, Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE);
try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a error trying to send the message.");
}
```

Existen varias formas diferentes de la `Message.Obtain` método. El ejemplo anterior utiliza la [ `Message.Obtain(Handler h, Int32 what)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/). Se trata de una solicitud asincrónica a un servicio fuera de proceso; no habrá ninguna respuesta del servicio, por lo que la `Handler` está establecido en `null`. El segundo parámetro, `Int32 what`, se almacenará en el `.What` propiedad de la `Message` objeto. El `.What` propiedad se utiliza por el código en el proceso del servicio para invocar métodos en el servicio.

El `Message` clase también expone dos propiedades adicionales que pueden resultar útiles para el nombre: `Arg1` y `Arg2`. Estas dos propiedades son valores enteros que pueden tener algunos especial acordados por los valores que tienen un significado entre el cliente y el servicio. Por ejemplo, `Arg1` puede contener un identificador de cliente y `Arg2` puede contener un número de pedido de compra para ese cliente. El [ `Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/System.Int32/System.Int32/) puede usarse para establecer las dos propiedades cuando el `Message` se crea. Es otra manera de rellenar estos dos valores establecer el `.Arg` y `.Arg2` propiedades directamente en el `Message` objeto después de que se ha creado.

### <a name="passing-additional-values-to-the-service"></a>Pasar valores adicionales para el servicio

Es posible pasar los datos más complejos al servicio mediante el uso de un `Bundle`. En este caso, se pueden colocar valores adicionales en un `Bundle` y se envía junto con el `Message` estableciendo el [ `.Data` propiedad](https://developer.xamarin.com/api/property/Android.OS.Message.Data/) propiedad antes de enviar.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> En general, un `Message` no debe tener una carga mayor de 1 MB. El límite de tamaño puede variar según la versión de Android y en cualquier cambio propietario es posible que ha realizado el proveedor para su implementación de la Android Abrir origen de proyecto (AOSP) que se incluye con el dispositivo.

## <a name="returning-values-from-the-service"></a>Devolver valores desde el servicio

La arquitectura de mensajería que se ha explicado en este punto es unidireccional, el cliente envía un mensaje al servicio. Si es necesario para el servicio devolver un valor a un cliente, a continuación, se revierte todo lo que se ha explicado en este punto. El servicio debe crear un `Message`y empaquetadas los valores devueltos y envío el `Message` a través de un `Messenger` al cliente. Sin embargo, el servicio no crea su propio `Messenger`; en su lugar, se basa en la creación de instancias de cliente y el paquete un `Messenger` como parte de la solicitud inicial. El servicio `Send` el mensaje mediante este proporcionadas por el cliente `Messenger`.  

La secuencia de eventos para la comunicación bidireccional es el siguiente:

1. El cliente se enlaza con el servicio. Cuando el servicio y el cliente se conectan, la `IServiceConnection` mantenido por el cliente tendrá una referencia a un `Messenger` objeto que se usa para transmitir `Message`s para el servicio. Para evitar confusiones, esto se hará referencia a como el _servicio Messenger_.
2. Crea una instancia de cliente un `Handler` (conoce como el _controlador cliente_) y que se utiliza para inicializar su propio `Messenger` (el _cliente Messenger_). Observe que el servicio Messenger y Messenger cliente son dos objetos diferentes que controlan el tráfico en dos direcciones distintas. Servicio Messenger controla los mensajes desde el cliente al servicio, mientras que el cliente de Messenger controlará los mensajes desde el servicio al cliente.
3. El cliente crea un `Message` objeto y establece el `ReplyTo` propiedad con el cliente de Messenger. A continuación, se envía el mensaje al servicio mediante el servicio Messenger.
4. El servicio recibe el mensaje del cliente y realiza el trabajo solicitado.
5. Cuando se trata del servicio enviar la respuesta al cliente, utilizará `Message.Obtain` para crear un nuevo `Message` objeto.
6. Para enviar este mensaje al cliente, el servicio extraerá Messenger cliente desde el `.ReplyTo` propiedad del cliente de mensaje y usarlo para `.Send` el `Message` al cliente.
7. Cuando el cliente recibe la respuesta, tiene su propio `Handler` que procesará el `Message` inspeccionando el `.What` propiedad (y si es necesario, extraer los parámetros contenidos en el `Message`).

Este código de ejemplo muestra cómo se creará una instancia del cliente la `Message` y empaquetar un `Messenger` que debe usar el servicio para su respuesta:

```csharp
Handler clientHandler = new ActivityHandler();
Messenger clientMessenger = new Messenger(activityHandler);

Message msg = Message.Obtain(null, Constants.GET_UTC_TIMESTAMP);
msg.ReplyTo = clientMessenger;

try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a problem sending the message.");
}
```

El servicio debe realizar algunos cambios en su propio `Handler` para extraer el `Messenger` y usarlo para enviar respuestas al cliente. Este fragmento de código es un ejemplo de cómo el servicio `Handler` crearía un `Message` y enviarlo al cliente:  

```csharp
// This is the message that the service will send to the client.
Message responseMessage = Message.Obtain(null, Constants.RESPONSE_TO_SERVICE);
Bundle dataToReturn = new Bundle();
dataToReturn.PutString(Constants.RESPONSE_MESSAGE_KEY, "This is the result from the service.");
responseMessage.Data = dataToReturn;

// The msg object here is the message that was received by the service. The service will not instantiate a client,
// It will use the client that is encapsulated by the message from the client.
Messenger clientMessenger = msg.ReplyTo;
if (clientMessenger!= null)
{
    try
    {
        clientMessenger.Send(responseMessage);
    }
    catch (Exception ex)
    {
        Log.Error(TAG, ex, "There was a problem sending the message.");
    }
}
```

Tenga en cuenta que en los ejemplos de código anteriores, el `Messenger` instancia creada por el cliente es *no* el mismo objeto que es recibido por el servicio. Estos son dos diferentes `Messenger` objetos que se ejecutan en dos procesos separados que representan el canal de comunicación.

## <a name="securing-the-service-with-android-permissions"></a>Proteger el servicio con permisos de Android

Un servicio que se ejecuta en un proceso global es accesible para todas las aplicaciones que se ejecutan en el que el dispositivo Android. En algunas situaciones, esta versatilidad y la disponibilidad no es deseable, y es necesario proteger el servicio contra el acceso desde clientes no autorizados. Una forma de limitar el acceso al servicio remoto es usar los permisos de Android.

Los permisos se pueden identificar por el `Permission` propiedad de la `ServiceAttribute` que decora el `Service` subclase. Esto asignará nombre un permiso que el cliente debe concederse al enlazar con el servicio. Si el cliente no tiene los permisos adecuados, Android producirá un `Java.Lang.SecurityException` cuando el cliente intenta enlazar con el servicio.

Hay cuatro niveles de permisos diferente que ofrece Android:

* **normal** &ndash; es el nivel de permiso predeterminado. Sirve para identificar los permisos de bajo riesgo que pueden concederse automáticamente por Android a los clientes que lo solicitan. El usuario no tiene que conceder explícitamente estos permisos, pero se pueden ver los permisos en la configuración de la aplicación.
* **firma** &ndash; se trata de una categoría especial de permiso que se concederá automáticamente por Android a las aplicaciones firmadas con el mismo certificado. Este permiso está diseñado para que sea fácil para un desarrollador de aplicaciones compartir datos entre sus aplicaciones sin molestar al usuario las aprobaciones de constantes o los componentes.
* **signatureOrSystem** &ndash; esto es muy similar a la **firma** permisos describen anteriormente. Además de ser automáticamente concedidos a las aplicaciones que están firmadas por el mismo certificado, este permiso también se concederá a las aplicaciones que se firman el mismo certificado que se usó para firmar las aplicaciones se instala con la imagen del sistema Android. Este permiso normalmente se usa solo los desarrolladores de Android ROM para permitir que sus aplicaciones funcionen con las aplicaciones de terceros. No se utiliza comúnmente por aplicaciones diseñadas generales de distribución para el público en general.
* **peligroso** &ndash; permisos peligrosos son aquellos que podría causar problemas para el usuario. Por este motivo, **peligroso** permisos deben ser aprobados explícitamente por el usuario.

Dado que `signature` y `normal` automáticamente concede permisos en tiempo de instalado Android, es fundamental que hospeda el servicio APK instalarse **antes** el APK que contiene el cliente. Si el cliente se instala en primer lugar, Android no concederá los permisos. En este caso, será necesario desinstalar al cliente de APK, instalar el servicio de APK y, a continuación, vuelva a instalar al cliente APK.

Hay dos formas habituales de proteger un servicio con permisos de Android:

1.  **Implementar la seguridad de nivel de firma** &ndash; seguridad de nivel de firma significa que permiso automáticamente es conceder a las aplicaciones que se firman con la misma clave que se usó para firmar el APK que contiene el servicio. Se trata de una manera sencilla para los desarrolladores a proteger su servicio aún mantenerlos accesibles desde sus propias aplicaciones. Se declaran permisos de nivel de firma estableciendo el `Permission` propiedad de la `ServiceAttribute` a `signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **Crear un permiso personalizado** &ndash; es posible que el desarrollador del servicio crear un permiso personalizado para el servicio. Esto es mejor cuando un desarrollador desea compartir su servicio con aplicaciones de otros desarrolladores. Un permiso personalizado requiere un mayor esfuerzo para implementar y se explicará a continuación.

Un ejemplo simplificado de creación personalizada `normal` permiso se describirá en la sección siguiente. Para obtener más información acerca de los permisos de Android, consulte la documentación de Google para [prácticas recomendadas de seguridad](https://developer.android.com/training/articles/security-tips.html). Para obtener más información acerca de los permisos de Android, consulte el [sección permisos](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) de la documentación de Android para el manifiesto de aplicación para obtener más información acerca de los permisos de Android.

> [!NOTE]
> En general, [Google desaconseja el uso de permisos personalizados](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) ya que pueden resultar confusos para los usuarios.

### <a name="creating-a-custom-permission"></a>Creación de un permiso personalizado

Para utilizar un permiso personalizado, se declara el servicio mientras el cliente solicita explícitamente ese permiso.

Para crear un permiso en el servicio de APK, con un `permission` elemento se agrega a la `manifest` elemento **AndroidManifest.xml**. Este permiso debe tener la `name`, `protectionLevel`, y `label` conjunto de atributos. El `name` atributo debe establecerse en una cadena que identifica el permiso. El nombre se mostrará en el **App Info** ver de la **configuración de Android** (como se muestra en la sección siguiente).

El `protectionLevel` atributo debe establecerse en uno de los valores de cadena de cuatro que se describieron anteriormente.  El `label` y `description` debe hacer referencia a recursos de cadena y se usan para proporcionar un nombre descriptivo y una descripción para el usuario.

Este fragmento de código es un ejemplo de cómo declarar un personalizado `permission` atributo **AndroidManifest.xml** del APK que contiene el servicio:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerservice">

    <uses-sdk android:minSdkVersion="21" />

    <permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP"
                android:protectionLevel="signature"
                android:label="@string/permission_label"
                android:description="@string/permission_description"
                />

    <application android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">

    </application>
</manifest>
```

A continuación, la **AndroidManifest.xml** del cliente de APK debe solicitar explícitamente este nuevo permiso. Esto se hace agregando el `users-permission` atributo a la **AndroidManifest.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerclient">

    <uses-sdk android:minSdkVersion="21" />

    <uses-permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP" />

    <application
            android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">
    </application>
    </manifest>
```

### <a name="view-the-permissions-granted-to-an-app"></a>Ver los permisos concedidos a una aplicación

Para ver los permisos que se ha concedido una aplicación, abra la aplicación de configuración de Android y seleccione **aplicaciones**. Busque y seleccione la aplicación en la lista. Desde el **App Info** pulse **permisos** que se abrirá una vista que muestra todos los permisos concedidos a la aplicación:

[![Capturas de pantalla de un dispositivo Android que muestra cómo buscar los permisos concedidos a una aplicación](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Resumen

Esta guía fue una discusión avanzada acerca de cómo ejecutar un servicio de Android en un proceso remoto. Se explican las diferencias entre una variable local y un servicio remoto, junto con algunos de los motivos por qué un servicio remoto puede resultar útil a la estabilidad y rendimiento de una aplicación Android. Después de explicar cómo implementar un servicio remoto y cómo un cliente puede comunicarse con el servicio, la guía se pasa a proporcionar una forma de limitar el acceso al servicio desde solo los clientes autorizados.


## <a name="related-links"></a>Vínculos relacionados

- [controlador](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Mensaje](https://developer.xamarin.com/api/type/Android.OS.Message/)
- [Messenger](https://developer.xamarin.com/api/type/Android.OS.Messenger/)
- [ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)
- [El atributo exportado](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [Producirá un error de servicios con procesos aislados y clase de aplicación personalizada resolver las sobrecargas correctamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Los procesos y subprocesos](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifiesto de Android - permisos](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Sugerencias de seguridad](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/MessengerServiceDemo/)
