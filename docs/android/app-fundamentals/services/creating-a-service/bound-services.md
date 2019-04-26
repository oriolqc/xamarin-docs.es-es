---
title: Servicios enlazados en Xamarin.Android
description: Servicios enlazados son servicios de Android que proporcionan una interfaz de cliente / servidor que un cliente (por ejemplo, una actividad de Android) puede interactuar con. Esta guía tratan los componentes clave relacionados con la creación de un servicio dependiente y cómo usarlo en una aplicación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/04/2018
ms.openlocfilehash: c0adee0dae1135bdfd076082e85a471db1cd1ecf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013391"
---
# <a name="bound-services-in-xamarinandroid"></a>Servicios enlazados en Xamarin.Android

_Servicios enlazados son servicios de Android que proporcionan una interfaz de cliente / servidor que un cliente (por ejemplo, una actividad de Android) puede interactuar con. Esta guía tratan los componentes clave relacionados con la creación de un servicio dependiente y cómo usarlo en una aplicación de Xamarin.Android._

## <a name="bound-services-overview"></a>Información general de servicios dependiente

Servicios que proporcionan una interfaz de cliente / servidor para que los clientes interactúan directamente con el servicio se conocen como _servicios enlazados_.  Puede haber múltiples clientes conectados a una sola instancia de un servicio al mismo tiempo. El servicio dependiente y el cliente están aislados entre sí. En su lugar, Android proporciona una serie de objetos intermedios que administrar el estado de la conexión entre los dos. Este estado es mantenido por un objeto que implementa el [ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/) interfaz.  Este objeto es creado por el cliente y se pasa como parámetro a la [ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/) método. El `BindService` está disponible en cualquier [ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context) objeto (por ejemplo, una actividad). Es una solicitud para el sistema operativo Android para iniciar el servicio y lo enlaza a un cliente. Hay tres formas de un cliente pueden enlazar a un servicio utilizando el `BindService` método:

* **Un enlazador de servicio** &ndash; un enlazador de servicio es una clase que implementa el [ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder) interfaz. La mayoría de las aplicaciones no implementen esta interfaz directamente, en su lugar extenderá el [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) clase. Este es el enfoque más común y es adecuado para cuando el cliente y el servicio existan en el mismo proceso.
* **Uso de una instancia de Messenger** &ndash; esta técnica es útil para cuando el servicio es posible que existan en un proceso independiente. En su lugar, las solicitudes de servicio están en el búfer entre el cliente y servicio a través de un [ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger). Un [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler) se crea en el servicio que se va a controlar el `Messenger` solicitudes. Esto se explicará en otra guía.
* **Mediante el lenguaje de definición de interfaz Android (AIDL)** &ndash; [AIDL](https://developer.android.com/guide/components/aidl) es una técnica avanzada que no se tratarán en esta guía.

Una vez que un cliente se ha enlazado a un servicio, la comunicación entre los dos es se produce a través de `Android.OS.IBinder` objeto.  Este objeto es responsable de la interfaz que permitirá que el cliente interactuar con el servicio. No es necesario para cada aplicación de Xamarin.Android implementar esta interfaz desde el principio, el SDK de Android proporciona el [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) clase que se encarga de la mayoría del código necesario con cálculo de referencias de objeto entre el cliente y el servicio.

Cuando un cliente se realiza con el servicio, debe separar de él mediante una llamada a la `UnbindService` método. Una vez que se desenlaza el último cliente a un servicio, Android detendrá y eliminar el servicio dependiente.

Este diagrama ilustra cómo la actividad, conexión de servicio, enlazador y servicios relacionan entre sí:

![Diagrama que muestra cómo se relacionan entre sí los componentes del servicio](bound-services-images/bound-services-02.png "un diagrama que muestra cómo se relacionan entre sí los componentes del servicio.")

Esta guía describirá cómo ampliar el `Service` clase para implementar un servicio dependiente. También se tratará implementar `IServiceConnection` y ampliar `Binder` para permitir que un cliente para comunicarse con el servicio. Una aplicación de ejemplo acompaña a esta guía, que contienen una solución con un único proyecto de Xamarin.Android denominado **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Se trata de una aplicación muy básica que se muestra cómo implementar un servicio y cómo enlazar una actividad a él. El servicio dependiente tiene una API muy simple con un solo método, `GetFormattedTimestamp`, que devuelve una cadena que indica al usuario cuando se ha iniciado el servicio y cuánto tiempo lleva ejecutándose. La aplicación también permite al usuario desenlazar y enlazar con el servicio manualmente.

[![Captura de pantalla de la aplicación que se ejecuta en un teléfono Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Implementar y consumir un servicio dependiente

Hay tres componentes que deben implementarse para una aplicación Android consumir un servicio dependiente:

1. **Ampliar el `Service` clase e implementar los métodos de devolución de llamada de ciclo de vida** &ndash; esta clase contendrá el código que llevará a cabo el trabajo que se solicitará del servicio. Esto se explicará más detalladamente a continuación.
2. **Cree una clase que implementa `IServiceConnection`**  &ndash; esta interfaz proporciona métodos de devolución de llamada se le invoca Android para notificar al cliente cuando la conexión con el servicio ha cambiado, es decir, el cliente ha conectado o desconectado para el servicio. La conexión de servicio también proporcionará una referencia a un objeto que el cliente puede usar para interactuar directamente con el servicio. Esta referencia se conoce como el _enlazador_.
3. **Cree una clase que implementa `IBinder`**  &ndash; A _enlazador_ implementación proporciona la API que un cliente usa para comunicarse con el servicio. El enlazador puede proporcionar una referencia al servicio enlazado, permitir que los métodos que deben invocarse directamente o el enlazador puede proporcionar a una API que encapsula y oculta el servicio dependiente de la aplicación cliente. Un `IBinder` debe proporcionar el código necesario para las llamadas a procedimiento remoto. No es necesario (ni recomendado) para implementar el `IBinder` interfaz directamente. En su lugar, deben extender las aplicaciones la `Binder` tipo que proporciona la mayor parte de la funcionalidad base requerida por un `IBinder`.
4. **Iniciar y enlazar a un servicio** &ndash; una vez que se han creado la conexión de servicio, el enlazador y el servicio de la aplicación Android es responsable de iniciar el servicio y el enlace a él.

Cada uno de estos pasos se tratarán en las secciones siguientes con más detalle.

### <a name="extend-the-service-class"></a>Ampliar la `Service` clase

Para crear un servicio con Xamarin.Android, es necesario para crear una subclase `Service` y adornar de la clase con el [ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute). El atributo se utiliza por las herramientas de compilación de Xamarin.Android para registrar correctamente el servicio en la aplicación **AndroidManifest.xml** muy similar a una actividad de archivos, un servicio dependiente tiene su propio ciclo de vida y la devolución de llamada de métodos asociados a la eventos importantes en su ciclo de vida. En la lista siguiente es un ejemplo de algunos de los métodos de devolución de llamada más comunes que implementará un servicio:

* `OnCreate` &ndash; Este método se invoca por Android como que está creando instancias del servicio. Sirve para inicializar las variables o los objetos requeridos por el servicio durante su vigencia. Este método es opcional.
* `OnBind` &ndash; Este método se debe implementar todos los servicios de enlazado. Se invoca cuando el primer cliente intenta conectarse al servicio. Devolverá una instancia de `IBinder` para que el cliente puede interactuar con el servicio. Siempre y cuando el servicio se está ejecutando, el `IBinder` objeto que se usará para atender las solicitudes de cliente futuras para enlazar con el servicio.
* `OnUnbind` &ndash; Este método se llama cuando se han desenlazado todos los clientes enlazados. Devolviendo `true` desde este método, el servicio más adelante se llamará `OnRebind` con la intención que se pasa a `OnUnbind` cuando nuevos clientes enlazar a él. Lo haría cuando un servicio continúa ejecutándose después de haber sido independiente. Esto podría ocurrir si el servicio recientemente independiente también era un servicio iniciado, y `StopService` o `StopSelf` no había se ha llamado. En este escenario, `OnRebind` permite que la intención que se van a recuperar. El valor predeterminado devuelve `false` , que no hace nada. Opcional.
* `OnDestroy` &ndash; Este método se llama cuando se Android destruye en el servicio. Cualquier limpieza necesaria, como la liberación de recursos, debe realizarse en este método. Opcional.

En este diagrama se muestran los eventos de ciclo de vida de un servicio dependiente:

![Diagrama que muestra el orden en el que se llaman los métodos del ciclo de vida](bound-services-images/bound-services-01.png "un diagrama que muestra el orden en el que se llaman los métodos del ciclo de vida.")

El siguiente fragmento de código, de la aplicación complementaria que acompaña a esta guía, muestra cómo implementar un servicio dependiente en Xamarin.Android:

```csharp
using Android.App;
using Android.Util;
using Android.Content;
using Android.OS;

namespace BoundServiceDemo
{
    [Service(Name="com.xamarin.ServicesDemo1")]
    public class TimestampService : Service, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampService).FullName;
        IGetTimestamp timestamper;

        public IBinder Binder { get; private set; }

        public override void OnCreate()
        {
            // This method is optional to implement
            base.OnCreate();
            Log.Debug(TAG, "OnCreate");
            timestamper = new UtcTimestamper();
        }

        public override IBinder OnBind(Intent intent)
        {
            // This method must always be implemented
            Log.Debug(TAG, "OnBind");
            this.Binder = new TimestampBinder(this);
            return this.Binder;
        }

        public override bool OnUnbind(Intent intent)
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnUnbind");
            return base.OnUnbind(intent);
        }

        public override void OnDestroy()
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnDestroy");
            Binder = null;
            timestamper = null;
            base.OnDestroy();
        }

        /// <summary>
        /// This method will return a formatted timestamp to the client.
        /// </summary>
        /// <returns>A string that details what time the service started and how long it has been running.</returns>
        public string GetFormattedTimestamp()
        {
            return timestamper?.GetFormattedTimestamp();
        }
    }
}
```

En el ejemplo, el `OnCreate` método inicializa un objeto que contiene la lógica para recuperar y aplicar formato a una marca de tiempo que se solicitarían por un cliente. Cuando el primer cliente intenta enlazar con el servicio, Android invocará el `OnBind` método. Creará una instancia de este servicio un `TimestampBinder` objeto que permitirá a los clientes tener acceso a esta instancia del servicio en ejecución. La `TimestampBinder` clase se describe en la sección siguiente.

### <a name="implementing-ibinder"></a>Implementar IBinder

Como se mencionó, un `IBinder` objeto proporciona el canal de comunicación entre un cliente y un servicio. Las aplicaciones de Android no deben implementar la `IBinder` interfaz, el [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/) debe ampliarse. La `Binder` clase proporciona gran parte de la infraestructura necesaria que es necesario serializar el objeto de enlazador desde el servicio (que puede estar ejecutándose en un proceso independiente) al cliente. En la mayoría de los casos, el `Binder` subclase es sólo unas pocas líneas de código y contiene una referencia al servicio. En este ejemplo, `TimestampBinder` tiene una propiedad que expone el `TimestampService` al cliente:

```csharp
public class TimestampBinder : Binder
{
    public TimestampBinder(TimestampService service)
    {
        this.Service = service;
    }

    public TimestampService Service { get; private set; }
}
```

Esto `Binder` hace posible invocar los métodos públicos en el servicio; por ejemplo:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Una vez `Binder` ha sido extendido, es necesario implementar `IServiceConnection` para conectar todos los elementos entre sí.

### <a name="creating-the-service-connection"></a>Crear la conexión de servicio

El `IServiceConnection` presentará | introducir | exponer | conectar el `Binder` objeto al cliente. Además de implementar el `IServiceConnection` interfaz, debe extender la clase `Java.Lang.Object`. La conexión de servicio también debe proporcionar alguna manera que el cliente puede tener acceso a la `Binder` (y, por tanto, se comunican con el servicio dependiente).

Este código es desde el que acompaña a proyecto de ejemplo es una manera de implementar `IServiceConnection`:

```csharp
using Android.Util;
using Android.OS;
using Android.Content;

namespace BoundServiceDemo
{
    public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampServiceConnection).FullName;

        MainActivity mainActivity;
        public TimestampServiceConnection(MainActivity activity)
        {
            IsConnected = false;
            Binder = null;
            mainActivity = activity;
        }

        public bool IsConnected { get; private set; }
        public TimestampBinder Binder { get; private set; }

        public void OnServiceConnected(ComponentName name, IBinder service)
        {
            Binder = service as TimestampBinder;
            IsConnected = this.Binder != null;

            string message = "onServiceConnected - ";
            Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

            if (IsConnected)
            {
                message = message + " bound to service " + name.ClassName;
                mainActivity.UpdateUiForBoundService();
            }
            else
            {
                message = message + " not bound to service " + name.ClassName;
                mainActivity.UpdateUiForUnboundService();
            }

            Log.Info(TAG, message);
            mainActivity.timestampMessageTextView.Text = message;

        }

        public void OnServiceDisconnected(ComponentName name)
        {
            Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
            IsConnected = false;
            Binder = null;
            mainActivity.UpdateUiForUnboundService();
        }

        public string GetFormattedTimestamp()
        {
            if (!IsConnected)
            {
                return null;
            }

            return Binder?.GetFormattedTimestamp();
        }
    }
}

```

Como parte del proceso de enlace, se invocará Android el `OnServiceConnected` método, que proporciona el `name` del servicio que se va a enlazar y `binder` que contiene una referencia al propio servicio. En este ejemplo, la conexión de servicio tiene dos propiedades, que contiene una referencia para el enlazador y una marca booleana para si el cliente está conectado al servicio o no.

El `OnServiceDisconnected` método solo se invoca cuando la conexión entre un cliente y un servicio se pierde o se divide inesperadamente. Este método permite al cliente una oportunidad para responder a la interrupción del servicio.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Iniciar y enlazar a un servicio con una intención explícita

Para utilizar un servicio dependiente, un cliente (por ejemplo, una actividad) debe crear una instancia de un objeto que implementa `Android.Content.IServiceConnection` e invocar el `BindService` método.` BindService` devolverá `true` si el servicio está enlazado, `false` si no lo está. El método `BindService` toma tres parámetros:

* **Un `Intent`**  &ndash; la intención explícitamente debe identificar qué servicio se conecte a.
* **Un `IServiceConnection` objeto** &ndash; este objeto es un intermediario que proporciona los métodos de devolución de llamada para notificar al cliente cuando se inicia y detiene el servicio dependiente.
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) enumeración** &ndash; este parámetro es un conjunto de marcas se usan por el sistema al enlazar el objeto. El valor más frecuente es [ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/), que iniciará automáticamente el servicio si no se está ejecutando.

El fragmento de código siguiente es un ejemplo de cómo iniciar un servicio dependiente en una actividad mediante una intención explícita:

```csharp
protected override void OnStart ()
{
    base.OnStart ();

    if (serviceConnection == null)
    {
        this.serviceConnection = new TimestampServiceConnection(this);
    }

    Intent serviceToStart = new Intent(this, typeof(TimestampService));
    BindService(serviceToStart, this.serviceConnection, Bind.AutoCreate);

}
```

> [!IMPORTANT]
> Solo es posible enlazar a un servicio con una intención explícita iniciarlo con Android 5.0 (API nivel 21).

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Notas de la arquitectura sobre la conexión de servicio y el enlazador.

Pueden desaprobar algunos puristas de programación orientada a objetos de la implementación anterior de la `TimestampBinder` clase ya que es una infracción de la [ley de Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) que, en su forma más sencilla indica "no responder a extraños; solo se hable con sus amigos". En esta implementación expone el hormigón `TimestampService` clase a todos los clientes.

En realidad, no es necesario para el cliente conocer el `TimestampService` y exponer esa clase concreta a los clientes puede hacer que una aplicación más frágil y es más difícil de mantener durante su ciclo de vida. Un enfoque alternativo es usar una interfaz que expone el `GetFormattedTimestamp()` método y las llamadas al servicio mediante proxy el `Binder` (o posible la clase de conexión de servicio):  

```csharp
public class TimestampBinder : Binder, IGetTimestamp
{
    TimestampService service;
    public TimestampBinder(TimestampService service)
    {
        this.service = service;
    }

    public string GetFormattedTimestamp()
    {
        return service?.GetFormattedTimestamp();
    }
}
```

Este ejemplo concreto que una actividad invocar métodos en el propio servicio:

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```


## <a name="related-links"></a>Vínculos relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.Content.Bind](https://developer.xamarin.com/api/type/Android.Content.Bind/)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [Android.Content.IServiceConnection](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)
- [Android.OS.Binder](https://developer.xamarin.com/api/type/Android.OS.Binder/)
- [Android.OS.IBinder](https://developer.xamarin.com/api/type/Android.OS.IBinder)
- [BoundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/BoundServiceDemo/)
