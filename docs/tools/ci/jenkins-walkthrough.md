---
title: Usar Jenkins con Xamarin
description: "Esta guía muestra cómo configurar Jenkins como un servidor de integración continua y automatizar la compilación de aplicaciones móviles creadas con Xamarin. Se describe cómo instalar Jenkins en OS X, configurarlo y configurar las tareas para compilar aplicaciones Xamarin.iOS y Xamarin.Android cuando los cambios se confirman en el sistema de administración de código fuente."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: 8d23211e28cb1b1dae13d67e32462888c66ff065
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="using-jenkins-with-xamarin"></a>Usar Jenkins con Xamarin

_Esta guía muestra cómo configurar Jenkins como un servidor de integración continua y automatizar la compilación de aplicaciones móviles creadas con Xamarin. Se describe cómo instalar Jenkins en OS X, configurarlo y configurar las tareas para compilar aplicaciones Xamarin.iOS y Xamarin.Android cuando los cambios se confirman en el sistema de administración de código fuente._

[Introducción a la integración continua con Xamarin](~/tools/ci/intro-to-ci.md) presenta la integración continua como una práctica de desarrollo de software útil que proporciona advertencias anticipadas de código roto o incompatible. Elementos de configuración permite a los desarrolladores para resolver los problemas y los problemas que puedan surgir y mantiene el software en un estado adecuado para la implementación. Este tutorial describe cómo usar el contenido de ambos documentos conjuntamente.

Esta guía muestra cómo instalar Jenkins en un equipo dedicado con OS X y configurarlo para que se ejecute automáticamente cuando se inicia el equipo. Una vez instalado Jenkins, se instalará complementos adicionales para admitir MS Build. Jenkins es compatible con Git desde el principio. Si se utiliza para el control de código fuente TFS, también deben instalarse un utilidades de complemento y la línea de comandos adicionales.

Una vez que se configura Jenkins y se han instalado los complementos necesarios, se creará uno o varios trabajos para compilar los proyectos Xamarin.Android y Xamarin.iOS. Un trabajo es una colección de pasos y los metadatos necesarios para realizar algún trabajo. Normalmente, un trabajo consta de las siguientes acciones:

-  **Código de administración de servicios (SCM) de origen** : se trata de una entrada de metadatos en los archivos de configuración Jenkins que contiene información sobre cómo conectarse a control de código fuente y qué archivos para recuperar.
-  **Desencadenadores** – desencadenadores se utilizan para iniciar un trabajo en función de determinadas acciones, como cuando un desarrollador confirma los cambios en el repositorio de código fuente.
-  **Instrucciones de compilación** : se trata de un complemento o un script que se va a compilar el código fuente y generar un archivo binario que se puede instalar en dispositivos móviles.
-  **Las acciones de compilación opcionales** : Esto podría incluir la ejecución de pruebas unitarias, realizar análisis estático en el código, firma de código, o a partir de otro trabajo para llevar a cabo otro trabajo relacionado de compilación.
-  **Las notificaciones** : puede enviar un trabajo de algún tipo de notificación sobre el estado de una compilación.
-  **Seguridad** : aunque es opcional, se recomienda encarecidamente que las características de seguridad Jenkins también esté habilitada.


Esta guía le guiará a través de cómo configurar un servidor de Jenkins que cubren cada uno de estos puntos. Al final del mismo, debemos tenemos un buen conocimiento de cómo instalar y configurar Jenkins para crear IPA y del APK para nuestros proyectos móviles Xamarin.

## <a name="requirements"></a>Requisitos

El servidor de compilación ideal es un equipo independiente dedicado para el único propósito de la compilación y posiblemente probar la aplicación. Un equipo dedicado garantiza que los artefactos que podrían ser necesarios para otras funciones (como el de un servidor web) no contaminar la compilación. Por ejemplo, si el servidor de compilación también actúa como un servidor web, el servidor web puede requerir una versión en conflicto de alguna biblioteca común. Debido a este conflicto el servidor web no funcionen correctamente o Jenkins puede crear compilaciones que no funcionan cuando se implementa para los usuarios.

El servidor de compilación para las aplicaciones móviles de Xamarin se configura muy parecida a la estación de trabajo del desarrollador. Tiene una cuenta de usuario en qué Jenkins, Visual Studio para Mac, y se instalará Xamarin.iOS y Xamarin.Android. Todos lo certificados, el aprovisionamiento de perfiles y almacenes de claves de firma de código deben instalarse también. *Normalmente es independiente de las cuentas de desarrollador de cuenta de usuario del servidor de compilación: asegúrese de instalar y configurar todas las claves, software y certificados mientras haya iniciado sesión con la cuenta de usuario del servidor de compilación.*

