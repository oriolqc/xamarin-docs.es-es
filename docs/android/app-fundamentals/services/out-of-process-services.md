---
title: Servicios de móvil Android en procesos remotos
description: Por lo general, todos los componentes de una aplicación de Android se ejecutarán en el mismo proceso. Android servicios son una excepción significativa a este en cuanto se pueden configurar para ejecutarse en sus propios procesos y compartir con otras aplicaciones, las de otros desarrolladores de Android incluidas. Esta guía describe cómo crear y utilizar un servicio remoto Android con Xamarin.
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 57be8187509ad7607c38ea17233e9ab5d25b41f1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="running-android-services-in-remote-processes"></a>Servicios de móvil Android en procesos remotos

_Por lo general, todos los componentes de una aplicación de Android se ejecutarán en el mismo proceso. Android servicios son una excepción significativa a este en cuanto se pueden configurar para ejecutarse en sus propios procesos y compartir con otras aplicaciones, las de otros desarrolladores de Android incluidas. Esta guía describe cómo crear y utilizar un servicio remoto Android con Xamarin._

## <a name="out-of-process-services-overview"></a>Fuera de información general de servicios de proceso

Cuando se inicia una aplicación, Android crea un proceso en el que se va a ejecutar la aplicación. Normalmente, todos los componentes de la aplicación se ejecutará en este uno proceso. Android servicios son una excepción significativa a este en cuanto se pueden configurar para ejecutarse en sus propios procesos y compartir con otras aplicaciones, las de otros desarrolladores de Android incluidas. Estos tipos de servicios se conocen como _servicios remoto_ o _servicios fuera de proceso_. El código de estos servicios se ubicará en el mismo APK que la aplicación principal; Sin embargo, cuando se inicia el servicio Android creará un nuevo proceso para que solo el servicio. En cambio, un servicio que se ejecuta en el mismo proceso que el resto de la aplicación se conoce a veces como un _servicio local_.

En general, no es necesario para una aplicación implementar un servicio remoto. Un servicio local es suficiente (y deseable) para los requisitos de una aplicación en muchos casos. Un fuera de proceso tiene su propio espacio de memoria que debe estar administrado por Android. Aunque esto presentar mayor sobrecarga a la aplicación, hay algunos escenarios donde puede resultar ventajoso para ejecutar un servicio en su propio proceso:

1. **Funcionalidad de uso compartido** &ndash; algunos desarrolladores de aplicaciones pueden tener varias aplicaciones y funcionalidad que se comparte entre todas las aplicaciones. Empaquetar esa funcionalidad en un servicio de Android que se ejecuta en su propio proceso puede simplificar el mantenimiento de la aplicación. También es posible empaquetar el servicio en su propio APK independiente e implementarla por separado desde el resto de la aplicación.
2. **Mejorar la experiencia del usuario** &ndash; hay dos formas posibles de que un servicio de fuera de proceso puede mejorar la experiencia del usuario de la aplicación. La primera manera relacionado con la administración de memoria. Cuando una colección de elementos no utilizados (GC) se produce ciclo Android pausará toda la actividad en el proceso hasta que se complete el catálogo global. El usuario puede percibir esta pausa como "entrecortarse" o "jank". Cuando un servicio se está ejecutando proceso propio, es el proceso de servicio que está en pausa, no el proceso de aplicación. Esta pausa será mucho menos evidente para el usuario como el proceso de aplicación (y, por tanto, la interfaz de usuario) no está en pausa.

    En segundo lugar, cuando los requisitos de memoria de un proceso sea demasiado grande, Android puede terminar ese proceso para liberar recursos para el dispositivo. Si un servicio tiene una superficie de memoria grande y se está ejecutando en el mismo proceso que la interfaz de usuario, a continuación, cuando Android forzosamente reclama los recursos de la interfaz de usuario se apagará, obligar al usuario a iniciar la aplicación. Sin embargo, si se cierra un servicio, que se ejecuta en su propio proceso Android, el proceso de interfaz de usuario queda intacto. La interfaz de usuario puede enlazar (y reinicie) el servicio, de forma transparente para el usuario y reanudar el funcionamiento normal.

