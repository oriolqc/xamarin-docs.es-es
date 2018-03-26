---
title: Conexión al equipo Mac
description: Xamarin.iOS para Visual Studio permite a los programadores crear, compilar y depurar aplicaciones de iOS en un equipo de Windows mediante el IDE de Visual Studio. En esta guía se explican las características proporcionadas por Xamarin.iOS para Visual Studio y cómo se realiza la conexión con el host de compilación de Mac.
ms.topic: article
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: e4f7b55fa859473e84298151bc08878bc2161192
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="connecting-to-the-mac"></a>Conexión al equipo Mac

_Xamarin.iOS para Visual Studio permite a los desarrolladores crear, compilar y depurar aplicaciones de iOS en un equipo Windows mediante el IDE de Visual Studio. En esta guía se explican las características que proporciona Xamarin.iOS para Visual Studio y cómo se realiza la conexión con el host de compilación de Mac._

Visual Studio se conecta a Mac sobre SSH, que proporciona varias ventajas, incluyendo:

- Visual Studio puede iniciar y controlar el agente de compilación directamente. Ya no es una aplicación visible para el usuario que requiera un inicio y detención manual.

- El nuevo administrador de conexión en Visual Studio detectará, autenticará y recordará el host de compilación de Mac.

- Puesto que todas las comunicaciones se pasan a través de un túnel de forma segura mediante SSH, solo se requiere una conexión de puerto único al puerto 22.

- Se notifica a Visual Studio de los cambios en cuanto se producen. Por ejemplo, cuando se conecta un dispositivo iOS en la barra de herramientas, se actualizará al instante.

- Pueden conectarse simultáneamente varias instancias de Visual Studio.

- La conexión no interfiere en el desarrollo. Solo se le solicitará una conexión con el equipo Mac cuando realice una operación para la que es necesario el equipo, como la depuración o utilizar iOS Designer.

La conexión con el equipo Mac se compone de varios procesos para las distintas partes de su funcionalidad, por ejemplo, el agente de iOS Designer y el agente de compilación, que están controlados por un agente. Visual Studio controla y actualiza este agente y reiniciará automáticamente alguno de los procesos independientes si se bloquean.

En el siguiente diagrama se muestra una visión general sencilla del flujo de trabajo de desarrollo de Xamarin.iOS:

[![Flujo de trabajo de desarrollo de iOS](images/xma2.png)](images/xma2.png#lightbox)

> [!IMPORTANT]
> Visual Studio inicia un proceso independiente de MSBuild para compilar los proyectos. Este proceso crea una nueva conexión con el equipo Mac, lo que significa que en realidad hay dos conexiones SSH desde Windows a Mac cuando Visual Studio compila. La compilación desde la [línea de comandos](#commandline) solo crea un proceso de MSBuild. Por motivos de simplicidad de este diagrama, todas las conexiones se representan con una sola flecha.

## <a name="requirements"></a>Requisitos

Xamarin.iOS para Visual Studio logra que los desarrolladores puedan crear, compilar y depurar aplicaciones de iOS en un equipo de Windows mediante el IDE de Visual Studio. No puede hacer esto solo: no se puede crear aplicaciones de iOS sin el compilador de Apple y no se pueden implementar sin los certificados y las herramientas de firma de código de Apple. Esto significa que su instalación de Xamarin.iOS para Visual Studio requiere una conexión a un equipo en red de Mac OS X (que es la que se refiere como el *host* o *host de la compilación*) para realizar estas tareas por usted. Una vez configuradas, las herramientas de Xamarin harán que el proceso sea lo más directo posible.

### <a name="system-requirements"></a>Requisitos del sistema

Los requisitos del sistema pueden encontrarse en la guía [Instalación de Xamarin.iOS en Windows](~/ios/get-started/installation/windows/index.md#system-requirements).


#### <a name="compatibility"></a>Compatibilidad

> [!IMPORTANT]
> La máquina Windows debe utilizar la misma versión de Xamarin.iOS que el Mac al que está conectado. Para asegurarse:                                                    
>                                                                                                                 
> - **Visual Studio 2015 y versiones anterior**: asegúrese de que se encuentra en el mismo [canal de actualizaciones](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) que Visual Studio para Mac.
>                                                                                                                 
> - **Versión de lanzamiento de Visual Studio 2017**: asegúrese de que se encuentra en el canal **Stable** de Visual Studio para Mac.
>                                                                                                                 
> - **Versión preliminar de Visual Studio 2017**: asegúrese de que se encuentra en el canal **Alpha** de Visual Studio para Mac. Visual Studio no comprobará que el SDK y Xcode de Xamarin.iOS existen y tienen versiones compatibles.
>   Esa comprobación la realizará el agente de compilación, lo que produce errores de compilación, y el agente de Diseñador de iOS, lo que produce errores del diseñador.

### <a name="connecting-to-the-mac"></a>Conexión al equipo Mac

#### <a name="mac-setup"></a>Configuración de Mac

Para configurar el host de Mac, debe habilitar la comunicación entre la extensión de Xamarin para Visual Studio y el equipo Mac. Para ello, permita el **Inicio de sesión remoto** en su Mac siguiendo los siguientes pasos:

1. Abra *Spotlight* (**⌘+espacio**) y busque *Inicio de sesión remoto*. Después, seleccione el resultado *Compartir*. Se abrirá el menú *Preferencias del sistema* en el panel *Compartir*:

   [![Búsqueda de inicio de sesión remoto en Spotlight](images/spotlight.png)](images/spotlight.png#lightbox)

2. Marque la opción *Inicio de sesión remoto* en la lista *Servicio* de la izquierda con el fin de permitir que Xamarin para Visual Studio se conecte al equipo Mac:

   [![Marcado de la opción Inicio de sesión remoto en la lista Servicio](images/sharing.png)](images/sharing.png#lightbox)

3. Asegúrese de que se establece *Inicio de sesión remoto* para permitir el acceso de *Todos* los usuarios o que el nombre de usuario de Mac o el grupo se incluye en la lista de usuarios permitidos en la lista de la derecha.

Además, si tiene el firewall de OS X configurado para bloquear las aplicaciones firmadas de forma predeterminada, puede que necesite permitir `mono-sgen` para recibir conexiones entrantes. Aparecerá un cuadro de diálogo de alerta para preguntarle si este es el caso.

Si hay una sesión actual abierta en su equipo Mac, ahora Visual Studio debería poder detectarla si se encuentran en la misma red.

Visual Studio iniciará y detendrá al agente en el equipo Mac, por lo que no hay nada que usted, como usuario, deba hacer.

### <a name="windows-setup"></a>Configuración de Windows

Asegúrese de [instalar](~/ios/get-started/installation/windows/index.md) las herramientas Xamarin en su equipo Windows.

### <a name="connecting-to-the-mac-build-host"></a>Conexión con el host de compilación de Mac

Hay dos maneras de conectarse al host de compilación de Mac:

En la barra de herramientas de iOS:

[![Barra de herramientas de iOS](images/image1.png)](images/image1.png#lightbox)

O seleccione **Herramientas > Opciones** en Visual Studio, después **Xamarin > Configuración de iOS** y haga clic en el botón **Buscar agente de Mac de Xamarin**:

[![Búsqueda de Xamarin Mac Agent](images/image2.png)](images/image2.png#lightbox)

Cualquier método llevará al cuadro de diálogo **Agente Mac**, que se muestra a continuación:

[![Cuadro de diálogo Mac Agent](images/image3.png)](images/image3.png#lightbox)

Esto mostrará una lista de todos los equipos que ya se han conectado anteriormente y se almacenan como equipos conocidos o equipos que están disponibles para realizar un *inicio de sesión remoto*.

Seleccione un equipo Mac haciendo doble clic en él para conectarse. La primera vez que se conecte a un equipo Mac, se le pedirá que escriba las credenciales de usuario de Mac para permitir la conexión remota:

[![Introducción de las credenciales de usuario de Mac](images/image4.png)](images/image4.png#lightbox)

El agente utilizará estas credenciales para crear una nueva conexión de SSH para Mac. Si se realiza correctamente, se creará una clave SSH y será [registrada](#commandline) en el archivo `authorized_keys` en ese equipo Mac. En las conexiones posteriores, el agente utilizará el archivo de nombre de usuario y la clave para conectarse con el host de compilación conocido al que se haya conectado más recientemente.

> [!NOTE]
> Debe utilizar el _nombre de usuario_ y no el _nombre completo_ al escribir sus credenciales.  Lo puede averiguar utilizando el comando `whoami` en la Terminal.  Por ejemplo, en la siguiente captura de pantalla, el nombre de cuenta será **amyb** y no **Amy Burns**:
>
> ![Búsqueda del nombre de usuario en la aplicación de Terminal](images/image5.png)

Cuando una conexión se ha realizado correctamente, se mostrará en el cuadro de diálogo Selección de host con un icono de **conectado** junto a ella, como se muestra a continuación:

[![Cuadro de diálogo Selección de host con un icono de conectado al lado](images/image6.png)](images/image6.png#lightbox)

Solo puede haber un equipo Mac conectado en cualquier momento.

Cada máquina en la lista, esté conectada o no, mostrará un menú contextual en el botón derecho, lo que permite **Conectar**, **Desconectar**, u **Olvidar el equipo Mac** según sea necesario:

[![Menús contextuales Conectar, Desconectar u Olvidar este equipo Mac](images/image7.png)](images/image7.png#lightbox)

Si decide **Olvidar este equipo Mac**, será necesario volver a escribir sus credenciales para conectarse de nuevo.

<a name="manual-add"/>

### <a name="manually-adding-a-mac"></a>Agregar manualmente un equipo Mac

En determinadas circunstancias, puede agregar manualmente un equipo Mac si no ve su nombre mDNS en el cuadro de diálogo Selección de host. Para ello, siga estos pasos:

1. Busque la dirección IP del equipo Mac para seleccionando **Preferencias del sistema > Compartir > Inicio de sesión remoto** en el Mac:

   [![Dirección IP del equipo Mac en Preferencias del sistema](images/image8.png)](images/image8.png#lightbox)

   O, si prefiere usar la línea de comandos, puede averiguar la dirección IP escribiendo `ipconfig getifaddr en0` en la Terminal (tenga en cuenta que, según el tipo de conexión, la variable podría ser `en1`, `en2`, etc.):

   [![Dirección IP de la aplicación de Terminal](images/image9.png)](images/image9.png#lightbox)

2. Regrese a Visual Studio y en el cuadro de diálogo Selección de host, seleccione **Agregar Mac...**:

   [![Cuadro de diálogo Selección de host](images/image10.png)](images/image10.png#lightbox)

3. Escriba la dirección IP del equipo Mac en el cuadro de diálogo Agregar equipo Mac y haga clic en **Agregar**:

   [![Introducción de la dirección IP del equipo Mac en el cuadro de diálogo Agregar equipo Mac](images/image11.png)](images/image11.png#lightbox)

4. Por último, escriba el nombre de usuario (no el nombre completo) de la cuenta de administrador del equipo Mac y la contraseña correspondiente:

   [![Introducción del nombre de usuario y la contraseña](images/image12.png)](images/image12.png#lightbox)

Una vez que pulse **Inicio de sesión**, Visual Studio iniciará sesión en el equipo Mac mediante SSH y agregará este Mac como una máquina conocida.

<a name="commandline"/>

### <a name="command-line-support"></a>Soporte técnico de la línea de comandos

El agente también admite la creación de una configuración de Xamarin.iOS desde la línea de comandos.  Para usarla, necesitará pasar los siguientes parámetros requeridos a MSBuild:

- `ServerAddress`: la dirección IP del servidor Mac.

- `ServerUser`: el nombre de usuario (no el nombre completo) que se usará para iniciar sesión en el servidor Mac.

- `ServerPassword`: la contraseña utilizada para iniciar sesión en el host de Mac (opcional).

El parámetro `ServerPassword` no es necesario.

En su lugar, la primera vez que se haya pasado una contraseña, ya sea mediante Visual Studio o la línea de comandos para esa configuración determinada de Windows, Mac y de usuario un par de claves se generarán y almacenarán en el equipo de Windows para un uso futuro. Se encuentra en **%localappdata%\Xamarin\MonoTouch\id_rsa**.  Si no pasa parámetro `ServerPassword`, el archivo de claves `id_rsa` se utilizará para la autenticación.

A continuación se muestra un comando de ejemplo para conectarse a Mac 10.211.55.2 mediante una cuenta de **xamUser** con la contraseña **mypassword**:

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

### <a name="summary"></a>Resumen

En este artículo se explora la conexión entre Visual Studio y las herramientas de compilación y diseño de iOS en el equipo Mac, lo que permite generar aplicaciones de Xamarin.iOS con Visual Studio.

### <a name="related-links"></a>Vínculos relacionados

- [Instalación](~/ios/get-started/installation/windows/index.md)
- [Solución de problemas de conexión](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Conexión de un equipo Mac al entorno de Visual Studio con XMA (vídeo)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
