---
title: Instalación de Xamarin.iOS en Windows
description: En este artículo se explica cómo configurar Xamarin.iOS para Visual Studio. En él también se tratan el proceso de instalación de la extensión de Xamarin para Visual Studio y la conexión al SDK de Apple instalado en el equipo Mac.
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/29/2017
ms.openlocfilehash: 02c4b27f12382d3c3d3eed778d1bfd92ae3f1e79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="installing-xamarinios-on-windows"></a>Instalación de Xamarin.iOS en Windows

_En este artículo se explica cómo configurar Xamarin.iOS para Visual Studio. En él también se tratan el proceso de instalación de la extensión de Xamarin para Visual Studio y la conexión al SDK de Apple instalado en el equipo Mac._

## <a name="overview"></a>Información general

Xamarin.iOS para Visual Studio permite escribir y probar aplicaciones de iOS en equipos Windows con un equipo Mac en red que proporciona el servicio de compilación e implementación.

El desarrollo para iOS en Visual Studio ofrece las siguientes funciones:

- Crear soluciones multiplataforma para aplicaciones de iOS, Android y Windows.
- Usar las herramientas de Visual Studio (como *Resharper* y *Team Foundation Server*) para todos los proyectos multiplataforma, incluido el código de origen de iOS.
- Trabajar con un IDE conocido aprovechando los enlaces de Xamarin.iOS de todas las API de Apple.

Xamarin.iOS para Visual Studio es compatible con configuraciones en las que se ejecute Visual Studio en una máquina virtual de Windows en un equipo Mac (con Parallels o VMWare), o si se encuentra en un equipo independiente visible en la misma red que un equipo Mac. Independientemente de la configuración que mejor le convenga, Visual Studio se conectará al equipo Mac rápidamente y de forma segura mediante SSH.

En este artículo se describen los pasos para instalar y configurar las herramientas de Xamarin.iOS en el equipo Mac y Windows, así como los pasos para conectar con el host de Mac para que los desarrolladores puedan crear, depurar e implementar aplicaciones de Xamarin.iOS con Visual Studio.

En el siguiente diagrama se muestra una visión general sencilla del flujo de trabajo de desarrollo de Xamarin.iOS:

