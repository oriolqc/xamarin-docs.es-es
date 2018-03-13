---
title: Implementar SiriKit
description: "Este artículo tratan los pasos necesarios para implementar la compatibilidad de SiriKit en las aplicaciones de Xamarin.iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 0e271fb78cfd225f9ccdae9a515685e89bfd7ac2
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="implementing-sirikit"></a>Implementar SiriKit

_Este artículo tratan los pasos necesarios para implementar la compatibilidad de SiriKit en las aplicaciones de Xamarin.iOS._



Nuevo en 10 de iOS, SiriKit permite que una aplicación de Xamarin.iOS proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas en un dispositivo iOS. Este artículo tratan los pasos necesarios para implementar la compatibilidad de SiriKit en las aplicaciones de Xamarin.iOS agregando las extensiones de calidades requiere las extensiones de interfaz de usuario de intentos y vocabulario.

Siri funciona con el concepto de **dominios**, grupos de conocer las acciones para las tareas relacionadas. Cada interacción con la aplicación con Siri debe estar en uno de sus dominios de servicio conocido, como se indica a continuación:

- Una llamada de vídeo o audio.
- Transporte de reserva.
- Administrar entrenamientos.
- Mensajería.
- Buscar fotografías.
- Enviar o recibir pagos.

Cuando el usuario realiza una solicitud de Siri que implican a uno de los servicios de la extensión de la aplicación, SiriKit envía la extensión de un **intención** objeto que describe la solicitud del usuario junto con cualquier dato de apoyo. La extensión de la aplicación, a continuación, genera la correspondiente **respuesta** de objeto para el determinado **intención**, que detalla cómo la extensión puede atender la solicitud.

Esta guía presentará un ejemplo rápido de incluida la compatibilidad con SiriKit en una aplicación existente. Para este ejemplo, usaremos la aplicación MonkeyChat falsa:

[![](implementing-sirikit-images/monkeychat01.png "El icono de MonkeyChat")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene su propio libro contacto de amigos del usuario, cada uno asociado con un nombre de pantalla (por ejemplo, Bobo por ejemplo) y permite al usuario enviar chats de texto a cada uno de ellos por su nombre de pantalla.

## <a name="extending-the-app-with-sirikit"></a>Extender la aplicación con SiriKit

Como se muestra en el [SiriKit de descripción de conceptos](~/ios/platform/sirikit/understanding-sirikit.md) guía, hay tres partes principales implicadas en la ampliación de una aplicación con SiriKit:

[![](implementing-sirikit-images/elements01.png "Extender la aplicación con el diagrama de SiriKit")](implementing-sirikit-images/elements01.png#lightbox)

Se incluyen los siguientes:

1. **Extensión de calidades** -comprueba las respuestas de los usuarios, confirma que la aplicación pueda controlar la solicitud y realiza la tarea para satisfacer la solicitud del usuario.
2. **Extensión de la interfaz de usuario de calidades** - *opcional*, proporciona una interfaz de usuario personalizada para las respuestas en el entorno de Siri y puede hacer que las aplicaciones de interfaz de usuario y personalización de marca en Siri para enriquecer la experiencia del usuario.
3. **Aplicación** -proporciona la aplicación con vocabularios específicos del usuario para ayudar a Siri trabajar con él. 

Todos estos elementos y los pasos para incluirlas en la aplicación se explicará en detalle en las secciones siguientes.


## <a name="preparing-the-app"></a>Preparación de la aplicación

SiriKit se basa en las extensiones, sin embargo, antes de agregar todas las extensiones de la aplicación, hay algunas cosas que el desarrollador debe hacer para ayudar a la adopción de SiriKit.

### <a name="moving-common-shared-code"></a>Mover código común compartido 

En primer lugar, el desarrollador puede mover parte del código común que se van a compartir entre la aplicación y las extensiones a cualquiera _proyectos compartidos_, _bibliotecas de clases portables (PCLs)_ o _nativo Bibliotecas de_.

Las extensiones deberá ser capaz de hacer todo lo que hace la aplicación. En los términos de la aplicación de ejemplo MonkeyChat, cosas como buscar contactos, agregar nuevos contactos, enviar mensajes y recuperar el historial de mensajes.

Moviendo este código común en un proyecto compartido, PCL o biblioteca nativa se facilita el proceso mantener ese código en un lugar común y se asegura de que la extensión y la aplicación primaria proporcionan funcionalidad y experiencias uniformes para el usuario.

En el caso de la aplicación de ejemplo MonkeyChat, los modelos de datos y el código de procesamiento, como el acceso de red y la base de datos se moverán a una biblioteca nativa.

Haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie Visual Studio para Mac y abra la aplicación MonkeyChat.
2. Haga doble clic en el nombre de la solución en el **solución Pad** y seleccione **agregar** > **nuevo proyecto...** : 

    [![](implementing-sirikit-images/prep01.png "Agregue un nuevo proyecto")](implementing-sirikit-images/prep01.png#lightbox)
3. Seleccione **iOS** > **biblioteca** > **biblioteca de clases** y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/prep02.png "Seleccione la biblioteca de clases")](implementing-sirikit-images/prep02.png#lightbox)
4. Escriba `MonkeyChatCommon` para el **nombre** y haga clic en el **crear** botón: 

    [![](implementing-sirikit-images/prep03.png "Escriba MonkeyChatCommon para el nombre")](implementing-sirikit-images/prep03.png#lightbox)
5. Haga doble clic en el **referencias** carpeta de la aplicación principal en la **el Explorador de soluciones** y seleccione **editar referencias...** . Compruebe el **MonkeyChatCommon** del proyecto y haga clic en el **Aceptar** botón: 

    [![](implementing-sirikit-images/prep05.png "Proteger el proyecto MonkeyChatCommon")](implementing-sirikit-images/prep05.png#lightbox)
6. En el **el Explorador de soluciones**, arrastre el código común compartido desde la aplicación principal para la biblioteca nativa.
7. En el caso de MonkeyChat, arrastre el **DataModels** y **procesadores** carpetas de la aplicación principal en la biblioteca nativa: 

    [![](implementing-sirikit-images/prep06.png "Las carpetas DataModels y los procesadores en el Explorador de soluciones")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie Visual Studio y abra la aplicación MonkeyChat.
2. Haga doble clic en el nombre de la solución en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo proyecto...** .
3. Seleccione **Visual C#** > **proyecto compartido** y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/prep02w.png "Seleccione la biblioteca de clases")](implementing-sirikit-images/prep02w.png#lightbox)
4. Escriba `MonkeyChatCommon` para el **nombre** y haga clic en el **crear** botón.
5. Haga doble clic en el **referencias** carpeta de la aplicación principal en la **el Explorador de soluciones** y seleccione **editar referencias...** . Compruebe el **MonkeyChatCommon** del proyecto y haga clic en el **Aceptar** botón: 

    [![](implementing-sirikit-images/prep05w.png "Proteger el proyecto MonkeyChatCommon")](implementing-sirikit-images/prep05w.png#lightbox)
6. En el **el Explorador de soluciones**, arrastre el código común compartido desde la aplicación principal para el proyecto compartido.
7. En el caso de MonkeyChat, arrastre el **DataModels** y **procesadores** carpetas de la aplicación principal en la biblioteca nativa.

-----

Modifique cualquiera de los archivos que se movieron a la biblioteca nativa y cambie el espacio de nombres para que coincida con el de la biblioteca. Por ejemplo, cambiar `MonkeyChat` a `MonkeyChatCommon`:

```csharp
using System;
namespace MonkeyChatCommon
{
    /// <summary>
    /// A message sent from one user to another within a conversation.
    /// </summary>
    public class MonkeyMessage
    {
        public MonkeyMessage ()
        {
        }
        ...
    }
}
```

A continuación, vuelva a la aplicación principal y agregar un `using` declaración de espacio de nombres de la biblioteca nativa en cualquier lugar la aplicación usa una de las clases que se han movido:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using MonkeyChatCommon;

namespace MonkeyChat
{
    public partial class MasterViewController : UITableViewController
    {
        public DetailViewController DetailViewController { get; set; }

        DataSource dataSource;
        ...
    }
}
```

### <a name="architecting-the-app-for-extensions"></a>Diseñar la aplicación para las extensiones

Normalmente, una aplicación se registra para varias calidades y el desarrollador debe asegurarse de que la aplicación se ha diseñado para el número apropiado de intención de extensiones.

En el caso de que una aplicación requiere más de un intento, el programador tiene la opción de todos los de su propio control de intención colocar en una extensión de intención o crear una extensión de intención independiente para cada intento.

Si decide crear una extensión de intención independiente para cada intento, el programador podría terminar duplicar una gran cantidad de código reutilizable en cada extensión y crear una gran cantidad de procesador y la sobrecarga de memoria.

Con el fin de elegir entre las dos opciones, vea si cualquiera de las calidades de forma natural forman un conjunto. Por ejemplo, una aplicación que realiza llamadas de audio y vídeo podría desear incluir tanto los intentos en una única extensión de intención como control de tareas similares y podría proporcionar la mayoría de reutilización de código.

Para cualquier intención o grupo de calidades que no quepan en un grupo existente, cree una nueva extensión de intención de solución de la aplicación para contenerlos.


### <a name="setting-the-required-entitlements"></a>Establecer los derechos necesarios

Cualquier aplicación Xamarin.iOS que incluye la integración de SiriKit, deben tener los derechos correctos establecida. Si el desarrollador no establecer correctamente estos derechos necesarios, no podrá instalar o probar la aplicación en hardware de iOS real 10 (o posterior), que también es necesarios desde el 10 de iOS Simulator no es compatible con SiriKit.

Haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga doble clic en el `Entitlements.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Cambie a la **origen** ficha.
3. Agregar el `com.apple.developer.siri` **propiedad**, establezca el **tipo** a `Boolean` y **valor** a `Yes`: 

    [![](implementing-sirikit-images/setup01.png "Agregue la propiedad com.apple.developer.siri")](implementing-sirikit-images/setup01.png#lightbox)
4. Guarde los cambios en el archivo.
5. Haga doble clic en el **archivo de proyecto** en el **el Explorador de soluciones** para abrirlo y editarlo.
6. Seleccione **agrupación de firma de iOS** y asegúrese de que el `Entitlements.plist` archivo está seleccionado en el **personalizado derechos** campo: 

    [![](implementing-sirikit-images/setup02.png "Seleccione el archivo Entitlements.plist en el campo de derechos personalizada")](implementing-sirikit-images/setup02.png#lightbox)
7. Haga clic en el botón **Aceptar** para guardar los cambios.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga doble clic en el `Entitlements.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
3. Agregar el `com.apple.developer.siri` **propiedad**, establezca el **tipo** a `Boolean` y **valor** a `Yes`: 

    [![](implementing-sirikit-images/setup01w.png "Agregue la propiedad com.apple.developer.siri")](implementing-sirikit-images/setup01w.png#lightbox)
4. Guarde los cambios en el archivo.
5. Haga doble clic en el **archivo de proyecto** en el **el Explorador de soluciones** para abrirlo y editarlo.
6. Seleccione **agrupación de firma de iOS** y asegúrese de que el `Entitlements.plist` archivo está seleccionado en el **personalizado derechos** campo.

-----

Cuando termine, la aplicación `Entitlements.plist` archivo debe ser similar al siguiente (en abierto en un editor externo):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.siri</key>
    <true/>
</dict>
</plist>
```

### <a name="correctly-provisioning-the-app"></a>El aprovisionamiento correctamente la aplicación

Debido a la seguridad estricta que Apple ha colocado en el marco de trabajo SiriKit, cualquier aplicación Xamarin.iOS que implementa SiriKit _debe_ tienen el identificador de la aplicación y derechos (vea la sección anterior) correcto y debe estar firmado con un correcto Perfil de aprovisionamiento.

Haga lo siguiente en el equipo Mac:

1. En un explorador web, vaya a [http://developer.apple.com](http://developer.apple.com) e inician sesión en su cuenta.
2. Haga clic en **certificados**, **identificadores** y **perfiles**.
3. Seleccione **perfiles de aprovisionamiento** y seleccione **Id. de aplicaciones**, a continuación, haga clic en el  **+**  botón.
4. Escriba un **nombre** para el nuevo perfil.
5. Escriba un **Id. del lote** después de Apple de nomenclatura de recomendación.
6. Desplácese hacia abajo hasta la **servicios de aplicaciones** sección, seleccione **SiriKit** y haga clic en el **continuar** botón: 

    [![](implementing-sirikit-images/setup03.png "Seleccione SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Compruebe todas las opciones, a continuación, **enviar** el ID. App
8. Seleccione **perfiles de aprovisionamiento** > **desarrollo**, haga clic en el  **+**  botón, seleccione la **ID de Apple**, a continuación, haga clic en **continuar**.
9. Haga clic en seleccionar **todos los**, a continuación, haga clic en **continuar**.
10. Haga clic en **seleccionar todo** , a continuación, haga clic en el nuevo, **continuar**.
11. Escriba un **nombre de perfil** usando Apple nombres sugerencias, a continuación, haga clic en **continuar**.
12. Inicie Xcode.
13. En el menú de Xcode seleccione **preferencias...**
14. Seleccione **cuentas**, a continuación, haga clic en el **ver detalles...** botón: 

    [![](implementing-sirikit-images/setup04.png "Seleccione las cuentas")](implementing-sirikit-images/setup04.png#lightbox)
15. Haga clic en el **descargar todos los perfiles** situado en la esquina izquierda inferior: 

    [![](implementing-sirikit-images/setup05.png "Descargar todos los perfiles")](implementing-sirikit-images/setup05.png#lightbox)
16. Asegúrese de que el **perfil de aprovisionamiento** creado anteriormente se ha instalado en Xcode.
17. Abra el proyecto para agregar compatibilidad de SiriKit a en Visual Studio para Mac.
18. Haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones**.
18. Asegúrese de que el **identificador de paquete** coincide con la creada en el Portal para desarrolladores de Apple anterior: 

    [![](implementing-sirikit-images/setup06.png "El identificador de paquete")](implementing-sirikit-images/setup06.png#lightbox)
18. En el **el Explorador de soluciones**, seleccione la **proyecto**.
19. Haga clic en el proyecto y seleccione **opciones**.
21. Seleccione **agrupación de firma de iOS**, seleccione la **identidad de firma** y **perfil de aprovisionamiento** creado anteriormente: 

    [![](implementing-sirikit-images/setup07.png "Seleccione la identidad de firma y perfil de aprovisionamiento")](implementing-sirikit-images/setup07.png#lightbox)
22. Haga clic en el botón **Aceptar** para guardar los cambios.

> [!IMPORTANT]
> **Nota:** SiriKit de pruebas solo funciona en un iOS real 10 dispositivos de Hardware y no en el archivo iOS 10 simulador. Si tiene problemas para instalar un SiriKit habilitado Xamarin.iOS aplicación en hardware real, asegúrese de que los derechos necesarios, Id. de aplicación, identificador de firma y perfil de aprovisionamiento se han configurado correctamente en de Apple Portal para desarrolladores y Visual Studio para Mac.

### <a name="requesting-siri-authorization"></a>Solicite la autorización de Siri

Antes de la aplicación agrega cualquier vocabulario específico de usuario o las extensiones de intentos se conecta a Siri, debe solicita autorización del usuario para tener acceso a Siri.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Editar la aplicación `Info.plist` de archivos, cambie a la **origen** ver y agregar la `NSSiriUsageDescription` clave con un valor de cadena que describe cómo la aplicación usará Siri y qué tipos de datos se enviará. Por ejemplo, la aplicación MonkeyChat podría ser "MonkeyChat contactos se enviarán a Siri":

[![](implementing-sirikit-images/request01.png "El NSSiriUsageDescription en el editor de Info.plist")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Editar la aplicación `Info.plist` de archivos y agregar el `NSSiriUsageDescription` clave con un valor de cadena que describe cómo la aplicación usará Siri y qué tipos de datos se enviará. Por ejemplo, la aplicación MonkeyChat podría ser "MonkeyChat contactos se enviarán a Siri":

[![](implementing-sirikit-images/request01w.png "El NSSiriUsageDescription en el editor de Info.plist")](implementing-sirikit-images/request01w.png#lightbox)

-----

Llame a la `RequestSiriAuthorization` método de la `INPreferences` clase cuando se inicia la aplicación por primera vez. Editar la `AppDelegate.cs` clase y hacer el `FinishedLaunching` método aspecto similar al siguiente:


```csharp
using Intents;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{

    // Request access to Siri
    INPreferences.RequestSiriAuthorization ((INSiriAuthorizationStatus status) => {
        // Respond to returned status
        switch (status) {
        case INSiriAuthorizationStatus.Authorized:
            break;
        case INSiriAuthorizationStatus.Denied:
            break;
        case INSiriAuthorizationStatus.NotDetermined:
            break;
        case INSiriAuthorizationStatus.Restricted:
            break;
        }
    });


    return true;
}
```

La primera vez que se llama a este método, se muestra una alerta solicitando al usuario que permite que la aplicación tener acceso a Siri. El mensaje que el desarrollador agrega a la `NSSiriUsageDescription` anteriormente se mostrarán en esta alerta. Si el usuario inicialmente deniega el acceso, puede usar el **configuración** aplicación para conceder acceso a la aplicación.

En cualquier momento, la aplicación puede comprobar la capacidad de la aplicación para tener acceso a Siri mediante una llamada a la `SiriAuthorizationStatus` método de la `INPreferences` clase.

### <a name="localization-and-siri"></a>Cuestiones de localización y Siri

En un dispositivo iOS, el usuario tiene permiso seleccionar un idioma para Siri que es diferente, a continuación, el valor predeterminado del sistema. Cuando se trabaja con datos localizados, la aplicación tendrá que utilizar el `SiriLanguageCode` método de la `INPreferences` clase para obtener el código de idioma de Siri. Por ejemplo:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Agregar vocabulario específico de usuario

El vocabulario específica del usuario se va a proporcionar palabras o frases que son únicos a usuarios individuales de la aplicación. Estos se proporcionarán en tiempo de ejecución de la aplicación principal (no en las extensiones de aplicación) como un conjunto ordenado de términos, ordenados en una prioridad de uso más importante para los usuarios, con los términos más importantes al principio de la lista.

Vocabulario específico de usuario debe pertenecer a una de las siguientes categorías:

- Póngase en contacto con nombres (que no están administrados por el marco de trabajo de contactos).
- Etiquetas de fotos.
- Nombres de álbumes de fotos.
- Nombres de entrenamiento.

Al seleccionar la terminología para registrar como vocabulario personalizado, elija solo términos que podrían malinterpretarse por alguien que no esté familiarizado con la aplicación. Nunca registrar los términos comunes como "Mi entrenamiento" o ""Álbum Mi". Por ejemplo, la aplicación MonkeyChat registrará los sobrenombres asociados con cada contacto en la libreta de direcciones del usuario.

La aplicación proporciona el vocabulario específico de usuario mediante una llamada a la `SetVocabularyStrings` método de la `INVocabulary` clase y pasar un `NSOrderedSet` colección desde la aplicación principal. Siempre debe llamar la aplicación la `RemoveAllVocabularyStrings` método primero, que se va a quitar los términos existentes antes de agregar otras nuevas. Por ejemplo:

```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using Foundation;
using Intents;

namespace MonkeyChatCommon
{
    public class MonkeyAddressBook : NSObject
    {
        #region Computed Properties
        public List<MonkeyContact> Contacts { get; set; } = new List<MonkeyContact> ();
        #endregion

        #region Constructors
        public MonkeyAddressBook ()
        {
        }
        #endregion

        #region Public Methods
        public NSOrderedSet<NSString> ContactNicknames ()
        {
            var nicknames = new NSMutableOrderedSet<NSString> ();

            // Sort contacts by the last time used
            var query = Contacts.OrderBy (contact => contact.LastCalledOn);

            // Assemble ordered list of nicknames by most used to least
            foreach (MonkeyContact contact in query) {
                nicknames.Add (new NSString (contact.ScreenName));
            }

            // Return names
            return new NSOrderedSet<NSString> (nicknames.AsSet ());
        }

        // This method MUST only be called on a background thread!
        public void UpdateUserSpecificVocabulary ()
        {
            // Clear any existing vocabulary
            INVocabulary.SharedVocabulary.RemoveAllVocabularyStrings ();

            // Register new vocabulary
            INVocabulary.SharedVocabulary.SetVocabularyStrings (ContactNicknames (), INVocabularyStringType.ContactName);
        }
        #endregion
    }
}
```

Con este código en su lugar, se podría denominarse como sigue:

```csharp
using System;
using System.Threading;
using UIKit;
using MonkeyChatCommon;
using Intents;

namespace MonkeyChat
{
    public partial class ViewController : UIViewController
    {
        #region AppDelegate Access
        public AppDelegate ThisApp {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do we have access to Siri?
            if (INPreferences.SiriAuthorizationStatus == INSiriAuthorizationStatus.Authorized) {
                // Yes, update Siri's vocabulary
                new Thread (() => {
                    Thread.CurrentThread.IsBackground = true;
                    ThisApp.AddressBook.UpdateUserSpecificVocabulary ();
                }).Start ();
            }
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

> [!IMPORTANT]
> **Nota:** Siri trata vocabulario personalizado como sugerencias y se incorporarán como gran parte de la terminología como sea posible. Sin embargo, espacio vocabulario personalizado se limita lo que es preciso registrar _sólo_ la terminología que puede resultar confusa, por lo tanto, manteniendo el número total de términos registrados al mínimo.

Para obtener más información, vea nuestra [referencia de usuario específica vocabulario](~/ios/platform/sirikit/understanding-sirikit.md) y de Apple [especificar personalizado vocabulario referencia](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

### <a name="adding-app-specific-vocabulary"></a>Agregar vocabulario específica de la aplicación

El vocabulario específico de la aplicación define las palabras y frases específicas que se conocerá a todos los usuarios de la aplicación, como tipos de vehículos o nombres de entrenamiento. Dado que estos son parte de la aplicación, se definen en un `AppIntentVocabulary.plist` archivo como parte de la agrupación de la aplicación principal. Además, se deben localizar estas palabras y frases.

Los términos del vocabulario específica de la aplicación deben pertenecer a una de las siguientes categorías:

- Invalidar las opciones.
- Nombres de entrenamiento.

El archivo de vocabulario específico de aplicación contiene dos claves de nivel de raíz:

- `ParameterVocabularies` **Necesario** -define términos personalizados y los parámetros de intención se aplican a la aplicación.
- `IntentPhrases` **Opcional** -contiene frases de ejemplo con las condiciones personalizadas definidas en el `ParameterVocabularies`.

Cada entrada en el `ParameterVocabularies` debe especificar una cadena de identificación, término y la intención de que el término se aplica a. Además, un único término podría aplicar a varios intentos.

Para obtener una lista completa de los valores aceptables y estructura de los archivos necesarios, vea de Apple [referencia del formato de archivo de aplicación vocabulario](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

Para agregar un `AppIntentVocabulary.plist` al proyecto de la aplicación, haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga clic en el nombre del proyecto en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo archivo...**   >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "Agregar una lista de propiedades")](implementing-sirikit-images/plist01.png#lightbox) 
2. Haga doble clic en el `AppIntentVocabulary.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
3. Haga clic en el  **+**  para agregar una clave, establezca el **nombre** a `ParameterVocabularies` y **tipo** a `Array`:

    [![](implementing-sirikit-images/plist02.png "Establezca el nombre en ParameterVocabularies y el tipo de matriz")](implementing-sirikit-images/plist02.png#lightbox)
4. Expanda `ParameterVocabularies` y haga clic en el  **+**  botón y establezca la **tipo** a `Dictionary`:

    [![](implementing-sirikit-images/plist03.png "Establezca el tipo en el diccionario")](implementing-sirikit-images/plist03.png#lightbox)
5. Haga clic en el  **+**  para agregar una nueva clave, establezca el **nombre** a `ParameterNames` y **tipo** a `Array`:

    [![](implementing-sirikit-images/plist04.png "Establezca el nombre en ParameterNames y el tipo de matriz")](implementing-sirikit-images/plist04.png#lightbox)
6. Haga clic en el  **+**  para agregar una nueva clave con el **tipo** de `String` y el valor como uno de los nombres de parámetro disponibles. Por ejemplo, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "La clave de INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05.png#lightbox)
7. Agregar el `ParameterVocabulary` clave para la `ParameterVocabularies` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist06.png "Agregue la clave de ParameterVocabulary a la clave ParameterVocabularies con el tipo de matriz")](implementing-sirikit-images/plist06.png#lightbox)
8. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist07.png#lightbox)
9. Agregar el `VocabularyItemIdentifier` clave con el **tipo** de `String` y especifique un identificador único para el término:

    [![](implementing-sirikit-images/plist08.png "Agregue la clave VocabularyItemIdentifier con el tipo de cadena y especifique un identificador único")](implementing-sirikit-images/plist08.png#lightbox)
10. Agregar el `VocabularyItemSynonyms` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist09.png "Agregue la clave VocabularyItemSynonyms con el tipo de matriz")](implementing-sirikit-images/plist09.png#lightbox)
11. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist10.png#lightbox)
12. Agregar el `VocabularyItemPhrase` clave con el **tipo** de `String` y el término que se define la aplicación:

    [![](implementing-sirikit-images/plist11.png "Agregue la clave VocabularyItemPhrase con el tipo de cadena y el término que se está definiendo la aplicación")](implementing-sirikit-images/plist11.png#lightbox)
13. Agregar el `VocabularyItemPronunciation` clave con el **tipo** de `String` y la pronunciación fonética del término:

    [![](implementing-sirikit-images/plist12.png "Agregue la clave VocabularyItemPronunciation con el tipo de cadena y la pronunciación fonética del término")](implementing-sirikit-images/plist12.png#lightbox)
14. Agregar el `VocabularyItemExamples` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist13.png "Agregue la clave VocabularyItemExamples con el tipo de matriz")](implementing-sirikit-images/plist13.png#lightbox)
15. Agregue algunos `String` claves con los usos de ejemplo del término:

    [![](implementing-sirikit-images/plist14.png "Agregar algunas claves de cadena con los usos de ejemplo del término")](implementing-sirikit-images/plist14.png#lightbox)
16. Repita los pasos anteriores para cualquier otros términos personalizados debe definir la aplicación.
17. Contraer el `ParameterVocabularies` clave.
18. Agregar el `IntentPhrases` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist15.png "Agregue la clave IntentPhrases con el tipo de matriz")](implementing-sirikit-images/plist15.png#lightbox)
19. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist16.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist16.png#lightbox)
20. Agregar el `IntentName` clave con el **tipo** de `String` e intención en el ejemplo:

    [![](implementing-sirikit-images/plist17.png "Agregar la clave IntentName con el tipo de cadena y la intención del ejemplo")](implementing-sirikit-images/plist17.png#lightbox)
21. Agregar el `IntentExamples` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist18.png "Agregue la clave IntentExamples con el tipo de matriz")](implementing-sirikit-images/plist18.png#lightbox)
22. Agregue algunos `String` claves con los usos de ejemplo del término:

    [![](implementing-sirikit-images/plist19.png "Agregar algunas claves de cadena con los usos de ejemplo del término")](implementing-sirikit-images/plist19.png#lightbox)
23. Repita los pasos anteriores para los propósitos necesita proporcionar ejemplos de uso de la aplicación.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga clic en el nombre del proyecto en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo archivo...**   >  **iOS**:

    [![](implementing-sirikit-images/plist01w.png "Agregar un nuevo Info.plist")](implementing-sirikit-images/plist01w.png#lightbox) 
2. Haga doble clic en el `AppIntentVocabulary.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
3. Haga clic en el  **+**  para agregar una clave, establezca el **nombre** a `ParameterVocabularies` y **tipo** a `Array`:

    [![](implementing-sirikit-images/plist02w.png "Establezca el nombre en ParameterVocabularies y el tipo de matriz")](implementing-sirikit-images/plist02w.png#lightbox)
4. Expanda `ParameterVocabularies` y haga clic en el  **+**  botón y establezca la **tipo** a `Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "Establezca el tipo en el diccionario")](implementing-sirikit-images/plist03w.png#lightbox)
5. Haga clic en el  **+**  para agregar una nueva clave, establezca el **nombre** a `ParameterNames` y **tipo** a `Array`:

    [![](implementing-sirikit-images/plist04w.png "Establezca el nombre en ParameterNames y el tipo de matriz")](implementing-sirikit-images/plist04w.png#lightbox)
6. Haga clic en el  **+**  para agregar una nueva clave con el **tipo** de `String` y el valor como uno de los nombres de parámetro disponibles. Por ejemplo, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "La clave de INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05w.png#lightbox)
7. Agregar el `ParameterVocabulary` clave para la `ParameterVocabularies` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist06w.png "Agregue la clave de ParameterVocabulary a la clave ParameterVocabularies con el tipo de matriz")](implementing-sirikit-images/plist06w.png#lightbox)
8. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist07w.png#lightbox)
9. Agregar el `VocabularyItemIdentifier` clave con el **tipo** de `String` y especifique un identificador único para el término:

    [![](implementing-sirikit-images/plist08w.png "Agregue la clave VocabularyItemIdentifier con el tipo de cadena y especifique un identificador único para el término")](implementing-sirikit-images/plist08w.png#lightbox)
10. Agregar el `VocabularyItemSynonyms` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist09w.png "Agregue la clave VocabularyItemSynonyms con el tipo de matriz")](implementing-sirikit-images/plist09w.png#lightbox)
11. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist10w.png#lightbox)
12. Agregar el `VocabularyItemPhrase` clave con el **tipo** de `String` y el término que se define la aplicación:

    [![](implementing-sirikit-images/plist11w.png "Agregue la clave VocabularyItemPhrase con el tipo de cadena y el término que se está definiendo la aplicación")](implementing-sirikit-images/plist11w.png#lightbox)
13. Agregar el `VocabularyItemPronunciation` clave con el **tipo** de `String` y la pronunciación fonética del término:

    [![](implementing-sirikit-images/plist12w.png "Agregue la clave VocabularyItemPronunciation con el tipo de cadena y la pronunciación fonética del término")](implementing-sirikit-images/plist12w.png#lightbox)
14. Agregar el `VocabularyItemExamples` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist13w.png "Agregue la clave VocabularyItemExamples con el tipo de matriz")](implementing-sirikit-images/plist13w.png#lightbox)
15. Agregue algunos `String` claves con los usos de ejemplo del término:

    [![](implementing-sirikit-images/plist14w.png "Agregar algunas claves de cadena con los usos de ejemplo del término")](implementing-sirikit-images/plist14w.png#lightbox)
16. Repita los pasos anteriores para cualquier otros términos personalizados debe definir la aplicación.
17. Contraer el `ParameterVocabularies` clave.
18. Agregar el `IntentPhrases` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist15w.png "Agregue la clave IntentPhrases con el tipo de matriz")](implementing-sirikit-images/plist15w.png#lightbox)
19. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist16w.png#lightbox)
20. Agregar el `IntentName` clave con el **tipo** de `String` e intención en el ejemplo:

    [![](implementing-sirikit-images/plist17w.png "Agregar la clave IntentName con el tipo de cadena y la intención del ejemplo")](implementing-sirikit-images/plist17w.png#lightbox)
21. Agregar el `IntentExamples` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist18w.png "Agregue la clave IntentExamples con el tipo de matriz")](implementing-sirikit-images/plist18w.png#lightbox)
22. Agregue algunos `String` claves con los usos de ejemplo del término:

    [![](implementing-sirikit-images/plist19w.png "Agregar algunas claves de cadena con los usos de ejemplo del término")](implementing-sirikit-images/plist19w.png#lightbox)
23. Repita los pasos anteriores para los propósitos necesita proporcionar ejemplos de uso de la aplicación.

-----

> [!IMPORTANT]
> **Nota:** el `AppIntentVocabulary.plist` se registrará con Siri en la prueba de dispositivos durante el desarrollo y pueden tardar algún tiempo para Siri incorporar el vocabulario personalizado. Como resultado, el evaluador deberá esperar unos minutos antes de intentar probar vocabulario específico de aplicación cuando se ha actualizado.

Para obtener más información, vea nuestra [referencia de aplicación específica vocabulario](~/ios/platform/sirikit/understanding-sirikit.md) y de Apple [especificar personalizado vocabulario referencia](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

## <a name="adding-an-intents-extension"></a>Agregar una extensión de intentos

Ahora que la aplicación se ha preparado para adoptar SiriKit, el desarrollador deberá agregar extensiones de intentos de uno (o más) a la solución y controlar los tipos de color requeridas para la integración de Siri.

Para cada extensión de calidades necesaria, haga lo siguiente:

- Agregar un proyecto de extensión de intentos para la solución de aplicación de Xamarin.iOS.
- Configurar la extensión de calidades `Info.plist` archivo.
- Modifique la clase principal de la extensión de intentos.

Para obtener más información, vea nuestra [la referencia de extensión de calidades](~/ios/platform/sirikit/understanding-sirikit.md) y de Apple [crear la referencia de extensión de calidades](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1).

### <a name="creating-the-extension"></a>Creación de la extensión

Para agregar una extensión de intentos para la solución, haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga doble clic en el **nombre de la solución** en el **solución Pad** y seleccione **agregar** > **Agregar nuevo proyecto...** .
2. En el cuadro de diálogo seleccione **iOS** > **extensiones** > **intención extensión** y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/intents05.png "Seleccionar extensión intención")](implementing-sirikit-images/intents05.png#lightbox)
3. A continuación, escriba un **nombre** para el intento de extensión y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/intents06.png "Escriba un nombre para la extensión de intención")](implementing-sirikit-images/intents06.png#lightbox)
4. Por último, haga clic en el **crear** botón para agregar la extensión de intención a la solución de aplicaciones: 

    [![](implementing-sirikit-images/intents07.png "Agregar la extensión de intención a la solución de aplicaciones")](implementing-sirikit-images/intents07.png#lightbox)
5. En el **el Explorador de soluciones**, haga doble clic en el **referencias** carpeta de la extensión de intención recién creado. Compruebe el nombre del proyecto de biblioteca de código compartido común (que la aplicación creada anteriormente) y haga clic en el **Aceptar** botón: 

    [![](implementing-sirikit-images/intents08.png "Seleccione el nombre del proyecto de biblioteca de código compartido común")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga doble clic en el **nombre de la solución** en el **el Explorador de soluciones** y seleccione **agregar** > **Agregar nuevo proyecto...** .
2. En el cuadro de diálogo seleccione **iOS** > **extensiones** > **intención extensión** y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/intents05w.png "Seleccionar extensión intención")](implementing-sirikit-images/intents05w.png#lightbox)
3. A continuación, escriba un **nombre** para el intento de extensión y haga clic en el **Aceptar** botón.
5. En el **el Explorador de soluciones**, haga doble clic en el **referencias** carpeta de la extensión de intención recién creado. Compruebe el nombre del proyecto de biblioteca de código compartido común (que la aplicación creada anteriormente) y haga clic en el **Aceptar** botón: 

    [![](implementing-sirikit-images/intents08w.png "Seleccione el nombre del proyecto de biblioteca de código compartido común")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Repita estos pasos para el número de extensiones de intención (basado en [diseñar la aplicación para las extensiones](#Architecting-the-App-for-Extensions) sección anterior) que requiera la aplicación.

### <a name="configuring-the-infoplist"></a>Configurar la Info.plist

Para cada una de las extensiones de intentos que ha agregado a la solución de la aplicación, debe configurarse en el `Info.plist` archivos para trabajar con la aplicación.

Al igual que cualquier extensión de la aplicación típica, la aplicación tendrá las claves existentes de `NSExtension` y `NSExtensionAttributes`. Para una extensión de calidades hay dos atributos nuevos que se deben configurar:

[![](implementing-sirikit-images/intents01.png "Los dos nuevos atributos que se deben configurar")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** : es necesario y se compone de una matriz de nombres de clase de intención que la aplicación desea admitir de la extensión de intención.
- **IntentsRestrictedWhileLocked** -es una clave opcional para la aplicación especificar el comportamiento de la extensión de la pantalla de bloqueo. Consta de una matriz de nombres de clase de intención que desea que la aplicación requiere que el usuario iniciar sesión para usar de la extensión de intención.

Para configurar la extensión de intención `Info.plist` de archivos, haga doble clic en el **el Explorador de soluciones** para abrirlo y editarlo. A continuación, cambie a la **origen** consulte a continuación, expanda el `NSExtension` y `NSExtensionAttributes` claves en el editor:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents02.png "Las claves de NSExtension y NSExtensionAttributes en el editor")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents02w.png "Las claves de NSExtension y NSExtensionAttributes en el editor")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Expanda el `IntentsSupported` clave y agregue el nombre de cualquier clase de intención será compatible con esta extensión. Para la aplicación de ejemplo MonkeyChat, admite la `INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents09.png "La clave de INSendMessageIntent")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents09w.png "La clave de INSendMessageIntent")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Si la aplicación, opcionalmente, requiere que el usuario iniciar sesión en el dispositivo para utilizar un intento determinado, expanda el `IntentRestrictedWhileLocked` clave y agregue los nombres de clase de los intentos que tienen acceso restringido. Para la aplicación de ejemplo MonkeyChat, el usuario debe haber iniciado sesión para enviar un mensaje de chat, por lo que hemos agregado `INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents10.png "La clave de INSendMessageIntent agregada")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents10w.png "La clave de INSendMessageIntent agregada")](implementing-sirikit-images/intents10w.png#lightbox)

-----


Para obtener una lista completa de dominios de intención disponibles, vea de Apple [intención de dominios de referencia](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configuración de la clase principal

A continuación, el programador deberá configurar la clase principal que actúa como punto de entrada principal para la extensión de la intención de Siri. Debe ser una subclase de `INExtension` que se ajusta a la `IINIntentHandler` delegar. Por ejemplo:

```csharp
using System;
using System.Collections.Generic;

using Foundation;
using Intents;

namespace MonkeyChatIntents
{
    [Register ("IntentHandler")]
    public class IntentHandler : INExtension, IINSendMessageIntentHandling, IINSearchForMessagesIntentHandling, IINSetMessageAttributeIntentHandling
    {
        #region Constructors
        protected IntentHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override NSObject GetHandler (INIntent intent)
        {
            // This is the default implementation.  If you want different objects to handle different intents,
            // you can override this and return the handler you want for that particular intent.

            return this;
        }
        #endregion
        ...
    }
}
```

Hay un método de Solitario que debe implementar la aplicación en la clase principal de la extensión de intención, la `GetHandler` método. Este método se pasa un intento por SiriKit y la aplicación debe devolver un **controlador intención** que coincide con el tipo de la intención de determinado.

Puesto que la aplicación de ejemplo MonkeyChat solo administra un intento, está devolviendo propio en el `GetHandler` método. Si la extensión administra más de un intento, el programador agrega una clase para cada tipo de intento y devolver una instancia aquí tomando como base el `Intent` pasado al método.

### <a name="handling-the-resolve-stage"></a>Control de la fase de resolución

La fase resolver es donde se aclarar y validar los parámetros de la extensión de intención pasan desde Siri y se han establecido a través de la conversación del usuario.

Para cada parámetro que se envía desde Siri, hay un `Resolve` método. La aplicación necesitará implementar este método para cada parámetro que la aplicación puede necesitar la Ayuda del Siri para obtener la respuesta correcta del usuario.

En el caso de la aplicación de MonkeyChat ejemplo, la extensión de intención requerirá un uno o varios destinatarios enviar el mensaje. Para cada destinatario en la lista, la extensión debe realizar una búsqueda de contacto que puede tener el siguiente resultado:

- Se encuentra exactamente un contacto que coincide.
- Se encontraron dos o más contactos coincidentes.
- No se encuentra ningún contacto coincidente.

Además, MonkeyChat requiere contenido para el cuerpo del mensaje. Si el usuario no ha proporcionado este, Siri debe preguntar al usuario para el contenido.

La extensión de la intención será necesario controlar correctamente cada uno de estos casos.


```csharp
[Export ("resolveRecipientsForSearchForMessages:withCompletion:")]
public void ResolveRecipients (INSendMessageIntent intent, Action<INPersonResolutionResult []> completion)
{
    var recipients = intent.Recipients;
    // If no recipients were provided we'll need to prompt for a value.
    if (recipients.Length == 0) {
        completion (new INPersonResolutionResult [] { (INPersonResolutionResult)INPersonResolutionResult.NeedsValue });
        return;
    }

    var resolutionResults = new List<INPersonResolutionResult> ();

    foreach (var recipient in recipients) {
        var matchingContacts = new INPerson [] { recipient }; // Implement your contact matching logic here to create an array of matching contacts
        if (matchingContacts.Length > 1) {
            // We need Siri's help to ask user to pick one from the matches.
            resolutionResults.Add (INPersonResolutionResult.GetDisambiguation (matchingContacts));
        } else if (matchingContacts.Length == 1) {
            // We have exactly one matching contact
            resolutionResults.Add (INPersonResolutionResult.GetSuccess (recipient));
        } else {
            // We have no contacts matching the description provided
            resolutionResults.Add ((INPersonResolutionResult)INPersonResolutionResult.Unsupported);
        }
    }

    completion (resolutionResults.ToArray ());
}

[Export ("resolveContentForSendMessage:withCompletion:")]
public void ResolveContent (INSendMessageIntent intent, Action<INStringResolutionResult> completion)
{
    var text = intent.Content;
    if (!string.IsNullOrEmpty (text))
        completion (INStringResolutionResult.GetSuccess (text));
    else
        completion ((INStringResolutionResult)INStringResolutionResult.NeedsValue);
}
```

Para obtener más información, vea nuestra [la referencia de fase resolver](~/ios/platform/sirikit/understanding-sirikit.md) y de Apple [Resolving y control de referencia de calidades](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1).

### <a name="handling-the-confirm-stage"></a>Control de la fase de confirmación

La fase de confirmación es donde la extensión de intención comprueba que tiene toda la información para satisfacer la solicitud del usuario. La aplicación desea enviar confirmación a lo largo de le todos los detalles necesarios de lo que está a punto de suceder a Siri para que se puede confirmar con el usuario que se trata de la acción deseada.

```csharp
[Export ("confirmSendMessage:completion:")]
public void ConfirmSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Verify user is authenticated and the app is ready to send a message.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Ready, userActivity);
    completion (response);
}
```

Para obtener más información, vea nuestra [la referencia de fase confirmar](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>La intención de procesamiento

Éste es el punto donde la extensión de intención realmente realiza la tarea para satisfacer la solicitud del usuario y devolver los resultados a Siri, por lo que el usuario pueda estar informado.


```csharp
public void HandleSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Implement the application logic to send a message here.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, userActivity);
    completion (response);
}

public void HandleSearchForMessages (INSearchForMessagesIntent intent, Action<INSearchForMessagesIntentResponse> completion)
{
    // Implement the application logic to find a message that matches the information in the intent.
    ...

    var userActivity = new NSUserActivity (nameof (INSearchForMessagesIntent));
    var response = new INSearchForMessagesIntentResponse (INSearchForMessagesIntentResponseCode.Success, userActivity);

    // Initialize with found message's attributes
    var sender = new INPerson (new INPersonHandle ("sarah@example.com", INPersonHandleType.EmailAddress), null, "Sarah", null, null, null);
    var recipient = new INPerson (new INPersonHandle ("+1-415-555-5555", INPersonHandleType.PhoneNumber), null, "John", null, null, null);
    var message = new INMessage ("identifier", "I am so excited about SiriKit!", NSDate.Now, sender, new INPerson [] { recipient });
    response.Messages = new INMessage [] { message };
    completion (response);
}

public void HandleSetMessageAttribute (INSetMessageAttributeIntent intent, Action<INSetMessageAttributeIntentResponse> completion)
{
    // Implement the application logic to set the message attribute here.
    ...

    var userActivity = new NSUserActivity (nameof (INSetMessageAttributeIntent));
    var response = new INSetMessageAttributeIntentResponse (INSetMessageAttributeIntentResponseCode.Success, userActivity);
    completion (response);
}
```

Para obtener más información, vea nuestra [la referencia de fase controlar](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Agregar una extensión de la interfaz de usuario de intentos

La extensión opcional de la interfaz de usuario de calidades presenta la oportunidad de poner la aplicación en la interfaz de usuario y personalización de marca en la experiencia de Siri y hacer que los usuarios se sienta conectado a la aplicación. Con esta extensión de la aplicación puede hacer que la marca, así como información visual y otra en la transcripción.

[![](implementing-sirikit-images/intentsui01.png "Un ejemplo de extensión de interfaz de usuario de calidades de salida")](implementing-sirikit-images/intentsui01.png#lightbox)

Al igual que la extensión del color, el desarrollador realizará el paso siguiente para la extensión de la interfaz de usuario de intentos:

- Agregar un proyecto de extensión de interfaz de usuario de intentos para la solución de aplicación de Xamarin.iOS.
- Configurar la extensión de la interfaz de usuario de calidades `Info.plist` archivo.
- Modifique la clase principal de la extensión de la interfaz de usuario de intentos.

Para obtener más información, vea nuestra [la referencia de extensión de interfaz de usuario de calidades](~/ios/platform/sirikit/understanding-sirikit.md) y de Apple [proporciona una referencia de la interfaz personalizada](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Creación de la extensión

Para agregar una extensión de la interfaz de usuario de intentos para la solución, haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga doble clic en el **nombre de la solución** en el **solución Pad** y seleccione **agregar** > **Agregar nuevo proyecto...** .
2. En el cuadro de diálogo seleccione **iOS** > **extensiones** > **extensión de interfaz de usuario de intención** y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/intents11.png "Seleccione la extensión de la intención de la interfaz de usuario")](implementing-sirikit-images/intents11.png#lightbox)
3. A continuación, escriba un **nombre** para el intento de extensión y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/intents12.png "Escriba un nombre para la extensión de intención")](implementing-sirikit-images/intents12.png#lightbox)
4. Por último, haga clic en el **crear** botón para agregar la extensión de intención a la solución de aplicaciones: 

    [![](implementing-sirikit-images/intents13.png "Agregar la extensión de intención a la solución de aplicaciones")](implementing-sirikit-images/intents13.png#lightbox)
5. En el **el Explorador de soluciones**, haga doble clic en el **referencias** carpeta de la extensión de intención recién creado. Compruebe el nombre del proyecto de biblioteca de código compartido común (que la aplicación creada anteriormente) y haga clic en el **Aceptar** botón: 

    [![](implementing-sirikit-images/intents14.png "Seleccione el nombre del proyecto de biblioteca de código compartido común")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga doble clic en el **nombre de la solución** en el **el Explorador de soluciones** y seleccione **agregar** > **Agregar nuevo proyecto...**
2. En el cuadro de diálogo seleccione **iOS** > **extensiones** > **extensión de interfaz de usuario de intención** y haga clic en el **siguiente** botón.
3. A continuación, escriba un **nombre** para el intento de extensión y haga clic en el **Aceptar** botón.
5. En el **el Explorador de soluciones**, haga doble clic en el **referencias** carpeta de la extensión de intención recién creado. Compruebe el nombre del proyecto de biblioteca de código compartido común (que la aplicación creada anteriormente) y haga clic en el **Aceptar** botón.
    
-----

### <a name="configuring-the-infoplist"></a>Configurar la Info.plist

Configurar la extensión de la interfaz de usuario de calidades `Info.plist` archivos para trabajar con la aplicación.

Al igual que cualquier extensión de la aplicación típica, la aplicación tendrá las claves existentes de `NSExtension` y `NSExtensionAttributes`. Para una extensión de intentos es un nuevo atributo que se debe configurar:

[![](implementing-sirikit-images/intents03.png "El nuevo uno atributo que se debe configurar")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** es necesario y se compone de una matriz de nombres de clase de intención que la aplicación desea admitir de la extensión de intención.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para configurar la extensión de la interfaz de usuario de intención `Info.plist` de archivos, haga doble clic en el **el Explorador de soluciones** para abrirlo y editarlo. A continuación, cambie a la **origen** consulte a continuación, expanda el `NSExtension` y `NSExtensionAttributes` claves en el editor:

[![](implementing-sirikit-images/intents04.png "Las claves de NSExtension y NSExtensionAttributes en el editor")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para configurar la extensión de la interfaz de usuario de intención `Info.plist` de archivos, haga doble clic en el **el Explorador de soluciones** para abrirlo y editarlo. Expanda el `NSExtension` y `NSExtensionAttributes` claves en el editor:

[![](implementing-sirikit-images/intents04w.png "Claves de Tthe NSExtension y NSExtensionAttributes en el editor")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Expanda el `IntentsSupported` clave y agregue el nombre de cualquier clase de intención será compatible con esta extensión. Para la aplicación de ejemplo MonkeyChat, admite la `INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents15.png "La clave de INSendMessageIntent")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents15w.png "La clave de INSendMessageIntent")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Para obtener una lista completa de dominios de intención disponibles, vea de Apple [intención de dominios de referencia](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configuración de la clase principal

Configurar la clase principal que actúa como punto de entrada principal para la extensión de la interfaz de usuario de intención de Siri. Debe ser una subclase de `UIViewController` que se ajusta a la `IINUIHostedViewController` interfaz. Por ejemplo:

```csharp
using System;
using Foundation;
using CoreGraphics;
using Intents;
using IntentsUI;
using UIKit;

namespace MonkeyChatIntentsUI
{
    public partial class IntentViewController : UIViewController, IINUIHostedViewControlling
    {
        #region Constructors
        protected IntentViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }

        public override void DidReceiveMemoryWarning ()
        {
            // Releases the view if it doesn't have a superview.
            base.DidReceiveMemoryWarning ();

            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Public Methods
        [Export ("configureWithInteraction:context:completion:")]
        public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
        {
            // Do configuration here, including preparing views and calculating a desired size for presentation.

            if (completion != null)
                completion (DesiredSize ());
        }

        [Export ("desiredSize:")]
        public CGSize DesiredSize ()
        {
            return ExtensionContext.GetHostedViewMaximumAllowedSize ();
        }
        #endregion
    }
}
```

Siri pasará un `INInteraction` instancia de clase a la `Configure` método de la `UIViewController` instancia dentro de la extensión de la interfaz de usuario de intención.

La `INInteraction` objeto proporciona tres fragmentos de información a la extensión de clave:

1. El objeto de intención que se está procesando.
2. El objeto de respuesta de la intención de la `Confirm` y `Handle` métodos de la extensión de intención.
3. El estado de interacción que define el estado de la interacción entre la aplicación y Siri.

El `UIViewController` instancia es la clase de entidad de seguridad para la interacción con Siri y porque se hereda de `UIViewController`, que tiene acceso a todas las características de UIKit.

Cuando se llama Siri la `Configure` método de la `UIViewController` pasa un valor en un contexto de vista que indica que el controlador de vista o bien se hospedará en una tarjeta de mapas o Siri Snippit.

Siri también se pasará en un controlador de finalización que la aplicación tiene que devolver el tamaño deseado de la vista una vez que la aplicación termine de configurarlo.

### <a name="design-the-ui-in-ios-designer"></a>Diseñar la interfaz de usuario en el Diseñador de iOS

Diseño de la interfaz de usuario de la extensión de interfaz de usuario del color en el Diseñador de iOS. Haga doble clic en la extensión `MainInterface.storyboard` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo. Arrastre todos los elementos de interfaz de usuario necesarios para crear la interfaz de usuario y guardar los cambios.

> [!IMPORTANT]
> **Nota:** aunque es posible agregar elementos interactivos como `UIButtons` o `UITextFields` a la extensión de la interfaz de usuario de intención `UIViewController`, estos se prohíben estrictamente como la interfaz de usuario de intención en no interactivo y el usuario no podrá interactuar con ellos.

### <a name="wire-up-the-user-interface"></a>El cable telefónico de la interfaz de usuario

Con la interfaz de usuario de la extensión de interfaz de usuario del color creado en el Diseñador de iOS, editar la `UIViewController` subclase e invalide el `Configure` método tal como se indica a continuación:

```csharp
[Export ("configureWithInteraction:context:completion:")]
public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
{
    // Do configuration here, including preparing views and calculating a desired size for presentation.
    ...

    // Return desired size
    if (completion != null)
        completion (DesiredSize ());
}

[Export ("desiredSize:")]
public CGSize DesiredSize ()
{
    return ExtensionContext.GetHostedViewMaximumAllowedSize ();
}
```

### <a name="overriding-the-default-siri-ui"></a>Reemplazar la interfaz de usuario de Siri predeterminado

La extensión de la interfaz de usuario de intentos se siempre se muestran junto con otro contenido Siri como el icono de la aplicación y el nombre en la parte superior de la interfaz de usuario o, según el propósito, se pueden mostrar botones (por ejemplo, envío o cancelar) en la parte inferior.

Hay algunos casos donde la aplicación puede reemplazar la información que Siri muestra al usuario de forma predeterminada, como mensajería o mapas donde la aplicación puede reemplazar la experiencia predeterminada con uno personalizado para la aplicación.

Si necesita la extensión de la interfaz de usuario de intentos invalidar los elementos de la UI Siri, de forma predeterminada el `UIViewController` subclase necesitará implementar la `IINUIHostedViewSiriProviding` interfaz y darse de alta a la visualización de un elemento de interfaz concreto.

Agregue el código siguiente a la `UIViewController` subclase para indicar que Siri que la extensión de la interfaz de usuario de intención ya se están mostrando el contenido del mensaje:

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Consideraciones

Apple sugiere que el desarrollador tomar en cuenta al diseñar e implementar las extensiones de interfaz de usuario de la intención de las consideraciones siguientes:

- **Ser consciente de uso de memoria** : dado que las extensiones de interfaz de usuario de intención son temporales y sólo se muestra durante un breve período, el sistema impone las restricciones de memoria más estricto que se usan con una aplicación completa.
- **Considere la posibilidad de como mínimo y el tamaño máximo de vista** -Asegúrese de que las extensiones de interfaz de usuario de la intención de aspecto atractivo en cada tipo de dispositivo de iOS, el tamaño y la orientación. Además, el tamaño deseado que la aplicación envía a Siri no puede concederse.
- **Usar Flexible y patrones de diseño adaptable** , nuevamente, para asegurarse de que la interfaz de usuario tiene un aspecto excelente en todos los dispositivos.

## <a name="summary"></a>Resumen

En este artículo se tratan SiriKit y se muestra cómo se pueden agregar a las aplicaciones de Xamarin.iOS para proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas en un dispositivo iOS.




## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referencia de marco de intentos](https://developer.apple.com/reference/intents)
- [Referencia de marco de interfaz de usuario de intentos](https://developer.apple.com/reference/intentsui)
- [Referencia de la intención de dominios](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
