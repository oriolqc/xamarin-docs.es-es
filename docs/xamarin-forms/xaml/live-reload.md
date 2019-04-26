---
title: Recarga en vivo de Xamarin (versión preliminar)
description: Implementar y ver los cambios en el XAML se reflejan en directo, sin necesidad de otra compilación.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
robots: noindex
ms.date: 10/26/2018
ms.openlocfilehash: 21ff09f2af93ee46578b959111bf744ba05a74d7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384938"
---
# <a name="xamarin-live-reload-preview"></a>Recarga en vivo de Xamarin (versión preliminar)

> [!NOTE]
> La versión preliminar de Xamarin Live Reload ha finalizado y queremos dar las gracias a todos por sus sugerencias y comentarios. Lea nuestra [plan](https://docs.microsoft.com/visualstudio/productinfo/vs-roadmap) para Visual Studio de 2019 obtener más información sobre las nuevas características de productividad que estamos trabajando para Xamarin.Forms. Esta extensión seguirá estando disponible para Visual Studio 2017, pero no se recibe las actualizaciones futuras.

Recarga de Xamarin Live permite **realizar cambios en el XAML y ver que se reflejen en directo, sin necesidad de otra compilación e implementar**. Los cambios realizados en el XAML se implementará en Guardar y se refleja en el destino de implementación.

## <a name="requirements"></a>Requisitos

* [Visual Studio 2017 versión 15.7 o superior](https://visualstudio.microsoft.com/vs/) con el **desarrollo móvil con .NET** carga de trabajo.
* [Xamarin.Forms 3.0.0 o posterior](https://www.nuget.org/packages/Xamarin.Forms/).

## <a name="getting-started"></a>Introducción
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. Instalar la recarga en vivo de Xamarin desde Visual Studio Marketplace

Recarga en vivo de Xamarin se distribuyen a través de Visual Studio Marketplace. Para instalar la extensión, visite la [página recarga en vivo de Xamarin en Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) sitio Web y haga clic en **descargar**.

Abra el .vsix que se descarga y haga clic en **instalar**.

![Confirmación de Xamarin Live Reload instalador de Visual Studio](images/LiveReloadVSIXInstall.png)

Como alternativa, puede buscar en el **Online** pestaña en el **extensiones y actualizaciones** diálogo dentro de Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. Configurar la aplicación para usar la recarga en vivo

Adición de recarga en vivo a las aplicaciones móviles existentes puede hacerse en tres pasos:

1. Asegúrese de que todos los proyectos se actualizan para usar [Xamarin.Forms 3.0.0 o posterior](https://www.nuget.org/packages/Xamarin.Forms/) o superior.

2. Agregar el **Xamarin.LiveReload** paquete NuGet:

    a. **.NET standard** : instalar el **Xamarin.LiveReload** NuGet en la biblioteca de .NET Standard 2.0. Esto no debe instalarse en los proyectos de plataforma. Asegúrese de que el **origen del paquete** está establecido en **todas**.
    
    b. **Proyectos compartidos** : instalar el **Xamarin.LiveReload** NuGet en todos los proyectos de plataforma (por ejemplo, Android, iOS, UWP, etcetera.). Asegúrese de que el **origen del paquete** está establecido en **todas**.

    [![Agregar paquete NuGet de Xamarin la recarga en vivo con el Administrador de paquetes de NuGet](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. Agregar `LiveReload.Init();` al constructor en el `Application` clase, como se muestra en el siguiente fragmento de código:

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        #if DEBUG
        LiveReload.Init();
        #endif
        
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3. Iniciar la recarga en vivo

Compile e implemente la aplicación. Una vez implementado la aplicación, abra un archivo XAML, realizar algunos cambios y guarde el archivo. Se volvió a implementar los cambios en el destino de implementación.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Live funciona de volver a cargar con los cambios realizados en cualquier archivo XAML. Los cambios realizados en C# o agregar y quitar paquetes de NuGet requiere una nueva compilación e implementación para que surta efecto.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>¿Hay recarga en vivo de Xamarin en Visual Studio para Mac? 

No, la versión preliminar de Xamarin Live Reload solo está disponible para Visual Studio 2017.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>¿Esto funciona con todas las bibliotecas, como Prism? 

Dado que se compila la aplicación, recarga en vivo funciona con todas las bibliotecas, como Prism y bibliotecas de controles de terceros, como Telerik, Infragistics, Syncfusion, ArcGIS, GrapeCity y otros proveedores de control.

### <a name="what-changes-does-live-reload-redeploy"></a>¿Qué cambios recarga en vivo volver a implementar? 

La recarga en vivo sólo aplica a los cambios realizados en XAML o CSS. Si realiza cambios en un archivo de C#, se deberán volver a compilar. 

### <a name="what-platforms-are-supported"></a>¿Qué plataformas son compatibles? 

La recarga en vivo funciona en cualquier plataforma compatible con Xamarin.Forms, incluidos Android, iOS y UWP.

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>¿Esto funciona en los emuladores, simuladores y dispositivos físicos? 

Sí, recarga en vivo funciona con todos los destinos de implementación válido, como los emuladores de Android, simuladores de iOS y dispositivos físicos. Implementación en un dispositivo requiere que el dispositivo y el equipo esté en la misma red Wi-Fi.

### <a name="does-this-work-with-corporate-networks"></a>¿Esto funciona con redes corporativas?

Si está depurando en un emulador de Android o el simulador de iOS, recarga en vivo usa localhost para comunicarse. Si desea implementar en un dispositivo, el dispositivo y el equipo deben estar en la misma red Wi-Fi. En escenarios donde esto no es posible, puede [configurar su propio servidor de Live Reload](#live-reload-server), lo que le permitirá a la recarga en vivo, independientemente de la configuración de conectividad de red.

### <a name="does-it-require-debugging-the-app"></a>¿Necesita depurar la aplicación? 

No. De hecho, incluso puede iniciar sus destinos aplicaciones compatibles (Android, iOS y UWP) en cualquier número de dispositivos o emuladores o simuladores y verlos todos actualizar a la vez. 

## <a name="limitations"></a>Limitaciones

* Se admite la recarga sólo de XAML.
* Estado de la interfaz de usuario no puede mantenerse entre las nuevas implementaciones, a menos que use MVVM.

## <a name="known-issues"></a>Problemas conocidos

* Solo se admite en Visual Studio.
* Vinculación debe establecerse en **no vincular** o **vincular solo SDK de marco** 
* Volver a cargar recursos de toda la aplicación (es decir, **App.xaml** o compartir los diccionarios de recursos), se restablece la navegación de la aplicación. 
* Recarga de ContentView actualmente requiere volver a cargar la página contenedora.
* Elementos que contienen AutomationId pueden producir un error de volver a cargar.
* Editar XAML mientras la depuración de UWP puede provocar un bloqueo en tiempo de ejecución. Solución: Use **iniciar sin depurar (CTRL+F5)** en lugar de **Iniciar depuración (F5)**.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="error-codes"></a>Códigos de error

* **XLR001**: *El proyecto actual hace referencia a la versión del paquete NuGet 'Xamarin.LiveReload' [versión] pero la extensión de Xamarin Live Reload requiere la versión [versión].*

  Con el fin de permitir la iteración rápida y evolución de la característica Live Reload, el paquete nuget y la extensión de Visual Studio deben coincidir exactamente. Actualice el paquete nuget a la misma versión de la extensión que ha instalado.

* **XLR002**: *La recarga en vivo requiere al menos la propiedad 'MqttHostname' al compilar desde la línea de comandos. Como alternativa, puede establecer 'EnableLiveReload' en 'false' para deshabilitar la característica.*

  Las propiedades requeridas por la recarga en vivo no están disponibles al compilar desde la línea de comandos (o en la integración continua) y, por lo tanto, se debe proporcionar explícitamente. 

* **XLR003**: *Paquete de nuget recarga en vivo requiere la instalación de la extensión de Xamarin Live volver a cargar Visual Studio.*

  Ha intentado compilar un proyecto que hace referencia al paquete de nuget recarga en vivo, pero no está instalada la extensión Visual.  

* *Excepción al cargar los ensamblados: System.IO.FileNotFoundException: No se pudo cargar el ensamblado ' Xamarin.Live.Reload, Version = 0.3.27.0, Culture = neutral, PublicKeyToken ='.*

  Debe usar el proyecto host `PackageReference` en lugar de `packages.config`

### <a name="app-doesnt-connect"></a>No conecta aplicación

Cuando se compila la aplicación, la información de **Herramientas > Opciones > Xamarin > recarga en vivo** (nombre, puerto y cifrado de claves de host) se incrustan en la aplicación, así que, cuando `LiveReload.Init()` se ejecuta ningún emparejamiento o la configuración es es necesario para la conexión se realice correctamente.

Aparte de problemas de red normales (firewall, el dispositivo en una red diferente), la razón principal por que la aplicación no puede conectarse correctamente IDE es porque su configuración es diferente de Visual Studio. Esto puede ocurrir si:

* Se compiló la aplicación en un equipo diferente.
* Aplicación se compilan e implementan en una sesión diferente de Visual Studio, y **generar automáticamente las claves de cifrado** está activada (valor predeterminado) **Herramientas > Opciones > Xamarin > Live Reload**.
* Configuración de Visual Studio se han cambiado (es decir, claves de nombre de host, puerto o cifrado), pero la aplicación no se compiló y volver a implementarlo.

Estos casos se resuelven al generar e implementar la aplicación de nuevo.

### <a name="uninstalling-preview-1"></a>Desinstalar Preview 1

Si tiene una versión preliminar anterior y tiene problemas para desinstalarlo, siga estos pasos:

1. Elimine la carpeta **C:\Program Files (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (Nota: reemplace "Enterprise" con la edición instalada y "Vista previa" con "2017" if, instala un estables frente a)
2. Abra un **símbolo** para que Visual Studio y ejecute `devenv /updateconfiguration`. 

## <a name="tips--tricks"></a>Sugerencias y trucos

* Siempre que no cambie la configuración de la recarga en vivo (incluidas las claves de cifrado, por ejemplo, si se desactiva **generar automáticamente las claves de cifrado**) y compilar desde el mismo equipo, no necesita para compilar e implementar la aplicación después de la inicial implementar, a menos que cambie el código o dependencias. Solo pueden iniciar nuevamente una aplicación implementada anteriormente y se conectará al último host usado.

* No hay ninguna limitación en cuántos dispositivos puede conectarse a la misma sesión de Visual Studio. Se puede implementar e iniciar la aplicación en dispositivos o simuladores tantos según sea necesario para ver el trabajo de recargando en vivo en todos ellos al mismo tiempo.

* La recarga en vivo sólo se volverá a cargar la parte de la interfaz de usuario de la aplicación, pero tampoco *no* volver a crear las páginas, ni lo reemplace su modelo de vista (o contexto de enlace). Esto significa que el *todo* siempre se conserva el estado de la aplicación a través de recarga, incluidas las dependencias insertadas.

## <a name="live-reload-server"></a>Servidor de la recarga en vivo

En escenarios donde una conexión desde la aplicación en ejecución en el equipo (tal como se indica mediante el uso de `localhost` o `127.0.0.1` en **Herramientas > Opciones > Xamarin > Live Reload**) no es posible (es decir, firewalls y redes diferentes), Puede configurar un servidor remoto en su lugar, lo que el IDE y la aplicación conectar a.

La recarga en vivo utiliza la norma [protocolo MQTT](http://mqtt.org/) para intercambiar mensajes y, por lo tanto, puede comunicarse con [servidores de terceros](https://github.com/mqtt/mqtt.github.io/wiki/servers). Hay incluso [servidores públicos](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (también conocido como *agentes*) que puede usar. Se ha probado la recarga en vivo con `broker.hivemq.com` y `iot.eclipse.org` los nombres de host, así como los servicios proporcionados por [www.cloudmqtt.com](https://www.cloudmqtt.com) y [www.cloudamqp.com](https://www.cloudamqp.com). También puede implementar su propio servidor MQTT en la nube, como [HiveMQ en Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud).

Puede configurar cualquier puerto, pero es habitual usar el puerto 1883 predeterminado para los servidores remotos. La característica Live Reload mensajes utilizan strong-to-end AES simétrico, por lo que es seguro conectarse a servidores remotos. De forma predeterminada, la clave de cifrado y el vector de inicialización (IV) se vuelven a generar en cada sesión de Visual Studio.

Probablemente la más sencilla consiste en instalar el [mosquitto](https://mosquitto.org) server en una VM de Ubuntu en blanco en Azure:

1. Crear una nueva máquina virtual de servidor de Ubuntu en Azure Portal
2. Agregar una nueva regla de puerto de entrada para 1883 (puerto MQTT de forma predeterminada) en la pestaña redes
3. Abra el [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) (modo de bash)
4. Escriba `ssh [USERNAME]@[PUBLIC_IP]` con el nombre de usuario que eligió en 1) y la dirección IP pública que se muestra en la página de información general de la máquina virtual
5. Ejecute `sudo apt-get install mosquitto`, escribir la contraseña que eligió en 1)

Ahora puede usar esa dirección IP para conectarse a su propio servidor MQTT.