En el diagrama siguiente se muestra todos estos elementos en un servidor de compilación Jenkins típico:

 [![](jenkins-walkthrough-images/image1.png "Este diagrama muestra todos estos elementos en un servidor de compilación Jenkins normal")](jenkins-walkthrough-images/image1.png#lightbox)

solo se puedan generar aplicaciones de iOS y firmadas en un equipo que ejecuta Mac OS X. Un Mac Mini es una opción de bajo costo razonable, pero cualquier equipo capaz de ejecutar OS X 10.10 (Yosemite) o posterior es suficiente.

Si se utiliza para el control de código fuente TFS, deseará instalar [Team Explorer Everywhere](http://www.microsoft.com/en-ca/download/details.aspx?id=40785), disponible en Microsoft. Team Explorer Everywhere proporciona acceso de multiplataformas a TFS en el Terminal en OS X.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>Instalar Jenkins

La primera tarea al uso Jenkins es instalarlo. Hay tres maneras de ejecutar Jenkins en OS X:

-  Como demonio que se ejecuta en segundo plano.
-  Dentro de un contenedor de servlet como Tomcat, Jetty, JBoss.
-  Como un proceso normal que se ejecuta bajo una cuenta de usuario.


Las aplicaciones de integración continua más tradicionales se ejecutan en segundo plano, ya sea como un demonio (en OS X o \*nix) o como un servicio (en Windows). Esto es conveniente para escenarios donde hay ninguna interacción de la interfaz gráfica de usuario necesaria y donde se puede realizar fácilmente la configuración del entorno de compilación. Aplicaciones móviles también requieren keystore y certificados que podrían resultar problemáticos para tener acceso a cuando Jenkins se ejecuta como un daemon de firma. Por estos motivos, este documento se centra en el tercer escenario: ejecuta Jenkins con una cuenta de usuario en el servidor de compilación.

Jenkins.App es una forma práctica para instalar Jenkins. Se trata de un contenedor de AppleScript que simplifica el inicio y la detención de un servidor Jenkins. En lugar de ejecutarse en un shell de bash, Jenkins se ejecuta como una aplicación con el icono de la base, como se muestra en la siguiente captura de pantalla:

 [![](jenkins-walkthrough-images/image2.png "En lugar de ejecutarse en un shell de bash, Jenkins se ejecuta como una aplicación con el icono de la base, como se muestra en esta captura de pantalla")](jenkins-walkthrough-images/image2.png#lightbox)

Iniciar o detener Jenkins es tan sencilla como iniciar o detener Jenkins.App.

Para instalar Jenkins.App, descargue la versión más reciente de la página de descarga del proyecto, en la imagen en la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image3.png "Aplicación de la página, que se ilustran en esta captura de pantalla de descarga de la versión más reciente de los proyectos de descarga")](jenkins-walkthrough-images/image3.png#lightbox)

Extraiga el archivo zip en la `/Applications` carpeta en el servidor de compilación, al igual que cualquier otra aplicación de OS X de inicio.
La primera vez que se inicia Jenkins.App, presentará un cuadro de diálogo que le informa de que descargará Jenkins:

 [![](jenkins-walkthrough-images/image4.png "Aplicación, se presentará un cuadro de diálogo que le informa de que descargará Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

Una vez que Jenkins.App ha terminado su descarga, muestra otro cuadro de diálogo que pregunta si desea personalizar el arranque Jenkins, tal como se muestra en la siguiente captura de pantalla:

 [![](jenkins-walkthrough-images/image5.png "Aplicación ha terminado su descarga, muestra otro cuadro de diálogo que pregunta si desea personalizar el arranque Jenkins, tal como se muestra en esta captura de pantalla")](jenkins-walkthrough-images/image5.png#lightbox)

Personalizar Jenkins es opcional y no tiene que realizarse cada vez que se inicia la aplicación: la configuración predeterminada para que funcionen Jenkins para la mayoría de los casos.

Si es necesario personalizar Jenkins, haga clic en el **cambiar valores predeterminados** botón. Esto presentará dos cuadros de diálogo consecutivos: uno que solicita parámetros de línea de comandos de Java y otro que solicita Jenkins parámetros de línea de comandos. Las dos capturas de pantalla siguientes muestran estos dos cuadros de diálogo:

 [![](jenkins-walkthrough-images/image6.png "Esta captura de pantalla muestra los cuadros de diálogo")](jenkins-walkthrough-images/image6.png#lightbox)

 [![](jenkins-walkthrough-images/image7.png "Esta captura de pantalla muestra los cuadros de diálogo")](jenkins-walkthrough-images/image7.png#lightbox)

Una vez que se está ejecutando Jenkins, puede que desee establecer como un elemento de inicio de sesión para que se iniciará cada vez que los inicios de sesión de usuario en el equipo. Para ello, haga clic en el icono de Jenkins en el área y elegir **opciones... Abrir en el inicio de sesión**, tal y como se muestra en la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image8.png "Para ello, haga doble clic en el icono de Jenkins en el área y eligiendo OptionsOpen al inicio de sesión, como se muestra en esta captura de pantalla")](jenkins-walkthrough-images/image8.png#lightbox)

Esto hará que Jenkins.App iniciar automáticamente cada vez que el usuario inicia sesión, pero no cuando el equipo arranca. Es posible especificar una cuenta de usuario que OS X usará para iniciar sesión automáticamente al arrancar el sistema. Abra la **preferencias del sistema**y seleccione el **usuario y grupos** icono tal y como se muestra en esta captura de pantalla:

 [![](jenkins-walkthrough-images/image9.png "Abra las preferencias del sistema y seleccione el icono de grupos de usuarios, tal y como se muestra en esta captura de pantalla")](jenkins-walkthrough-images/image9.png#lightbox)

Haga clic en el **opciones de inicio de sesión** botón y, a continuación, elija la cuenta que va a usar para inicio de sesión OS X al arrancar el sistema.

En este momento se ha instalado Jenkins. Sin embargo, si desean crear aplicaciones móviles de Xamarin, es necesario instalar algunos complementos.


### <a name="installing-plugins"></a>Instalar complementos

Cuando haya finalizado el programa de instalación de Jenkins.App, iniciará Jenkins y se inicie el explorador web con la dirección URL http://localhost: 8080, tal y como se muestra en la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image10.png "8080, tal y como se muestra en esta captura de pantalla")](jenkins-walkthrough-images/image10.png#lightbox)

En esta página, seleccione **Jenkins > Jenkins administrar > Administrar complementos** en el menú en la esquina superior izquierda, tal y como se muestra en la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image11.png "En esta página, seleccione Jenkins administrar Jenkins administrar complementos en el menú en la esquina superior izquierda")](jenkins-walkthrough-images/image11.png#lightbox)

Esto mostrará la **Jenkins complemento Administrador** página. Si hace clic en la ficha disponible, verá una lista de más de 600 complementos que puede descargarse e instalarse. Esto se muestra en la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image12.png "Si hace clic en la ficha disponible, verá una lista de más de 600 complementos que puede descargarse e instalarse")](jenkins-walkthrough-images/image12.png#lightbox)

Desplazarse a través de todos los complementos para buscar que varios pueden ser tediosos y propenso a errores de 600. Jenkins proporciona un campo de búsqueda de filtro en la esquina superior derecha de la interfaz. Uso de este campo de filtro para buscar simplificará la localización e instalado uno o todos los complementos siguientes:

-  **Complemento de MSBuild Jenkins** : este complemento permite la compilación de Visual Studio y Visual Studio para Mac soluciones (.sln) y proyectos (.csproj).
-  **Entorno Injector complemento** : se trata de un complemento opcional pero muy útil que hace posible establecer variables de entorno en el trabajo y nivel de compilación. También ofrece protección adicional para las variables, como las contraseñas utilizadas al código firmar la aplicación. A veces se abrevia como el *EnvInject Plugin* .
-  **Team Foundation Server complemento** : se trata de un complemento opcional que solo es necesario si usas Team Foundation Server o servicios de control de código fuente de Team Foundation.

Jenkins es compatible con Git sin los complementos adicionales.

Después de instalar todos los complementos, desea reiniciar Jenkins y configurar la configuración global para cada complemento. La configuración global para un complemento puede encontrarse seleccionando **Jenkins > Administrar Jenkins > configurar el sistema** desde la esquina superior izquierda, tal y como se muestra en la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image13.png "La configuración global para un complemento puede encontrarse seleccionando Jenkins / administrar Jenkins / configurar el sistema de la esquina superior izquierda pasar esquina")](jenkins-walkthrough-images/image13.png#lightbox)

Cuando se selecciona esta opción de menú, se le llevará a la **configurar el sistema [Jenkins]** página. Esta página contiene secciones para configurar Jenkins propio y establecer algunos de los valores de complemento global.  La captura de pantalla siguiente muestra un ejemplo de esta página:

 [![](jenkins-walkthrough-images/image14.png "Esta captura de pantalla muestra un ejemplo de esta página")](jenkins-walkthrough-images/image14.png#lightbox)


#### <a name="configuring-the-msbuild-plugin"></a>Configurar el complemento de MSBuild

El complemento de MSBuild debe configurarse para usar **/Library/Frameworks/Mono.framework/Commands/xbuild** compilar Visual Studio para los archivos de solución y proyecto de Mac. Desplácese hacia abajo el **configurar el sistema [Jenkins]** página hasta que el **agregar MSBuild** aparece el botón, como se muestra en la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image15.png "Desplácese hacia abajo en la página Configurar sistema Jenkins hasta que aparezca el botón Agregar MSBuild")](jenkins-walkthrough-images/image15.png#lightbox)

Haga clic en este botón y rellene el **nombre** y **ruta de acceso** a **MSBuild** campos en el formulario que aparece. El nombre de su **MSBuild** instalación debe ser un nombre significativo, mientras el **ruta de acceso a MSBuild** debe ser la ruta de acceso `xbuild`, que suele ser **/Library/marcos / Mono.framework/Commands/xbuild**. Después de que guardar los cambios haciendo clic en Guardar o en el botón Aplicar en la parte inferior de la página, Jenkins podrán usar `xbuild` para compilar las soluciones.

#### <a name="configuring-the-tfs-plugin"></a>Configurar el complemento TFS

Esta sección es obligatoria si va a usar TFS para el control de código fuente.

En el orden de una estación de trabajo de OS X interactuar con un servidor TFS, Team Explorer Everywhere debe instalarse en la estación de trabajo. Team Explorer Everywhere es un conjunto de herramientas de Microsoft que incluye a un cliente de línea de comandos multiplataforma para tener acceso a TFS. Team Explorer Everywhere puede descargarse de Microsoft e instalarse en tres pasos:

1. Descomprima el archivo de almacenamiento a un directorio que es accesible para la cuenta de usuario. Por ejemplo, puede descomprimir el archivo **~/tee**.
2. Configurar la ruta de acceso del sistema o el shell para incluir la carpeta que contiene los archivos que se descomprime en el paso anterior. Por ejemplo,

        echo export PATH~/tee/:$PATH' >> ~/.bash_profile

3. Para confirmar que Team Explorer Everywhere está instalado, abra una sesión de terminal y ejecute el `tf` comando. Si tf se configura correctamente, verá el siguiente resultado en la sesión de terminal:

        $ tf
        Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

        Available commands and their options:

Una vez instalado el cliente de línea de comandos de TFS, Jenkins debe configurarse con la ruta de acceso completa a la `tf` cliente de línea de comandos. Desplácese hacia abajo el **configurar el sistema [Jenkins]** página hasta que encuentre la sección de Team Foundation Server, como se muestra en la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image17.png "Desplácese hacia abajo en la página Configurar sistema Jenkins hasta que encuentre la sección de Team Foundation Server")](jenkins-walkthrough-images/image17.png#lightbox)

Escriba la ruta de acceso completa a la `tf` y haga clic en el **guardar** botón.

### <a name="configure-jenkins-security"></a>Configurar la seguridad de Jenkins

Cuando instala por primera vez Jenkins tiene la seguridad deshabilitada, por lo que es posible que cualquier usuario configurar y ejecutar cualquier tipo de trabajo de forma anónima. En esta sección se explica cómo configurar la seguridad mediante la base de datos de usuario Jenkins para configurar la autenticación y autorización.

Configuración de seguridad puede encontrarse seleccionando **Jenkins > Administrar Jenkins > configurar la seguridad Global**, tal y como se muestra en esta captura de pantalla:

 [![](jenkins-walkthrough-images/image18.png "Configuración de seguridad puede encontrarse seleccionando Jenkins / Jenkins administrar y configurar la seguridad Global")](jenkins-walkthrough-images/image18.png#lightbox)

En el **configurar la seguridad Global** página, compruebe la **Habilitar seguridad** casilla de verificación y **el Control de acceso** debe ser formulario, similar a la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image19.png "En la página Configurar la seguridad Global, comprobar la seguridad de habilitar la casilla de verificación y el formulario de Control de acceso deben ser, similares a esta captura de pantalla")](jenkins-walkthrough-images/image19.png#lightbox)

Alternar el botón de radio para **base de datos de usuario Jenkins propio** en el **sección de dominio Kerberos de seguridad**y asegúrese de que **permitir que los usuarios se suscriban** también se activa, como se muestra en el captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image20.png "Alternar el botón de opción de base de datos de usuario propia de Jenkins en la sección de dominio Kerberos de seguridad y asegúrese de que también esté marcada permitir a los usuarios para iniciar sesión")](jenkins-walkthrough-images/image20.png#lightbox)

Por último, reinicie Jenkins y crear una nueva cuenta. La primera cuenta que se crea la cuenta raíz, y esta cuenta se promocionarán automáticamente a un administrador. Vuelva a la **configurar la seguridad Global** página y desproteger la **seguridad basada en la matriz** botón de radio. La cuenta raíz se debe conceder acceso completo, y la cuenta anónima debe proporcionar acceso de solo lectura, tal y como se muestra en la captura de pantalla siguiente:

 [![](jenkins-walkthrough-images/image21.png "La cuenta raíz se debe conceder acceso completo, y la cuenta anónima tendrá acceso de solo lectura")](jenkins-walkthrough-images/image21.png#lightbox)

Una vez que se guarda esta configuración y Jenkins se reinicia, se activará la seguridad.


#### <a name="disabling-security"></a>Deshabilitar la seguridad

En caso de una contraseña olvidada o bloqueo Jenkins todo, es posible deshabilitar seguridad siguiendo estos pasos:

1. Detener Jenkins. Si usas Jenkins.app, puede hacerlo con el botón secundario en el icono de Jenkins.App en el área y seleccione Salir en el menú que aparece:

    ![](jenkins-walkthrough-images/image19.png "Icono de la aplicación en el acoplamiento y seleccionando Salir en el menú que aparece")
2. Abra el archivo **~/.jenkins/config.xml** en un editor de texto.
3. Cambie el valor de la `<usesecurity></usesecurity>` elemento de `true` a `false`.
4. Eliminar el `<authorizationstrategy></authorizationstrategy>` y `<securityrealm></securityrealm>` elementos desde el archivo.
5. Reinicie Jenkins.

## <a name="setting-up-a-job"></a>Cómo configurar un trabajo

En el nivel superior, Jenkins organiza todas las distintas tareas necesarias para compilar software en un *trabajo*. Un trabajo también tiene metadatos asociados a ella, que proporciona información acerca de la compilación, como cómo obtener el código fuente, con qué frecuencia se debe ejecutar la compilación, las variables especiales que son necesarias para crear y cómo notificar a los desarrolladores si se produce un error en la compilación.

Los trabajos se crean seleccionando **Jenkins > nuevo trabajo** en el menú en la esquina superior derecha, como se muestra en la captura de pantalla siguiente:


![](jenkins-walkthrough-images/image22.png "Los trabajos se crean seleccionando Jenkins nuevo trabajo en el menú en la esquina superior derecha")

Esto mostrará la **nuevo trabajo [Jenkins]** página. Escriba un nombre para el trabajo y seleccione el **compilar un proyecto de software de estilo libre** botón de radio. Captura de pantalla siguiente muestra un ejemplo de esto:

![](jenkins-walkthrough-images/image23.png "Escriba un nombre para el trabajo y seleccionar un botón de opción de proyecto de software de estilo libre de la compilación")

Al hacer clic en el **Aceptar** botón presenta la página de configuración para el trabajo. Esto debe ser similar a la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image24.png "Esto debe ser similar a esta captura de pantalla")

Jenkins organiza los trabajos en un directorio en el disco duro que se encuentra en la siguiente ruta: **~/.jenkins/jobs/[JOB nombre]**

Esta carpeta contiene todos los archivos y los artefactos específicos para el trabajo, como registros, archivos de configuración y el código fuente que debe ser compilado.

Una vez que se ha creado el trabajo inicial, debe configurarse con uno o varios de los siguientes:

 - Debe especificarse el sistema de administración de código fuente.
 - Uno o varios *acciones de compilación* debe agregarse al proyecto. Estos son los pasos o tareas necesarias para compilar la aplicación.
 - Debe tener asignado el trabajo uno *desencadenador de compilación* : un conjunto de instrucciones que le informa de la frecuencia con Jenkins para recuperar el código y compilar el proyecto final.

### <a name="configuring-source-code-control"></a>Configuración de Control de código fuente

La primera tarea Jenkins es recuperar el código fuente desde el sistema de administración de código fuente. Jenkins admite muchos de los sistemas de administración de código de origen populares disponibles hoy mismo. Esta sección describen dos sistemas populares, Git y Team Foundation Server. Cada uno de estos sistemas de administración de código de origen se explica con más detalle en las secciones siguientes.

#### <a name="using-git-for-source-code-control"></a>Usar Git para el Control de código fuente

Si usa TFS para control de código fuente [omitir](#Using_TFS_for_Source_Code_Management) esta sección y vaya a la siguiente sección con TFS.

Jenkins es compatible con Git desde el principio: no hay complementos adicionales son necesarios. Para usar Git, haga clic en el **Git** botón de radio y escriba la dirección URL para el repositorio Git, tal y como se muestra en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image25.png "Para usar Git, haga clic en el botón de radio Git y escriba la dirección URL para el repositorio de Git")

Una vez que se guardan los cambios, la configuración de Git está completa.

#### <a name="using-tfs-for-source-code-management"></a>Usar TFS para administración de código fuente

Esta sección solo se aplica a los usuarios TFS.

Haga clic en el **Team Foundation Server** botón de radio y la sección de configuración de TFS deben ser, similares a lo que aparece en la captura de pantalla siguiente:


![](jenkins-walkthrough-images/image26.png "Haga clic en el botón de opción de Team Foundation Server y debe aparecer la sección de configuración de TFS")

Proporcione la información necesaria para TFS. Captura de pantalla siguiente muestra un ejemplo del formulario completado:

![](jenkins-walkthrough-images/image27.png "Esta captura de pantalla muestra un ejemplo del formulario completado")

#### <a name="testing-the-source-code-control-configuration"></a>Probar la configuración de Control de código fuente

Una vez que se ha configurado el control de código fuente adecuado, haga clic en **guardar** para guardar los cambios. Esto le devolverá a la página de inicio para el trabajo, que será similar a la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image28.png "Esto le devolverá a la página de inicio para el trabajo, que será similar a esta captura de pantalla")

La manera más sencilla para validar que el control de código fuente está correctamente configurado es desencadenar una compilación manualmente, incluso si no hay ninguna acción de compilación especificada. Para iniciar manualmente una compilación, la página de inicio del trabajo tiene un **generar ahora** vínculo en el menú de la izquierda, tal y como se muestra en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image29.png "Para iniciar manualmente una compilación, la página de inicio del trabajo tiene un vínculo de compilación ahora en el menú de la izquierda")

