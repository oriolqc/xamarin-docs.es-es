---
title: Recarga en vivo
description: Implementar y ver los cambios en el XAML reflejar en vivo, sin necesidad de compilación de otro.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
ms.date: 04/23/2018
ms.openlocfilehash: 11e876207285689b230bb2fada3a4c836443e360
ms.sourcegitcommit: a69439ad4c9fd0abe759143687d3b23582573d90
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="xamarin-live-reload"></a>Volver a cargar en vivo de Xamarin

![Vista previa](~/media/shared/preview.png)

Le permite volver a cargar el Live de Xamarin **realizar cambios en el código XAML y ver reflejados en vivo, sin necesidad de otra compilación e implementar**. Los cambios realizados en el XAML se vuelve a instalar en Guardar y se refleja en el destino de la implementación.

Puesto que la aplicación se compila cuando se usa en vivo, volver a cargar, funciona con todas las bibliotecas y los controles de terceros. Live funciona de volver a cargar en todas las plataformas Xamarin.Forms admite, como Android, iOS y UWP y funciona en todos los destinos de implementación válido, como simuladores, emuladores y dispositivos físicos.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Recarga en vivo sólo está disponible en Visual Studio de 2017 actualmente.

## <a name="requirements"></a>Requisitos

* [Visual Studio 2017 15,7 Preview 4](https://www.visualstudio.com/vs/preview/) o superior con la **desarrollo móvil con .NET** carga de trabajo.
* [Xamarin.Forms 3.0.354232-pre3](https://www.nuget.org/packages/Xamarin.Forms/3.0.0.354232-pre3) o superior.

## <a name="getting-started"></a>Introducción
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. Instalar Xamarin en vivo recarga desde el catálogo de soluciones de Visual Studio.

Recarga de Xamarin en vivo se distribuye a través de Visual Studio Marketplace. Para instalar la extensión, visite la [página de recarga de vida de Xamarin en Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) sitio Web y haga clic en **descargar**.

Abra el .vsix que se descarga y haga clic en **instalar**.

![Instalador de Visual Studio confirmación de recarga de vida de Xamarin](images/LiveReloadVSIXInstall.png)

> Como alternativa, puede buscar en el **en línea** pestaña en el **extensiones y actualizaciones** diálogo dentro de Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. Configurar la aplicación para que use Live, volver a cargar.

Agregación Live recarga a aplicaciones móviles existentes se puede realizar en tres pasos:

1. Asegúrese de que todos los proyectos se actualizan para usar [Xamarin.Forms 3.0.354232-pre3](https://www.nuget.org/packages/Xamarin.Forms/3.0.0.354232-pre3) o superior.
2. Instalar el **Xamarin.LiveReload** NuGet en la biblioteca estándar de .NET 2.0. No se necesita instalarse en los proyectos de plataforma. Asegúrese de que el **origen del paquete** está establecido en **todos los**.

![Agregar NuGet de recarga en vivo de Xamarin con el Administrador de paquetes de NuGet](images/addlivereloadnuget.png)

3. Agregar `LiveReload.Init();` al constructor en el `Application` de la clase, como se muestra en el siguiente fragmento de código:

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        LiveReload.Init();
    
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3. Iniciar volver a cargar en vivo.

Compile e implemente la aplicación. Una vez que la aplicación está implementado, abra un archivo XAML, realizar algunos cambios y guarde el archivo. Se volvió a implementar los cambios en el destino de implementación.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Live funciona de volver a cargar con los cambios en cualquier archivo XAML. Cambios en C# o agregar o quitar paquetes de NuGet requiere una nueva compilación e implementar para que surta efecto.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>¿Está disponible en Visual Studio Xamarin Live recarga para Mac? 

La versión preliminar inicial de recarga de Xamarin en vivo sólo está disponible para Visual Studio de 2017. Compatibilidad con Visual Studio para Mac está planeada para una versión futura.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>¿Esto funciona con todas las bibliotecas, como prisma? 

Puesto que la aplicación se compila, Live recarga funciona con todas las bibliotecas, como Prism y bibliotecas de controles de terceros, como Telerik, Infragistics, Syncfusion, ArcGIS, GrapeCity y otros proveedores de control.

### <a name="what-changes-does-live-reload-redeploy"></a>¿Qué cambios recarga Live volver a implementar? 

Recarga en vivo sólo aplica a los cambios realizados en XAML. Si realiza cambios en un archivo de C#, se requerirá una nueva compilación. Compatibilidad para volver a cargar C# está planeada para una versión futura.

### <a name="what-platforms-are-supported"></a>¿En qué plataformas son compatibles? 

Recarga en directo funciona en cualquier plataforma compatible con Xamarin.Forms, como UWP, iOS y Android.

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>¿Esto funciona en dispositivos físicos, emuladores y simuladores? 

Sí, Live recarga funciona con todos los destinos de implementación válido, como Android emuladores, simuladores de iOS y dispositivos físicos. Implementación en un dispositivo requiere que el dispositivo y el equipo se encuentren en la misma red Wi-Fi.

### <a name="does-this-work-with-corporate-networks"></a>¿Esto funciona con las redes corporativas?

Si está depurando en un emulador de Android o el simulador de iOS, Live recarga utiliza localhost para comunicarse. Si desea implementar en un dispositivo, el dispositivo y el equipo deben estar en la misma red Wi-Fi. En escenarios donde no es posible, puede [configurar su propio servidor de Live recarga](#live-reload-server), que le permitirá para recargar Live, independientemente de la configuración de conectividad de red.

### <a name="does-it-require-debugging-the-app"></a>¿Requiere para depurar la aplicación? 

No. De hecho, incluso puede iniciar todos los objetivos de aplicaciones compatibles (Android, iOS y UWP) en cualquier número de dispositivos o simuladores/emuladores y verlas todas actualizar a la vez. 

## <a name="limitations"></a>Limitaciones

* Se admite solo volver a cargar de XAML.
* Solo se admite en Visual Studio.
* Solo funciona con las bibliotecas de .NET estándar.
* No se admiten las hojas de estilos CSS.
* Estado de la interfaz de usuario no puede mantenerse entre las nuevas implementaciones, a menos que utilice MVVM.

## <a name="live-reload-server"></a>Servidor de recarga en vivo

En escenarios donde una conexión de la aplicación en ejecución en el equipo (tal como se indica mediante el uso de `localhost` o `127.0.0.1` en **Herramientas > Opciones > Xamarin > Live recarga**) no es posible (es decir, los servidores de seguridad, redes diferentes), Puede configurar un servidor remoto en su lugar, lo que el IDE y la aplicación conectar a.

Recarga en vivo utiliza la norma [protocolo MQTT](http://mqtt.org/) para intercambiar mensajes y, por tanto, puede comunicarse con [servidores de terceros](https://github.com/mqtt/mqtt.github.io/wiki/servers). Hay incluso [servidores públicos](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (también conocido como *corredores de bolsa*) disponibles que se pueden utilizar. Recarga en vivo se ha probado con `broker.hivemq.com` y `iot.eclipse.org` nombres de host, así como los servicios proporcionados por [www.cloudmqtt.com](https://www.cloudmqtt.com) y [www.cloudamqp.com](https://www.cloudamqp.com). También puede implementar su propio servidor MQTT en la nube, como [HiveMQ en Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud) o [conejo MQ en AWS](http://www.rabbitmq.com/ec2.html). 

Puede configurar cualquier puerto, pero es habitual usar el puerto de 1883 predeterminado para los servidores remotos. Mensajes de volver a cargar activos usan-to-end AES simétrico cifrado seguro, por lo que es seguro conectarse a servidores remotos. De forma predeterminada, la clave de cifrado y el vector de inicialización (IV) se vuelven a generar en cada sesión de Visual Studio.

## <a name="troubleshooting"></a>Solución de problemas

Cuando se compila la aplicación, la información de **Herramientas > Opciones > Xamarin > Live, volver a cargar** (nombre, puerto y cifrado de claves de host) se incrustan en la aplicación, así que, cuando `LiveReload.Init();` se ejecuta, ningún emparejamiento o la configuración es es necesario para la conexión se realice correctamente.

Aparte de problemas de red normales (firewall, dispositivos en una red diferente), la razón principal por que la aplicación no puede conectarse correctamente IDE es porque su configuración es diferente de Visual Studio. Esto puede ocurrir si:

* Se compiló la aplicación en un equipo diferente.
* Aplicación se compila y se implementa en una sesión de Visual Studio diferentes, y **generar automáticamente las claves de cifrado** está activada (valor predeterminado) **Herramientas > Opciones > Xamarin > Live recarga**.
* Configuración de Visual Studio se cambiaron (es decir, nombre de host, puerto o cifrado de claves), pero la aplicación no se ha compilado y volver a implementarse.

Estos casos se resuelven al generar e implementar la aplicación de nuevo.

## <a name="tips--tricks"></a>Sugerencias y trucos

* Siempre y cuando no cambie la configuración de Live recarga (incluidas las claves de cifrado, por ejemplo, si desactiva **generar automáticamente las claves de cifrado**) y realiza la compilación desde el mismo equipo, no es necesario compilar e implementar la aplicación después de la inicial implementar, a menos que cambie el código o dependencias. Se puede simplemente inicie de nuevo una aplicación implementada anteriormente y se conectará a la última host que se utiliza.

* No hay ninguna limitación en cuántos dispositivos pueden conectar a la misma sesión de Visual Studio. Puede implementar e iniciar la aplicación en tantos dispositivos o simuladores según sea necesario para ver el trabajo de volver a cargar en vivo en todos ellos al mismo tiempo.

* Recarga en vivo sólo se volverá a cargar la parte de la interfaz de usuario de la aplicación, pero tampoco *no* volver a crear las páginas, ni reemplace el modelo de vista (o el contexto de enlace). Esto significa que la *todo* siempre se conserva el estado de la aplicación a través de recargas más rápidas, incluidas las dependencias insertadas.
