---
title: Espacio aislado de una aplicación Xamarin.Mac
description: Este artículo trata el espacio aislado de una aplicación Xamarin.Mac para lanzamiento de la tienda de aplicaciones. Se ocupa de todos los elementos que van en espacio aislado, como los directorios de contenedor, los derechos, permisos de usuario determinado, separación de privilegios y cumplimiento de kernel.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a02d7639975de092b05f31bacedd6bde4c9392f9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30787914"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Espacio aislado de una aplicación Xamarin.Mac

_Este artículo trata el espacio aislado de una aplicación Xamarin.Mac para lanzamiento de la tienda de aplicaciones. Se ocupa de todos los elementos que van en espacio aislado, como los directorios de contenedor, los derechos, permisos de usuario determinado, separación de privilegios y cumplimiento de kernel._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tiene la misma capacidad para el espacio aislado de una aplicación como lo hace cuando se trabaja con Objective-C o Swift.

[![Un ejemplo de la aplicación en ejecución](sandboxing-images/intro01.png "muestra un ejemplo de la aplicación en ejecución")](sandboxing-images/intro01-large.png#lightbox)

En este artículo, hablaremos sobre los conceptos básicos sobre cómo trabajar con espacio aislado en una aplicación Xamarin.Mac y todos los elementos que van en espacio aislado: directorios de contenedor, derechos, permisos de usuario determinado, separación de privilegios y cumplimiento de kernel. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` atributos se utiliza para conectarlo las clases de C# a los objetos de Objective-C y la interfaz de usuario de elementos.

## <a name="about-the-app-sandbox"></a>Acerca del espacio aislado de la aplicación

El espacio aislado de la aplicación proporciona una defensa segura frente al daño que puede estar causada por una aplicación malintencionada que se va a ejecutar en un equipo Mac limitando el acceso que tiene una aplicación a los recursos del sistema.

Una aplicación no en espacio aislado tiene todos los derechos del usuario que ejecuta la aplicación y puede tener acceso a o todo lo que el usuario puede realizar hacer. Si la aplicación contiene vulnerabilidades de seguridad (o cualquier marco de trabajo que se esté utilizando), un pirata informático podría aprovechar los puntos débiles y utilice la aplicación controlar el equipo Mac que se está ejecutando en.

Si limita el acceso a los recursos de según la aplicación, una aplicación en espacio aislado proporciona una línea de defensa contra el robo, daños o malintencionada por parte de una aplicación que se ejecuta en el equipo del usuario.

El espacio aislado de la aplicación es una tecnología de control de acceso incorporada macOS (se aplica en el nivel de kernel) que proporciona una estrategia de doble:

1. El espacio aislado de la aplicación permite al desarrollador describir _cómo_ una aplicación interactuará con el sistema operativo y, de este modo, se le conceden los derechos de acceso que son necesarias para realizar el trabajo y nada más.
2. El espacio aislado de la aplicación permite al usuario sin problemas conceder más acceso al sistema a través de abrir y guardar cuadros de diálogo, arrastre y coloque las operaciones y las interacciones del usuario de otros, comunes.

### <a name="preparing-to-implement-the-app-sandbox"></a>Preparación para implementar el espacio aislado de la aplicación

Los elementos del espacio aislado de aplicación que se describen en detalle en el artículo son los siguientes:

- Directorios de contenedor
- Derechos
- Permisos de usuario determinado
- Separación de privilegios
- Cumplimiento de kernel

Una vez que comprenda estos detalles, podrá crear un plan para adoptar el espacio aislado de aplicación en la aplicación Xamarin.Mac.

En primer lugar, debe determinar si su aplicación es un buen candidato para el espacio aislado (la mayoría de las aplicaciones es). A continuación, debe resolver las incompatibilidades de API y determinar qué elementos del espacio aislado de aplicación que vas a necesitar. Por último, considerar la utilización de separación de privilegios para maximizar el nivel de defensa de la aplicación.

Al adoptar el espacio aislado de la aplicación, algunas ubicaciones de sistema de archivos que usa su aplicación serán diferentes. En particular, la aplicación tendrá un directorio de contenedor que se usará para los archivos de compatibilidad de aplicaciones, bases de datos, memorias caché y cualquier otro archivo que no es documentos de usuario. MacOS y Xcode proporcionan compatibilidad para migrar estos tipos de archivos desde sus ubicaciones heredados al contenedor.

## <a name="sandboxing-quick-start"></a>Inicio rápido de espacio aislado

En esta sección, vamos a crear una aplicación sencilla de Xamarin.Mac que utiliza una vista Web (que requiere una conexión de red que está restringida en espacio aislado a menos que se le solicita específicamente) como un ejemplo de cómo empezar a usar el espacio aislado de la aplicación.

Comprobaremos que la aplicación está realmente en un espacio aislada y aprender a localizar y solucionar errores comunes de espacio aislado de la aplicación.

### <a name="creating-the-xamarinmac-project"></a>Crear el proyecto Xamarin.Mac

Vamos a hacer lo siguiente para crear el proyecto de ejemplo:

1. Inicie Visual Studio para Mac y haga clic en el **nueva solución...** .
2. Desde el **nuevo proyecto** cuadro de diálogo, seleccione **Mac** > **aplicación** > **cacao aplicación**: 

    [![Crear una nueva aplicación de cacao](sandboxing-images/sample01.png "crear una nueva aplicación de cacao")](sandboxing-images/sample01-large.png#lightbox)
3. Haga clic en el **siguiente** botón, escriba `MacSandbox` para el nombre del proyecto y haga clic en el **crear** botón: 

    [![Escriba el nombre de la aplicación](sandboxing-images/sample02.png "escribir el nombre de la aplicación")](sandboxing-images/sample02-large.png#lightbox)
4. En el **solución Pad**, haga doble clic en el **Main.storyboard** archivo para abrirlo y editarlo en Xcode: 

    [![Editar el guión gráfico principal](sandboxing-images/sample03.png "el guión gráfico principal de edición")](sandboxing-images/sample03-large.png#lightbox)
5. Arrastre un **vista Web** en la ventana, cambiar su tamaño para rellenar el área de contenido y configurarlo para aumentar o reducir con la ventana: 

    [![Agregar una vista web](sandboxing-images/sample04.png "agregar una vista web")](sandboxing-images/sample04-large.png#lightbox)
6. Crear una salida para la vista web denominada `webView`: 

    [![Crear una nueva toma](sandboxing-images/sample05.png "crear una nueva salida")](sandboxing-images/sample05-large.png#lightbox)
7. Vuelva a Visual Studio para Mac y haga doble clic en el **ViewController.cs** un archivo en el **solución Pad** para abrirlo y editarlo.
8. Agregue la siguiente instrucción using: `using WebKit;`
9. Realizar el `ViewDidLoad` método aspecto similar al siguiente: 

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();
    webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
}
```

10. Guarde los cambios.

Ejecutar la aplicación y asegúrese de que el sitio Web de Apple se muestra en la ventana como se indica a continuación:

[![Muestra una aplicación de ejemplo ejecución](sandboxing-images/sample06.png "que muestra una aplicación de ejemplo ejecución")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>Firma y el aprovisionamiento de la aplicación

Antes de que podemos habilitar el espacio aislado de la aplicación, primero es necesario aprovisionar y firmar la aplicación Xamarin.Mac.

Le permiten hacer lo siguiente:

1. Inicie sesión en el Portal para desarrolladores de Apple: 

    [![Iniciar sesión en el Portal para desarrolladores de Apple](sandboxing-images/sign01.png "iniciar sesión en el Portal para desarrolladores de Apple")](sandboxing-images/sign01-large.png#lightbox)
2. Seleccione **certificados, identificadores & perfiles**: 

    [![Seleccionar certificados, identificadores y perfiles](sandboxing-images/sign02.png "Seleccionar certificados, identificadores y perfiles")](sandboxing-images/sign02-large.png#lightbox)
3. En **aplicaciones de Mac**, seleccione **identificadores**: 

    [![Al seleccionar identificadores](sandboxing-images/sign03.png "al seleccionar identificadores")](sandboxing-images/sign03-large.png#lightbox)
4. Crear un nuevo identificador para la aplicación: 

    [![Crear un nuevo identificador de aplicación](sandboxing-images/sign04.png "crear un nuevo ID de aplicación.")](sandboxing-images/sign04-large.png#lightbox)
5. En **perfiles de aprovisionamiento**, seleccione **desarrollo**: 

    [![Selección de desarrollo](sandboxing-images/sign05.png "selección de desarrollo")](sandboxing-images/sign05-large.png#lightbox)
6. Crear un nuevo perfil y seleccione **desarrollo de aplicaciones de Mac**: 

    [![Crear un nuevo perfil](sandboxing-images/sign06.png "crear un nuevo perfil")](sandboxing-images/sign06-large.png#lightbox)
7. Seleccione el identificador de aplicación que hemos creado anteriormente: 

    [![Seleccionar el identificador de la aplicación](sandboxing-images/sign07.png "al seleccionar el identificador de la aplicación")](sandboxing-images/sign07-large.png#lightbox)
8. Seleccione los desarrolladores para este perfil: 

    [![Agregar a los desarrolladores](sandboxing-images/sign08.png "agregar a los desarrolladores")](sandboxing-images/sign08-large.png#lightbox)
9. Seleccione los equipos para este perfil: 

    [![Selección de los equipos permitidos](sandboxing-images/sign09.png "selección de los equipos permitidos")](sandboxing-images/sign09-large.png#lightbox)
10. Asigne un nombre de perfil: 

    [![Dar un nombre al perfil](sandboxing-images/sign10.png "dando un nombre al perfil")](sandboxing-images/sign10-large.png#lightbox)
11. Haga clic en el **realiza** botón.

> [!IMPORTANT]
> En algunos casos deberá descargar directamente el nuevo perfil de aprovisionamiento del Portal para desarrolladores de Apple y haga doble clic en él para instalar. También tendrá que detener y reiniciar Visual Studio para Mac antes de poder tener acceso al perfil nuevo.

A continuación, es necesario cargar el nuevo identificador de la aplicación y el perfil en el equipo de desarrollo. Vamos a hacer lo siguiente:

1. Iniciar Xcode y seleccione **preferencias** desde el **Xcode** menú: 

    ![Editar cuentas en Xcode](sandboxing-images/sign11.png "editar cuentas en Xcode.")
2. Haga clic en el **ver detalles...**  botón: 

    ![Haga clic en el botón Ver detalles](sandboxing-images/sign12.png "al hacer clic en el botón Ver detalles")
3. Haga clic en el **actualizar** botón (en la esquina inferior izquierda).
4. Haga clic en el **realiza** botón.

A continuación, es necesario seleccionar el nuevo identificador de la aplicación y perfil de aprovisionamiento en nuestro proyecto Xamarin.Mac. Vamos a hacer lo siguiente:

1. En el **solución Pad**, haga doble clic en el **Info.plist** archivo para abrirlo y editarlo.
2. Asegúrese de que el **identificador de paquete** coincide con el identificador de la aplicación hemos creado anteriormente (ejemplo: `com.appracatappra.MacSandbox`): 

    [![Editar el identificador de paquete](sandboxing-images/sign13.png "editar el identificador de paquete")](sandboxing-images/sign13-large.png#lightbox)
3. A continuación, haga doble clic en el **Entitlements.plist** de archivos y asegúrese de nuestro **iCloud almacén de clave y valor** y **iCloud contenedores** todos coinciden con el identificador de la aplicación hemos creado anteriormente (ejemplo: `com.appracatappra.MacSandbox`): 

    [![Editar el archivo Entitlements.plist](sandboxing-images/sign17.png "editando el archivo Entitlements.plist")](sandboxing-images/sign17-large.png#lightbox)
3. Guarde los cambios.
4. En el **solución Pad**, haga doble clic en para abrir sus opciones para modificar el archivo de proyecto:  

    ![Editign opciones de la solución](sandboxing-images/sign14.png "Editign opciones de la solución")
5. Seleccione **Mac firma**, a continuación, busque **firmar el paquete de aplicación** y **firmar el paquete de instalador**. En **perfil de aprovisionamiento**, seleccione lo que hemos creado anteriormente: 

    ![Configurar el perfil de aprovisionamiento](sandboxing-images/sign15.png "establecer el perfil de aprovisionamiento")
6. Haga clic en el **realiza** botón.

> [!IMPORTANT]
> Es posible que deba salga y reinicie Visual Studio para Mac prepararla para reconocer el nuevo identificador de la aplicación y perfil de aprovisionamiento que se haya instalado mediante Xcode.

#### <a name="troubleshooting-provisioning-issues"></a>Solucionar problemas de aprovisionamiento

En este momento debe intentar ejecutar la aplicación y asegúrese de que todo el contenido y se haya creado correctamente. Si la aplicación sigue ejecutándose como antes, todo lo que es bueno. Si se produce un error, podría aparecer un cuadro de diálogo similar al siguiente:

[![Un ejemplo de cuadro de diálogo de problema aprovisionamiento](sandboxing-images/sign16.png "muestra un ejemplo de cuadro de diálogo de problema aprovisionamiento")](sandboxing-images/sign16-large.png#lightbox)

Estas son las causas más comunes de aprovisionamiento y la firma de problemas:

- El identificador de paquete de aplicación no coincide con el identificador de aplicación del perfil seleccionado.
- El ID del programador no coincide con el ID del programador del perfil seleccionado.
- El UUID de la que se está probando en direcciones Mac no está registrado como parte del perfil seleccionado.

En el caso de un problema, corrija el problema en el Portal para desarrolladores de Apple, actualizar los perfiles de Xcode y realice una compilación limpia en Visual Studio para Mac.

### <a name="enable-the-app-sandbox"></a>Habilitar el espacio aislado de la aplicación

Habilitar el espacio aislado de la aplicación seleccionando una casilla en las opciones de proyectos. Haga lo siguiente:

1. En el **solución Pad**, haga doble clic en el **Entitlements.plist** archivo para abrirlo y editarlo.
2. Comprobar ambos **habilitar derechos** y **habilitar el espacio aislado de la aplicación**: 

    [![Derechos de edición y habilitar el espacio aislado](sandboxing-images/sign17.png "derechos de edición y habilitar el espacio aislado")](sandboxing-images/sign17-large.png#lightbox)
3. Guarde los cambios.

En este momento, ha habilitado el espacio aislado de la aplicación pero no ha proporcionado el acceso de red requeridos para la vista Web. Si se ejecuta la aplicación ahora, debería obtener una ventana en blanco:

[![Mostrar la está bloqueando el acceso web](sandboxing-images/sample08.png "que muestra la está bloqueando el acceso web")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Comprobar que la aplicación se encuentra en un espacio aislado

Además el comportamiento de bloqueo de recurso, hay tres maneras principales para indicar si una aplicación de Xamarin.Mac ha sido correctamente en un espacio aislada:

1. En Finder, compruebe el contenido de la `~/Library/Containers/` carpeta - si la aplicación está en un espacio aislado, habrá una carpeta denominada como identificador de paquete de la aplicación (ejemplo: `com.appracatappra.MacSandbox`): 

    [![Abrir el paquete de la aplicación](sandboxing-images/sample09.png "abrir el paquete de la aplicación")](sandboxing-images/sample09-large.png#lightbox)
2. El sistema considera la aplicación en espacio aislado en el Monitor de actividad:
    - Iniciar el Monitor de actividad (en `/Applications/Utilities`). 
    - Elija **vista** > **columnas** y asegúrese de que el **espacio aislado** elemento de menú está activado.
    - Asegúrese de que la columna de espacio aislado se denomine `Yes` para la aplicación: 

    [![Comprobación de la aplicación en el Monitor de actividad](sandboxing-images/sample10.png "comprobación de la aplicación en el Monitor de actividad")](sandboxing-images/sample10-large.png#lightbox)
3. Compruebe que la aplicación binaria se encuentra en un espacio aislado:
    - Inicie la aplicación Terminal.
    - Vaya a las aplicaciones `bin` directory.
    - Emitir este comando: `codesign -dvvv --entitlements :- executable_path` (donde `executable_path` es la ruta de acceso a la aplicación): 

    [![Comprobación de la aplicación en la línea de comandos](sandboxing-images/sample11.png "comprobación de la aplicación en la línea de comandos")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Para depurar una aplicación en espacio aislado

El depurador se conecta a aplicaciones de Xamarin.Mac a través de TCP, lo que significa que de forma predeterminada cuando se habilita el espacio aislado, no se puede conectar a la aplicación, por lo que si se intenta ejecutar la aplicación sin los permisos adecuados habilitados, obtendrá un error *"no se puede conectar a el depurador"*. 

[![Establecer las opciones necesarias](sandboxing-images/debug01.png "establecer las opciones necesarias")](sandboxing-images/debug01-large.png#lightbox)

El **permitir conexiones de red salientes (cliente)** permiso es necesario para que el depurador, éste se habilita, normalmente la depuración. Puesto que no se pueden depurar sin él, hemos actualizado el `CompileEntitlements` de destino para `msbuild` para agregar automáticamente ese permiso a los derechos para cualquier aplicación que se encuentre en un espacio aislado para depuración solo compila. Versiones de lanzamiento deben usar los derechos especificados en el archivo de derechos, sin modificar.

### <a name="resolving-an-app-sandbox-violation"></a>Resolver una infracción de espacio aislado de la aplicación

Si un espacio aislado Xamarin.Mac aplicación intentó obtener acceso a un recurso que tiene no se permite explícitamente, se produce una infracción de espacio aislado de la aplicación. Por ejemplo, la vista Web ya no poder mostrar el sitio Web de Apple.

El origen más común de infracciones de espacio aislado de la aplicación se produce cuando la configuración de derechos especificada en Visual Studio para Mac no coinciden con los requisitos de la aplicación. Nuevo, se copia en nuestro ejemplo, la conexión de red que faltan derechos que mantienen la vista Web de trabajo.

#### <a name="discovering-app-sandbox-violations"></a>Detección de infracciones de espacio aislado de la aplicación

Si sospecha que se está produciendo una infracción de espacio aislado de la aplicación en la aplicación Xamarin.Mac, es la forma más rápida para detectar el problema mediante la **consola** aplicación.

Haga lo siguiente:

1. La aplicación en cuestión para compilar y ejecutar desde Visual Studio para Mac.
2. Abra la **consola** aplicación (de `/Applications/Utilties/`).
3. Seleccione **todos los mensajes** en la barra lateral y escriba `sandbox` en la búsqueda: 

    [![Un ejemplo de un problema de espacio aislado en la consola de](sandboxing-images/resolve01.png "muestra un ejemplo de un problema de espacio aislado en la consola de")](sandboxing-images/resolve01-large.png#lightbox)

En nuestra aplicación de ejemplo anterior, puede ver que está bloqueando la Kernal la `network-outbound` tráfico gracias al espacio aislado de aplicación, ya que no hemos solicitado ese derecho.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Corregir las infracciones de derechos de espacio aislado de la aplicación

Ahora que hemos visto cómo buscar infracciones de espacio aislado de aplicación, veamos cómo puede resolverse mediante el ajuste de los derechos de nuestra aplicación.

Haga lo siguiente:

1. En el **solución Pad**, haga doble clic en el **Entitlements.plist** archivo para abrirlo y editarlo.
2. En el **derechos** sección, compruebe el **permitir conexiones de red salientes (cliente)** casilla de verificación: 

    [![Editar los derechos](sandboxing-images/sign17.png "los derechos de edición")](sandboxing-images/sign17-large.png#lightbox)
3. Guarde los cambios en la aplicación.

Si todo lo anterior para nuestra aplicación de ejemplo, a continuación, compilar y ejecutarlo, ahora se mostrará el contenido web según lo previsto.

## <a name="the-app-sandbox-in-depth"></a>El espacio aislado de aplicación detallada

Los mecanismos de control de acceso proporcionados por el espacio aislado de la aplicación son pocas y fáciles de entender. Sin embargo, que el espacio aislado de la aplicación será aprobado por cada aplicación de forma única y se basa en los requisitos de la aplicación.

Dada su mejor esfuerzo para impedir que la aplicación Xamarin.Mac quedarían por código malintencionado, debe haber sólo una sola vulnerabilidad en la aplicación de cualquier (o una de las bibliotecas o marcos de trabajo consume) para obtener el control de las interacciones de la aplicación con el sistema.

El espacio aislado de la aplicación se diseñó para evitar que la adquisición (o limitar el daño que puede causar) por lo que le permite especificar las interacciones previsto de la aplicación con el sistema. El sistema sólo permitirá el acceso a los recursos que requiera la aplicación para realizar su trabajo y nada más.

Al diseñar el espacio aislado de la aplicación, está diseñando para un escenario más desfavorable. Si la aplicación pierde su carácter confidencial por código malintencionado, se limita al acceso a solo los archivos y recursos en el espacio aislado de la aplicación.

### <a name="entitlements-and-system-resource-access"></a>Los derechos de acceso al sistema y recursos

Tal y como se ha explicado anteriormente, una aplicación de Xamarin.Mac que no ha sido en espacio aislado se concede los derechos completos y el acceso del usuario que ejecuta la aplicación. Si se pone en peligro el código malintencionado, una aplicación protegida con no puede actuar como un agente para el comportamiento hostil, con una amplia que abarcan potencial para hacer daño.

Al habilitar el espacio aislado de la aplicación, quite todo excepto un conjunto mínimo de privilegios, que se puede, a continuación, volver a habilitar de forma solo necesidad de uso de los derechos de la aplicación Xamarin.Mac. 

Modificar recursos de espacio aislado de aplicación de la aplicación mediante la edición de su **Entitlements.plist** archivo y comprobando o seleccionar los derechos necesarios en los cuadros de lista desplegable de editores:

[![Editar los derechos](sandboxing-images/sign17.png "los derechos de edición")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>Acceso al sistema de archivos y directorios de contenedor

Cuando la aplicación Xamarin.Mac adopta el espacio aislado de la aplicación, que tiene acceso a las siguientes ubicaciones:

- **El directorio de contenedor de la aplicación** -en la primera ejecución, el sistema operativo crea una clase especial _contenedor directorio_ donde todos sus recursos Ir, que solo puede tener acceso. La aplicación tendrá acceso completo de lectura/escritura a este directorio.
- **Directorios de contenedor del grupo de aplicaciones** -la aplicación pueda tener acceso a uno o varios _contenedores del grupo_ que se comparten entre aplicaciones en el mismo grupo.
- **Los archivos especificados por el usuario** -automáticamente, la aplicación obtiene acceso a los archivos que están explícitamente abierto o arrastrar y colocar en la aplicación por el usuario.
- **Elementos relacionados** -con los derechos adecuados, la aplicación puede tener acceso a un archivo con el mismo nombre pero con una extensión diferente. Por ejemplo, un documento que se ha guardado como un `.txt` archivo y un `.pdf`.
- **Directorios temporales, los directorios de herramienta de línea de comandos y determinadas ubicaciones legible para el mundo** -la aplicación tiene distintos niveles de acceso a los archivos en otras ubicaciones bien definidos según lo especificado por el sistema.

#### <a name="the-app-container-directory"></a>El directorio de contenedor de aplicación

Directorio de la aplicación de Xamarin.Mac aplicación contenedor tiene las siguientes características:

- Se encuentra en una ubicación oculta en el directorio del usuario principal (normalmente `~Library/Containers`) y puede obtenerse con el `NSHomeDirectory` función (ver abajo) dentro de la aplicación. Porque está en el directorio particular, cada usuario obtendrá su propio contenedor para la aplicación.
- La aplicación tendrá acceso de lectura/escritura no restringido en el directorio de contenedor y todos sus subdirectorios y archivos contenidos en él.
- La mayoría de las API de búsqueda de la ruta de acceso del macOS son con respecto al contenedor de la aplicación. Por ejemplo, el contenedor tendrá su propio **biblioteca** (acceso a través de `NSLibraryDirectory`), **compatibilidad con aplicaciones** y **preferencias** subdirectorios.
- macOS establece y aplica la conexión entre y aplicación y su contenedor mediante la firma de código. Aunque sea otra aplicación intenta suplantar la identidad de la aplicación mediante el uso de su **identificador de paquete**, no podrá tener acceso al contenedor debido a la firma de código.
- El contenedor no es para los archivos de generado por los usuarios. En su lugar es para los archivos que usa su aplicación, como bases de datos, memorias caché u otros tipos de datos específicos.
- Para _caja de zapatos_ tipos de aplicaciones (por ejemplo, aplicación de fotografía de Apple), el contenido del usuario entrarán en el contenedor.

> [!IMPORTANT]
> Por desgracia, Xamarin.Mac no tiene cobertura del 100% API todavía (a diferencia de Xamarin.iOS), como resultado la `NSHomeDirectory` API no se ha asignado en la versión actual de Xamarin.Mac.

Como solución temporal, puede usar el código siguiente:

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>El directorio de contenedor del grupo de aplicaciones

A partir de Mac macOS 10.7.5 (y superior), una aplicación puede utilizar el `com.apple.security.application-groups` derechos para tener acceso a un contenedor compartido que es común a todas las aplicaciones en el grupo. Puede utilizar este contenedor compartido para el contenido de orientados al público no es de usuario como base de datos u otros tipos de archivos de soporte técnico (por ejemplo, las memorias caché).

Los contenedores de grupo se agregan automáticamente al contenedor de espacio aislado de la aplicación (si son parte de un grupo) y se almacenan en `~/Library/Group Containers/<application-group-id>`. El identificador de grupo _debe_ comienzan con el identificador de equipo de desarrollo y un período, por ejemplo:

```plist
<team-id>.com.company.<group-name>
```

Para obtener más información, vea de Apple [agregar una aplicación a un grupo de aplicación](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) en [referencia principal de los derechos](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195).

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Powerbox y el archivo de acceso al sistema fuera del contenedor de la aplicación

Una aplicación en espacio aislado de Xamarin.Mac puede tener acceso a ubicaciones del sistema de archivos fuera de su contenedor de las maneras siguientes:

- En la dirección específica del usuario (a través de abrir y guardar cuadros de diálogo u otros métodos, como arrastrar y colocar).
- Mediante el uso de los derechos para las ubicaciones de sistema de archivos específico (como `/bin` o `/usr/lib`).
* Cuando la ubicación del sistema de archivos está en ciertos directorios que son world legible (por ejemplo, el uso compartido).

_Powerbox_ es la macOS tecnología de seguridad que interactúa con el usuario para expandir los derechos de acceso del archivo de la aplicación Xamarin.Mac en espacio aislado. Powerbox no tiene ninguna API, pero se activa de forma transparente cuando la aplicación llama una `NSOpenPanel` o `NSSavePanel`. Acceso de Powerbox está habilitado a través de los derechos que establezca para la aplicación Xamarin.Mac.

Cuando una aplicación en espacio aislado muestra abierto o cuadro de diálogo Guardar, la ventana se presenta mediante Powerbox (y no AppKit) y, por tanto, tiene acceso a ningún archivo o directorio que el usuario tiene acceso a.

Cuando un usuario selecciona un archivo o directorio de abrir o guardar el cuadro de diálogo (o arrastra cualquiera en el icono de la aplicación), Powerbox agrega la ruta de acceso asociado al espacio aislado de la aplicación.

Además, el sistema permite automáticamente las siguientes acciones para una aplicación en espacio aislado:

- Conexión a un método de entrada del sistema.
- Llame a los servicios seleccionados por el usuario desde el **servicios** menú (solo para los servicios marcan como _seguro para las aplicaciones de espacio aislado_ por el proveedor de servicios).
- Elija archivos abiertos por el usuario desde el **Abrir recientes** menú.
- Usar copiar y pegar entre otras aplicaciones.
- Leer archivos de las siguientes ubicaciones legible para el mundo:
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- Leer y escribir archivos en los directorios creados por `NSTemporaryDirectory`.

De forma predeterminada abrir o guardar una aplicación en espacio aislado de Xamarin.Mac tendrá acceso a archivos hasta que la aplicación finaliza (a menos que el archivo todavía estaba abierto cuando se cierra la aplicación). Archivos abiertos se restaurará automáticamente para el espacio aislado de la aplicación a través de la característica de reanudación macOS la próxima vez que se inicie la aplicación.

Para proporcionar persistencia a archivos ubicados fuera de contenedor de un App Xamarin.Mac, utilice marcadores de ámbito de seguridad (ver abajo).

#### <a name="related-items"></a>Elementos relacionados

El espacio aislado de la aplicación permite que una aplicación tener acceso a los elementos relacionados que tienen el mismo nombre de archivo, pero diferentes extensiones. La característica tiene dos partes: un) una lista de Extensiones relacionadas en la aplicación `Info.plst` de archivos, código b) para indicar el espacio aislado lo que la aplicación harán con estos archivos.

Hay dos escenarios donde esto tiene sentido:

1. La aplicación debe ser capaz de guardar una versión diferente del archivo (con una nueva extensión). Por ejemplo, exportar un `.txt` del archivo a un `.pdf` archivo. Para controlar esta situación, debe usar un `NSFileCoordinator` para tener acceso al archivo. Llamaremos a la `WillMove(fromURL, toURL)` método en primer lugar, mueva el archivo a la nueva extensión y, a continuación, llamar a `ItemMoved(fromURL, toURL)`.
2. La aplicación tiene que abrir un archivo con una extensión principal y varios archivos auxiliares con diferentes extensiones. Por ejemplo, una película y un archivo de subtítulos. Use una `NSFilePresenter` para obtener acceso al archivo secundario. Proporcione el archivo principal para la `PrimaryPresentedItemURL` propiedad y el archivo secundario para el `PresentedItemURL` propiedad. Cuando se abre el archivo principal, llame a la `AddFilePresenter` método de la `NSFileCoordinator` clase para registrar el archivo secundario.

En del ambos escenarios, la aplicación **Info.plist** archivo debe declarar los tipos de documentos que puede abrir la aplicación. Para cualquier tipo de archivo, agregue el `NSIsRelatedItemType` (con un valor de `YES`) a su entrada en el `CFBundleDocumentTypes` matriz.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Abrir y guardar el comportamiento del cuadro de diálogo con las aplicaciones en un espacio aislado

Los límites siguientes se colocan en la `NSOpenPanel` y `NSSavePanel` al llamar a ellos desde una aplicación en espacio aislado de Xamarin.Mac:

- No se puede invocar mediante programación el **Aceptar** botón.
- No se puede modificar mediante programación la selección de un usuario en un `NSOpenSavePanelDelegate`.

Además, las siguientes modificaciones de herencia que están en su lugar:

- **Aplicación no en espacio aislado** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>Marcadores de ámbito de seguridad y acceso a los recursos persistentes

Como se mencionó anteriormente, una aplicación en espacio aislado Xamarin.Mac puede tener acceso a un archivo o recurso fuera de su contenedor por medio de la interacción del usuario directo (como se indica por PowerBox). Sin embargo, el acceso a estos recursos no automáticamente persisten en todas inicie aplicación o el sistema se reinicia.

Mediante el uso de _Security-Scoped marcadores_, puede conservar el intento del usuario y mantener el acceso debido a recursos después de una aplicación reiniciar una aplicación en espacio aislado Xamarin.Mac.

#### <a name="security-scoped-bookmark-types"></a>Tipos de marcador de ámbito de seguridad

Cuando se trabaja con marcadores de Security-Scoped y acceso a los recursos persistentes, hay dos casos de uso sistine:

- **Un marcador App-Scoped proporciona acceso persistente a un archivo especificado por el usuario o una carpeta.** 

    Por ejemplo, si la aplicación en espacio aislado de Xamarin.Mac permite utilizar para abrir un documento externo para su edición (mediante un `NSOpenPanel`), la aplicación puede crear un marcador de App-Scoped para que pueda acceder al mismo archivo en el futuro.
- **Un marcador de Document-Scoped proporciona un acceso persistente de documento específico a un archivo secundario.** 

Por ejemplo, una aplicación de edición de vídeo que crea un archivo de proyecto que tiene acceso a las imágenes individuales, clips de vídeo y archivos de sonido que se combinan en una sola película.

Cuando el usuario importa un archivo de recursos en el proyecto (a través de un `NSOpenPanel`), la aplicación crea un marcador de Document-Scoped para el elemento que está almacenado en el proyecto, por lo que el archivo esté siempre accesible para la aplicación.

Un marcador Document-Scoped pueden resolverse por cualquier aplicación que puede abrir los datos de marcador y el propio documento. Esto es compatible con la portabilidad, que permite al usuario enviar los archivos de proyecto a otro usuario y tener todos los marcadores de trabajo también para ellos.

> [!IMPORTANT]
> Un marcador de Document-Scoped puede _sólo_ que apunte a un único archivo y no una carpeta y que el archivo no puede estar en una ubicación utilizada por el sistema (como `/private` o `/Library`).

#### <a name="using-security-scoped-bookmarks"></a>Uso de marcadores de ámbito de seguridad

Con cualquier tipo de marcador Security-Scoped, requiere que realice los pasos siguientes:

1. **Establecer los derechos adecuados en la aplicación de Xamarin.Mac que debe usar marcadores de Security-Scoped** -For App-Scoped marcadores, establezca la `com.apple.security.files.bookmarks.app-scope` clave de derechos a `true`. Para los marcadores Document-Scoped, establezca el `com.apple.security.files.bookmarks.document-scope` clave de derechos a `true`.
2. **Crear un marcador de Security-Scoped** -llevará a cabo para cualquier archivo o carpeta que el usuario ha proporcionado acceso a (a través de `NSOpenPanel` por ejemplo), que la aplicación necesitará acceso persistente para. Use la `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` método de la `NSUrl` clase para crear el marcador.
3. **Resolver el marcador Security-Scoped** : cuando la aplicación necesita tener acceso al recurso nuevo (por ejemplo, después de reiniciar) deberá resolver el marcador a una dirección URL de ámbito de seguridad. Use la `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` método de la `NSUrl` clase para resolver el marcador.
4. **Notificar explícitamente al sistema que desea tener acceso al archivo de la dirección URL Security-Scoped** : este paso debe realizarse inmediatamente después de obtener la dirección URL de Security-Scoped anterior o, si posteriormente desea volver a obtener acceso al recurso si tiene renuncia del acceso a él. Llame a la `StartAccessingSecurityScopedResource ()` método de la `NSUrl` clase para iniciar el acceso a una dirección URL Security-Scoped.
5. **Notificar explícitamente al sistema que se realizan al acceder al archivo desde la dirección URL Security-Scoped** -tan pronto como sea posible, debe informar al sistema cuando la aplicación ya no necesita acceso al archivo (por ejemplo, si el usuario la cierra). Llame a la `StopAccessingSecurityScopedResource ()` método de la `NSUrl` clase deje de tener acceso a una dirección URL Security-Scoped.

Una vez ceder el acceso a un recurso, debe volver al paso 4 para volver a establecer el acceso. Si se reinicia la aplicación Xamarin.Mac, debe volver al paso 3 y volver a resolver el marcador.

> [!IMPORTANT]
> Error al liberar el acceso a recursos de direcciones URL Security-Scoped hará que una aplicación de Xamarin.Mac a la pérdida de recursos del núcleo. Como resultado, la aplicación ya no podrá agregar ubicaciones del sistema de archivos a su contenedor hasta que se reinicie.

### <a name="the-app-sandbox-and-code-signing"></a>El espacio aislado de la aplicación y la firma de código

Después de habilitar el espacio aislado de la aplicación y habilitar los requisitos específicos de la aplicación Xamarin.Mac (a través de derechos), se debe codificar el proyecto de inicio de sesión para el espacio aislado surta efecto. Debe realizar porque los derechos necesarios para el espacio aislado de la aplicación están relacionados con la firma de la aplicación de firma de código. 

macOS exige un vínculo entre el contenedor de una aplicación y la firma de código, de esta manera que ninguna otra aplicación puede tener acceso a ese contenedor, incluso si está suplantando a las aplicaciones de identificador de paquete. Este mecanismo funciona del siguiente modo:

1. Cuando el sistema crea el contenedor de la aplicación, establece un _lista de Control de acceso_ (ACL) en ese contenedor. La entrada de control de acceso inicial en la lista contiene la aplicación _requisito designado_ (DR), que describe cómo futuras versiones de la aplicación se pueda reconocer (cuando se ha actualizado).
2. Cada vez que se inicia una aplicación con el mismo identificador de lote, el sistema comprueba que firma de código de la aplicación cumple los requisitos de designado especificada en una de las entradas de la ACL del contenedor. Si el sistema no encuentra a una coincidencia, impide que la aplicación se ejecuten.

El código funciona de la firma de las maneras siguientes:

1. Antes de crear el proyecto Xamarin.Mac, obtenga un certificado de desarrollo, un certificado de distribución y un certificado de Id. de desarrollador desde el Portal para desarrolladores de Apple.
2. Cuando la tienda de aplicaciones Mac distribuye la aplicación Xamarin.Mac, está firmado con una firma de código de Apple.

Cuando las pruebas y depuración, usará una versión de la aplicación de Xamarin.Mac ha iniciado sesión (que se usará para crear el contenedor de la aplicación). Más adelante, si desea probar o instalar la versión de App Store de Apple, se firmarán con la firma de Apple y se producirá un error al iniciar (porque no tiene la misma firma de código que el contenedor de la aplicación original). En esta situación, obtendrá un informe de errores similares al siguiente:

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Para solucionar este problema, debe ajustar la entrada ACL para que señale a la versión firmada de Apple de la aplicación.

Para obtener más información sobre cómo crear y descargar los perfiles de aprovisionamiento necesarios para el espacio aislado, vea el [firma y el aprovisionamiento de la aplicación](#Signing_and_Provisioning_the_App) sección anterior.

#### <a name="adjusting-the-acl-entry"></a>Ajustar la entrada ACL

Para permitir que la versión firmada de Apple de la aplicación Xamarin.Mac para ejecutar, haga lo siguiente:

1. Abra la aplicación Terminal (en `/Applications/Utilities`).
2. Abra una ventana de herramienta de búsqueda para la versión firmada de Apple de la aplicación Xamarin.Mac.
3. Tipo de `asctl container acl add -file ` en la ventana de Terminal.
4. Arrastre el icono de la aplicación Xamarin.Mac desde la ventana de herramienta de búsqueda y colóquela en la ventana de Terminal.
5. La ruta de acceso completa al archivo se agregará al comando de Terminal.
6. Presione **ENTRAR** para ejecutar el comando.

La ACL del contenedor contiene ahora los requisitos de código designado para ambas versiones de la aplicación Xamarin.Mac y macOS ahora le permitirá cualquiera de las versiones para que se ejecute.

#### <a name="display-a-list-of-acl-code-requirements"></a>Mostrar una lista de requisitos de ACL de código

Puede ver una lista de los requisitos de código en la ACL del contenedor haciendo lo siguiente:

1. Abra la aplicación Terminal (en `/Applications/Utilities`).
2. Escriba `asctl container acl list -bundle <container-name>`.
3. Presione **ENTRAR** para ejecutar el comando.

El `<container-name>` es normalmente el identificador de paquete para la aplicación Xamarin.Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Diseñar una aplicación Xamarin.Mac para el espacio aislado de la aplicación

Hay un flujo de trabajo comunes que se debe seguir al diseñar una aplicación Xamarin.Mac para el espacio aislado de la aplicación. Es decir, los detalles de la implementación del espacio aislado en una aplicación va a ser único para la funcionalidad de la aplicación determinada.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Seis pasos para adoptar el espacio aislado de la aplicación

Diseñar una aplicación de Xamarin.Mac para el espacio aislado de la aplicación normalmente consta de los siguientes pasos:

1. Determinar si la aplicación es adecuada para el espacio aislado.
2. Diseñar una estrategia de desarrollo y la distribución.
3. Resolver las incompatibilidades de API.
4. Se aplican los derechos de espacio aislado de aplicación necesarios al proyecto Xamarin.Mac.
5. Agregue la separación de privilegios mediante XPC.
6. Implementar una estrategia de migración.

> [!IMPORTANT]
> Debe no solo espacio aislado del archivo ejecutable principal en el grupo de aplicaciones, pero también cada auxiliar incluye aplicación o herramienta en ese paquete. Esto es necesario para cualquier aplicación distribuida desde la tienda de aplicaciones Mac y, si es posible, se debe realizar para cualquier otra forma de distribución de aplicaciones.

Para obtener una lista de todos los archivos binarios del ejecutables en el paquete de la aplicación de Xamarin.Mac, escriba el comando siguiente en Terminal:

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

Donde `[Your-App-Bundle]` es el nombre y la ruta de acceso al paquete de la aplicación.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Determinar si una aplicación de Xamarin.Mac es adecuada para el espacio aislado

Mayoría de las aplicaciones Xamarin.Mac es totalmente compatible con el espacio aislado de la aplicación y, por tanto, adecuado para el espacio aislado. Si la aplicación requiere un comportamiento que no permite el espacio aislado de la aplicación, debería considerar una solución alternativa.

Si su aplicación requiere uno de los siguientes comportamientos, no es compatible con el espacio aislado de la aplicación:

- **Servicios de autorización** -con la aplicación de espacio aislado, no puede trabajar con las funciones descritas en [referencia de C de servicios de autorización](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **Accesibilidad API** -no espacio aislado aplicaciones asistencia como lectores de pantalla o que controlar a otras aplicaciones.
- **Enviar eventos de Apple a aplicaciones arbitrario** -si la aplicación requiere el envío de eventos de Apple a una aplicación desconocida, arbitraria, no puede estar en un espacio aislado. Para obtener una lista de aplicaciones llamadas conocida, la aplicación puede seguir siendo en recintos de seguridad y los derechos deberá incluir la lista de aplicaciones llamado.
- **Enviar diccionarios de información de usuario en las notificaciones distribuidas a otras tareas** -con la aplicación de espacio aislado, no puede incluir un `userInfo` diccionario al enviar a un `NSDistributedNotificationCenter` objeto para otras tareas de mensajería.
- **Cargar las extensiones de Kernel** -la carga de extensiones del Kernel está prohibida por el espacio aislado de la aplicación.
- **Simular la entrada de usuario en Abrir y guardar cuadros de diálogo** : mediante programación manipular abrir o guardar los cuadros de diálogo para simular o modificar proporcionados por el usuario está prohibido por el espacio aislado de la aplicación.
- **Al tener acceso a ni las preferencias de configuración en otras aplicaciones** -manipular la configuración de otras aplicaciones está prohibido por el espacio aislado de la aplicación.
- **Configuración de red** -manipular la configuración de red está prohibida por el espacio aislado de la aplicación.
- **Terminar otras aplicaciones** -espacio aislado aplicación prohíbe usar `NSRunningApplication` para terminar de otras aplicaciones.

### <a name="resolving-api-incompatibilities"></a>Resolver incompatibilidades de API

Al diseñar una aplicación Xamarin.Mac para el espacio aislado de la aplicación, puede encontrar incompatibilidades con el uso de algunas API de macOS.

Estos son algunos problemas comunes y las cosas que puede hacer para solucionarlos:

- **Abrir, guardar y realizar el seguimiento documentos** : si va a administrar documentos usando cualquier tecnología distinto de `NSDocument`, debe cambiar a él debido a la compatibilidad integrada para el espacio aislado de la aplicación. `NSDocument` funciona con PowerBox y proporciona compatibilidad para mantener los documentos incluidos en el espacio aislado si el usuario mueve a ellos en Finder automáticamente.
- **Mantener el acceso al archivo de recursos del sistema** : si la aplicación depende de acceso constante a los recursos fuera de su contenedor de Xamarin.Mac usar marcadores de ámbito de seguridad para mantener el acceso.
- **Crear un elemento de inicio de sesión para una aplicación** -con la aplicación de espacio aislado, no se puede crear un inicio de sesión con elemento `LSSharedFileList` ni puede manipular el estado de los servicios de inicio mediante `LSRegisterURL`. Use la `SMLoginItemSetEnabled` funciona como se describe en manzanas [agregar elementos de inicio de sesión mediante el marco de trabajo de administración de servicio](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) documentación.
- **Acceso a los datos de usuario** : si usa funciones POSIX como `getpwuid` para obtener el directorio principal del usuario de servicios de directorio, considere el uso de símbolos cacao o Core Foundation como `NSHomeDirectory`.
- **Obtener acceso a las preferencias de otras aplicaciones** : porque el espacio aislado de la aplicación dirige las API de búsqueda de ruta de acceso al contenedor de la aplicación, modificar las preferencias tiene lugar dentro de ese contenedor y tener acceso a otra de las preferencias de aplicaciones en no permitido. 
- **HTML5 incrustado vídeo sobre el uso de vistas Web** -si la aplicación Xamarin.Mac usa WebKit para reproducir vídeos insertados de HTML5, también debe vincular la aplicación con el marco de trabajo de AV Foundation. El espacio aislado de la aplicación impedirá CoreMedia play en estos vídeos en caso contrario.

### <a name="applying-required-app-sandbox-entitlements"></a>Aplicar requiere derechos de espacio aislado de la aplicación

Debe editar los derechos para cualquier aplicación de Xamarin.Mac que desea ejecutar en el espacio aislado de la aplicación y comprobar la **habilitar el espacio aislado de aplicación** casilla de verificación.

En función de la funcionalidad de la aplicación, deberá habilitar otros derechos tener acceso a características del sistema operativo o recursos. Por lo que la aplicación en espacio aislado funciona mejor cuando se minimiza los derechos que se solicita al mínimo necesario para ejecutar la aplicación simplemente aleatoriamente habilitar derechos.

Para determinar qué derechos requiere una aplicación Xamarin.Mac, realice lo siguiente:

1. Habilitar el espacio aislado de la aplicación y ejecutar la aplicación Xamarin.Mac.
2. Ejecutar a través de las características de la aplicación.
3. Abra la aplicación de consola (disponible en `/Applications/Utilities`) y busque `sandboxd` infracciones en la **todos los mensajes** registro.
4. Para cada `sandboxd` infracción, resuelva el problema mediante el contenedor de la aplicación en lugar de otras ubicaciones del sistema de archivos o aplicar derechos de espacio aislado de la aplicación para habilitar el acceso a características del sistema operativo restringidos.
5. Vuelva a ejecutar y probar todas las características de aplicaciones de Xamarin.Mac de nuevo.
6. Repita el proceso hasta que todos los `sandboxd` infracciones se han resueltos.

### <a name="add-privilege-separation-using-xpc"></a>Agregar la separación de privilegios mediante XPC

Al desarrollar una aplicación Xamarin.Mac para el espacio aislado de la aplicación, mire los comportamientos de la aplicación en los términos de privilegios y el acceso, a continuación, considere la posibilidad de separar las operaciones de alto riesgo en sus propios servicios XPC.

Para obtener más información, consulte de Apple [crear servicios de XPC](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) y [Daemons y Guía de programación de servicios](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i).

### <a name="implement-a-migration-strategy"></a>Implementar una estrategia de migración

Si está lanzando una nueva versión en recintos de seguridad de una aplicación de Xamarin.Mac que no estaba anteriormente en un espacio aislada, debe asegurarse de que los usuarios actuales tienen una ruta de actualización sin problemas. 

 Para obtener más información sobre cómo implementar un manifiesto de migración de contenedor, lea de Apple [migrar una aplicación a un espacio aislado](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) documentación.

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de espacio aislado de una aplicación Xamarin.Mac. En primer lugar, se crea una aplicación de Xamarin.Mac simplemente para mostrar los conceptos básicos del espacio aislado de aplicación. A continuación, se ha explicado cómo resolver las infracciones de espacio aislado. A continuación, utilizamos una profundidad mirar el espacio aislado de la aplicación y por último, analizamos diseñar una aplicación Xamarin.Mac para el espacio aislado de la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Publicación en App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [Acerca de espacio aislado de la aplicación](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problemas comunes de aplicación en espacio aislado](https://developer.apple.com/library/content/qa/qa1773/_index.html)