Cuando se ha iniciado una compilación, el cuadro de diálogo Crear historial muestra un círculo azul intermitente, una barra de progreso, el número de compilación y la hora de inicio de la compilación, similar a la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image30.png "Cuando se ha iniciado una compilación, el cuadro de diálogo Crear historial muestra un círculo azul intermitente, una barra de progreso, el número de compilación y la hora de inicio de la compilación")

Si el trabajo tiene éxito, se mostrará un círculo azul. Si se produce un error en el trabajo, se mostrará un círculo rojo.

Para ayudar a solucionar problemas que pueden surgir como parte de la compilación, capturarán Jenkins toda la salida de consola para el trabajo. Para ver la salida de la consola, haga clic en el trabajo en **generar historial**y, a continuación, en la **salida de la consola** vínculo en el menú izquierdo. La siguiente captura de pantalla muestra la **salida de la consola** vínculo, así como algunos de los resultados de un trabajo con éxito:

![](jenkins-walkthrough-images/image31.png "Esta captura de pantalla muestra el vínculo de salida de la consola, así como algunos de los resultados de un trabajo con éxito")

#### <a name="location-of-build-artifacts"></a>Ubicación de los artefactos de compilación

Jenkins recuperará todo el código fuente en una carpeta especial llamada un *área de trabajo*. Este directorio se puede encontrar en la carpeta en la siguiente ubicación:

    ~/.jenkins/jobs/[JOB NAME]/workspace

