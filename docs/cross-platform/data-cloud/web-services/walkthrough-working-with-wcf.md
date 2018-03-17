---
title: 'Tutorial: trabajar con WCF'
description: "Este tutorial describe cómo una aplicación móvil creada con Xamarin puede consumir un servicio web WCF mediante la clase BasicHttpBinding."
ms.topic: article
ms.prod: xamarin
ms.assetid: D0E83342-2E79-4D25-BD57-43718F5628C4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/17/2018
ms.openlocfilehash: b4705bc9c8fdb1a671c7de2453ea088bf2afe424
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="walkthrough---working-with-wcf"></a>Tutorial: trabajar con WCF

_Este tutorial describe cómo una aplicación móvil creada con Xamarin puede consumir un servicio web WCF mediante la clase BasicHttpBinding._


Es un requisito común de aplicaciones móviles para que pueda comunicarse con sistemas de back-end. Existen muchas opciones y opciones de marcos de back-end, uno de los cuales es [Windows Communication Foundation](http://msdn.microsoft.com/en-us/library/ms731082.aspx) (WCF). Este tutorial le proporcionará un ejemplo de cómo una aplicación móvil de Xamarin puede consumir un servicio WCF mediante el `BasicHttpBinding` clase. El tutorial incluyen los siguientes temas:

1.  **Crear un servicio WCF** -en esta sección se creará un servicio WCF muy básico tiene dos métodos. El primer método tomará un parámetro de cadena, mientras que el otro método tardará un objeto de C#. En esta sección también se explica cómo configurar la estación de trabajo de un desarrollador para permitir el acceso remoto al servicio WCF.
1.  **Crear una aplicación de Xamarin.Android** -una vez que se ha creado el servicio WCF, crearemos una sencilla aplicación de Xamarin.Android que usará el servicio WCF. Esta sección explica cómo crear una clase de proxy de servicio WCF para facilitar la comunicación con el servicio WCF.
1.  **Crear una aplicación de Xamarin.iOS** -la parte final de este tutorial implica la creación de una aplicación simple de Xamarin.iOS que usará el servicio WCF.

<a name="Requirements" />

## <a name="requirements"></a>Requisitos

En este tutorial se supone que dispone de cierta familiaridad con la creación y uso de servicios WCF.

<a name="Creating_a_WCF_Service" />

## <a name="creating-a-wcf-service"></a>Crear un servicio WCF

La primera tarea antes de nosotros consiste en crear un servicio WCF para una aplicación móvil para comunicarse con.

1. Inicie Visual Studio de 2017 y crear un nuevo proyecto.
1. En el **nuevo proyecto** cuadro de diálogo, seleccione la **WCF > biblioteca de servicios WCF** plantilla y nombre de la solución `HelloWorldService`:

  ![](walkthrough-working-with-wcf-images/new-wcf-service.png "Crear una nueva biblioteca de servicio WCF")

1. En **el Explorador de soluciones**, agregue una nueva clase denominada `HelloWorldData` al proyecto:

        using System.Runtime.Serialization;

        namespace HelloWorldService
        {
            [DataContract]
            public class HelloWorldData
            {
                [DataMember]
                public bool SayHello { get; set; }

                [DataMember]
                public string Name { get; set; }

                public HelloWorldData()
                {
                    Name = "Hello ";
                    SayHello = false;
                }
            }
        }

1. En **el Explorador de soluciones**, cambie el nombre `IService1.cs` a `IHelloWorldService.cs`y cambie el nombre `Service1.cs` a `HelloWorldService.cs`.
1. En **el Explorador de soluciones**, abra `IHelloWorldService.cs` y reemplace el código con el código siguiente:

        using System.ServiceModel;

        namespace HelloWorldService
        {
            [ServiceContract]
            public interface IHelloWorldService
            {
                [OperationContract]
                string SayHelloTo(string name);

                [OperationContract]
                HelloWorldData GetHelloData(HelloWorldData helloWorldData);
            }
        }

    Este servicio proporciona dos métodos: una que toma una cadena como un parámetro y otro que toma un objeto. NET.

1. En **el Explorador de soluciones**, abra `HelloWorldService.cs` y reemplace el código con el código siguiente:

        using System;

        namespace HelloWorldService
        {
            public class HelloWorldService : IHelloWorldService
            {
                public HelloWorldData GetHelloData(HelloWorldData helloWorldData)
                {
                    if (helloWorldData == null)
                        throw new ArgumentException("helloWorldData");

                    if (helloWorldData.SayHello)
                        helloWorldData.Name = "Hello World to {helloWorldData.Name}";

                    return helloWorldData;
                }

                public string SayHelloTo(string name)
                {
                    return "Hello World to you, {name}";
                }
            }
        }

1. En **el Explorador de soluciones**, abra `App.config`, actualizar la `name` atributo de la `<service>` nodo, el `contract` atributo de la `<endpoint>` nodo y el `baseAddress` atributo de la `<add>` nodo:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            ...
            <services>
              <service name="HelloWorldService.HelloWorldService">
                <endpoint address="" binding="basicHttpBinding" contract="HelloWorldService.IHelloWorldService">
                  <identity>
                    <dns value="localhost" />
                  </identity>
                </endpoint>
                <endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />
                <host>
                  <baseAddresses>
                    <add baseAddress="http://localhost:8733/Design_Time_Addresses/HelloWorldService/" />
                  </baseAddresses>
                </host>
              </service>
            </services>
            ...
        </configuration>

1. Compile y ejecute el servicio WCF. El servicio se hospedará en el cliente de prueba WCF:

  ![](walkthrough-working-with-wcf-images/hosted-wcf-service.png "Servicio WCF que se ejecuta en el cliente de prueba")

1. Con el cliente de prueba WCF que se ejecuta, inicie un explorador y navegue hasta el punto de conexión para el servicio WCF:

  ![](walkthrough-working-with-wcf-images/wcf-service-browser.png "Página de información del explorador de servicio WCF")

> [!IMPORTANT]
> **Nota:** en la siguiente sección solo es necesaria si debe aceptar las conexiones remotas en una estación de trabajo de Windows 10. La sección puede omitirse si tiene una plataforma alternativa en la que se va a implementar el servicio WCF.

<a name="Allow_Remote_Access_to_IIS_Express" />

## <a name="configuring-remote-access-to-iis-express"></a>Configurar acceso remoto a IIS Express

Hospedaje de WCF localmente es adecuado cuando sólo se incluyen las conexiones desde el equipo local. Sin embargo, los dispositivos remotos (por ejemplo, un dispositivo Android o iPhone) no tendrá acceso a un servicio WCF local. Por lo tanto, en esta sección se explica cómo configurar Windows 10 y IIS Express para aceptar conexiones remotas:

1.  **Configurar IIS Express para las conexiones remotas Aceptar** -este paso consiste en Editar el archivo de configuración de IIS Express Aceptar conexiones remotas en un puerto específico y, a continuación, configurar una regla para IIS Express Aceptar el tráfico entrante.
1.  **Agregar una excepción al Firewall de Windows** -debe abrir un puerto a través de Firewall de Windows que las aplicaciones remotas pueden usar para comunicarse con el servicio WCF.

Debe conocer la dirección IP de la estación de trabajo. Para los fines de este ejemplo supondremos que nuestra estación de trabajo tiene la dirección IP 192.168.1.143.

1. Comencemos mediante la configuración de IIS Express para que escuche las solicitudes externas. Podemos hacerlo editando el archivo de configuración de IIS Express en `[solutiondirectory]\.vs\config\applicationhost.config`, tal y como se muestra en la captura de pantalla siguiente:

    [![](walkthrough-working-with-wcf-images/image05.png "Podemos hacerlo editando el archivo de configuración de IIS Express en solutiondirectory.vsconfigapplicationhost.config, como se muestra en esta captura de pantalla")](walkthrough-working-with-wcf-images/image05.png#lightbox)


    Busque la `site` elemento con el nombre `HelloWorldWcfHost`. Debe tener un aspecto como el fragmento XML siguiente:

        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
            </bindings>
        </site>

    Tendrá que agregar otro `binding` para abrir el puerto 8734 para el tráfico externo. Agregue el siguiente código XML para el `bindings` elemento, sustituyendo la dirección IP de su propia dirección IP:

        <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />

    Esto va a configurar IIS Express para poder aceptar tráfico HTTP procedente de cualquier dirección IP remota en el puerto 8734 la dirección IP externa del equipo. Esto por encima del fragmento de código se supone que la dirección IP del equipo que ejecuta IIS Express es 192.168.1.143. Después de los cambios, el `bindings` elemento debe ser similar al siguiente:

        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
                <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
            </bindings>
        </site>

1. A continuación, necesitamos configurar IIS Express Aceptar conexiones entrantes en el puerto 8734. Inicio de una copia de seguridad un símbolo del sistema administrativo y ejecute este comando:

    `> netsh http add urlacl url=http://192.168.1.143:9608/ user=everyone`

1. El último paso es configurar Firewall de Windows para permitir el tráfico externo en el puerto 8734. Desde un símbolo del sistema administrativo, ejecute el siguiente comando:

    `> netsh advfirewall firewall add rule name="IISExpressXamarin" dir=in protocol=tcp localport=8734 profile=private remoteip=localsubnet action=allow`

    Este comando le permitirá el tráfico entrante en el puerto 8734 desde todos los dispositivos en la misma subred que la estación de trabajo de Windows 10.

Ha creado un servicio WCF muy básico hospedado en IIS Express que va a aceptar conexiones entrantes desde otros dispositivos o equipos de la subred. Puede probar lo siguiente al ejecutar la aplicación y la visita a `http://localhost:8733/Design_Time_Addresses/HelloWorldService/` en la estación de trabajo y `http://192.168.1.143:8734/Design_Time_Addresses/HelloWorldService/` desde otro equipo de la subred.

Para permitir que IIS Express mantener la ejecución y que sirve al servicio, desactivar el **editar y continuar** opción *propiedades del proyecto > Web > depuradores*.

## <a name="creating-a-proxy-for-the-web-service"></a>Crear a un Proxy para el servicio Web

Debe crearse un proxy de servicio web para el servicio WCF, antes de que una aplicación puede utilizar el servicio. Esto se puede lograr de la siguiente manera:

1. Agregar una biblioteca de clases .NET estándar denominado `HelloWorldServiceProxy`y elimine todas las clases en el proyecto.
1. Ejecute el `HelloWorldService` proyecto.
1. Con el `HelloWorldService` proyecto de equipo móvil y agregar un nuevo **servicio conectado** al proyecto, mediante el **proveedor de referencia de servicio Web de Microsoft WCF**.
1. En el **punto de conexión de servicio** pestaña de la **configurar referencia de servicio Web WCF** cuadro de diálogo, haga clic en el **Discover** botón, elimine `mex` desde el final de los punto de conexión en el **URI** lista desplegable, escriba `HelloWorldServiceProxy` como el **Namespace**y haga clic en el **siguiente** botón.
1. En el **opciones de tipo de datos** pestaña de la **configurar referencia de servicio Web WCF** cuadro de diálogo, acepte los valores predeterminados, haga clic en el **siguiente** botón.
1. En el **opciones de cliente** pestaña de la **configurar referencia de servicio Web WCF** cuadro de diálogo, asegúrese de que el **público** casilla de verificación está seleccionada y haga clic en el **finalizar**  botón.
1. Compilar el `HelloWorldServiceProxy` proyecto.

> [!NOTE]
> **Tenga en cuenta**: una alternativa para crear el proxy con el proveedor de referencia de servicio de Web de Microsoft WCF en Visual Studio de 2017 es usar la herramienta de utilidad de metadatos de ServiceModel (svcutil.exe). Para obtener más información, consulte [la herramienta de utilidad de metadatos de ServiceModel (Svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Creating_a_Xamarin_Android_Application" />

## <a name="creating-a-xamarinandroid-application"></a>Crear una aplicación Xamarin.Android

El proxy de servicio WCF puede ser utilizado por una aplicación Xamarin.Android, como se indica a continuación:

1. En Visual Studio, agregue un nuevo proyecto de Android en blanco a la solución y asígnele el nombre `HelloWorld.Android`.
1. En el `HelloWorld.Android` del proyecto, agregue una referencia a la `HelloWorldServiceProxy` proyecto y una referencia a la `System.ServiceModel` espacio de nombres.
1. En **el Explorador de soluciones**, abra `Resources/layout/main.axml` y reemplace el XML existente por el siguiente código XML:

        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                  android:orientation="vertical"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent">
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/sayHelloWorldButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/say_hello_world" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/sayHelloWorldTextView" />
            </LinearLayout>
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/getHelloWorldDataButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/get_hello_world_data" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/getHelloWorldDataTextView" />
            </LinearLayout>
        </LinearLayout>

    Las capturas de pantalla siguiente muestran la interfaz de usuario en el diseñador:

    [![](walkthrough-working-with-wcf-images/image09.png "Se trata de una captura de pantalla de aspecto de esta interfaz de usuario en el diseñador")](walkthrough-working-with-wcf-images/image09.png#lightbox)

1. En **el Explorador de soluciones**, abra `Resources/values/Strings.xml` y agregue el siguiente código XML:

        <string name="say_hello_world">Say Hello World</string>
        <string name="get_hello_world_data">Get Hello World data</string>

1. En **el Explorador de soluciones**, abra `MainActivity.cs` y reemplace el código existente por el código siguiente:

        [Activity(Label = "HelloWorld.Android", MainLauncher = true)]
        public class MainActivity : Activity
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");

            HelloWorldServiceClient _client;
            Button _getHelloWorldDataButton;
            TextView _getHelloWorldDataTextView;
            Button _sayHelloWorldButton;
            TextView _sayHelloWorldTextView;
            ...
        }

    Reemplace `<insert_WCF_service_endpoint_here>` con la dirección de su punto de conexión WCF.

1. En `MainActivity.cs`, modifique la `OnCreate` método para que contenga el código siguiente:

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            InitializeHelloWorldServiceClient();

            // This button will invoke the GetHelloWorldData - the method that takes a C# object as a parameter.
            _getHelloWorldDataButton = FindViewById<Button>(Resource.Id.getHelloWorldDataButton);
            _getHelloWorldDataButton.Click += GetHelloWorldDataButtonOnClick;
            _getHelloWorldDataTextView = FindViewById<TextView>(Resource.Id.getHelloWorldDataTextView);

            // This button will invoke SayHelloWorld - this method takes a simple string as a parameter.
            _sayHelloWorldButton = FindViewById<Button>(Resource.Id.sayHelloWorldButton);
            _sayHelloWorldButton.Click += SayHelloWorldButtonOnClick;
            _sayHelloWorldTextView = FindViewById<TextView>(Resource.Id.sayHelloWorldTextView);
        }

    El código anterior inicializa las variables de instancia de la clase y se conecta algunos controladores de eventos.

1. En `MainActivity.cs`, crear una instancia de la clase de proxy de cliente mediante la adición de los dos métodos siguientes:

        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }

    El código anterior crea una instancia e inicializa un `HelloWorldServiceClient` objeto.

1. En `MainActivity.cs`, agregar controladores incluso para los dos botones en la `Activity`:

        async void GetHelloWorldDataButtonOnClick(object sender, EventArgs e)
        {
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            _getHelloWorldDataTextView.Text = "Waiting for WCF...";
            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                _getHelloWorldDataTextView.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButtonOnClick(object sender, EventArgs e)
        {
            _sayHelloWorldTextView.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                _sayHelloWorldTextView.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

1. Ejecute la aplicación, asegúrese de que está ejecutando el servicio WCF y haga clic en los dos botones. La aplicación llamará a WCF de forma asincrónica, siempre que el `Endpoint` campo se ha configurado correctamente:

  [![](walkthrough-working-with-wcf-images/image08.png "Dentro de 30 segundos se debe recibir una respuesta de cada método WCF y nuestra aplicación debería ser similar a esta captura de pantalla")](walkthrough-working-with-wcf-images/image08.png#lightbox)

<a name="Creating_a_Xamarin_iOS_Application" />

## <a name="creating-a-xamarinios-application"></a>Crear una aplicación de Xamarin.iOS

El proxy de servicio WCF puede ser utilizado por una aplicación Xamarin.iOS, como se indica a continuación:

1. En Visual Studio, agregue un nuevo iPhone **única aplicación de vista** proyecto a la solución y asígnele el nombre `HelloWorld.iOS`.
1. En el `HelloWorld.iOS` del proyecto, agregue una referencia a la `HelloWorldServiceProxy` proyecto y una referencia a la `System.ServiceModel` espacio de nombres.
1. En **el Explorador de soluciones**, haga doble clic en `Main.storyboard` para abrir el archivo en el Diseñador de iOS. A continuación, agregue el siguiente `UIButton` y `UITextView` controles:

    ||nombre|Title|
    |--- |--- |--- |
    |`UIButton`|`sayHelloWorldButton`|Diga "¡Hello, World"|
    |`UITextView`|`sayHelloWorldText`||
    |`UIButton`|`getHelloWorldDataButton`|"¡Hello, World" de obtener datos|
    |`UITextView`|`getHelloWorldDataText`||

    Después de agregar los controles, la interfaz de usuario debe ser similar a la captura de pantalla siguiente:

    [![](walkthrough-working-with-wcf-images/image12.png "Después de agregar los controles, la interfaz de usuario debe ser similar a esta captura de pantalla")](walkthrough-working-with-wcf-images/image12.png#lightbox)

1. En **el Explorador de soluciones**, abra `ViewController.cs` y agregue el código siguiente:

        public partial class ViewController : UIViewController
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");
            HelloWorldServiceClient _client;
            ...
        }

    Reemplace `<insert_WCF_service_endpoint_here>` con la dirección de su punto de conexión WCF.

1. En `ViewController.cs`, actualizar la `ViewDidLoad` método por lo que TI tiene el siguiente aspecto:

        public override void ViewDidLoad()
        {
            base.ViewDidLoad();
            InitializeHelloWorldServiceClient();

            getHelloWorldDataButton.TouchUpInside += GetHelloWorldDataButton_TouchUpInside;
            sayHelloWorldButton.TouchUpInside += SayHelloWorldButton_TouchUpInside;
        }

1. En `ViewController.cs`, agregue el `InitializeHelloWorldServiceClient` y `CreateBasicHttpBinding` métodos:

        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }

1. En `ViewController.cs`, agregar controladores de eventos para el `TouchUpInside` eventos en los dos `UIButton` instancias:

        async void GetHelloWorldDataButton_TouchUpInside(object sender, EventArgs e)
        {
            getHelloWorldDataText.Text = "Waiting for WCF...";
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                getHelloWorldDataText.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButton_TouchUpInside(object sender, EventArgs e)
        {
            sayHelloWorldText.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                sayHelloWorldText.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

1. Ejecute la aplicación, asegúrese de que está ejecutando el servicio WCF y haga clic en los dos botones. La aplicación llamará a WCF de forma asincrónica, siempre que el `Endpoint` campo se ha configurado correctamente:

    [![](walkthrough-working-with-wcf-images/image10.png "Dentro de 30 segundos se debe recibir una respuesta de cada método WCF y nuestra aplicación debería ser similar a esta captura de pantalla")](walkthrough-working-with-wcf-images/image10.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumen

Este tutorial trata cómo trabajar con un servicio WCF en una aplicación móvil mediante Xamarin.Android y Xamarin.iOS. Se ha explicado cómo crear un servicio WCF y se explica cómo configurar Windows 10 y IIS Express para aceptar conexiones de dispositivos remotos. A continuación, se explica cómo generar a un cliente de proxy WCF y se muestra cómo utilizar al cliente de proxy en aplicaciones Xamarin.iOS y Xamarin.Android.


## <a name="related-links"></a>Vínculos relacionados

- [HelloWorld (ejemplo)](https://developer.xamarin.com/samples/mobile/WCF-Walkthrough/)
- [Desarrollar aplicaciones orientadas a servicios mediante WCF](https://docs.microsoft.com/en-us/dotnet/framework/wcf/index)
- [Cómo: crear un cliente de Windows Communication Foundation](https://docs.microsoft.com/en-us/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Herramienta de utilidad de metadatos de ServiceModel (svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