[![Flujo de trabajo de desarrollo de Xamarin.iOS](images/xma2.png)](images/xma2.png#lightbox)

> [!IMPORTANT]
> Visual Studio inicia un proceso independiente de MSBuild para compilar los proyectos. Este proceso crea una nueva conexión con el equipo Mac, lo que significa que en realidad hay dos conexiones SSH desde Windows a Mac cuando Visual Studio compila. La compilación desde la [línea de comandos](~/ios/get-started/installation/windows/connecting-to-mac/index.md) solo crea un proceso de MSBuild. Por motivos de simplicidad de este diagrama, todas las conexiones se representan con una sola flecha.

## <a name="requirements"></a>Requisitos

Xamarin.iOS para Visual Studio logra que los desarrolladores puedan crear, compilar y depurar aplicaciones de iOS en un equipo de Windows mediante el IDE de Visual Studio. No puede hacer esto solo: no se puede crear aplicaciones de iOS sin el compilador de Apple y no se pueden implementar sin los certificados y las herramientas de firma de código de Apple. Esto significa que una instalación de Xamarin.iOS para Visual Studio requiere una conexión a un equipo en red de Mac OS X para poder realizar estas tareas. Una vez configuradas, las herramientas de Xamarin harán que el proceso sea lo más directo posible.


<a name="system-requirements"/>

### <a name="system-requirements"></a>Requisitos del sistema

Los requisitos del sistema son los siguientes:

### <a name="windows"></a>Windows

1. Windows 7 o una versión posterior.

2. Visual Studio 2015 Professional o posterior

    a. Si tiene una licencia Enterprise, debe instalar Visual Studio Enterprise.

3. Xamarin para Visual Studio.

Las herramientas de Xamarin no se pueden utilizar con las ediciones Express de Visual Studio debido a la falta de compatibilidad con los complementos. Xamarin es compatible con Visual Studio Community.

### <a name="mac"></a>Mac

1. Equipo Mac con macOS Sierra (10.12) o una versión posterior, aunque se recomienda la versión estable más reciente.

2. SDK de iOS de Xamarin. Esto se instala al descargar Visual Studio para Mac.

3. Xcode IDE y SDK de iOS de Apple (se recomienda la versión estable más reciente del Mac App Store).

**El equipo de Windows debe ser capaz de ponerse en contacto con el equipo Mac a través de la red.**

### <a name="apple-developer-account"></a>Cuenta de desarrollador de Apple

Para implementar aplicaciones en un dispositivo o enviarlos a la App Store, se necesita una cuenta de desarrollador de Apple. Los certificados de desarrollador y los perfiles de aprovisionamiento pertinentes deben crearse e instalarse en el equipo Mac en red para que Xamarin.iOS para Visual Studio pueda funcionar. Consulte el artículo [Device Provisioning (Aprovisionamiento de dispositivos)](~/ios/get-started/installation/device-provisioning/index.md) para conocer los pasos que se deben llevar a cabo a fin de obtener un certificado de desarrollo y aprovisionar un dispositivo.

## <a name="features"></a>Características 

Xamarin.iOS para Visual Studio permite la creación, edición, compilación e implementación de proyectos de Xamarin.iOS desde Windows. Se incluyen las siguientes características:

- Crear nuevos proyectos de iOS.

- Editar proyectos de iOS y soluciones multiplataforma, entre los que también se incluyen los proyectos de Xamarin.Android y UWP.

- Compilar proyectos de iOS y soluciones multiplataforma, entre los que también se incluyen los proyectos de Xamarin.Android y UWP.

- Compatibilidad con guiones gráficos y .xib mediante iOS Designer.

- Implementar y depurar aplicaciones de iOS en los que la propia aplicación se ejecuta en un simulador o en un dispositivo conectado al Mac.

- Simulador de iOS en Windows: para más información sobre cómo utilizar el simulador de iOS en Windows, consulte [esta guía](~/tools/ios-simulator.md).

<a name="configuring" />

## <a name="configuring-your-mac"></a>Configuración del Mac

<a name="installation"/>

### <a name="installation"></a>Instalación

Para instalar herramientas de Xamarin.iOS en el host de mac, debe [instalar Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation).

Una vez instalado el software, siga los pasos descritos en las secciones siguientes para configurar Xamarin.iOS en macOS y permitir que Xamarin para Visual Studio se conecte.

> [!IMPORTANT]
> La máquina Windows debe utilizar la misma versión de Xamarin.iOS que el Mac al que está conectado. Para asegurarse:
>
> - **Visual Studio 2015 y versiones anterior**: asegúrese de que se encuentra en el mismo [canal de actualizaciones](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) que Visual Studio para Mac.
>
> - **Versión de lanzamiento de Visual Studio 2017**: asegúrese de que se encuentra en el canal **Stable** de Visual Studio para Mac.
>
> - **Versión preliminar de Visual Studio 2017**: asegúrese de que se encuentra en el canal **Alpha** de Visual Studio para Mac.

<a name="configuration" />


### <a name="configuration"></a>Configuración

Para obtener acceso a la comunicación entre la extensión Xamarin para Visual Studio y el equipo Mac, debe permitir el **Inicio de sesión remoto** en el equipo Mac. Siga los pasos indicados a continuación para configurarlo:

1. Abra *Spotlight* (**Cmd + espacio**) y busque **Inicio de sesión remoto** y, después, seleccione el resultado **Compartir**. Se abrirá el menú **Preferencias del sistema** en el panel **Compartir**.

![Búsqueda de inicio de sesión remoto en Spotlight](images/spotlight.png)

2. Marque la opción **Inicio de sesión remoto** en la lista **Servicio** de la izquierda con el fin de permitir que Xamarin para Visual Studio se conecte al equipo Mac.

![Marcado de la opción Inicio de sesión remoto en la lista Servicio](images/sharing.png)

3. Asegúrese de que se establece **Inicio de sesión remoto** para permitir el acceso de **Todos los usuarios** o que el nombre de usuario de Mac o el grupo se incluye en la lista de usuarios permitidos en la lista de la derecha.

Si se encuentra en la misma red, Visual Studio ya debería detectar el equipo Mac.

> [!NOTE]
> Si tiene el firewall de macOS configurado para bloquear las aplicaciones firmadas de forma predeterminada, puede que necesite permitir `mono-sgen` para recibir conexiones entrantes. Aparecerá un cuadro de diálogo de alerta para preguntarle si este es el caso.

<a name="developersetup"/>

### <a name="ios-developer-setup"></a>Configuración del desarrollador de iOS

Para el desarrollo de iOS, es importante que el equipo Mac esté configurado con las identidades de firma correspondientes. Esto permite firmar correctamente sus aplicaciones para que se distribuyan a través del App Store o ad hoc. Siga el vínculo siguiente para obtener instrucciones sobre cómo configurar un equipo Mac para el desarrollo de iOS con Xamarin:

- [Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md?ide=vs)

Una vez configurado el equipo Mac, es el momento de configurar el equipo Windows.

<a name="windowsinstallation"/>

## <a name="windows-installation"></a>Instalación en el equipo Windows

Xamarin puede instalarse como parte de la instalación de Visual Studio 2015 o 2017. Para instalar las herramientas de Visual Studio para Xamarin, consulte la guía de [instalación de Windows](~/cross-platform/get-started/installation/windows.md).

## <a name="installation-complete"></a>Instalación completada

Una vez completado el proceso de instalación, todavía quedan unos pasos más necesarios para que todo funcione:

- [Conectar Visual Studio al equipo Mac](#connectingtomac): Visual Studio debe estar conectado al host de compilación de Mac para poder compilar proyectos de Xamarin.iOS.
- [Configurar la barra de herramientas de Visual Studio](#toolbar): esto le permitirá acceder fácilmente a las características de Xamarin.iOS en Visual Studio.

<a name="connectingtomac" /> 

### <a name="connecting-to-the-mac"></a>Conexión al equipo Mac

Se establece una conexión desde Xamarin.iOS para Visual Studio con el host de compilación de Mac a través de una conexión SSH entre los equipos. Para obtener más información sobre la conexión, consulte la guía [Connecting to Mac (Conectarse al equipo Mac)](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

Para conectar el equipo Mac, siga los pasos que se describen a continuación:

- Vaya a **Herramientas > Opciones** y, en **Xamarin**, seleccione **iOS Settings (Configuración de iOS)**:

  [![Pantalla Configuración de iOS](images/image2.png)](images/image2.png#lightbox)

- Siempre que el equipo Mac se haya [configurado](#configuration) correctamente para permitir el **Inicio de sesión remoto**, podrá seleccionar el equipo Mac en la lista:

  [![Cuadro de diálogo Host remoto](images/xma3.png)](images/xma3.png#lightbox)

- Se le solicitarán las credenciales administrativas del host del equipo Mac:

  [![Cuadro de diálogo de inicio de sesión](images/xma4.png)](images/xma4.png#lightbox)

- Cuando se haya conectado, se mostrará el icono "Conexión correcta" junto al nombre del equipo:

  [![Cuadro de diálogo Host remoto con el icono Conexión realizada correctamente junto al nombre del equipo](images/image6.png)](images/image6.png#lightbox)

Cada vez que inicie Visual Studio, se le volverá a conectar.

<a name="toolbar" />

## <a name="visual-studio-toolbar-configuration"></a>Configuración de la barra de herramientas de Visual Studio

Cuando se abra un proyecto de iOS, la barra de herramientas de iOS aparecerá de forma predeterminada, sin necesidad de configurar nada.

Siga estos pasos si no aparece la barra de herramientas de iOS.

Para configurar la barra de herramientas, abra el menú **Ver > Barras de herramientas** y asegúrese de que esté activada la entrada **iOS**. Elija el elemento del menú como se muestra en esta captura de pantalla. Debe marcarse para indicar que la barra de herramientas se puede ver:

[![Elección de Barras de herramientas > iOS](images/image31.png)](images/image31.png#lightbox)

### <a name="visual-studio-2015"></a>Visual Studio 2015

En versiones anteriores a Visual Studio 2017, puede ser que el botón **Plataformas de solución** deba agregarse a la barra de herramientas estándar. Esto permite seleccionar un dispositivo iOS o el simulador de iOS durante la depuración. Siga estas instrucciones para resolverlo.

Haga clic en el botón de menú en el lado derecho de la barra estándar:

- Elija **Agregar o quitar botones**.
- Seleccione **Plataformas de solución**.

[![Selección de la plataforma de solución](images/image35.png)](images/image35.png#lightbox)

Ahora, las barras de herramientas **estándar** y de **iOS** deben ser similares a las que se muestran en esta captura de pantalla:

[![Ahora, las barras de herramientas Estándar e iOS deben ser similares a las que se muestran en esta captura de pantalla](images/image36.png)](images/image36.png#lightbox)

Una vez completada la configuración de la barra de herramientas, podrá empezar a utilizar Xamarin iOS para Visual Studio.

## <a name="summary"></a>Resumen

En este artículo se presenta una guía paso a paso para instalar, configurar y utilizar Xamarin iOS para Visual Studio.

Asimismo, se tratan la instalación y la configuración de las herramientas de requisitos previos de Windows y Mac OS X.


## <a name="related-links"></a>Vínculos relacionados

- [Instalación](~/cross-platform/get-started/installation/windows.md)
- [Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)
- [Introducción a Xamarin.iOS para Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [Conexión de un equipo Mac al entorno de Visual Studio con XMA (vídeo)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