3. **Mejorar el rendimiento de la aplicación** &ndash; proceso de la interfaz de usuario esté terminado o apagar independiente del proceso del servicio. Al mover las tareas de inicio largas a un servicio de fuera de proceso, es posible que el tiempo de inicio de la interfaz de usuario puede ser mejora (suponiendo que el proceso del servicio se mantiene activo entre las veces que se inicia la interfaz de usuario).

En muchos sentidos, enlace a un servicio que se ejecuta en otro proceso es el mismo que [enlazar a un servicio local](~/android/app-fundamentals/services/creating-a-service/bound-services.md). El cliente va a invocar `BindService` para enlazar (e iniciar, si es necesario) el servicio. Un `Android.OS.IServiceConnection` se creará un objeto para administrar la conexión entre el cliente y el servicio. Si el cliente se enlaza correctamente al servicio, Android, devolverá un objeto a través de la `IServiceConnection` que se puede utilizar para invocar métodos en el servicio. El cliente, a continuación, se interactúa con el servicio que utiliza este objeto. Para revisar, estos son los pasos para enlazar a un servicio:

* **Crear un intento** &ndash; un intento explícito debe usarse para enlazar con el servicio.
* **Implemente y creación de instancias de un `IServiceConnection` objeto** &ndash; la `IServiceConnection` objeto actúa como intermediario entre el cliente y el servicio.  Es responsable de supervisar la conexión entre cliente y servidor.
* **Invocar la `BindService` método** &ndash; llamada `BindService` enviará el propósito y la conexión de servicio creada en los pasos anteriores para Android, que se encargará de iniciar el servicio y establecer la comunicación entre cliente y servicio.

La necesidad de cruzar los límites del proceso que introduce una complejidad adicional: la comunicación es unidireccional (cliente a servidor) y el cliente no puede invocar directamente los métodos en la clase de servicio. Recuerde que cuando un servicio ejecuta el mismo proceso que el cliente, Android proporciona un `IBinder` objeto que puede permitir la comunicación bidireccional. No es el caso de servicio que se ejecuta en su propio proceso. Un cliente se comunica con un servicio remoto con la Ayuda de la `Android.OS.Messenger` clase.

Cuando un cliente solicita para enlazar con el servicio remoto, Android invocará la `Service.OnBind` método de ciclo de vida, lo que devolverá interno `IBinder` objeto encapsulado por el `Messenger`. El `Messenger` es un contenedor fino sobre una clase especial `IBinder` implementación proporcionada por el SDK de Android. El `Messenger` se encarga de la comunicación entre los dos procesos diferentes. El programador es unconcerned con los detalles de serializar un mensaje, el mensaje de cálculo de referencias entre los límites del proceso y, a continuación, se deserializa en el cliente. Este trabajo se controla mediante la `Messenger` objeto. Este diagrama muestra los componentes del lado cliente Android que intervienen cuando un cliente inicia el enlace a un servicio de fuera de proceso:

![Diagrama que muestra los pasos y los componentes de un enlace a un servicio del cliente](out-of-process-services-images/ipc-01.png "diagrama que muestra los pasos y los componentes de un enlace a un servicio del cliente.")

La `Service` clase en el proceso remoto pasará a través de las devoluciones de llamada del ciclo de vida mismo que un servicio dependiente en un proceso local pasará a través de, y muchas de las API implicadas son los mismos. `Service.OnCreate` se utiliza para inicializar un `Handler` e inyectar a `Messenger` objeto. Del mismo modo, `OnBind` es invalidado, pero en lugar de devolver un `IBinder` objeto, el servicio devolverá el `Messenger`.  Este diagrama muestra lo que ocurre en el servicio cuando se enlaza un cliente:

![Diagrama que muestra los pasos y los componentes de un servicio se recorre cuando se enlaza a un cliente remoto](out-of-process-services-images/ipc-02.png "atraviesa diagrama que muestra los pasos y los componentes de un servicio al que se enlaza a un cliente remoto.")

Cuando un `Message` es recibido por un servicio, se procesa en la instancia de `Android.OS.Handler`. El servicio va a implementar su propio `Handler` subclase que debe reemplazar el `HandleMessage` método. Este método es invocado por el `Messenger` y recibe el `Message` como un parámetro. El `Handler` inspeccionará el `Message` metadatos y utilizar esa información para invocar métodos en el servicio.