La ruta de acceso al área de trabajo se almacenará en una variable de entorno denominada `$WORKSPACE`.

Es posible examinar la carpeta de área de trabajo en Jenkins, vaya a la página de inicio de un trabajo y, a continuación, haga clic en el **área de trabajo** vínculo en el menú izquierdo. Captura de pantalla siguiente muestra un ejemplo del área de trabajo para un trabajo denominado **HelloWorld**:

![](jenkins-walkthrough-images/image32.png "Esta captura de pantalla muestra un ejemplo del área de trabajo para un trabajo denominado HelloWorld")

### <a name="build-triggers"></a>Crear desencadenadores

Hay varias estrategias distintas para iniciar las compilaciones en Jenkins: se conocen como *crear desencadenadores*. Un desencadenador de compilación ayuda Jenkins decidir cuándo iniciar un trabajo y compile el proyecto. Dos de los desencadenadores de compilación más comunes son:

- **Crear periódicamente** – este desencadenador hace que Jenkins iniciar un trabajo a intervalos especificados, por ejemplo, cada dos horas o a medianoche los días laborables. Se iniciará la compilación independientemente de si ha habido cambios en el repositorio de código fuente.
- **Sondeo SCM** – este desencadenador sondeará el control de código fuente de forma regular. Si los cambios se han confirmado en el repositorio de código fuente, Jenkins iniciará una nueva compilación.

