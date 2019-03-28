---
title: Uso de Jenkins con Xamarin
description: Este documento describe cómo usar Jenkins para la integración continua con las aplicaciones de Xamarin. Describe cómo instalar, configurar y usar Jenkins.
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: 2e6a75fa3c4c63e8dea402c6761f8ef753908540
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507206"
---
# <a name="using-jenkins-with-xamarin"></a>Uso de Jenkins con Xamarin

_Esta guía muestra cómo configurar Jenkins como un servidor de integración continua y automatizar la compilación de aplicaciones móviles creadas con Xamarin. Describe cómo instalar Jenkins en OS X, configurarlo y configurar los trabajos para compilar aplicaciones de Xamarin.iOS y Xamarin.Android cuando se confirman cambios en el sistema de administración de código fuente._

[Introducción a la integración continua con Xamarin](~/tools/ci/intro-to-ci.md) presenta la integración continua como una práctica de desarrollo de software útil que proporciona advertencias anticipadas de código roto o incompatible. CI permite a los desarrolladores solucionar problemas y problemas que puedan surgir y se mantiene el software en un estado adecuado para la implementación. Este tutorial describe cómo usar el contenido de ambos documentos conjuntamente.

Esta guía muestra cómo instalar Jenkins en un equipo dedicado ejecutándose en OS X y configurar para ejecutarse automáticamente cuando se inicia el equipo. Una vez que Jenkins está instalado, instalamos complementos adicionales para admitir MS Build. Jenkins es compatible con Git desde el principio. Si se utiliza TFS para el control de código fuente, también deben instalarse un utilidades de complemento y la línea de comandos adicionales.

Una vez que Jenkins está configurado y los complementos necesarios se han instalado, se creará uno o varios trabajos para compilar los proyectos de Xamarin.Android y Xamarin.iOS. Un trabajo es una colección de pasos y los metadatos necesarios para realizar algún trabajo. Normalmente, un trabajo consta de lo siguiente:

- **Administración de código (SCM) de origen** : se trata de una entrada de metadatos en los archivos de configuración de Jenkins que contiene información sobre cómo conectarse a control de código fuente y qué archivos para recuperar.
- **Desencadenadores** : los desencadenadores se usan para iniciar un trabajo según ciertas acciones, como cuando un desarrollador confirma cambios en el repositorio de código fuente.
- **Instrucciones de compilación** : se trata de un complemento o un script que se va a compilar el código fuente y generar un archivo binario que se puede instalar en dispositivos móviles.
- **Acciones de compilación opcionales** : Esto podría incluir la ejecución de pruebas unitarias, realización de análisis estático en el código, firma de código, o a partir de otro trabajo para llevar a cabo otro trabajo relacionado de compilación.
- **Las notificaciones** : puede enviar un trabajo de algún tipo de notificación sobre el estado de una compilación.
- **Seguridad** : aunque es opcional, se recomienda encarecidamente que las características de seguridad de Jenkins también esté habilitada.

Esta guía le guiará a través de cómo configurar un servidor de Jenkins que abarcan cada uno de estos puntos. Al final del mismo, deberíamos tener una buena comprensión de cómo instalar y configurar Jenkins para crear los APK e IPA para nuestros proyectos móviles de Xamarin.

## <a name="requirements"></a>Requisitos

El servidor de compilación ideal es un equipo independiente dedicado para el único propósito de crear y, posiblemente, probar la aplicación. Un equipo dedicado garantiza que los artefactos que podrían ser necesarios para otras funciones (como los de un servidor web) no contaminen la compilación. Por ejemplo, si el servidor de compilación también actúa como un servidor web, el servidor web puede requerir una versión en conflicto de alguna biblioteca común. Debido a este conflicto el servidor web no funcionen correctamente o Jenkins puede crear compilaciones que no funcionan cuando se implementa para los usuarios.

El servidor de compilación para las aplicaciones móviles de Xamarin es muy parecida a la estación de trabajo del desarrollador configure. Tiene una cuenta de usuario de que Jenkins, Visual Studio para Mac, y se instalará Xamarin.iOS y Xamarin.Android. Todos lo certificados, el aprovisionamiento de perfiles y los almacenes de claves de firma de código deben instalarse también. *Normalmente la cuenta de usuario del servidor de compilación es independiente de las cuentas de desarrollador: asegúrese de instalar y configurar todo el software, las claves y certificados mientras ha iniciado sesión con la cuenta de usuario del servidor de compilación.*