Comunicación unidireccional se produce cuando un cliente crea un `Message` objeto y lo envía al servicio utilizando el `Messenger.Send` método. `Messenger.Send` se serializará el `Message` y entregue los datos serializados desactivado para Android, que se usará para enrutar el mensaje a través del límite de proceso y el servicio.  El `Messenger` hospedada por el servicio creará un `Message` objeto de los datos entrantes. Esto `Message` se coloca en una cola, donde los mensajes son enviado uno a uno para el `Handler`. El `Handler` inspeccionará los metadatos contenidos en la `Message` e invocar los métodos apropiados en el `Service`. En el diagrama siguiente se muestra estos distintos conceptos en acción:

![Diagrama que muestra cómo se pasan los mensajes entre los procesos](out-of-process-services-images/ipc-03.png "diagrama que muestra cómo se pasan los mensajes entre los procesos.")

Esta guía tratará los detalles de la implementación de un servicio de fuera de proceso. Lo veremos cómo implementar un servicio que está pensado para ejecutarse en su propio proceso y cómo un cliente puede comunicarse con ese servicio utilizando el `Messenger` framework. También veremos la comunicación bidireccional: el cliente envía un mensaje a un servicio y el servicio de envío de un mensaje al cliente. Dado que los servicios pueden compartirse entre diferentes aplicaciones, esta guía también explican una técnica para limitar el acceso de cliente al servicio mediante el uso de permisos Android.

