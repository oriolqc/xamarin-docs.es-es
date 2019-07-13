---
title: Espacio aislado de una aplicación de Xamarin.Mac
description: Este artículo trata el espacio aislado de una aplicación de Xamarin.Mac para la versión en la aplicación de Store. Abarca todos los elementos que se incluyen en el espacio aislado, como directorios contenedores, los derechos, los permisos de usuario determinado, separación de privilegios y cumplimiento de kernel.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: e38ca07aeef1cbd8e121421ebcbad2207a1bb823
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865985"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Espacio aislado de una aplicación de Xamarin.Mac

_Este artículo trata el espacio aislado de una aplicación de Xamarin.Mac para la versión en la aplicación de Store. Abarca todos los elementos que se incluyen en el espacio aislado, como directorios contenedores, los derechos, los permisos de usuario determinado, separación de privilegios y cumplimiento de kernel._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, tiene la misma capacidad para una aplicación de espacio aislado como lo hace cuando se trabaja con Objective-C o Swift.

[![Un ejemplo de la aplicación en ejecución](sandboxing-images/intro01.png "un ejemplo de la aplicación en ejecución")](sandboxing-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos del uso de espacio aislado en una aplicación de Xamarin.Mac y todos los elementos que se incluyen en el espacio aislado: directorios contenedores, los derechos, los permisos de usuario determinado, separación de privilegios y cumplimiento de kernel. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como se tratan los conceptos clave y las técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documentar, también explica la `Register` y `Export` atributos se utiliza para conectar las clases de C# para objetos de Objective-C y la interfaz de usuario de elementos.

## <a name="about-the-app-sandbox"></a>Sobre el espacio aislado de la aplicación

El espacio aislado de la aplicación proporciona defensa seguro contra daño que puede deberse a una aplicación malintencionada que se va a ejecutar en un equipo Mac al limitar el acceso que tiene una aplicación a los recursos del sistema.

Una aplicación que no sean en espacio aislado tiene todos los derechos del usuario que se está ejecutando la aplicación y puede tener acceso a o todo lo que el usuario puede realizar hacer. Si la aplicación contiene vulnerabilidades de seguridad (o cualquier marco de trabajo que se está usando), un pirata informático podría aprovechar esos puntos débiles y usar la aplicación para tomar el control del equipo Mac que se ejecuta en.

Al limitar el acceso a los recursos por la aplicación, una aplicación en espacio aislado proporciona una línea de defensa contra el robo, daños o malas intenciones por parte de una aplicación que se ejecuta en el equipo del usuario.

El espacio aislado de la aplicación es una tecnología de control de acceso integrada en macOS (se aplica en el nivel de kernel) que proporciona una estrategia de doble:

1. El espacio aislado de la aplicación permite al desarrollador describir _cómo_ una aplicación interactúa con el sistema operativo y, de este modo, se concede sólo los derechos de acceso que son necesarios para realizar el trabajo y nada más.
2. El espacio aislado de la aplicación permite al usuario al conceder más acceso al sistema a través de la apertura sin problemas y guardar los cuadros de diálogo, arrastre y coloque las operaciones y las interacciones de usuario comunes y otros.

### <a name="preparing-to-implement-the-app-sandbox"></a>Preparación para implementar el espacio aislado de la aplicación

Los elementos del espacio aislado de aplicación que se tratarán en detalle en este artículo son los siguientes:

- Directorios de contenedor
- Derechos
- Permisos de usuario determinado
- Separación de privilegios
- Cumplimiento de kernel

Una vez que comprenda estos detalles, podrá crear un plan para adoptar el espacio aislado de la aplicación en la aplicación de Xamarin.Mac.

En primer lugar, deberá determinar si su aplicación es un buen candidato para el espacio aislado (la mayoría de las aplicaciones es). A continuación, deberá resolver las incompatibilidades de API y determinar qué elementos de espacio aislado de la aplicación requerirá. Por último, consideren el uso de separación de privilegios para maximizar el nivel de defensa de la aplicación.

Al adoptar el recinto de seguridad de la aplicación, algunas ubicaciones del sistema de archivos que usa la aplicación será diferentes. Especialmente, la aplicación tendrá un directorio de contenedor que se usará para los archivos de compatibilidad de aplicaciones, bases de datos, las memorias caché y otros archivos que no son documentos de usuario. MacOS y Xcode proporcionan soporte técnico para migrar estos tipos de archivos desde sus ubicaciones heredados al contenedor.

## <a name="sandboxing-quick-start"></a>Inicio rápido de espacio aislado

En esta sección, crearemos una sencilla aplicación de Xamarin.Mac que utiliza una vista Web (que requiere una conexión de red que está restringida en espacio aislado a menos que se le solicita específicamente) como un ejemplo de introducción con el espacio aislado de la aplicación.

Se deberá comprobar que la aplicación está realmente en espacio aislada y obtenga información sobre cómo localizar y solucionar errores comunes de espacio aislado de la aplicación.

### <a name="creating-the-xamarinmac-project"></a>Crear el proyecto de Xamarin.Mac

Vamos a hacer lo siguiente para crear nuestro proyecto de ejemplo:

1. Inicie Visual Studio para Mac y haga clic en el **nueva solución...** .
2. Desde el **nuevo proyecto** cuadro de diálogo, seleccione **Mac** > **aplicación** > **Cocoa App**: 

    [![Crear una nueva aplicación de Cocoa](sandboxing-images/sample01.png "creación de una aplicación Cocoa")](sandboxing-images/sample01-large.png#lightbox)
3. Haga clic en el **siguiente** botón, escriba `MacSandbox` para el nombre del proyecto y haga clic en el **crear** botón: 

    [![Escriba el nombre de la aplicación](sandboxing-images/sample02.png "escribir el nombre de la aplicación")](sandboxing-images/sample02-large.png#lightbox)
4. En el **panel de solución**, haga doble clic en el **Main.storyboard** archivo para abrirlo y editarlo en Xcode: 

    [![Editar el guión gráfico principal](sandboxing-images/sample03.png "el guión gráfico principal de edición")](sandboxing-images/sample03-large.png#lightbox)
5. Arrastre un **vista Web** en la ventana, cambiar su tamaño para rellenar el área de contenido y configúrelo para aumentar y reducir con la ventana: 

    [![Agregar una vista web](sandboxing-images/sample04.png "agregar una vista web")](sandboxing-images/sample04-large.png#lightbox)
6. Crear una salida para la vista web denominada `webView`: 

    [![Crear una nueva salida](sandboxing-images/sample05.png "crear una nueva salida")](sandboxing-images/sample05-large.png#lightbox)
7. Vuelva a Visual Studio para Mac y haga doble clic en el **ViewController.cs** de archivos en el **panel de solución** para abrirlo y editarlo.
8. Agregue la siguiente instrucción using: `using WebKit;`
9. Realizar el `ViewDidLoad` método aspecto parecido al siguiente: 

    ```csharp
    public override void AwakeFromNib ()
    {
        base.AwakeFromNib ();
        webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
    }
    ```

10. Guarde los cambios.

Ejecutar aplicación y asegurarse de que el sitio Web de Apple se muestra en la ventana como sigue:

[![Muestra un ejemplo de aplicación en ejecución](sandboxing-images/sample06.png "muestra un ejemplo de aplicación en ejecución")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>Firma y aprovisionamiento de la aplicación

Antes de que podemos habilitar el espacio aislado de la aplicación, primero es necesario aprovisionar e iniciar sesión a nuestra aplicación de Xamarin.Mac.

Permiten hacer lo siguiente:

1. Inicie sesión en el Portal para desarrolladores de Apple: 

    [![Iniciar sesión en el Portal para desarrolladores de Apple](sandboxing-images/sign01.png "iniciar sesión en el Portal para desarrolladores de Apple")](sandboxing-images/sign01-large.png#lightbox)
2. Seleccione **certificados, identificadores y perfiles**: 

    [![Seleccionar certificados, identificadores y perfiles](sandboxing-images/sign02.png "Seleccionar certificados, identificadores y perfiles")](sandboxing-images/sign02-large.png#lightbox)
3. En **aplicaciones de Mac**, seleccione **identificadores**: 

    [![Seleccionar identificadores](sandboxing-images/sign03.png "seleccionar identificadores")](sandboxing-images/sign03-large.png#lightbox)
4. Cree un nuevo identificador para la aplicación: 

    [![Crear un nuevo identificador de aplicación](sandboxing-images/sign04.png "crear un nuevo identificador de aplicación")](sandboxing-images/sign04-large.png#lightbox)
5. En **perfiles de aprovisionamiento**, seleccione **desarrollo**: 

    [![Selección de desarrollo](sandboxing-images/sign05.png "selección de desarrollo")](sandboxing-images/sign05-large.png#lightbox)
6. Crear un nuevo perfil y seleccione **desarrollo de aplicaciones de Mac**: 

    [![Crear un nuevo perfil](sandboxing-images/sign06.png "crear un nuevo perfil")](sandboxing-images/sign06-large.png#lightbox)
7. Seleccione el identificador de aplicación que hemos creado anteriormente: 

    [![Seleccionar el identificador de aplicación](sandboxing-images/sign07.png "selección del identificador de aplicación")](sandboxing-images/sign07-large.png#lightbox)
8. Seleccione los desarrolladores para este perfil: 

    [![Los desarrolladores agregar](sandboxing-images/sign08.png "agregar a los desarrolladores")](sandboxing-images/sign08-large.png#lightbox)
9. Seleccione los equipos para este perfil: 

    [![Seleccionar los equipos permitidos](sandboxing-images/sign09.png "seleccionar los equipos permitidos")](sandboxing-images/sign09-large.png#lightbox)
10. Asigne un nombre de perfil: 

    [![Asigne un nombre de perfil](sandboxing-images/sign10.png "dar un nombre al perfil")](sandboxing-images/sign10-large.png#lightbox)
11. Haga clic en el **realiza** botón.

> [!IMPORTANT]
> En algunos casos es posible que deba descargar directamente el nuevo perfil de aprovisionamiento desde el Portal para desarrolladores de Apple y haga doble clic en él para instalarlo. También es posible que deba detener y reiniciar Visual Studio para Mac, podrá tener acceso al nuevo perfil.

A continuación, necesitamos el nuevo Id. de aplicación y perfil de carga en el equipo de desarrollo. Vamos a hacer lo siguiente:

1. Iniciar Xcode y seleccione **preferencias** desde el **Xcode** menú: 

    ![Editar cuentas en Xcode](sandboxing-images/sign11.png "editar cuentas en Xcode")
2. Haga clic en el **ver detalles...**  botón: 

    ![Al hacer clic en el botón Ver detalles](sandboxing-images/sign12.png "al hacer clic en el botón Ver detalles")
3. Haga clic en el **actualizar** botón (en la esquina inferior izquierda).
4. Haga clic en el **realiza** botón.

A continuación, se debe seleccionar el nuevo Id. de aplicación y el perfil de aprovisionamiento en nuestro proyecto de Xamarin.Mac. Vamos a hacer lo siguiente:

1. En el **panel de solución**, haga doble clic en el **Info.plist** archivo para abrirlo y editarlo.
2. Asegúrese de que el **identificador de paquete** coincide con el identificador de aplicación creados anteriormente (ejemplo: `com.appracatappra.MacSandbox`): 

    [![Editar el identificador de paquete](sandboxing-images/sign13.png "editar el identificador de paquete")](sandboxing-images/sign13-large.png#lightbox)
3. A continuación, haga doble clic en el **Entitlements.plist** de archivos y asegúrese de nuestro **Store pares clave-valor de iCloud** y **contenedores de iCloud** todos coinciden con el identificador de aplicación creados anteriormente (ejemplo: `com.appracatappra.MacSandbox`): 

    [![Editar el archivo Entitlements.plist](sandboxing-images/sign17.png "editando el archivo Entitlements.plist")](sandboxing-images/sign17-large.png#lightbox)
4. Guarde los cambios.
5. En el **panel de solución**, haga doble clic en el archivo de proyecto para abrir sus opciones de edición:  

    ![Editign opciones de la solución](sandboxing-images/sign14.png "Editign opciones de la solución")
6. Seleccione **firma de Mac**, a continuación, compruebe **firmar el lote de aplicación** y **firmar el paquete de instalador**. En **perfil de aprovisionamiento**, seleccione lo que hemos creado anteriormente: 

    ![Establecer el perfil de aprovisionamiento](sandboxing-images/sign15.png "establecer el perfil de aprovisionamiento")
7. Haga clic en el **realiza** botón.

> [!IMPORTANT]
> Es posible que deba salir y reiniciar Visual Studio para Mac para obtenerla para reconocer el nuevo Id. de aplicación y el perfil de aprovisionamiento que se instaló con Xcode.

#### <a name="troubleshooting-provisioning-issues"></a>Solución de problemas de aprovisionamiento

En este momento debe intentar ejecutar la aplicación y asegúrese de que todo el contenido y se haya creado correctamente. Si la aplicación sigue ejecutándose como antes, todo está bien. Si se produce un error, puede que reciba un cuadro de diálogo como el siguiente:

[![Un ejemplo de cuadro de diálogo de problema aprovisionamiento](sandboxing-images/sign16.png "muestra un ejemplo de aprovisionamiento en el cuadro de diálogo de problema")](sandboxing-images/sign16-large.png#lightbox)

Estas son las causas más comunes de aprovisionamiento y firma problemas:

- El identificador de paquete de aplicación no coincide con el identificador de aplicación del perfil seleccionado.
- El identificador de desarrollador no coincide con el identificador de desarrollador del perfil seleccionado.
- El UUID del equipo Mac que se está probando en no está registrado como parte del perfil seleccionado.

En el caso de un problema, corrija el problema en el Portal para desarrolladores de Apple, actualice los perfiles en Xcode y realizar una compilación limpia en Visual Studio para Mac.

### <a name="enable-the-app-sandbox"></a>Habilitar el espacio aislado de la aplicación

Habilitar el espacio aislado de la aplicación seleccionando una casilla en las opciones de proyectos. Haga lo siguiente:

1. En el **panel de solución**, haga doble clic en el **Entitlements.plist** archivo para abrirlo y editarlo.
2. Comprobar ambos **habilitar derechos** y **habilitar el espacio aislado de la aplicación**: 

    [![Edición de derechos y habilitar el espacio aislado](sandboxing-images/sign17.png "los derechos de edición y habilitar el espacio aislado")](sandboxing-images/sign17-large.png#lightbox)
3. Guarde los cambios.

En este momento, ha habilitado el recinto de seguridad de la aplicación, pero no ha proporcionado el acceso de red necesarios para la vista Web. Si ejecuta la aplicación ahora, debería obtener una ventana en blanco:

[![Que se muestra el acceso web bloqueando](sandboxing-images/sample08.png "que se muestra el acceso web está bloqueado")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Comprobando que la aplicación en espacio aislado

Aparte del comportamiento de bloqueo de recurso, hay tres maneras principales para indicar si una aplicación de Xamarin.Mac ha sido correctamente en espacio aislada:

1. En el Finder, compruebe el contenido de la `~/Library/Containers/` carpeta - si la aplicación está en un espacio aislado, habrá una carpeta denominada como identificador de paquete de la aplicación (ejemplo: `com.appracatappra.MacSandbox`): 

    [![Abrir el paquete de la aplicación](sandboxing-images/sample09.png "Abrir paquete de la aplicación")](sandboxing-images/sample09-large.png#lightbox)
2. El sistema considera la aplicación en espacio aislado en el Monitor de actividad:
    - Iniciar el Monitor de actividad (en `/Applications/Utilities`). 
    - Elija **vista** > **columnas** y asegúrese de que el **Sandbox** elemento de menú está activado.
    - Asegúrese de que la columna de espacio aislado lee `Yes` para la aplicación: 

    [![Comprobación de la aplicación en el Monitor de actividad](sandboxing-images/sample10.png "comprobación de la aplicación en el Monitor de actividad")](sandboxing-images/sample10-large.png#lightbox)
3. Compruebe que la aplicación binaria es en espacio aislado:
    - Inicie la aplicación Terminal.
    - Vaya a las aplicaciones `bin` directory.
    - Use este comando: `codesign -dvvv --entitlements :- executable_path` (donde `executable_path` es la ruta de acceso a la aplicación): 

    [![Comprobación de la aplicación en la línea de comandos](sandboxing-images/sample11.png "comprobación de la aplicación en la línea de comandos")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Depurar una aplicación en espacio aislado

El depurador se conecta a las aplicaciones de Xamarin.Mac a través de TCP, lo que significa que de forma predeterminada cuando se habilita el espacio aislado, no se puede conectar a la aplicación, por lo que si se intenta ejecutar la aplicación sin los permisos adecuados habilitados, se produce un error *"no se puede conectar a el depurador"* . 

[![Establecer las opciones necesarias](sandboxing-images/debug01.png "establecer las opciones necesarias")](sandboxing-images/debug01-large.png#lightbox)

El **permitir conexiones de red salientes (cliente)** permiso es necesario para que el depurador, este se habilita la depuración con normalidad. Puesto que no se puede depurar sin él, hemos actualizado la `CompileEntitlements` de destino para `msbuild` para agregar automáticamente ese permiso a los derechos para cualquier aplicación que está aislada para la depuración solo compilaciones. Las compilaciones de versión deben usar los derechos especificados en el archivo de derechos, sin modificar.

### <a name="resolving-an-app-sandbox-violation"></a>Resolver una infracción de espacio aislado de la aplicación

Si una aplicación intentó obtener acceso a un recurso que tiene de Xamarin.Mac en espacio aislado no se permite explícitamente, se produce una infracción de espacio aislado de la aplicación. Por ejemplo, nuestra vista Web ya no ser capaz de mostrar el sitio Web de Apple.

El origen más común de infracciones de espacio aislado de la aplicación se produce cuando la configuración de derechos especificada en Visual Studio para Mac no coinciden con los requisitos de la aplicación. Nuevo, la copia en nuestro ejemplo, la conexión de red que faltan los derechos que mantienen la vista Web de trabajo.

#### <a name="discovering-app-sandbox-violations"></a>Detección de infracciones de espacio aislado de la aplicación

Si sospecha una infracción de espacio aislado de la aplicación se está produciendo en la aplicación de Xamarin.Mac, la forma más rápida para detectar el problema es mediante la **consola** app.

Haga lo siguiente:

1. Compilar la aplicación en cuestión y ejecútelo desde Visual Studio para Mac.
2. Abra el **consola** aplicación (desde `/Applications/Utilties/`).
3. Seleccione **todos los mensajes** en la barra lateral y escriba `sandbox` en la búsqueda: 

    [![Un ejemplo de un problema de espacio aislado en la consola de](sandboxing-images/resolve01.png "un ejemplo de un problema de espacio aislado en la consola")](sandboxing-images/resolve01-large.png#lightbox)

Para nuestra aplicación de ejemplo anterior, puede ver que está bloqueando la Kernal la `network-outbound` tráfico gracias al espacio aislado de aplicación, ya que no hemos solicitado ese derecho.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Corregir las infracciones de derechos de App Sandbox

Ahora que hemos visto cómo buscar infracciones de espacio aislado de aplicación, vamos a ver cómo pueden resolverse mediante el ajuste de los derechos de la aplicación.

Haga lo siguiente:

1. En el **panel de solución**, haga doble clic en el **Entitlements.plist** archivo para abrirlo y editarlo.
2. En el **derechos** sección, compruebe el **permitir conexiones de red salientes (cliente)** casilla de verificación: 

    [![Edición de los derechos](sandboxing-images/sign17.png "los derechos de edición")](sandboxing-images/sign17-large.png#lightbox)
3. Guarde los cambios en la aplicación.

Si, a continuación, se lo anterior para nuestra aplicación de ejemplo de compilación y lo ejecuta, el contenido web ahora se muestran según lo esperado.

## <a name="the-app-sandbox-in-depth"></a>El espacio aislado de aplicación detallados

Los mecanismos de control de acceso proporcionados por el espacio aislado de la aplicación son pocos y fácil de entender. Sin embargo, que se adoptarán el recinto de seguridad de la aplicación por cada aplicación de manera es único y se basa en los requisitos de la aplicación.

Dada su mejor esfuerzo para proteger la aplicación de Xamarin.Mac quedarían por código malintencionado, debe haber solo una sola vulnerabilidad en la aplicación (o una de las bibliotecas o marcos de trabajo consume) para obtener el control de las interacciones de la aplicación con el sistema.

El espacio aislado de la aplicación se diseñó para evitar que la adquisición de (o limitar el daño que puede causar), ya que permite especificar las interacciones previsto de la aplicación con el sistema. El sistema sólo concederá acceso a los recursos que requiere la aplicación para realizar su trabajo y nada más.

Al diseñar el recinto de seguridad de la aplicación, va a diseñar un escenario pesimista. Si la aplicación se ven comprometida por código malintencionado, se limita al acceso a solo los archivos y recursos en espacio aislado de la aplicación.

### <a name="entitlements-and-system-resource-access"></a>Los derechos y acceso a los recursos del sistema

Como hemos visto anteriormente, una aplicación de Xamarin.Mac que no haya espacio aislada se concede los derechos completos y el acceso del usuario que se está ejecutando la aplicación. Si se pone en peligro por código malintencionado, una aplicación protegida no puede actuar como un agente para el comportamiento hostil, con una amplia que abarcan potencial para causar daño.

Al habilitar el espacio aislado de la aplicación, eliminarlos todos excepto un conjunto mínimo de privilegios, que luego volver a habilitar de forma solo necesidad de uso de los derechos de la aplicación de Xamarin.Mac. 

Modificar recursos de espacio aislado de la aplicación de la aplicación mediante la edición de su **Entitlements.plist** archivo y comprobación o seleccionar los derechos necesarios en los cuadros de lista desplegable de editores:

[![Edición de los derechos](sandboxing-images/sign17.png "los derechos de edición")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>Acceso al sistema de archivos y directorios de contenedor

Cuando la aplicación de Xamarin.Mac adopta el recinto de seguridad de la aplicación, tiene acceso a las siguientes ubicaciones:

- **El directorio de aplicación de contenedor** -en la primera ejecución, el sistema operativo crea un especial _Directory contenedor_ donde todos sus recursos vaya, que solo puede tener acceso. La aplicación tendrá acceso de lectura y escritura a este directorio.
- **Directorios de contenedor de grupo de aplicación** -la aplicación se puede conceder acceso a uno o varios _contenedores grupales_ que se comparten entre las aplicaciones en el mismo grupo.
- **Los archivos especificados por el usuario** -automáticamente, la aplicación obtiene acceso a los archivos que están explícitamente abierto o arrastrar y colocar en la aplicación por el usuario.
- **Elementos relacionados** -con los derechos adecuados, la aplicación puede tener acceso a un archivo con el mismo nombre pero con una extensión distinta. Por ejemplo, un documento que se ha guardado como un `.txt` archivo y un `.pdf`.
- **Directorios temporales, la herramienta de línea de comandos y determinadas ubicaciones de lectura** -la aplicación tiene varios grados de acceso a archivos en otras ubicaciones bien definidos según lo especificado por el sistema.

#### <a name="the-app-container-directory"></a>El directorio de contenedor de aplicaciones

Directorio de contenedor de aplicación de la aplicación de Xamarin.Mac tiene las siguientes características:

- Se encuentra en una ubicación oculta en el directorio del usuario principal (normalmente `~Library/Containers`) y puede obtenerse con el `NSHomeDirectory` función (ver abajo) dentro de la aplicación. Porque está en el directorio particular, cada usuario obtendrá su propio contenedor para la aplicación.
- La aplicación tiene acceso de lectura/escritura sin restricciones en el directorio de contenedor y todos sus subdirectorios y archivos dentro de él.
- La mayoría de las API de búsqueda de ruta de acceso de macOS son con respecto al contenedor de la aplicación. Por ejemplo, el contenedor tiene su propio **biblioteca** (acceso a través de `NSLibraryDirectory`), **compatibilidad con aplicaciones** y **preferencias** subdirectorios.
- macOS establece y aplica la conexión entre una aplicación y su contenedor mediante la firma de código. Incluso es otra aplicación intenta suplantar la identidad de la aplicación mediante el uso de su **identificador de paquete**, no podrá tener acceso al contenedor debido a la firma de código.
- El contenedor no es para los archivos generados por el usuario. En su lugar es para los archivos que usa la aplicación, como bases de datos, las memorias caché u otros tipos de datos específicos.
- Para _shoebox_ tipos de aplicaciones (por ejemplo, la aplicación de fotografía de Apple), que irá contenido del usuario en el contenedor.

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

Comenzando por el equipo Mac, macOS 10.7.5 (y superior), una aplicación puede utilizar el `com.apple.security.application-groups` derecho a acceder a un contenedor compartido que es común a todas las aplicaciones en el grupo. Puede usar este contenedor compartido para que no es de usuario contenido con orientación como base de datos u otros tipos de archivos de soporte técnico (por ejemplo, las memorias caché).

Los contenedores del grupo se agregan automáticamente al contenedor de recinto de seguridad de cada aplicación (si son parte de un grupo) y se almacenan en `~/Library/Group Containers/<application-group-id>`. El identificador de grupo _debe_ comienzan con el identificador del equipo de desarrollo y un período, por ejemplo:

```plist
<team-id>.com.company.<group-name>
```

Para obtener más información, consulte Apple [agregar una aplicación a un grupo de aplicaciones](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) en [referencia principal de los derechos](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195).

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Acceso de sistema Powerbox y el archivo fuera del contenedor de aplicación

Una aplicación de Xamarin.Mac en espacio aislado puede tener acceso a ubicaciones de archivo del sistema fuera de su contenedor de las maneras siguientes:

- En la dirección específica del usuario (a través de abrir y guardar cuadros de diálogo u otros métodos, como arrastrar y colocar).
- Mediante el uso de los derechos para las ubicaciones de archivo específico del sistema (como `/bin` o `/usr/lib`).
* Cuando la ubicación del sistema de archivos está en ciertos directorios que son legible (por ejemplo, recursos compartidos) del mundo.

_Powerbox_ es tecnología de seguridad que interactúa con el usuario para expandir los derechos de acceso del archivo de la aplicación de Xamarin.Mac en espacio aislado de macOS. Powerbox no tiene ninguna API, pero está activado de forma transparente cuando la aplicación llama a un `NSOpenPanel` o `NSSavePanel`. Acceso Powerbox se habilita a través de los derechos que establezca para la aplicación de Xamarin.Mac.

Cuando una aplicación en espacio aislado se muestra una apertura o cuadro de diálogo Guardar, la ventana presenta Powerbox (y no AppKit) y, por tanto, tiene acceso a cualquier archivo o directorio que el usuario tiene acceso.

Cuando un usuario selecciona un archivo o directorio de la apertura o cuadro de diálogo Guardar (o arrastre en el icono de aplicación), Powerbox agrega la ruta de acceso asociado al espacio aislado de la aplicación.

Además, el sistema permite automáticamente las siguientes acciones para una aplicación en espacio aislado:

- Conexión a un método de entrada del sistema.
- Llamar a los servicios seleccionados por el usuario desde el **servicios** menú (solo para los servicios marcan como _seguro para aplicaciones de espacio aislado_ por el proveedor de servicios).
- Elija los archivos abiertos por el usuario desde el **Abrir recientes** menú.
- Usar copiar y pegar entre otras aplicaciones.
- Leer archivos de las siguientes ubicaciones del mundo natural:
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- Leer y escribir archivos en los directorios creados por `NSTemporaryDirectory`.

De forma predeterminada los archivos que se abrió o guardó por una aplicación de Xamarin.Mac en espacio aislado estando accesibles hasta que finaliza la aplicación (a menos que el archivo todavía estaba abierto cuando se cierra la aplicación). Los archivos abiertos se restaurará automáticamente para el espacio aislado de la aplicación a través de la función de reanudación de macOS la próxima vez que se inicia la aplicación.

Para proporcionar persistencia a archivos ubicados fuera del contenedor de una App Xamarin.Mac, utilice marcadores de ámbito de seguridad (ver abajo).

#### <a name="related-items"></a>Elementos relacionados

El espacio aislado de la aplicación permite que una aplicación tener acceso a los elementos relacionados que tienen el mismo nombre de archivo, pero diferentes extensiones. La característica tiene dos partes: (a) una lista de Extensiones relacionadas en la aplicación `Info.plst` archivo, (b) el código para indicar el espacio aislado de lo que la aplicación harán con estos archivos.

Hay dos escenarios donde esto tiene sentido:

1. La aplicación debe ser capaz de guardar una versión diferente del archivo (con una nueva extensión). Por ejemplo, exportar un `.txt` del archivo a un `.pdf` archivo. Para controlar esta situación, debe usar un `NSFileCoordinator` para tener acceso al archivo. Llamaremos el `WillMove(fromURL, toURL)` método en primer lugar, mueva el archivo a la nueva extensión y, a continuación, llamar a `ItemMoved(fromURL, toURL)`.
2. La aplicación debe abrir un archivo con una extensión principal y varios archivos auxiliares con extensiones distintas. Por ejemplo, una película y un archivo de subtítulos. Use un `NSFilePresenter` para tener acceso al archivo secundario. Proporcione el archivo principal para el `PrimaryPresentedItemURL` propiedad y el archivo secundario para el `PresentedItemURL` propiedad. Cuando se abre el archivo principal, llame a la `AddFilePresenter` método de la `NSFileCoordinator` clase para registrar el archivo secundario.

En del ambos escenarios, la aplicación **Info.plist** archivo debe declarar los tipos de documento que se puede abrir la aplicación. Para cualquier tipo de archivo, agregue el `NSIsRelatedItemType` (con un valor de `YES`) a su entrada en el `CFBundleDocumentTypes` matriz.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Abrir y guardar el comportamiento del cuadro de diálogo con las aplicaciones en espacio aislado

Los límites siguientes se colocan en el `NSOpenPanel` y `NSSavePanel` al llamar a ellos desde una aplicación de Xamarin.Mac en espacio aislado:

- No se puede invocar mediante programación el **Aceptar** botón.
- No se puede cambiar mediante programación la selección de un usuario en un `NSOpenSavePanelDelegate`.

Además, las siguientes modificaciones de herencia son en su lugar:

- **Aplicación que no sean en espacio aislado** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>Marcadores de ámbito de seguridad y acceso a los recursos persistentes

Como se indicó anteriormente, una aplicación de Xamarin.Mac en espacio aislado puede tener acceso a un archivo o recurso fuera de su contenedor por medio de la interacción del usuario directo (como se indica por PowerBox). Sin embargo, el acceso a estos recursos no conservar automáticamente entre los lanzamientos de la aplicación o sistema se reinicia.

Mediante el uso de _Security-Scoped marcadores_, puede conservar la intención del usuario y mantener el acceso a dados los recursos de una aplicación después de reiniciar una aplicación de Xamarin.Mac en espacio aislado.

#### <a name="security-scoped-bookmark-types"></a>Tipos de marcador de ámbito de seguridad

Cuando se trabaja con Security-Scoped marcadores y acceso a los recursos persistentes, hay dos casos de uso sistine:

- **Un marcador App-Scoped proporciona acceso persistente a un archivo especificado por el usuario o una carpeta.** 

    Por ejemplo, si la aplicación de Xamarin.Mac en espacio aislado permite utilizar para abrir un documento externo para su edición (mediante un `NSOpenPanel`), la aplicación puede crear un marcador App-Scoped para que puede acceder al mismo archivo en el futuro.
- **Un marcador Document-Scoped proporciona un acceso persistente de documento específico en un archivo secundario.** 

Por ejemplo, una aplicación de edición de vídeo que crea un archivo de proyecto que tiene acceso a las imágenes individuales, clips de vídeo y archivos de sonido que se combina en una película única.

Cuando el usuario importa un archivo de recursos en el proyecto (a través de un `NSOpenPanel`), la aplicación crea un marcador Document-Scoped para el elemento que se almacena en el proyecto, para que el archivo esté siempre accesible para la aplicación.

Un marcador Document-Scoped pueden resolverse por cualquier aplicación que puede abrir los datos de marcador y el propio documento. Esto es compatible con la portabilidad, lo que permite al usuario enviar los archivos de proyecto a otro usuario y tener todos los marcadores valerles también.

> [!IMPORTANT]
> Un marcador Document-Scoped puede _sólo_ que apunte a un único archivo y no una carpeta y archivo no puede estar en una ubicación utilizada por el sistema (como `/private` o `/Library`).

#### <a name="using-security-scoped-bookmarks"></a>Uso de marcadores de ámbito de seguridad

Con cualquier tipo de marcador Security-Scoped, requiere que realice los pasos siguientes:

1. **Establecer los derechos adecuados en la aplicación de Xamarin.Mac que deba utilizar marcadores Security-Scoped** -For App-Scoped marcadores, establezca el `com.apple.security.files.bookmarks.app-scope` clave derechos a `true`. Los marcadores Document-Scoped, establezca el `com.apple.security.files.bookmarks.document-scope` clave derechos a `true`.
2. **Crear un marcador Security-Scoped** -deberá hacerlo para cualquier archivo o carpeta que el usuario ha proporcionado acceso a (a través de `NSOpenPanel` por ejemplo), que la aplicación necesitará acceso persistente a. Use la `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` método de la `NSUrl` clase para crear el marcador.
3. **Resolver el marcador Security-Scoped** : cuando la aplicación necesita tener acceso al recurso nuevo (por ejemplo, después de reiniciar) deberá resolver el marcador a una dirección URL con ámbito de seguridad. Use la `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` método de la `NSUrl` clase para resolver el marcador.
4. **Notificar explícitamente al sistema que se desea tener acceso al archivo de la dirección URL de Security-Scoped** : este paso debe realizarse inmediatamente después de obtener la dirección URL de Security-Scoped anterior o, si más adelante desea volver a obtener acceso al recurso después de tener abandonar el acceso a él. Llame a la `StartAccessingSecurityScopedResource ()` método de la `NSUrl` clase para iniciar el acceso a una dirección URL de Security-Scoped.
5. **Notificar al sistema que haya terminado de explícitamente acceso al archivo de la dirección URL de Security-Scoped** -tan pronto como sea posible, debe informar al sistema cuando la aplicación ya no necesita acceso al archivo (por ejemplo, si el usuario lo cierra). Llame a la `StopAccessingSecurityScopedResource ()` método de la `NSUrl` clase para detener el acceso a una dirección URL de Security-Scoped.

Después de que el usuario perderá el acceso a un recurso, deberá volver al paso 4 para volver a establecer el acceso. Si se reinicia la aplicación de Xamarin.Mac, debe volver al paso 3 y volver a resolver el marcador.

> [!IMPORTANT]
> Error al liberar el acceso a recursos de direcciones URL Security-Scoped hará que una aplicación de Xamarin.Mac a perder los recursos del núcleo. Como resultado, la aplicación ya no podrá agregar ubicaciones del sistema de archivos a su contenedor hasta que se reinicie.

### <a name="the-app-sandbox-and-code-signing"></a>El espacio aislado de la aplicación y la firma de código

Después de habilitar el espacio aislado de la aplicación y habilitar los requisitos específicos para la aplicación de Xamarin.Mac (a través de los derechos), se debe codificar el proyecto de inicio de sesión para el espacio aislado surta efecto. Debe realizar porque los derechos necesarios para el espacio aislado de la aplicación están vinculados a la firma de la aplicación de la firma de código. 

macOS exige un vínculo entre contenedor la aplicación en un y su firma de código, de esta manera ninguna otra aplicación puede tener acceso a ese contenedor, incluso si está suplantando a las aplicaciones de identificador de paquete. Este mecanismo funciona del siguiente modo:

1. Cuando el sistema crea el contenedor de la aplicación, Establece una _lista de Control de acceso_ (ACL) en ese contenedor. La entrada de control de acceso inicial en la lista contiene la aplicación _designado requisito_ (DR), se puede reconocer que describe cómo futuras versiones de la aplicación (cuando se ha actualizado).
2. Cada vez que inicia una aplicación con el mismo identificador de paquete, el sistema comprueba que firma de código de la aplicación cumple los requisitos de designado especificada en una de las entradas de la ACL del contenedor. Si el sistema no encuentra a una coincidencia, impide que la aplicación se ejecuten.

El código funciona de la firma de las maneras siguientes:

1. Antes de crear el proyecto de Xamarin.Mac, obtener un certificado de desarrollo, un certificado de distribución y un certificado de Id. de desarrollador, desde el Portal para desarrolladores de Apple.
2. Cuando el Store de la aplicación Mac distribuye la aplicación de Xamarin.Mac, está firmado con una firma de código de Apple.

Cuando las pruebas y depuración, usará una versión de la aplicación de Xamarin.Mac que una sesión iniciada (que se usará para crear el contenedor de la aplicación). Más adelante, si desea probar o instale la versión desde el Store de la aplicación de Apple, se firmarán con la firma de Apple e iniciará (puesto que no tiene la misma firma de código como el contenedor original de la aplicación). En este caso, obtendrá un informe de bloqueo similar al siguiente:

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Para solucionar este problema, deberá ajustar la entrada ACL para que apunte a la versión firmada de Apple de la aplicación.

Para obtener más información sobre cómo crear y descargar los perfiles de aprovisionamiento necesarios para el espacio aislado, consulte el [firma y aprovisionamiento de la aplicación](#Signing_and_Provisioning_the_App) sección anterior.

#### <a name="adjusting-the-acl-entry"></a>Ajuste de la entrada ACL

Para permitir que la versión firmada de Apple de la aplicación de Xamarin.Mac se ejecute, realice lo siguiente:

1. Abra la aplicación Terminal (en `/Applications/Utilities`).
2. Abra una ventana de Finder para la versión firmada de Apple de la aplicación de Xamarin.Mac.
3. Tipo `asctl container acl add -file` en la ventana de Terminal.
4. Arrastre el icono de la aplicación de Xamarin.Mac desde la ventana de Finder y colóquela en la ventana de Terminal.
5. La ruta de acceso completa al archivo se agregará al comando en Terminal.
6. Presione **ENTRAR** para ejecutar el comando.

Ahora, la ACL del contenedor contiene los requisitos de código designado para ambas versiones de la aplicación de Xamarin.Mac y macOS ahora permitirá que cualquier versión que se ejecutará.

#### <a name="display-a-list-of-acl-code-requirements"></a>Mostrar una lista de los requisitos de código ACL

Puede ver una lista de los requisitos de código en la ACL de un contenedor mediante los pasos siguientes:

1. Abra la aplicación Terminal (en `/Applications/Utilities`).
2. Escriba `asctl container acl list -bundle <container-name>`.
3. Presione **ENTRAR** para ejecutar el comando.

El `<container-name>` suele ser el identificador de paquete para la aplicación de Xamarin.Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Diseñar una aplicación de Xamarin.Mac para el espacio aislado de la aplicación

Hay un flujo de trabajo comunes que se debe seguir al diseñar una aplicación de Xamarin.Mac para el espacio aislado de la aplicación. Es decir, los detalles de la implementación de espacio aislado en una aplicación va a ser único a la funcionalidad de la aplicación especificada.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Seis pasos para adoptar el recinto de seguridad de la aplicación

Diseñar una aplicación de Xamarin.Mac para el espacio aislado de la aplicación normalmente consta de los pasos siguientes:

1. Determinar si la aplicación es adecuada para el espacio aislado.
2. Diseñar una estrategia de desarrollo y distribución.
3. Resolver las incompatibilidades de API.
4. Se aplican los derechos de espacio aislado de aplicación necesarios al proyecto de Xamarin.Mac.
5. Agregue la separación de privilegios mediante XPC.
6. Implementar una estrategia de migración.

> [!IMPORTANT]
> Debe sandbox no solo el archivo ejecutable principal en el grupo de aplicaciones, pero también cada auxiliar incluye aplicación o herramienta en ese paquete. Esto es necesario para cualquier aplicación distribuida desde el Store de la aplicación de Mac y, si es posible, se debe realizar para cualquier otra forma de distribución de aplicaciones.

Para obtener una lista de todos los archivos binarios ejecutables en el paquete de una aplicación de Xamarin.Mac, escriba el comando siguiente en Terminal:

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

Donde `[Your-App-Bundle]` es el nombre y ruta de acceso al paquete de la aplicación.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Determinar si una aplicación de Xamarin.Mac es adecuada para el espacio aislado

La mayoría de las aplicaciones de Xamarin.Mac son totalmente compatibles con el espacio aislado de la aplicación y, por lo tanto, adecuado para el espacio aislado. Si la aplicación requiere un comportamiento que no permite el espacio aislado de la aplicación, debe considerar un enfoque alternativo.

Si la aplicación requiere uno de los comportamientos siguientes, es incompatible con el espacio aislado de la aplicación:

- **Servicios de autorización** -con la aplicación de espacio aislado, no puede trabajar con las funciones descritas en [referencia de C de servicios de autorización](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **API de accesibilidad** -no sandbox aplicaciones de asistencia como lectores de pantalla o que otras aplicaciones de control.
- **Enviar eventos de Apple para aplicaciones arbitrarias** -si la aplicación requiere el envío de eventos de Apple para una aplicación arbitraria, desconocida, no puede estar en un espacio aislado. Para obtener una lista conocida de aplicaciones llamadas, la aplicación puede estar todavía en espacio aislado y los derechos deberá incluir la lista de la aplicación de llamada.
- **Enviar información de diccionarios de usuario en las notificaciones distribuidas a otras tareas** -con la aplicación de espacio aislado, no puede incluir un `userInfo` diccionario al registrar un `NSDistributedNotificationCenter` objeto para otras tareas de mensajería.
- **Cargar las extensiones de Kernel** -la carga de extensiones del Kernel está prohibida por el espacio aislado de la aplicación.
- **Simulación de entrada de usuario en los cuadros de diálogo Guardar y abrir** : mediante programación manipular abrir o guardar los cuadros de diálogo para simular o modificar la entrada del usuario está prohibido por el espacio aislado de la aplicación.
- **Preferencias de configuración en otras aplicaciones o de acceso a** -manipular la configuración de otras aplicaciones prohibida por el espacio aislado de la aplicación.
- **Configuración de red** -manipular la configuración de red está prohibida por el espacio aislado de la aplicación.
- **Finalización de otras aplicaciones** -The App Sandbox prohíbe el uso de `NSRunningApplication` para terminar de otras aplicaciones.

### <a name="resolving-api-incompatibilities"></a>Resolver las incompatibilidades de API

Al diseñar una aplicación de Xamarin.Mac para el espacio aislado de la aplicación, puede encontrar incompatibilidades con el uso de algunas API de macOS.

Estos son algunos problemas comunes y las cosas que puede hacer para resolverlos:

- **Abrir, guardar y seguimiento de documentos** : si va a administrar documentos con cualquier tecnología que no sea `NSDocument`, debe cambiar a él debido a la compatibilidad integrada para el espacio aislado de la aplicación. `NSDocument` funciona con PowerBox y proporciona compatibilidad para mantener documentos dentro de su espacio aislado si el usuario mueve en Finder automáticamente.
- **Conservar el acceso a los recursos del sistema de archivos** : si la aplicación depende de acceso persistente a recursos fuera de su contenedor, de Xamarin.Mac usar marcadores de ámbito de seguridad para mantener el acceso.
- **Crear un elemento de inicio de sesión para una aplicación** -con la aplicación de espacio aislado, no se puede crear un inicio de sesión mediante elementos `LSSharedFileList` ni se puede manipular el estado de los servicios de inicio mediante `LSRegisterURL`. Use la `SMLoginItemSetEnabled` funcione como se describe en manzanas [agregar elementos de inicio de sesión mediante el marco de administración de servicio](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) documentación.
- **Acceso a los datos de usuario** : si usa funciones POSIX como `getpwuid` para obtener el directorio principal del usuario de servicios de directorio, considere el uso de símbolos de cacao o Core Foundation como `NSHomeDirectory`.
- **Obtener acceso a las preferencias de otras aplicaciones** : porque el espacio aislado de la aplicación dirige las API de búsqueda de ruta de acceso al contenedor de la aplicación, modificar las preferencias tiene lugar dentro de ese contenedor y tener acceso a otra de las preferencias de aplicaciones en no permitido. 
- **Con vídeo incrustado de HTML5 en las vistas Web** -si la aplicación de Xamarin.Mac usa WebKit para reproducir vídeos insertados de HTML5, también debe vincular la aplicación con el marco de AV Foundation. El espacio aislado de la aplicación evitará CoreMedia play estos vídeos en caso contrario.

### <a name="applying-required-app-sandbox-entitlements"></a>Aplicar los derechos necesarios de espacio aislado de la aplicación

Deberá editar los derechos para cualquier aplicación de Xamarin.Mac que desea ejecutar en el recinto de seguridad de la aplicación y compruebe el **habilitar espacio aislado de aplicación** casilla de verificación.

Según la funcionalidad de la aplicación, es posible que deba habilitar otros derechos de acceso a características del sistema operativo y los recursos. Espacio aislado de la aplicación funciona mejor al minimizar los derechos que solicita al mínimo necesario para ejecutar la aplicación por lo que simplemente aleatoriamente habilitar los derechos.

Para determinar qué derechos requiere una aplicación de Xamarin.Mac, realice lo siguiente:

1. Habilitar el espacio aislado de la aplicación y ejecute la aplicación de Xamarin.Mac.
2. Ejecutar a través de las características de la aplicación.
3. Abra la aplicación de consola (disponible en `/Applications/Utilities`) y busque `sandboxd` las infracciones en la **todos los mensajes** registro.
4. Para cada `sandboxd` infracción, resuelva el problema mediante el contenedor de la aplicación en lugar de otras ubicaciones del sistema de archivos o aplicar derechos de espacio aislado de la aplicación para habilitar el acceso a características del sistema operativo restringidos.
5. Vuelva a ejecutar y probar de nuevo todas las características de aplicación de Xamarin.Mac.
6. Repita hasta que todos `sandboxd` infracciones que se han resueltos.

### <a name="add-privilege-separation-using-xpc"></a>Agregar la separación de privilegios mediante XPC

Al desarrollar una aplicación de Xamarin.Mac para el espacio aislado de la aplicación, examine los comportamientos de la aplicación en los términos de privilegios y el acceso, a continuación, considere la posibilidad de separar las operaciones de alto riesgo en sus propios servicios XPC.

Para obtener más información, consulte Apple [crear XPC servicios](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) y [Guía de programación de servicios y los demonios](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i).

### <a name="implement-a-migration-strategy"></a>Implementar una estrategia de migración

Si está lanzando una nueva versión de una aplicación de Xamarin.Mac que no estaba anteriormente en espacio aislada en un espacio aislado, deberá asegurarse de que los usuarios actuales tienen una ruta de actualización sin problemas. 

 Para obtener más información sobre cómo implementar un manifiesto de la migración de contenedor, lea de Apple [migrar una aplicación a un espacio aislado](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) documentación.

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de recinto de seguridad de una aplicación de Xamarin.Mac. En primer lugar, hemos creado una aplicación de Xamarin.Mac simplemente para mostrar los conceptos básicos de espacio aislado de la aplicación. A continuación, mostramos cómo resolver las infracciones de espacio aislado. A continuación, utilizamos una profundidad mirar el espacio aislado de la aplicación y por último, examinamos en diseñar una aplicación de Xamarin.Mac para el espacio aislado de la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Publicación en App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [Acerca de espacio aislado de la aplicación](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problemas comunes de espacio aislado de la aplicación](https://developer.apple.com/library/content/qa/qa1773/_index.html)