El siguiente diagrama ilustra todos estos elementos en un servidor de compilación de Jenkins típico:

[![](jenkins-walkthrough-images/image1.png "Este diagrama ilustra todos estos elementos en un servidor de compilación de Jenkins típico")](jenkins-walkthrough-images/image1.png#lightbox)

solo se pueden desarrollar aplicaciones de iOS e inicien sesión en un equipo que ejecuta Mac OS. Un Mini Mac es una opción de bajo costo razonable, pero cualquier equipo que pueda ejecutar OS X 10.10 (Yosemite) o versiones posteriores es suficiente.

Si se utiliza TFS para el control de código fuente, desea instalar [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/). Team Explorer Everywhere proporciona acceso multiplataforma a TFS en el Terminal de macOS.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>Instalación de Jenkins

Es la primera tarea al uso de Jenkins instalarlo. Hay tres maneras de ejecutar Jenkins en OS X:

- Como un demonio, que se ejecuta en segundo plano.
- Dentro de un contenedor de servlet como Tomcat, Jetty o JBoss.
- Como un proceso normal que se ejecuta bajo una cuenta de usuario.

Las aplicaciones más tradicionales de integración continua se ejecutan en segundo plano, ya sea como un demonio (en OS X o \*nix) o como un servicio (en Windows). Esto es adecuado para escenarios donde no hay ninguna intervención de GUI y dónde se puede realizar fácilmente la configuración del entorno de compilación. Aplicaciones móviles también requieren keystore y certificados que podrían resultar problemáticos para el acceso cuando Jenkins se ejecuta como un demonio de firma. Por estas razones, este documento se centra en el tercer escenario: se ejecuta Jenkins en una cuenta de usuario en el servidor de compilación.

Jenkins.App es una forma práctica para instalar Jenkins. Se trata de un contenedor AppleScript que simplifica el proceso de inicio y detención de un servidor de Jenkins. En lugar de ejecutarse en un shell de bash, Jenkins se ejecuta como una aplicación con el icono en el Dock, como se muestra en la siguiente captura de pantalla:

[![](jenkins-walkthrough-images/image2.png "En lugar de ejecutarse en un shell de bash, Jenkins se ejecuta como una aplicación con el icono de la base, como se muestra en esta captura de pantalla")](jenkins-walkthrough-images/image2.png#lightbox)

Iniciar o detener Jenkins es tan sencillo como iniciar o detener Jenkins.App.

Para instalar Jenkins.App, descargue la versión más reciente de la página de descarga del proyecto, en la imagen en la captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image3.png "Aplicación de página, descrito en esta captura de pantalla de descarga de la versión más reciente de los proyectos de descarga")](jenkins-walkthrough-images/image3.png#lightbox)

Extraiga el archivo zip en la `/Applications` carpeta en el servidor de compilación y, al igual que cualquier otra aplicación de OS X de inicio.
La primera vez que se inicia Jenkins.App, presentará un cuadro de diálogo que le informa de que descargará Jenkins:

[![](jenkins-walkthrough-images/image4.png "Aplicación, presentará un cuadro de diálogo que le informa de que descargará Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

Una vez finalizado su descarga, Jenkins.App mostrará otro cuadro de diálogo que le preguntará si desea personalizar el inicio de Jenkins, tal como se muestra en la captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image5.png "Aplicación ha finalizado su descarga, mostrará otro cuadro de diálogo que le preguntará si desea personalizar el inicio de Jenkins, tal como se muestra en esta captura de pantalla")](jenkins-walkthrough-images/image5.png#lightbox)

Personalización de Jenkins es opcional y no tiene que realizarse cada vez que se inicia la aplicación: la configuración predeterminada para Jenkins funcionarán para la mayoría de las situaciones.

Si es necesario personalizar Jenkins, haga clic en el **cambiar valores predeterminados** botón. Se le presentará dos cuadros de diálogo consecutivos: uno que solicita los parámetros de línea de comandos de Java y otro que solicita los parámetros de línea de comandos de Jenkins. Las dos capturas de pantalla siguientes muestran estos dos cuadros de diálogo:

[![](jenkins-walkthrough-images/image6.png "Esta captura de pantalla muestra los cuadros de diálogo")](jenkins-walkthrough-images/image6.png#lightbox)

[![](jenkins-walkthrough-images/image7.png "Esta captura de pantalla muestra los cuadros de diálogo")](jenkins-walkthrough-images/image7.png#lightbox)

Una vez que se ejecuta Jenkins, desea establecerlo como un elemento de inicio de sesión para que se iniciará cada vez que el usuario inicia sesión en el equipo. Puede hacerlo con el botón secundario en el icono de Jenkins en el Dock y eligiendo **Opciones … > Abrir al inicio de sesión**, tal y como se muestra en la captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image8.png "Puede hacerlo con el botón secundario en el icono de Jenkins en el Dock y eligiendo OptionsOpen al inicio de sesión, como se muestra en esta captura de pantalla")](jenkins-walkthrough-images/image8.png#lightbox)

Esto hará que Jenkins.App iniciar automáticamente cada vez que el usuario inicia sesión, pero no cuando el equipo arranca. Es posible especificar una cuenta de usuario que va a usar para iniciar sesión automáticamente con OS X en tiempo de arranque. Abra el **preferencias del sistema**y seleccione el **usuarios y grupos** icono como se muestra en esta captura de pantalla:

[![](jenkins-walkthrough-images/image9.png "Abra las preferencias del sistema y seleccione el icono de grupos de usuarios como se muestra en esta captura de pantalla")](jenkins-walkthrough-images/image9.png#lightbox)

Haga clic en el **opciones de inicio de sesión** botón y, a continuación, elija la cuenta que va a usar para el inicio de sesión de OS X en tiempo de arranque.

En este momento se ha instalado Jenkins. Sin embargo, si queremos generar aplicaciones móviles de Xamarin, es necesario instalar algunos complementos.

### <a name="installing-plugins"></a>Instalación de complementos

Cuando haya finalizado el instalador Jenkins.App, comenzará a Jenkins y el explorador web con la dirección URL de inicio http://localhost:8080, tal y como se muestra en la captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image10.png "8080, como se muestra en esta captura de pantalla")](jenkins-walkthrough-images/image10.png#lightbox)

En esta página, seleccione **Jenkins > Administrar Jenkins > Administrar complementos** en el menú en la esquina superior izquierda, como se muestra en la captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image11.png "Desde esta página, seleccione Jenkins administrar Jenkins administrar complementos en el menú en la esquina superior izquierda")](jenkins-walkthrough-images/image11.png#lightbox)

Esto mostrará la **Jenkins Plugin Manager** página. Si hace clic en la ficha disponible, verá una lista de más de 600 complementos que puede descargarse e instalarse. Esto se muestra en la captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image12.png "Si hace clic en la ficha disponible, verá una lista de más de 600 complementos que puede descargarse e instalarse")](jenkins-walkthrough-images/image12.png#lightbox)

Desplazarse a través de todos los complementos para encontrar que algunas pueden resultar tediosos y propenso a 600. Jenkins proporciona un campo de búsqueda de filtro en la esquina superior derecha de la interfaz. Mediante este campo de filtro para buscar simplificará la localización e instalado uno o todos los complementos siguientes:

- **Complemento de Jenkins MSBuild** : este complemento permite la compilación de Visual Studio y Visual Studio para Mac soluciones (.sln) y proyectos (.csproj).
- **Complemento del inyector de entorno** : se trata de un complemento opcional pero muy útil que permite establecer variables de entorno en el trabajo y el nivel de compilación. También ofrece protección adicional para las variables, como las utilizadas para código firmar la aplicación. A veces se abrevia como el *EnvInject Plugin* .
- **Team Foundation Server Plugin** : se trata de un complemento opcional que solo es necesario si usa Team Foundation Server o Team Foundation Services para el control de código fuente.

Jenkins es compatible con Git sin ningún complemento adicional.

Después de instalar todos los complementos, desea reiniciar Jenkins y configurar las opciones globales para cada complemento. Puede encontrar la configuración global para un complemento seleccionando **Jenkins > Administrar Jenkins > configurar el sistema** desde la esquina superior izquierda, como se muestra en la captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image13.png "La configuración global para un complemento se puede encontrar al seleccionar Jenkins / Manage Jenkins / configurar el sistema de la esquina superior izquierda esquina de mano")](jenkins-walkthrough-images/image13.png#lightbox)

Cuando se selecciona esta opción de menú, se le llevará a la **configurar el sistema [Jenkins]** página. Esta página contiene secciones para configurar Jenkins propio y establecer algunos de los valores globales del complemento.  La captura de pantalla siguiente muestra un ejemplo de esta página:

[![](jenkins-walkthrough-images/image14.png "Esta captura de pantalla muestra un ejemplo de esta página")](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>Configurar el complemento de MSBuild

El complemento de MSBuild debe configurarse para usar **/Library/Frameworks/Mono.framework/Commands/xbuild** compilar de Visual Studio para los archivos de solución y proyecto de Mac. Desplácese hacia abajo el **configurar el sistema [Jenkins]** página hasta que el **agregar MSBuild** aparece el botón, como se muestra en la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image15.png "Desplácese hacia abajo en la página Configurar sistema Jenkins hasta que aparezca el botón Agregar MSBuild")](jenkins-walkthrough-images/image15.png#lightbox)

Haga clic en este botón y rellene el **nombre** y **ruta** a **MSBuild** campos del formulario que aparece. El nombre de su **MSBuild** instalación debe ser algo significativo, mientras el **ruta de acceso a MSBuild** debe ser la ruta de acceso `xbuild`, que normalmente es **/Library/Frameworks / Mono.framework/Commands/xbuild**. Después, guarde los cambios haciendo clic en Guardar o en el botón Aplicar en la parte inferior de la página, Jenkins podrán usar `xbuild` para compilar las soluciones.

#### <a name="configuring-the-tfs-plugin"></a>Configurar el complemento de TFS

En esta sección es obligatoria si va a usar TFS para el control de código fuente.

En el orden de una estación de trabajo de macOS interactuar con un servidor de TFS, [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) debe estar instalado en la estación de trabajo. Team Explorer Everywhere es un conjunto de herramientas de Microsoft que incluye a un cliente de línea de comandos multiplataforma para tener acceso a TFS. Team Explorer Everywhere se puede descargar de Microsoft e instalarse en tres pasos:

1. Descomprima el archivo a un directorio que se puede acceder a la cuenta de usuario. Por ejemplo, es posible que descomprima el archivo en **~/tee**.
2. Configurar la ruta de acceso de shell o del sistema para incluir la carpeta que contiene los archivos que se han descomprimido en el paso anterior. Por ejemplo,

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. Para confirmar que está instalado Team Explorer Everywhere, abra una sesión de terminal y ejecute el `tf` comando. Si tf está configurado correctamente, verá el siguiente resultado en la sesión de terminal:

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

Una vez instalado el cliente de línea de comandos de TFS, Jenkins debe configurarse con la ruta de acceso completa a la `tf` cliente de línea de comandos. Desplácese hacia abajo el **configurar el sistema [Jenkins]** página hasta que encuentre la sección de Team Foundation Server, como se muestra en la captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image17.png "Desplácese hacia abajo en la página Configurar sistema Jenkins hasta que encuentre la sección de Team Foundation Server")](jenkins-walkthrough-images/image17.png#lightbox)

Escriba la ruta de acceso completa a la `tf` y haga clic en el **guardar** botón.

### <a name="configure-jenkins-security"></a>Configurar la seguridad de Jenkins

Cuando instala por primera vez, Jenkins tiene la seguridad deshabilitada, por lo que es posible que cualquier usuario configurar y ejecutar cualquier tipo de trabajo de forma anónima. En esta sección se explica cómo configurar la seguridad mediante la base de datos de usuario de Jenkins para configurar la autenticación y autorización.

Configuración de seguridad se puede encontrar al seleccionar **Jenkins > Administrar Jenkins > Configurar seguridad Global**, tal y como se muestra en esta captura de pantalla:

[![](jenkins-walkthrough-images/image18.png "Configuración de seguridad se puede encontrar al seleccionar Jenkins / Manage Jenkins / configurar seguridad Global")](jenkins-walkthrough-images/image18.png#lightbox)

En el **configurar seguridad Global** página, compruebe la **Habilitar seguridad** casilla de verificación y la **Control de acceso** formulario debe aparecer, similar a la captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image19.png "En la página Configurar seguridad Global, comprobar la seguridad para habilitar la casilla de verificación y el formulario de Control de acceso deben ser, similares a esta captura de pantalla")](jenkins-walkthrough-images/image19.png#lightbox)

Alternar el botón de radio para **base de datos de usuario de Jenkins** en el **sección de seguridad del dominio**y asegúrese de que **permitir que los usuarios se registren** también está activada, como se muestra en el captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image20.png "Alternar el botón de radio para la base de datos de usuario propias de Jenkins en la sección de seguridad del dominio y asegúrese de que permitir que los usuarios a registrarse también está activada")](jenkins-walkthrough-images/image20.png#lightbox)

Por último, reinicie Jenkins y crear una nueva cuenta. La primera cuenta que se crea es la cuenta raíz, y esta cuenta se promocionarán automáticamente a un administrador. Vuelva a la **configurar seguridad Global** página y vaya tachando el **seguridad basada en la matriz** botón de radio. Se debe conceder acceso completo a la cuenta raíz y la cuenta anónima debe tener acceso de solo lectura, como se muestra en la captura de pantalla siguiente:

[![](jenkins-walkthrough-images/image21.png "La cuenta raíz se debe conceder acceso total, y la cuenta anónima debe tener acceso de solo lectura")](jenkins-walkthrough-images/image21.png#lightbox)

Una vez que esta configuración se guarda y se reinicie Jenkins, se activará la seguridad.

#### <a name="disabling-security"></a>Deshabilitar la seguridad

En el caso de una contraseña olvidada o bloqueo anchos Jenkins, es posible deshabilitar la seguridad siguiendo estos pasos:

1. Dejar de Jenkins. Si usas Jenkins.app, puede hacerlo con el botón secundario en el icono de Jenkins.App en el Dock y seleccionando Quit en el menú emergente:

    ![Icono de la aplicación en el Dock y seleccionar Quit en el menú emergente](jenkins-walkthrough-images/image19.png)

2. Abra el archivo **~/.jenkins/config.xml** en un editor de texto.
3. Cambie el valor de la `<usesecurity></usesecurity>` elemento desde `true` a `false`.
4. Eliminar el `<authorizationstrategy></authorizationstrategy>` y `<securityrealm></securityrealm>` elementos desde el archivo.
5. Reinicie Jenkins.

## <a name="setting-up-a-job"></a>Configuración de un trabajo

En el nivel superior, Jenkins organiza todas las diversas tareas necesarias para compilar software en un *trabajo*. Un trabajo también tiene metadatos asociados, que proporciona información sobre la compilación como obtener el código fuente, con qué frecuencia se debe ejecutar la compilación, las variables especiales que son necesarias para compilar y cómo notificar a los desarrolladores si se produce un error en la compilación.

Los trabajos se crean seleccionando **Jenkins > nuevo trabajo** en el menú en la esquina superior derecha, como se muestra en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image22.png "Los trabajos se crean seleccionando nuevo trabajo de Jenkins desde el menú en la esquina superior derecha")

Esto mostrará la **nuevo trabajo de [Jenkins]** página. Escriba un nombre para el trabajo y seleccione el **compilar un proyecto de software de estilo libre** botón de radio. Captura de pantalla siguiente muestra un ejemplo de esto:

![](jenkins-walkthrough-images/image23.png "Escriba un nombre para el trabajo y seleccione un botón de radio del proyecto de software de estilo libre de la compilación")

Al hacer clic en el **Aceptar** botón presenta la página de configuración para el trabajo. Esto debería ser similar a la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image24.png "Esto debería ser similar a esta captura de pantalla")