> [!IMPORTANT]
> [Bugzilla 51940 - servicios con procesos aislados y clase de aplicación personalizada no se puede resolver correctamente las sobrecargas](https://bugzilla.xamarin.com/show_bug.cgi?id=51940) informes que un servicio Xamarin.Android no se iniciará correctamente cuando el `IsolatedProcess` está establecido en `true`. Esta guía se proporciona como una referencia. Una aplicación Xamarin.Android todavía debe ser capaz de comunicarse con un servicio de fuera de proceso que se escribe en Java.

## <a name="requirements"></a>Requisitos

Esta guía supone que está familiarizado con la creación de servicios.

Aunque es posible usar intentos implícita con aplicaciones que tienen como destino anterior API de Android, esta guía se centrará exclusivamente en el uso de intentos explícitas. Aplicaciones destinadas a Android 5.0 (API nivel 21) o superior deben usar un intento explícito para enlazar con un servicio; Ésta es la técnica que se muestra en esta guía...

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Crear un servicio que se ejecuta en un proceso independiente

Como se describió anteriormente, el hecho de que un servicio se ejecuta en su propio proceso significa que algunas API diferentes está implicado. Como una visión general rápida, estos son los pasos para enlazar con y consumir un servicio remoto:  

* **Crear el `Service` subclase** &ndash; subclase el `Service` escriba e implemente los métodos del ciclo de vida de un servicio dependiente. También es necesario establecer metadatos que le informará Android que el servicio se ejecuta en su propio proceso.
* **Implemente un `Handler`**  &ndash; el `Handler` es responsable de analizar las solicitudes del cliente, extraer los parámetros que se pasan desde el cliente e invocar los métodos apropiados en el servicio.
* **Crear una instancia de un `Messenger`**  &ndash; tal y como se ha descrito anteriormente, cada uno de ellos `Service` debe mantener una instancia de la `Messenger` clase que se usará para enrutar las solicitudes de cliente a la `Handler` que creó en el paso anterior.

Un servicio que está pensado para ejecutarse en su propio proceso es, básicamente, todavía un servicio dependiente. La clase de servicio que ampliará la base de `Service` clase y se decora con el `ServiceAttribute` que contiene los metadatos que necesita Android para agrupar en el manifiesto de Android. Para comenzar con las siguientes propiedades de la `ServiceAttribute` que son importantes para un servicio de fuera de proceso:

1. `Exported` &ndash; Esta propiedad debe establecerse en `true` para permitir que otras aplicaciones interactúen con el servicio. El valor predeterminado de esta propiedad es `false`.
2. `Process` &ndash; Esta propiedad se debe establecer. Se utiliza para especificar el nombre del proceso que se ejecutará el servicio.
3. `IsolatedProcess` &ndash; Esta propiedad habilita la seguridad adicional, indicando Android para ejecutar el servicio en un espacio aislado aislado con un permiso mínimo para iteract con el resto del sistema. Vea [Bugzilla 51940 - Services con procesos aislados y clase de aplicación personalizada no se puede resolver correctamente las sobrecargas](https://bugzilla.xamarin.com/show_bug.cgi?id=51940).
4. `Permission` &ndash; Es posible controlar el acceso de cliente al servicio mediante la especificación de un permiso que los clientes deben solicitar (y recibir).

Para ejecutar un servicio en su propio proceso, el `Process` propiedad en el `ServiceAttribute` debe establecerse en el nombre del servicio. Para interactuar con las aplicaciones externas, el `Exported` propiedad debe establecerse en `true`. Si `Exported` es `false`, los clientes solo en el mismo APK (es decir, la misma aplicación) y en ejecución en el mismo proceso podrá interactuar con el servicio.

¿Qué tipo de proceso que el servicio se ejecutará en depende del valor de la `Process` propiedad. Android identifica tres tipos diferentes de procesos:

-   **Proceso privado** &ndash; un proceso privado es aquel que solo está disponible para la aplicación que se inició. Para identificar un proceso como privado, su nombre debe comenzar con un **:** (punto y coma). El servicio que se muestra en el fragmento de código anterior y la captura de pantalla es un proceso privado. El siguiente fragmento de código es un ejemplo de la `ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **Proceso global** &ndash; un servicio que se ejecuta en un proceso global es accesible para todas las aplicaciones que se ejecutan en el dispositivo. Un proceso global debe ser un nombre de clase completo que comienza por un carácter en minúsculas.
    (A menos que se tomen medidas para proteger el servicio, otras aplicaciones pueden enlazar e interactuar con él. Proteger el servicio frente al uso no autorizado se explicará más adelante en esta guía.)

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **Aislamiento de proceso** &ndash; un proceso aislado es un proceso que se ejecuta en su propio espacio aislado, aislado del resto del sistema y sin ningún permiso especial de su propio. Para ejecutar un servicio en un proceso aislado, el `IsolatedProcess` propiedad de la `ServiceAttribute` está establecido en `true` tal y como se muestra en este fragmento de código:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Vea [Bugzilla 51940 - Services con procesos aislados y clase de aplicación personalizada no se puede resolver correctamente las sobrecargas](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

Un servicio aislado es una manera sencilla para proteger una aplicación y el dispositivo con código no seguro. Por ejemplo, una aplicación puede descargar y ejecutar una secuencia de comandos desde un sitio Web. En este caso, proporciona una capa adicional de seguridad para poner en peligro el dispositivo Android desde código no seguro realizar esto en un proceso aislado.

> [!IMPORTANT]
> Una vez que se ha exportado un servicio, no debería cambiar el nombre del servicio. Cambiar el nombre del servicio puede interrumpir otras aplicaciones que usan el servicio.

Para ver el efecto que el `Process` tiene propiedad, la captura de pantalla siguiente muestra un servicio que se ejecuta en su propio proceso privada:

![Captura de pantalla que muestra un servicio que se ejecuta en un proceso privado](out-of-process-services-images/ipc-04.png "captura de pantalla que muestra un servicio que se ejecuta en un proceso privado.")

Esta captura de pantalla siguiente muestra `Process="com.xamarin.xample.messengerservice.timestampservice_process"` y el servicio se ejecuta en un proceso global:

![Captura de pantalla de un servicio que se ejecuta en un proceso global](out-of-process-services-images/ipc-05.png "captura de pantalla de un servicio que se ejecuta en un proceso global.")

Una vez el `ServiceAttribute` se ha establecido, las necesidades de servicio para implementar un `Handler`.

### <a name="implementing-a-handler"></a>Implementar un controlador

Para procesar las solicitudes de cliente, el servicio debe implementar un `Handler` e invalide el `HandleMessage` methodThis es el método toma un `Message` instancia qué que encapsula la llamada al método desde el cliente y convierte esa llamada en alguna acción o tarea que se llevará a cabo el servicio. El `Message` objeto expone una propiedad denominada `What` que es un valor entero, el significado de los cuales se comparte entre el cliente y el servicio y se relaciona con algunas tareas que debe realizar para el cliente del servicio.

El siguiente fragmento de código de la aplicación de ejemplo muestra un ejemplo de `HandleMessage`. En este ejemplo, hay dos acciones que puede solicitar un cliente del servicio:

* La primera acción es un _Hello, World_ mensaje, el cliente ha enviado un mensaje simple al servicio.
* La segunda acción se invoca un método en el servicio y recuperar una cadena, en este caso, la cadena es un mensaje que se devuelve en qué momento el servicio iniciado y durante cuánto tiempo lleva ejecutándose:

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

También es posible a parámetros del paquete para el servicio en la `Message`. Esto se explicará más adelante en esta guía. Está creando el tema siguiente para tener en cuenta el `Messenger` objeto que se va a procesar la entrada `Message`s.

### <a name="instantiating-the-messenger"></a>Crear instancias de Messenger

Como se explicó anteriormente, deserializar el `Message` objeto e invocar `Handler.HandleMessage` es la responsabilidad de la `Messenger` objeto. El `Messenger` clase también proporciona un `IBinder` que el cliente usará para enviar mensajes al servicio del objeto.  

Cuando se inicia el servicio, creará una instancia de la `Messenger` e inyectar la `Handler`. Un buen lugar para realizar esta inicialización se encuentra en la `OnCreate` método del servicio. Este fragmento de código es un ejemplo de un servicio que inicializa su propio `Handler` y `Messenger`:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

En este punto, es el último paso para la `Service` invalidar `OnBind`.

### <a name="implementing-serviceonbind"></a>Implementar Service.OnBind

Todos los servicios de enlace, si se ejecutan en su propio proceso o no es así, deben implementar la `OnBind` método. El valor devuelto de este método es un objeto que el cliente puede utilizar para interactuar con el servicio. ¿Qué es ese objeto depende de si el servicio es un servicio local o un servicio remoto. Mientras que un servicio local devolverá un personalizado `IBinder` implementación, un servicio remoto devolverá el `IBinder` que se encapsula pero la `Messenger` que se creó en la sección anterior:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Una vez que se llevan a cabo estos tres pasos, el servicio remoto puede considerarse completado.

## <a name="consuming-the-service"></a>Utilizar el servicio

Todos los clientes deben implementar algún código para que pueda enlazar y consumir el servicio remoto. Conceptualmente, desde el punto de vista del cliente, existen muy pocas diferencias entre el enlace al servicio local o a un servicio remoto. El cliente invoca la `BindService` método, pasando una intención explícita para identificar el servicio y un `IServiceConnection` que le ayuda a administrar la conexión entre el cliente y el servicio.

Este fragmento de código es un ejemplo de cómo crear un **intención explícita** para el enlace a un servicio remoto. El objetivo debe identificar el paquete que contiene el servicio y el nombre del servicio. Una manera de definir esta información es usar un `Android.Content.ComponentName` objeto y que proporcionar a la intención. Este fragmento de código es un ejemplo:  

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

Cuando el servicio está enlazado, el `IServiceConnection.OnServiceConnected` método se invoca y proporciona un `IBinder` a un cliente. Sin embargo, el cliente no se use directamente el `IBinder`. En su lugar, creará una instancia de un `Messenger` objeto desde el que `IBinder`. Se trata de la `Messenger` que el cliente usará para interactuar con el servicio remoto.

El siguiente es un ejemplo de una forma muy básica `IServiceConnection` implementación que se muestra cómo un cliente controlaría la conexión y desconexión de un servicio. Tenga en cuenta que la `OnServiceConnected` método recibe y `IBinder`, y el cliente crea un `Messenger` del `IBinder`:

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

Una vez que se crean la conexión de servicio y la intención, es posible que el cliente pueda llamar a `BindService` e iniciar el proceso de enlace:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Después de que el cliente ha enlazado correctamente al servicio y el `Messenger` está disponible, es posible que el cliente envíe `Messages` al servicio.

## <a name="sending-messages-to-the-service"></a>Enviar mensajes al servicio

Una vez que el cliente está conectado y tiene un `Messenger` objeto, es posible comunicarse con el servicio mediante su envío `Message` objetos a través de la `Messenger`. Esta comunicación es unidireccional, el cliente envía el mensaje pero no hay ningún mensaje devuelto desde el servicio al cliente. En este sentido, el `Message` es un mecanismo de enviar y olvidarse.

La mejor manera de crear un `Message` objeto consiste en utilizar el [ `Message.Obtain` ](https://developer.xamarin.com/api/type/Android.OS.Message/#Public%20Methods) método de fábrica. Este método se extraerá una `Message` objeto de un grupo global que se mantiene por Android. `Message.Obtain` También tiene algunos métodos sobrecargados que permiten la `Message` objeto que se iniciará con los valores y los parámetros requeridos por el servicio.  Una vez el `Message` es crear instancias, envían al servicio mediante una llamada a `Messenger.Send`. Este fragmento de código es un ejemplo de crear y enviar un `Message` al proceso del servicio:

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

Hay varias formas diferentes de la `Message.Obtain` método. El ejemplo anterior utiliza la [ `Message.Obtain(Handler h, Int32 what)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/). Dado que se trata de una solicitud asincrónica a un servicio fuera de proceso; no habrá ninguna respuesta del servicio, por lo que la `Handler` está establecido en `null`. El segundo parámetro, `Int32 what`, se almacenará en la `.What` propiedad de la `Message` objeto. El `.What` propiedad se usa en código en el proceso del servicio para invocar métodos en el servicio.

El `Message` clase también expone dos propiedades adicionales que pueden resultar útiles a la recipent: `Arg1` y `Arg2`. Estas dos propiedades son valores enteros que pueden tener algunas especial acordado valores que tienen un significado entre el cliente y el servicio. Por ejemplo, `Arg1` puede contener un identificador de cliente y `Arg2` puede contener un número de pedido de compra para ese cliente. El [ `Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/System.Int32/System.Int32/) puede usarse para establecer las dos propiedades cuando el `Message` se crea. Es otra manera de rellenar estos dos valores establecer la `.Arg` y `.Arg2` propiedades directamente en el `Message` objeto después de que se ha creado.

### <a name="passing-additional-values-to-the-service"></a>Pasar valores adicionales para el servicio

Es posible pasar datos más complejos al servicio mediante un `Bundle`. En este caso, se pueden colocar valores adicionales en un `Bundle` y se envía junto con el `Message` estableciendo la [ `.Data` propiedad](https://developer.xamarin.com/api/property/Android.OS.Message.Data/) propiedad antes de enviar.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> En general, un `Message` no debe tener una carga mayor de 1 MB. El límite de tamaño puede variar según la versión de Android y en cualquier cambio propietario el proveedor podría realizados en su implementación de la Android Abrir origen de proyecto (AOSP) que se incluye con el dispositivo.

## <a name="returning-values-from-the-service"></a>Devolver valores desde el servicio

La arquitectura de mensajería que se ha tratado en este punto es unidireccional, el cliente envía un mensaje al servicio. Si es necesario para el servicio devolver un valor a un cliente, a continuación, todo lo que se ha tratado en este punto se ha invertido. El servicio debe crear un `Message`, empaquetado los valores devueltos y envío el `Message` a través de un `Messenger` al cliente. Sin embargo, el servicio no crea su propio `Messenger`; en su lugar, se basa en el paquete y un cliente crear instancias de un `Messenger` como parte de la solicitud inicial. El servicio `Send` el mensaje mediante este proporcionadas por el cliente `Messenger`.  

La secuencia de eventos para la comunicación bidireccional es esta:

1. El cliente se enlaza con el servicio. Cuando el servicio y el cliente se conectan, el `IServiceConnection` que es mantenida por el cliente tendrá una referencia a un `Messenger` objeto que se usa para transmitir `Message`s para el servicio. Para evitar confusiones, esto se conocerán como los _servicio Messenger_.
2. Crea una instancia de cliente un `Handler` (conoce como el _controlador de cliente_) y que se utiliza para inicializar su propio `Messenger` (la _cliente Messenger_). Tenga en cuenta que el servicio Messenger y la mensajería de cliente son dos objetos diferentes que controlan el tráfico en dos direcciones diferentes. El servicio Messenger procesa mensajes desde el cliente al servicio, mientras que el cliente de Messenger controlará los mensajes procedentes del servicio al cliente.
3. El cliente crea un `Message` objeto y establece el `ReplyTo` propiedad con la mensajería de cliente. El mensaje se envía al servicio utilizando al servicio Messenger.
4. El servicio recibe el mensaje del cliente y realiza el trabajo solicitado.
5. Cuando se trata del servicio enviar la respuesta al cliente, va a usar `Message.Obtain` para crear un nuevo `Message` objeto.
6. Para enviar este mensaje al cliente, el servicio va a extraer la mensajería de cliente desde el `.ReplyTo` propiedad del cliente de mensajes y usarla para `.Send` el `Message` al cliente.
7. Cuando el cliente recibe la respuesta, tiene su propio `Handler` que procesará el `Message` inspeccionando el `.What` propiedad (y si es necesario, extraer los parámetros incluidos en la `Message`).

Este ejemplo de código muestra cómo el cliente creará una instancia el `Message` y empaquetar un `Messenger` que el servicio debe usar para su respuesta:

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

Tenga en cuenta que en los ejemplos de código anteriores, el `Messenger` instancia creada por el cliente es *no* el mismo objeto que se recibe por el servicio. Estas son dos diferentes `Messenger` objetos que se ejecutan en dos procesos independientes que representan el canal de comunicación.

## <a name="securing-the-service-with-android-permissions"></a>Proteger el servicio con permisos de Android

Un servicio que se ejecuta en un proceso global es accesible para todas las aplicaciones que se ejecutan en dicho dispositivo Android. En algunas situaciones, no es deseable esta apertura y la disponibilidad y es necesario proteger el servicio contra el acceso de clientes no autorizados. Una forma de limitar el acceso al servicio remoto es usar permisos Android.

Permisos pueden identificarse mediante el `Permission` propiedad de la `ServiceAttribute` que decora el `Service` subclase. Esto asignará nombre un permiso que el cliente debe concederse al enlazar con el servicio. Si el cliente no tiene los permisos adecuados, Android, se producirá un `Java.Lang.SecurityException` cuando el cliente intenta enlazar con el servicio.

Hay cuatro niveles de permisos diferentes que proporciona Android:

* **normal** &ndash; es el nivel de permiso de forma predeterminada. Se utiliza para identificar los permisos de bajo riesgo que se pueden conceder automáticamente Android a los clientes que lo solicitan. El usuario no tiene que conceder explícitamente estos permisos, pero se pueden ver los permisos en la configuración de la aplicación.
* **firma** &ndash; se trata de una categoría especial de permiso que se concederán automáticamente Android para que las aplicaciones firmadas con el mismo certificado. Este permiso está diseñado para que sea fácilmente para un desarrollador de aplicaciones compartir datos entre sus aplicaciones sin molestarle al usuario para aprobaciones de constantes o componentes.
* **signatureOrSystem** &ndash; es muy similar a la **firma** permisos se ha descrito anteriormente. Además de ser automáticamente concedidos a las aplicaciones que están firmadas por el mismo certificado, este permiso también se concederá a las aplicaciones que se firman el mismo certificado que se usó para firmar las aplicaciones se instala con la imagen de sistema Android. Este permiso normalmente se usa únicamente por los desarrolladores de Android ROM para permitir que sus aplicaciones trabajar con aplicaciones de terceros. No suele utilizarse en aplicaciones que sirven de distribución general para el público en general.
* **peligroso** &ndash; permisos peligrosos son aquellos que pueden causar problemas para el usuario. Por esta razón, **peligroso** permisos deben ser aprobados explícitamente por el usuario.

Dado que `signature` y `normal` automáticamente concede permisos en tiempo de instalar Android, es fundamental que hospeda el servicio APK instalarse **antes de** el APK que contiene el cliente. Si el cliente se instala en primer lugar, Android no van a conceder los permisos. En este caso, será necesario desinstalar al cliente APK, instalar el servicio APK y, a continuación, vuelva a instalar al cliente APK.

Hay dos formas habituales de proteger un servicio con permisos Android:

1.  **Implementar la seguridad de nivel de firma** &ndash; seguridad de nivel de firma significa que permiso automáticamente es concedidos a las aplicaciones que están firmadas con la misma clave que se usó para firmar el APK que contiene el servicio. Se trata de una manera sencilla para que los desarrolladores proteger su servicio, pero aún mantenerlos accesibles desde sus propias aplicaciones. Se declaran permisos de nivel de firma estableciendo la `Permission` propiedad de la `ServiceAttribute` a `signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **Crear un permiso personalizado** &ndash; es posible que el desarrollador del servicio crear un permiso personalizado para el servicio. Esto es mejor cuando un desarrollador desea compartir su servicio con aplicaciones de otros desarrolladores. Un permiso personalizado requiere un poco más esfuerzo para implementar y se explican a continuación.

Un ejemplo simplificado de creación de un archivo `normal` permiso se describe en la sección siguiente. Para obtener más información acerca de los permisos de Android, consulte la documentación de Google para [prácticas recomendadas y seguridad](https://developer.android.com/training/articles/security-tips.html). Para obtener más información acerca de los permisos de Android, consulte el [sección permisos](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) de la documentación de Android para el manifiesto de aplicación para obtener más información acerca de los permisos de Android.

> [!NOTE]
> En general, [Google desaconseja el uso de permisos personalizados](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) tal y como puede resultar confusos para los usuarios.

### <a name="creating-a-custom-permission"></a>Crear un permiso personalizado

Para usar un permiso personalizado, se declara el servicio mientras el cliente solicita explícitamente ese permiso.

Para crear un permiso en el servicio APK, un `permission` elemento se agrega a la `manifest` elemento **AndroidManifest.xml**. Este permiso debe tener la `name`, `protectionLevel`, y `label` establecido algún atributo. El `name` atributo debe establecerse en una cadena que identifica de forma única el permiso. El nombre se mostrará en el **App Info** ver de la **configuración de Android** (como se muestra en la siguiente sección).

El `protectionLevel` atributo debe establecerse en uno de los valores de cadena de cuatro que se describieron anteriormente.  El `label` y `description` debe hacer referencia a los recursos de cadena y se utilizan para proporcionar un nombre descriptivo y una descripción para el usuario.

Este fragmento de código es un ejemplo de cómo declarar un personalizado `permission` atributo **AndroidManifest.xml** de la APK que contiene el servicio:

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

A continuación, la **AndroidManifest.xml** del cliente APK debe solicitar explícitamente este nuevo permiso. Esto se hace agregando el `users-permission` atribuir a la **AndroidManifest.xml**:

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

Para ver los permisos que ha concedido a una aplicación, abra la aplicación de configuración de Android y seleccione **aplicaciones**. Busque y seleccione la aplicación en la lista. Desde el **App Info** pantalla, pulse **permisos** que abrirá una vista que muestra todos los permisos concedidos a la aplicación:

[![Capturas de pantalla de un dispositivo Android que muestra cómo buscar los permisos concedidos a una aplicación](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Resumen

Esta guía era una discusión avanzada acerca de cómo ejecutar un servicio de Android en un proceso remoto. Se explican las diferencias entre una variable local y un servicio remoto, junto con algunas de las razones por qué un servicio remoto puede ser útil para la estabilidad y el rendimiento de una aplicación Android. Después de explicar cómo implementar un servicio remoto y cómo un cliente puede comunicarse con el servicio, la guía fue para proporcionar una forma de limitar el acceso al servicio desde solo los clientes autorizados.


## <a name="related-links"></a>Vínculos relacionados

- [Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Message](https://developer.xamarin.com/api/type/Android.OS.Message/)
- [Messenger](https://developer.xamarin.com/api/type/Android.OS.Messenger/)
- [ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)
- [El atributo Exported](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [Servicios con procesos aislados y clase de aplicación personalizada no se puede resolver correctamente las sobrecargas](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Procesos y subprocesos](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifiesto de Android - permisos](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Consejos de seguridad](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/MessengerServiceDemo/)
