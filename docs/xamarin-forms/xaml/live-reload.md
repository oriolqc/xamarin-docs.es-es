---
title: Recarga en vivo
description: Implementar y ver los cambios en el XAML reflejar en vivo, sin necesidad de compilación de otro.
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
ms.date: 05/11/2018
ms.openlocfilehash: 15de334500ea25d22657c257a4a4fc6887cc122c
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935433"
---
# <a name="xamarin-live-reload"></a>Volver a cargar en vivo de Xamarin

![Vista previa](~/media/shared/preview.png)

Le permite volver a cargar el Live de Xamarin **realizar cambios en el código XAML y ver reflejados en vivo, sin necesidad de otra compilación e implementar**. Los cambios realizados en el XAML se vuelve a instalar en Guardar y se refleja en el destino de la implementación.

Puesto que la aplicación se compila cuando se usa en vivo, volver a cargar, funciona con todas las bibliotecas y los controles de terceros. Live funciona de volver a cargar en todas las plataformas Xamarin.Forms admite, como Android, iOS y UWP y funciona en todos los destinos de implementación válido, como simuladores, emuladores y dispositivos físicos.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Recarga en vivo sólo está disponible en Visual Studio de 2017 actualmente.

## <a name="requirements"></a>Requisitos

* [Visual Studio 2017 versión 15,7 o superior](https://www.visualstudio.com/vs/) o superior con la **desarrollo móvil con .NET** carga de trabajo.
* [Xamarin.Forms 3.0.0 o superior](https://www.nuget.org/packages/Xamarin.Forms/) o superior.

## <a name="getting-started"></a>Introducción
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1. Instalar Xamarin en vivo Recargar desde el catálogo de soluciones de Visual Studio

Recarga de Xamarin en vivo se distribuye a través de Visual Studio Marketplace. Para instalar la extensión, visite la [página de recarga de vida de Xamarin en Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload) sitio Web y haga clic en **descargar**.

Abra el .vsix que se descarga y haga clic en **instalar**.

![Instalador de Visual Studio confirmación de recarga de vida de Xamarin](images/LiveReloadVSIXInstall.png)

Como alternativa, puede buscar en el **en línea** pestaña en el **extensiones y actualizaciones** diálogo dentro de Visual Studio.

### <a name="2-configure-your-app-to-use-live-reload"></a>2. Configurar la aplicación para utilizar Live, volver a cargar

Agregación Live recarga a aplicaciones móviles existentes se puede realizar en tres pasos:

1. Asegúrese de que todos los proyectos se actualizan para usar [Xamarin.Forms 3.0.0 o superior](https://www.nuget.org/packages/Xamarin.Forms/) o superior.

2. Agregar el **Xamarin.LiveReload** paquete NuGet:

    a. **Estándar de .NET** : instalar el **Xamarin.LiveReload** NuGet en la biblioteca estándar de .NET 2.0. No se necesita instalarse en los proyectos de plataforma. Asegúrese de que el **origen del paquete** está establecido en **todos los**.
    
    b. **Los proyectos compartidos** : instalar el **Xamarin.LiveReload** NuGet en todos los proyectos de la plataforma (por ejemplo, Android, iOS, UWP, etcetera.). Asegúrese de que el **origen del paquete** está establecido en **todos los**.

    [![Agregar NuGet de recarga en vivo de Xamarin con el Administrador de paquetes de NuGet](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. Agregar `LiveReload.Init();` al constructor en el `Application` de la clase, como se muestra en el siguiente fragmento de código:

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

### <a name="3-start-live-reloading"></a>3. Iniciar volver a cargar en vivo

Compile e implemente la aplicación. Una vez que la aplicación está implementado, abra un archivo XAML, realizar algunos cambios y guarde el archivo. Se volvió a implementar los cambios en el destino de implementación.

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

Live funciona de volver a cargar con los cambios en cualquier archivo XAML. Cambios en C# o agregar o quitar paquetes de NuGet requiere una nueva compilación e implementar para que surta efecto.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>¿Está disponible en Visual Studio Xamarin Live recarga para Mac? 

La versión preliminar inicial de recarga de Xamarin en vivo sólo está disponible para Visual Studio de 2017. Compatibilidad con Visual Studio para Mac está planeada para una versión futura.

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>¿Esto funciona con todas las bibliotecas, como prisma? 

Puesto que la aplicación se compila, Live recarga funciona con todas las bibliotecas, como Prism y bibliotecas de controles de terceros, como Telerik, Infragistics, Syncfusion, ArcGIS, GrapeCity y otros proveedores de control.

### <a name="what-changes-does-live-reload-redeploy"></a>¿Qué cambios recarga Live volver a implementar? 

Recarga en vivo sólo aplica a los cambios realizados en XAML o CSS. Si realiza cambios en un archivo de C#, se requerirá una nueva compilación. Compatibilidad para volver a cargar C# está planeada para una versión futura.

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
* Estado de la interfaz de usuario no puede mantenerse entre las nuevas implementaciones, a menos que utilice MVVM.

## <a name="known-issues"></a>Problemas conocidos

* Solo se admite en Visual Studio.
* Vinculación debe establecerse en **vínculo no** o **solo vínculo Framework SDK** 
* Volver a cargar recursos de toda la aplicación (es decir, **App.xaml** o comparte los diccionarios de recursos), se restablece la navegación de la aplicación. Este problema se solucionará en la próxima versión de vista previa.
* Editar XAML mientras depuración UWP puede provocar un bloqueo en tiempo de ejecución. Solución alternativa: Use **iniciar sin depurar (CTRL+F5)** en lugar de **Iniciar depuración (F5)**.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="error-codes"></a>Códigos de error

* **XLR001**: *el proyecto actual hace referencia la versión del paquete NuGet 'Xamarin.LiveReload' '[versión]' pero la extensión Xamarin Live recarga requiere la versión [versión].*

  Con el fin de permitir la iteración rápida y evolución de la característica de Live, volver a cargar, el paquete de nuget y la extensión de Visual Studio deben coincidir exactamente. Actualizar el paquete nuget a la misma versión de la extensión que ha instalado.

* **XLR002**: *recarga en vivo requiere al menos la propiedad 'MqttHostname' al compilar desde la línea de comandos. Por otra parte, defina 'EnableLiveReload' en 'false' para deshabilitar la característica.*

  Las propiedades requeridas por Live recarga no están disponibles al compilar desde la línea de comandos (o en la integración continua) y, por lo tanto, se debe proporcionar explícitamente. 

* **XLR003**: *paquete nuget de recarga en vivo requiere la instalación de la extensión Xamarin Live recarga Visual Studio.*

  Intentó crear un proyecto que hace referencia el paquete de nuget Live recarga pero no está instalada la extensión de Visual.  

* *Excepción al cargar los ensamblados: System.IO.FileNotFoundException: no se pudo cargar el ensamblado ' Xamarin.Live.Reload, Version = 0.3.27.0, Culture = neutral, PublicKeyToken ='.*

  El proyecto de host debería estar utilizando `PackageReference` en lugar de `packages.config`

### <a name="app-doesnt-connect"></a>No se conecta aplicación

Cuando se compila la aplicación, la información de **Herramientas > Opciones > Xamarin > Live, volver a cargar** (nombre, puerto y cifrado de claves de host) se incrustan en la aplicación, así que, cuando `LiveReload.Init()` se ejecuta, ningún emparejamiento o la configuración es es necesario para la conexión se realice correctamente.

Aparte de problemas de red normales (firewall, dispositivos en una red diferente), la razón principal por que la aplicación no puede conectarse correctamente IDE es porque su configuración es diferente de Visual Studio. Esto puede ocurrir si:

* Se compiló la aplicación en un equipo diferente.
* Aplicación se compila y se implementa en una sesión de Visual Studio diferentes, y **generar automáticamente las claves de cifrado** está activada (valor predeterminado) **Herramientas > Opciones > Xamarin > Live recarga**.
* Configuración de Visual Studio se cambiaron (es decir, nombre de host, puerto o cifrado de claves), pero la aplicación no se ha compilado y volver a implementarse.

Estos casos se resuelven al generar e implementar la aplicación de nuevo.

### <a name="uninstalling-preview-1"></a>Desinstalar Preview 1

Si tiene una versión preliminar anterior y tiene problemas para desinstalarlo, siga estos pasos:

1. Elimine la carpeta **C:\Program Files (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (Nota: reemplace "Enterprise" con la edición instalada y "Vista previa" con "2017" if instala un estable frente a)
2. Abra un **símbolo** para que Visual Studio y ejecute `devenv /updateconfiguration`. 

## <a name="tips--tricks"></a>Sugerencias y trucos

* Siempre y cuando no cambie la configuración de Live recarga (incluidas las claves de cifrado, por ejemplo, si desactiva **generar automáticamente las claves de cifrado**) y realiza la compilación desde el mismo equipo, no es necesario compilar e implementar la aplicación después de la inicial implementar, a menos que cambie el código o dependencias. Se puede simplemente inicie de nuevo una aplicación implementada anteriormente y se conectará a la última host que se utiliza.

* No hay ninguna limitación en cuántos dispositivos pueden conectar a la misma sesión de Visual Studio. Puede implementar e iniciar la aplicación en tantos dispositivos o simuladores según sea necesario para ver el trabajo de volver a cargar en vivo en todos ellos al mismo tiempo.

* Recarga en vivo sólo se volverá a cargar la parte de la interfaz de usuario de la aplicación, pero tampoco *no* volver a crear las páginas, ni reemplace el modelo de vista (o el contexto de enlace). Esto significa que la *todo* siempre se conserva el estado de la aplicación a través de recargas más rápidas, incluidas las dependencias insertadas.

## <a name="live-reload-server"></a>Servidor de recarga en vivo

En escenarios donde una conexión de la aplicación en ejecución en el equipo (tal como se indica mediante el uso de `localhost` o `127.0.0.1` en **Herramientas > Opciones > Xamarin > Live recarga**) no es posible (es decir, los servidores de seguridad, redes diferentes), Puede configurar un servidor remoto en su lugar, lo que el IDE y la aplicación conectar a.

Recarga en vivo utiliza la norma [protocolo MQTT](http://mqtt.org/) para intercambiar mensajes y, por tanto, puede comunicarse con [servidores de terceros](https://github.com/mqtt/mqtt.github.io/wiki/servers). Hay incluso [servidores públicos](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers) (también conocido como *corredores de bolsa*) disponibles que se pueden utilizar. Recarga en vivo se ha probado con `broker.hivemq.com` y `iot.eclipse.org` nombres de host, así como los servicios proporcionados por [www.cloudmqtt.com](https://www.cloudmqtt.com) y [www.cloudamqp.com](https://www.cloudamqp.com). También puede implementar su propio servidor MQTT en la nube, como [HiveMQ en Azure](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud).

Puede configurar cualquier puerto, pero es habitual usar el puerto de 1883 predeterminado para los servidores remotos. Mensajes de volver a cargar activos usan-to-end AES simétrico cifrado seguro, por lo que es seguro conectarse a servidores remotos. De forma predeterminada, la clave de cifrado y el vector de inicialización (IV) se vuelven a generar en cada sesión de Visual Studio.

Probablemente la forma más sencilla consiste en instalar el [mosquitto](https://mosquitto.org) server en una VM de Ubuntu en blanco en Azure:

1. Crear una nueva máquina virtual de servidor de Ubuntu en Portal de Azure
2. Agregar una nueva regla de puerto de entrada para 1883 (puerto MQTT de forma predeterminada) en la ficha funciones de red
3. Abra la [Shell en la nube](https://docs.microsoft.com/azure/cloud-shell/overview) (modo de bash)
4. Escriba `ssh [USERNAME]@[PUBLIC_IP]` con el nombre de usuario que eligió en 1) y la dirección IP pública que se muestra en la página de información general de la máquina virtual
5. Ejecutar `sudo apt-get install mosquitto`, escribir la contraseña que eligió en 1)

Ahora puede usar esa dirección IP para conectarse a su propio servidor MQTT.