Jenkins organiza los trabajos en un directorio en el disco duro que se encuentra en la siguiente ruta: **~/.jenkins/jobs/[JOB nombre]**

Esta carpeta contiene todos los archivos y los artefactos específicos para el trabajo como registros, archivos de configuración y el código fuente que debe compilarse.

Una vez creado el trabajo inicial, se debe configurar con uno o varios de los siguientes:

- Debe especificarse el sistema de administración de código fuente.
- Uno o varios *acciones de compilación* debe agregarse al proyecto. Estos son los pasos o tareas necesarias para compilar la aplicación.
- El trabajo se le debe asignar uno *desencadenador de compilación* : un conjunto de instrucciones que le informa de Jenkins con qué frecuencia para recuperar el código y compilar el proyecto final.

### <a name="configuring-source-code-control"></a>Configuración de Control de código fuente

La primera tarea Jenkins es recuperar el código fuente desde el sistema de administración de código fuente. Jenkins admite muchos de los sistemas de administración de código fuente populares disponibles hoy en día. En esta sección se trata dos sistemas populares, Git y Team Foundation Server. Con más detalle en las secciones siguientes se detallan cada uno de estos sistemas de administración de código fuente.

#### <a name="using-git-for-source-code-control"></a>Uso de Git para Control de código fuente