Sondeo SCM es un desencadenador popular porque proporciona comentarios rápidos cuando un desarrollador confirma los cambios que producen la compilación se interrumpa. Esto es útil para avisar a los equipos que algún código confirmada recientemente está causando problemas y permite a los desarrolladores de resolver el problema mientras que los cambios están aún frescos en mente.

Compilaciones periódicas a menudo se utilizan para crear una versión de la aplicación que se puede distribuir a los evaluadores. Por ejemplo, una compilación periódica puede programarse para el viernes por la noche para que los miembros del equipo QA pueden probar el trabajo de la semana anterior.


### <a name="compiling-a-xamarinios-applications"></a>Compilar una aplicación de Xamarin.iOS
Xamarin.iOS proyectos se pueden compilar en la línea de comandos utilizando `xbuild` o `msbuild`. El comando de shell se ejecutará en el contexto de la cuenta de usuario que se está ejecutando Jenkins. Es importante que la cuenta de usuario tiene acceso al perfil de aprovisionamiento para que la aplicación se puede empaquetar correctamente para la distribución. Es posible agregar este comando de shell a la página de configuración del trabajo.

Desplácese hacia abajo hasta la **generar** sección. Haga clic en el **agregar el paso de compilación** botón y seleccione **ejecutar shell**, tal y como se muestra en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image33.png "Haga clic en el botón de paso de compilación de agregar y seleccione Ejecutar shell")