Si está utilizando TFS para el control de código fuente [omitir](#using-tfs-for-source-code-management) esta sección y vaya a la sección siguiente con TFS.

Jenkins es compatible con Git desde el principio: no hay complementos adicionales son necesarias. Para usar Git, haga clic en el **Git** botón de radio y escriba la dirección URL para el repositorio de Git, como se muestra en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image25.png "Para usar Git, haga clic en el botón de radio de Git y escriba la dirección URL para el repositorio de Git")

Una vez que se guardan los cambios, la configuración de Git está completa.

#### <a name="using-tfs-for-source-code-management"></a>Uso de TFS para administración de código fuente

En esta sección solo se aplica a los usuarios de TFS.

Haga clic en el **Team Foundation Server** botón de radio y la sección de configuración de TFS deben aparecer, similares a lo que aparece en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image26.png "Haga clic en el botón de radio de Team Foundation Server y debe aparecer la sección de configuración de TFS")

Proporcione la información necesaria para TFS. Captura de pantalla siguiente muestra un ejemplo del formulario completado:

![](jenkins-walkthrough-images/image27.png "Esta captura de pantalla muestra un ejemplo del formulario completado")

#### <a name="testing-the-source-code-control-configuration"></a>Probar la configuración de Control de código fuente

Una vez que se ha configurado el control de código fuente adecuado, haga clic en **guardar** para guardar los cambios. Esto devolverá a la página principal para el trabajo, que será similar a la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image28.png "Esto le devolverá a la página principal para el trabajo, que será similar a esta captura de pantalla")