[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>Generar un proyecto Xamarin.Android

Generar un proyecto de Xamarin.Android es muy similar en concepto a compilar un proyecto de Xamarin.iOS. Para crear un APK de un proyecto Xamarin.Android, Jenkins debe configurarse para llevar a cabo los dos pasos siguientes:

 - Compilar el proyecto mediante el complemento de MSBuild
 - Inicio de sesión y zip alinean el APK con un almacén de claves de versión válido.

Estos dos pasos se tratará con más detalle en las dos secciones siguientes.

### <a name="creating-the-apk"></a>Crear el APK

Haga clic en el **agregar el paso de compilación** botón y seleccione **compilar un proyecto de Visual Studio o una solución con MSBuild**, tal y como se muestra en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image36.png "Crear el APK haga clic en el botón de paso de compilación de agregar y seleccione generar un proyecto de Visual Studio o una solución con MSBuild")

Una vez que el paso de compilación se agrega al proyecto, rellene los campos de formulario que aparecen. Captura de pantalla siguiente es un ejemplo del formulario completado:

![](jenkins-walkthrough-images/image37.png "Una vez que el paso de compilación se agrega al proyecto, rellene los campos de formulario que aparecen")


Este paso de compilación se ejecutará `xbuild` en el **$WORKSPACE** carpeta. El archivo de compilación de MSBuild se establece en el **Xamarin.Android.csproj** archivo. El **argumentos de línea de comandos** especificar una versión de lanzamiento del destino **PackageForAndroid**. El producto de este paso será un APK que en la siguiente ubicación:

    $WORKSPACE/[PROJECT NAME]/bin/Release

Captura de pantalla siguiente muestra un ejemplo de este APK:

![](jenkins-walkthrough-images/image38.png "Esta captura de pantalla muestra un ejemplo de este APK")

Este APK no está listo para la implementación, como no se ha firmado con un almacén de claves privada y debe estar alineado zip.

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>Firma y Zipaligning el APK de versión

Firma y zipaligning el APK son técnicamente dos tareas independientes que se realizan con dos herramientas de línea de comandos diferente desde el SDK de Android. Sin embargo, resulta conveniente seguir estos pasos de acción de una compilación. Para obtener más información sobre la firma y zipaligning un APK, consulte la documentación de Xamarin sobre cómo preparar una aplicación Android para la versión.

Estos dos comandos requieren parámetros de línea de comandos que pueden variar en cada proyecto. Además, algunos de estos parámetros de línea de comandos son las contraseñas que no deben aparecer en la salida de la consola cuando se está ejecutando la compilación. Algunos de estos parámetros de línea de comandos se podrá almacenar en variables de entorno. Las variables de entorno necesarias para firma o alinear zip se describen en la tabla siguiente:

<table>
    <thead>
        <tr>
            <td>Variable de entorno</td>
            <td>Descripción</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>KEYSTORE_FILE</td>
            <td>Se trata de la ruta de acceso para el almacén de claves para firmar el APK</td>
        </tr>
        <tr>
            <td>KEYSTORE_ALIAS</td>
            <td>La clave en el almacén de claves que se usará para firmar el APK.</td>
        </tr>
        <tr>
            <td>INPUT_APK</td>
            <td>El APK creado por `xbuild`.</td>
        </tr>
        <tr>
            <td>SIGNED_APK</td>
            <td>El APK firmado generados por `jarsigner`.</td>
        </tr>
        <tr>
            <td>FINAL_APK</td>
            <td>Este es el archivo zip alineado APK generado por `zipalign`.</td>
        </tr>
        <tr>
            <td>STORE_PASS</td>
            <td>Esta es la contraseña que se usa para tener acceso al contenido del almacén de claves para cante el archivo.</td>
        </tr>
    </tbody>