La manera más sencilla para validar que el control de código fuente está configurado correctamente es desencadenar una compilación manualmente, aunque no hay ninguna acción de compilación especificada. Para iniciar manualmente una compilación, la página principal del trabajo tiene un **generar ahora** vincular en el menú de la izquierda, como se muestra en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image29.png "Para iniciar manualmente una compilación, la página principal del trabajo tiene un vínculo de compilación ahora en el menú de la izquierda")

Cuando se ha iniciado una compilación, muestra el cuadro de diálogo Historial de compilaciones XAML un círculo azul parpadeando, una barra de progreso, el número de compilación y la hora de inicio de la compilación, similar a la siguiente captura de pantalla de:

![](jenkins-walkthrough-images/image30.png "Cuando se ha iniciado una compilación, el cuadro de diálogo Historial de compilación muestra un círculo azul intermitente, una barra de progreso, el número de compilación y la hora en que se inició la compilación")

Si el trabajo tiene éxito, se mostrará un círculo azul. Si se produce un error en el trabajo, se mostrará un círculo rojo.

Para ayudar a solucionar los problemas que pueden surgir como parte de la compilación, Jenkins capturará todos los resultados de la consola para el trabajo. Para ver la salida de consola, haga clic en el trabajo en **historial de compilaciones XAML**y, a continuación, en el **salida de la consola** vínculo en el menú izquierdo. La siguiente captura de pantalla muestra la **salida de la consola** vínculo, así como parte de los resultados de un trabajo correcta:

![](jenkins-walkthrough-images/image31.png "Esta captura de pantalla muestra el vínculo de salida de consola, así como parte de los resultados de un trabajo con éxito")

#### <a name="location-of-build-artifacts"></a>Ubicación de los artefactos de compilación

Jenkins recuperará todo el código fuente en una carpeta especial denominada una *área de trabajo*. Este directorio puede encontrarse dentro de la carpeta en la siguiente ubicación:

    ```
    ~/.jenkins/jobs/[JOB NAME]/workspace
    ```

La ruta de acceso al área de trabajo se almacenará en una variable de entorno denominada `$WORKSPACE`.

Es posible examinar la carpeta de área de trabajo en Jenkins, vaya a la página de aterrizaje de un trabajo y, a continuación, haga clic en el **área de trabajo** vínculo en el menú izquierdo. Captura de pantalla siguiente muestra un ejemplo del área de trabajo de un trabajo denominado **HelloWorld**:

![](jenkins-walkthrough-images/image32.png "Esta captura de pantalla muestra un ejemplo del área de trabajo de un trabajo denominado HelloWorld")

### <a name="build-triggers"></a>Desencadenadores de compilación

Existen varias estrategias diferentes para iniciar las compilaciones de Jenkins, estos se conocen como *desencadenadores de compilación*. Un desencadenador de compilación ayuda Jenkins decidir cuándo se debe iniciar un trabajo y compile el proyecto. Dos de los desencadenadores de compilación más comunes son:

- **Crear periódicamente** : este desencadenador hace que Jenkins iniciar un trabajo a intervalos especificados, por ejemplo, cada dos horas o a medianoche los días laborables. Se iniciará la compilación independientemente de si ha habido cambios en el repositorio de código fuente.
- **Poll SCM** : este desencadenador sondeará el control de código fuente de forma periódica. Si los cambios se han confirmado en el repositorio de código fuente, Jenkins iniciará una nueva compilación.