</table>

Como se describe en la sección de requisitos, se pueden establecer estas variables de entorno durante la compilación mediante el complemento de EnvInject. El trabajo debe tener una nueva compilación paso agregó basándose en las variables de entorno de inserción, tal y como se muestra en la captura de pantalla siguiente:

![](jenkins-walkthrough-images/image39.png "El trabajo debe tener una nueva compilación paso agrega basándose en las variables de entorno de inserción")

En el **propiedades contenido** forman el campo que va a aparecer, se agregan las variables de entorno, uno por línea, en el formato siguiente:

    ENVIRONMENT_VARIABLE_NAME = value

Captura de pantalla siguiente muestra las variables de entorno que se necesitan para firmar el APK:

![](jenkins-walkthrough-images/image40.png "Esta captura de pantalla muestra las variables de entorno que se necesitan para firmar el APK")

Tenga en cuenta que algunas de las variables de entorno para los archivos APK están integradas el `WORKSPACE` variable de entorno.

La variable de entorno final es la contraseña para acceder al contenido del almacén de claves: `STORE_PASS`. Las contraseñas son los valores confidenciales que deben estar oculta o se omite en archivos de registro. El complemento de EnvInject puede configurarse para proteger estos valores para que no se muestran en los registros.

Inmediatamente antes de la **generar** sección de la configuración del trabajo es un **entorno Build** sección. Cuando el **insertar contraseñas** es activar o desactivar la casilla de verificación, campos de alguna forma que aparezca. Estos campos de formulario se utilizan para capturar el nombre y valor de la variable de entorno. Captura de pantalla siguiente es un ejemplo de cómo agregar el `STORE_PASS` variable de entorno:

![](jenkins-walkthrough-images/image41.png "Esta captura de pantalla es un ejemplo de cómo agregar la variable de entorno STOREPASS")

Después de han inicializado las variables de entorno, el siguiente paso es agregar un paso de compilación para la firma y código postal alinear el APK. Inmediatamente después de que el paso de compilación para insertar las variables de entorno será otro **ejecutar shell** compilación de comando que ejecutará `jarsigner` y `zipalign`. Cada comando tendrá una línea hacia arriba tal como se muestra en el fragmento siguiente:


    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
    zipalign -f -v 4 $SIGNED_APK $FINAL_APK

Captura de pantalla siguiente muestra un ejemplo de cómo especificar el `jarsigner` y `zipalign` comandos en el paso:

![](jenkins-walkthrough-images/image42.png "Esta captura de pantalla muestra un ejemplo de cómo especificar los comandos jarsigner y zipalign en el paso")

Una vez que todas las acciones de compilación están en su lugar, es recomendable para desencadenar una compilación manual para comprobar que todo funciona. Si se produce un error en la compilación, el **salida de la consola** deben revisarse para obtener información sobre la causa de un fallo de generación.

### <a name="submitting-tests-to-test-cloud"></a>Envío de pruebas para pruebas en la nube

Pruebas automatizadas se pueden enviar a la nube de prueba mediante comandos de shell. Para obtener más información acerca de cómo configurar una serie de pruebas en la nube de la prueba de Xamarin, tenemos guías para usar [Xamarin.UITest](https://developer.xamarin.com/guides/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/) o [Calabash](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/).


## <a name="summary"></a>Resumen

En esta guía se introdujo Jenkins como un servidor de compilación en Mac OS X y configurarlo para compilar y preparar las aplicaciones móviles de Xamarin para la versión. Jenkins se instala en un equipo Mac OS X junto con varios complementos para admitir el proceso de compilación. Se crean y configura un trabajo que se extraiga el código de TFS o Git y, a continuación, compilar ese código en una aplicación lista de versión. También hemos examinado dos maneras de programar cuándo deben ejecutarse los trabajos.

## <a name="related-links"></a>Vínculos relacionados

- [Integración continua](https://developer.xamarin.com~/testcloud/ci/)
- [Envío de pruebas para la prueba de Xamarin en la nube](https://developer.xamarin.com~/testcloud/submitting/)
- [¿Por qué no es compatible Jenkins con Xamarin?](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)