Sondear SCM es un desencadenador popular porque ofrece comentarios rápido cuando un desarrollador confirma los cambios que producen la compilación se interrumpa. Esto es útil para alertar a los equipos que algún código confirmada recientemente está causando problemas y permite a los desarrolladores solucionar el problema, mientras que los cambios son aún frescos en mente.

Las compilaciones periódicas a menudo se usan para crear una versión de la aplicación que se puede distribuir a los evaluadores. Por ejemplo, una compilación periódica puede programarse para el viernes por la noche para que los miembros del equipo de preguntas y respuestas pueden probar el trabajo de la semana anterior.

### <a name="compiling-a-xamarinios-applications"></a>Compilar las aplicaciones de Xamarin.iOS
Se pueden compilar los proyectos de Xamarin.iOS en la línea de comandos utilizando `xbuild` o `msbuild`. El comando de shell se ejecutará en el contexto de la cuenta de usuario que se ejecuta Jenkins. Es importante que la cuenta de usuario tiene acceso al perfil de aprovisionamiento para que la aplicación puede empaquetarse adecuadamente para su distribución. Es posible agregar este comando de shell a la página Configuración del trabajo.

Desplácese hacia abajo hasta la **compilar** sección. Haga clic en el **Agregar paso de compilación** y seleccione **Execute shell**, tal y como se muestra en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image33.png "Haga clic en el botón de paso de compilación de agregar y seleccione Execute shell")

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>Creación de un proyecto de Xamarin.Android

Creación de un proyecto de Xamarin.Android es un concepto muy similar a la creación de un proyecto de Xamarin.iOS. Para crear un APK de un proyecto de Xamarin.Android, Jenkins debe configurarse para llevar a cabo los dos pasos siguientes:

- Compile el proyecto mediante el complemento de MSBuild
- Inicio de sesión y zip alinean el APK con un almacén de claves de versión válida.

Estos dos pasos se tratarán con más detalle en las dos secciones siguientes.

### <a name="creating-the-apk"></a>Creación de APK

Haga clic en el **Agregar paso de compilación** botón y seleccione **compilar un proyecto de Visual Studio o la solución mediante MSBuild**, tal y como se muestra en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image36.png "Creación haga clic en el APK en el botón de paso de compilación de agregar y seleccione compilación de un proyecto de Visual Studio o una solución con MSBuild")

Una vez que el paso de compilación se agrega al proyecto, rellene los campos de formulario que aparecen. Captura de pantalla siguiente es un ejemplo del formulario completado:

![](jenkins-walkthrough-images/image37.png "Una vez que el paso de compilación se agrega al proyecto, rellene los campos de formulario que aparecen")

Este paso de compilación ejecutará `xbuild` en el **$WORKSPACE** carpeta. El archivo de compilación de MSBuild está establecido en el **Xamarin.Android.csproj** archivo. El **argumentos de línea de comandos** especificar una versión de lanzamiento del destino **PackageForAndroid**. El producto de este paso será un APK que en la siguiente ubicación:

    ```
    $WORKSPACE/[PROJECT NAME]/bin/Release
    ```

Captura de pantalla siguiente muestra un ejemplo de este APK:

![](jenkins-walkthrough-images/image38.png "Esta captura de pantalla muestra un ejemplo de este APK")

Este APK no está listo para la implementación, ya no se ha firmado con un almacén de claves privada y debe estar alineado zip.

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>Firma y Zipalign al APK de lanzamiento

Firma y zipalign al APK son técnicamente dos tareas independientes que se realizan dos herramientas de línea de comandos independiente desde el SDK de Android. Sin embargo, es conveniente seguir estos pasos en la compilación de una acción. Para obtener más información sobre la firma y usar zipalign con un APK, consulte la documentación de Xamarin sobre la preparación de una aplicación Android para su lanzamiento.

Ambos comandos requieren parámetros de línea de comandos que pueden variar entre proyectos. Además, algunos de estos parámetros de línea de comandos son las contraseñas que no deben aparecer en la salida de la consola cuando se está ejecutando la compilación. Algunos de estos parámetros de línea de comandos almacenaremos en variables de entorno. En la tabla siguiente se describen las variables de entorno necesarias para firmar y alinear zip:

|Variable de entorno|Descripción|
|--- |--- |
|KEYSTORE_FILE|Se trata de la ruta de acceso al almacén de claves para firmar el APK|
|KEYSTORE_ALIAS|La clave en el almacén de claves que se usará para firmar el APK.|
|INPUT_APK|El APK creados por `xbuild`.|
|SIGNED_APK|El APK firmado producidos por `jarsigner`.|
|FINAL_APK|Este es el archivo zip alineado APK generado por `zipalign`.|
|STORE_PASS|Esta es la contraseña que se usa para tener acceso al contenido del almacén de claves para firmar el archivo.|

Como se describe en la sección de requisitos, se pueden establecer estas variables de entorno durante la compilación mediante el complemento EnvInject. El trabajo debe tener una nueva compilación paso agregó según las variables de entorno @inject, como se muestra en la siguiente captura de pantalla:

![](jenkins-walkthrough-images/image39.png "El trabajo debe tener una nueva compilación paso agregó basándose en las variables de entorno de inserción")

En el **propiedades contenido** forman el campo que va a aparecer, se agregan las variables de entorno, uno por línea, en el formato siguiente:

    ```
    ENVIRONMENT_VARIABLE_NAME = value
    ```

Captura de pantalla siguiente muestra las variables de entorno necesarias para firmar el APK:

![](jenkins-walkthrough-images/image40.png "Esta captura de pantalla muestra las variables de entorno necesarias para firmar el APK")

Tenga en cuenta que algunas de las variables de entorno para los archivos APK se basan en el `WORKSPACE` variable de entorno.

La variable de entorno final es la contraseña para tener acceso al contenido del almacén de claves: `STORE_PASS`. Las contraseñas son los valores confidenciales que se deben oculta o se omite en los archivos de registro. El complemento EnvInject puede configurarse para proteger estos valores para que no se muestren en los registros.

Inmediatamente antes de la **compilar** sección de la configuración del trabajo es un **Build Environment** sección. Cuando el **insertar contraseñas** se activa la casilla de verificación, campos de alguna forma que aparezca. Estos campos de formulario se utilizan para capturar el nombre y valor de la variable de entorno. Captura de pantalla siguiente es un ejemplo de cómo agregar el `STORE_PASS` variable de entorno:

![](jenkins-walkthrough-images/image41.png "Esta captura de pantalla es un ejemplo de cómo agregar la variable de entorno STOREPASS")

Una vez que se han inicializado las variables de entorno, el siguiente paso es agregar un paso de compilación para la firma y código postal alinear el APK. Inmediatamente después de que el paso de compilación para insertar las variables de entorno será otro **Execute shell** compilación de comando que ejecutará `jarsigner` y `zipalign`. Cada comando pueden tardar hasta una sola línea, como se muestra en el fragmento de código siguiente:

    ```
    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
    zipalign -f -v 4 $SIGNED_APK $FINAL_APK
    ```

Captura de pantalla siguiente muestra un ejemplo de cómo especificar la `jarsigner` y `zipalign` comandos en el paso:

![](jenkins-walkthrough-images/image42.png "Esta captura de pantalla muestra un ejemplo de cómo especificar los comandos de jarsigner y zipalign en el paso")

Una vez que todas las acciones de compilación están en su lugar, es recomendable para desencadenar una compilación manual para comprobar que todo funciona. Si se produce un error en la compilación, el **salida de la consola** deben revisarse para obtener información sobre lo que ha provocado un error en la compilación.

### <a name="submitting-tests-to-test-cloud"></a>Envío de las pruebas en Test Cloud

Las pruebas automatizadas pueden enviarse a Test Cloud mediante comandos de shell. Para obtener más información acerca de cómo configurar una serie de pruebas en Xamarin Test Cloud, consulte esta guía para el uso de [Xamarin.UITest](/appcenter/test-cloud/preparing-for-upload/uitest/).

## <a name="summary"></a>Resumen

En esta guía se introdujo Jenkins como un servidor de compilación en Mac OS y configurado para compilar y preparar las aplicaciones móviles de Xamarin para la versión. Jenkins se instala en un equipo macOS junto con varios complementos para admitir el proceso de compilación. Se crean y configura un trabajo que extraerá el código desde TFS o Git y, a continuación, compilar ese código en una aplicación lista de versión. También hemos examinado de dos maneras diferentes para programar cuándo se deben ejecutar los trabajos.

## <a name="related-links"></a>Vínculos relacionados

- [Integración continua](~/tools/ci/index.md)
- [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/)
