---
title: Implementación de SiriKit en Xamarin.iOS
description: Este documento describe los pasos necesarios para implementar la compatibilidad de SiriKit en las aplicaciones de Xamarin.iOS. Describe las extensiones de intenciones y extensiones de interfaz de usuario de intenciones.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: ea037aaaac97d9f326f1a2fbcb28d97c9d8a9b45
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110256"
---
# <a name="implementing-sirikit-in-xamarinios"></a>Implementación de SiriKit en Xamarin.iOS

_En este artículo se trata los pasos necesarios para implementar la compatibilidad de SiriKit en las aplicaciones de Xamarin.iOS._

Nuevo iOS 10, SiriKit permite que una aplicación de Xamarin.iOS proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas de un dispositivo iOS. En este artículo se trata los pasos necesarios para implementar la compatibilidad de SiriKit en las aplicaciones de Xamarin.iOS agregando extensiones intenciones requeridas, las extensiones de interfaz de usuario de intenciones y el vocabulario.

Siri funciona con el concepto de **dominios**, sabe de grupos de acciones para las tareas relacionadas. Cada interacción de la aplicación con Siri debe estar en uno de sus dominios de servicio conocido como sigue:

- Una llamada de vídeo o audio.
- Transporte de reserva.
- Administración de sesiones de ejercicios.
- Mensajería.
- Buscar fotografías.
- Enviar o recibir los pagos.

Cuando el usuario realiza una solicitud de Siri que implican a uno de los servicios de la extensión de aplicación, SiriKit envía la extensión de un **intención** objeto que describe la solicitud del usuario junto con cualquier dato de apoyo. La extensión de la aplicación, a continuación, genera adecuado **respuesta** de objeto para el determinado **intención**, que detalla cómo la extensión puede atender la solicitud.

Esta guía presentará un ejemplo rápido de incluida la compatibilidad con SiriKit en una aplicación existente. Para este ejemplo, vamos a usar la aplicación MonkeyChat falsa:

[![](implementing-sirikit-images/monkeychat01.png "El icono de MonkeyChat")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene su propio libro contacto de amigos del usuario, cada uno asociado con un nombre de pantalla (por ejemplo, Bobo, por ejemplo) y permite al usuario enviar chats de texto a cada uno de ellos por su nombre de la pantalla.

## <a name="extending-the-app-with-sirikit"></a>Extender la aplicación con SiriKit

Como se muestra en el [descripción de conceptos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) guía, hay tres partes principales implicados en la ampliación de una aplicación con SiriKit:

[![](implementing-sirikit-images/elements01.png "Extender la aplicación con el diagrama de SiriKit")](implementing-sirikit-images/elements01.png#lightbox)

Se incluyen los siguientes:

1. **Extensión de intents** -comprueba las respuestas de los usuarios, confirma que la aplicación puede atender la solicitud y realiza realmente la tarea para satisfacer la solicitud del usuario.
2. **Extensión de interfaz de usuario de intents** - *opcional*, proporciona una interfaz de usuario personalizada para las respuestas en el entorno de Siri y puede poner las aplicaciones de interfaz de usuario y personalización de marca en Siri para enriquecer la experiencia del usuario.
3. **Aplicación** -proporciona la aplicación con vocabularios específicos del usuario para ayudarle a trabajar con él Siri. 

Todos estos elementos y los pasos necesarios para incluirlos en la aplicación se tratarán en detalle en las secciones siguientes.


## <a name="preparing-the-app"></a>Preparación de la aplicación

SiriKit se basa en las extensiones, sin embargo, antes de agregar todas las extensiones a la aplicación, hay algunas cosas que el desarrollador debe hacer para ayudar a la adopción de SiriKit.

### <a name="moving-common-shared-code"></a>Mover código común compartido 

En primer lugar, el desarrollador puede mover parte del código común que se van a compartir entre la aplicación y las extensiones a cualquiera _proyectos compartidos_, _bibliotecas de clases portables (PCL)_ o _nativo Bibliotecas_.

Las extensiones deberá ser capaz de hacer todo lo que hace la aplicación. En términos de la aplicación de ejemplo MonkeyChat, cosas como buscar contactos, agregar nuevos contactos y enviar mensajes y recuperar el historial de mensajes.

Al mover este código común a un proyecto compartido, PCL o biblioteca nativa hace fácil de mantener ese código en un lugar común y garantiza que la aplicación primaria y la extensión de proporcionan una experiencia uniforme y funcionalidad para el usuario.

En el caso de la aplicación de ejemplo MonkeyChat, los modelos de datos y el código de procesamiento, como el acceso de red y la base de datos se moverá a una biblioteca nativa.

Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie Visual Studio para Mac y abra la aplicación MonkeyChat.
2. Haga doble clic en el nombre de la solución en el **panel de solución** y seleccione **agregar** > **nuevo proyecto...** : 

    [![](implementing-sirikit-images/prep01.png "Agregue un nuevo proyecto")](implementing-sirikit-images/prep01.png#lightbox)
3. Seleccione **iOS** > **biblioteca** > **biblioteca de clases** y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/prep02.png "Seleccione la biblioteca de clases")](implementing-sirikit-images/prep02.png#lightbox)
4. Escriba `MonkeyChatCommon` para el **nombre** y haga clic en el **crear** botón: 

    [![](implementing-sirikit-images/prep03.png "Escriba el nombre MonkeyChatCommon")](implementing-sirikit-images/prep03.png#lightbox)
5. Haga doble clic en el **referencias** carpeta de la aplicación principal en el **el Explorador de soluciones** y seleccione **editar referencias...** . Compruebe el **MonkeyChatCommon** del proyecto y haga clic en el **Aceptar** botón: 

    [![](implementing-sirikit-images/prep05.png "Compruebe el proyecto MonkeyChatCommon")](implementing-sirikit-images/prep05.png#lightbox)
6. En el **el Explorador de soluciones**, arrastre el código común compartido desde la aplicación principal para la biblioteca nativa.
7. En el caso de MonkeyChat, arrastre el **DataModels** y **procesadores** carpetas de la aplicación principal en la biblioteca nativa: 

    [![](implementing-sirikit-images/prep06.png "Las carpetas DataModels y procesadores en el Explorador de soluciones")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie Visual Studio y abra la aplicación MonkeyChat.
2. Haga doble clic en el nombre de la solución en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo proyecto...** .
3. Seleccione **Visual C#**   >  **proyecto compartido** y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/prep02.w157-sml.png "Seleccione la biblioteca de clases")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Escriba `MonkeyChatCommon` para el **nombre** y haga clic en el **crear** botón.
5. Haga doble clic en el **referencias** carpeta de la aplicación principal en el **el Explorador de soluciones** y seleccione **editar referencias...** . Compruebe el **MonkeyChatCommon** del proyecto y haga clic en el **Aceptar** botón: 

    [![](implementing-sirikit-images/prep05w.png "Compruebe el proyecto MonkeyChatCommon")](implementing-sirikit-images/prep05w.png#lightbox)
6. En el **el Explorador de soluciones**, arrastre el código común compartido desde la aplicación principal para el proyecto compartido.
7. En el caso de MonkeyChat, arrastre el **DataModels** y **procesadores** carpetas de la aplicación principal en la biblioteca nativa.

-----

Editar cualquiera de los archivos que se movieron a la biblioteca nativa y cambiar el espacio de nombres para que coincida con la biblioteca. Por ejemplo, cambiar `MonkeyChat` a `MonkeyChatCommon`:

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

A continuación, vuelva a la aplicación principal y agregar un `using` declaración de espacio de nombres de la biblioteca nativa en cualquier parte la aplicación usa una de las clases que se han movido:

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

### <a name="architecting-the-app-for-extensions"></a>Arquitectura de la aplicación para las extensiones

Normalmente, una aplicación se Suscríbase varios intentos y el programador debe asegurarse de que la aplicación está estructurada para el número apropiado de intención de extensiones.

En la situación donde una aplicación requiere más de un intento, el desarrollador tiene la opción de ubicar todas su propio control de intención en una extensión de intención o crear una extensión de intención independiente para cada intento.

Si opta por crear una extensión de intención independiente para cada intento, el desarrollador podría termina por duplicar una gran cantidad de código repetitivo en cada extensión y crear una gran cantidad de procesador y la sobrecarga de memoria.

Para ayudar a elegir entre las dos opciones, vea si cualquiera de las intenciones naturalmente forman un conjunto. Por ejemplo, una aplicación que realiza llamadas de audio y vídeo podría desear incluir ambos de esos intentos en una única extensión intención como control de tareas similares y podría proporcionar la reutilización de código más.

Para cualquier propósito o el grupo de intenciones que no se ajustan a un grupo existente, cree una nueva extensión de intención de solución de la aplicación para contenerlos.


### <a name="setting-the-required-entitlements"></a>Establecer los derechos necesarios

Cualquier aplicación de Xamarin.iOS que incluye la integración de SiriKit, deben tener los derechos correctos establecidos. Si el desarrollador no establecer correctamente estos derechos necesarios, no podrán instalar o probar la aplicación en hardware real iOS 10 (o superior) que también es el requisito desde el 10 de iOS Simulator no es compatible con SiriKit.

Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Entitlements.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
2. Cambie a la **origen** ficha.
3. Agregar el `com.apple.developer.siri` **propiedad**, establezca el **tipo** a `Boolean` y **valor** a `Yes`: 

    [![](implementing-sirikit-images/setup01.png "Agregue la propiedad com.apple.developer.siri")](implementing-sirikit-images/setup01.png#lightbox)
4. Guarde los cambios en el archivo.
5. Haga doble clic en el **archivo de proyecto** en el **el Explorador de soluciones** para abrirlo y editarlo.
6. Seleccione **firma de lote de iOS** y asegúrese de que el `Entitlements.plist` archivo seleccionado en el **derechos personalizados** campo: 

    [![](implementing-sirikit-images/setup02.png "Seleccione el archivo Entitlements.plist en el campo de derechos personalizados")](implementing-sirikit-images/setup02.png#lightbox)
7. Haga clic en el botón **Aceptar** para guardar los cambios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el `Entitlements.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
3. Agregar el `com.apple.developer.siri` **propiedad**, establezca el **tipo** a `Boolean` y **valor** a `Yes`: 

    [![](implementing-sirikit-images/setup01w.png "Agregue la propiedad com.apple.developer.siri")](implementing-sirikit-images/setup01w.png#lightbox)
4. Guarde los cambios en el archivo.
5. Haga doble clic en el **archivo de proyecto** en el **el Explorador de soluciones** para abrirlo y editarlo.
6. Seleccione **firma de lote de iOS** y asegúrese de que el `Entitlements.plist` archivo seleccionado en el **derechos personalizados** campo.

-----

Cuando termine, la aplicación `Entitlements.plist` archivo debe ser similar al siguiente (en abrirse en un editor externo):

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

Debido a la seguridad estricta que Apple ha colocado en el marco de trabajo de SiriKit, cualquier aplicación de Xamarin.iOS que implementa SiriKit _debe_ tiene el Id. de aplicación y los derechos (consulte la sección anterior) correctos y deben estar firmados con un correcto Perfil de aprovisionamiento.

Haga lo siguiente en el equipo Mac:

1. En un explorador web, vaya a [ http://developer.apple.com ](http://developer.apple.com) e inicie sesión con su cuenta.
2. Haga clic en **certificados**, **identificadores** y **perfiles**.
3. Seleccione **perfiles de aprovisionamiento** y seleccione **identificadores de aplicación**, a continuación, haga clic en el **+** botón.
4. Escriba un **nombre** para el nuevo perfil.
5. Escriba un **Id. de agrupación** siguiendo Apple nomenclatura de recomendación.
6. Desplácese hacia abajo hasta la **App Services** sección, seleccione **SiriKit** y haga clic en el **continuar** botón: 

    [![](implementing-sirikit-images/setup03.png "Seleccione SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Compruebe todos los valores, a continuación, **enviar** el ID. App
8. Seleccione **perfiles de aprovisionamiento** > **desarrollo**, haga clic en el **+** botón, seleccione el **ID de Apple**, a continuación, haga clic en **continuar**.
9. Haga clic en seleccionar **todas**, a continuación, haga clic en **continuar**.
10. Haga clic en **seleccionar todo** , a continuación, haga clic en el nuevo, **continuar**.
11. Escriba un **nombre de perfil** usando Apple nomenclatura de sugerencias, a continuación, haga clic en **continuar**.
12. Inicie Xcode.
13. En el menú de Xcode seleccione **preferencias...**
14. Seleccione **cuentas**, a continuación, haga clic en el **ver detalles...** Botón: 

    [![](implementing-sirikit-images/setup04.png "Seleccionar cuentas")](implementing-sirikit-images/setup04.png#lightbox)
15. Haga clic en el **descargar todos los perfiles** botón en la esquina inferior izquierda: 

    [![](implementing-sirikit-images/setup05.png "Descargar todos los perfiles")](implementing-sirikit-images/setup05.png#lightbox)
16. Asegúrese de que el **perfil de aprovisionamiento** creados anteriormente se ha instalado en Xcode.
17. Abra el proyecto para agregar compatibilidad con SiriKit a en Visual Studio para Mac.
18. Haga doble clic en el `Info.plist` de archivos en el **el Explorador de soluciones**.
18. Asegúrese de que el **identificador de paquete** coincida con el que creó en el Portal para desarrolladores de Apple anterior: 

    [![](implementing-sirikit-images/setup06.png "El identificador de paquete")](implementing-sirikit-images/setup06.png#lightbox)
18. En el **el Explorador de soluciones**, seleccione el **proyecto**.
19. Haga clic en el proyecto y seleccione **opciones**.
21. Seleccione **firma de lote de iOS**, seleccione el **identidad de firma** y **perfil de aprovisionamiento** creado anteriormente: 

    [![](implementing-sirikit-images/setup07.png "Seleccione la identidad de firma y perfil de aprovisionamiento")](implementing-sirikit-images/setup07.png#lightbox)
22. Haga clic en el botón **Aceptar** para guardar los cambios.

> [!IMPORTANT]
> Pruebas SiriKit solo funciona en un real iOS 10 dispositivos de Hardware y no en el 10 de iOS Simulator. Si tiene problemas para instalar un SiriKit habilitado la aplicación de Xamarin.iOS en hardware real, asegúrese de que los derechos necesarios, Id. de aplicación, identificador de firma y perfil de aprovisionamiento se ha configurado correctamente en Apple Developer Portal y Visual Studio para Mac.

### <a name="requesting-siri-authorization"></a>Solicitud de autorización de Siri

Antes de la aplicación agrega cualquier vocabulario específico del usuario o las extensiones de intenciones se conecta a Siri, debe solicita autorización del usuario para tener acceso a Siri.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Editar la aplicación `Info.plist` de archivo, cambie a la **origen** ver y agregar el `NSSiriUsageDescription` clave con un valor de cadena que describe el uso de la aplicación Siri y qué tipos de datos se enviarán. Por ejemplo, la aplicación MonkeyChat podría decir "MonkeyChat contactos se enviarán a Siri":

[![](implementing-sirikit-images/request01.png "El NSSiriUsageDescription en el editor Info.plist")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Editar la aplicación `Info.plist` y agréguele el `NSSiriUsageDescription` clave con un valor de cadena que describe el uso de la aplicación Siri y qué tipos de datos se enviarán. Por ejemplo, la aplicación MonkeyChat podría decir "MonkeyChat contactos se enviarán a Siri":

[![](implementing-sirikit-images/request01w.png "El NSSiriUsageDescription en el editor Info.plist")](implementing-sirikit-images/request01w.png#lightbox)

-----

Llame a la `RequestSiriAuthorization` método de la `INPreferences` clase cuando la aplicación se inicia por primera vez. Editar el `AppDelegate.cs` clase y hacer el `FinishedLaunching` método aspecto parecido al siguiente:


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

La primera vez que se llama a este método, se muestra una alerta que solicita al usuario que la aplicación pueda tener acceso a Siri. El mensaje que el desarrollador agrega a la `NSSiriUsageDescription` anterior se mostrará esta alerta. Si el usuario inicialmente deniega el acceso, puede usar el **configuración** aplicación para conceder acceso a la aplicación.

En cualquier momento, la aplicación puede comprobar la capacidad de la aplicación para tener acceso a Siri mediante una llamada a la `SiriAuthorizationStatus` método de la `INPreferences` clase.

### <a name="localization-and-siri"></a>Localización y Siri

En un dispositivo iOS, el usuario es capaz de seleccionar un idioma para Siri que es diferente, a continuación, el valor predeterminado del sistema. Cuando se trabaja con datos localizados, la aplicación debe usar el `SiriLanguageCode` método de la `INPreferences` clase para obtener el código de idioma de Siri. Por ejemplo:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Adición de vocabulario específico del usuario

El vocabulario específico del usuario que va a proporcionar palabras o frases que son únicas para los usuarios individuales de la aplicación. Estos se proporcionará en tiempo de ejecución de la aplicación principal (no en las extensiones de aplicación) como un conjunto ordenado de términos, ordenados en una prioridad de uso más importante para los usuarios, con los términos más importantes al principio de la lista.

Vocabulario específico del usuario debe pertenecer a una de las categorías siguientes:

- Nombres de contacto (que no están administrados por el framework de contactos).
- Etiquetas de foto.
- Nombres de álbumes de fotos.
- Nombres de entrenamiento.

Al seleccionar terminología para registrar como vocabulario personalizada, elija solo los términos que podría malinterpretarse por alguien que no esté familiarizado con la aplicación. Nunca register términos comunes como "Mi entrenamiento" o "Mi álbum". Por ejemplo, la aplicación MonkeyChat registrará el alias asociados con cada contacto de la libreta de direcciones del usuario.

La aplicación proporciona el vocabulario específico del usuario mediante una llamada a la `SetVocabularyStrings` método de la `INVocabulary` clase y pasar un `NSOrderedSet` colección desde la aplicación principal. La aplicación debe llamar siempre el `RemoveAllVocabularyStrings` método primero, para quitar los términos existentes antes de agregar otros nuevos. Por ejemplo:

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

Con este código en su lugar, podría llamarse como sigue:

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
> Siri trata vocabulario personalizado como sugerencias y se incorporará como gran parte de la terminología como sea posible. Sin embargo, espacio de vocabulario personalizado es limitado, lo que es preciso registrar _sólo_ la terminología que puede resultar confusa, por lo tanto, mantener el número total de términos registrados al mínimo.

Para obtener más información, consulte nuestra [referencia vocabulario específica de usuario](~/ios/platform/sirikit/understanding-sirikit.md) y Apple [especificación de referencia de vocabulario personalizado](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

### <a name="adding-app-specific-vocabulary"></a>Adición de vocabulario específico de la aplicación

El vocabulario específico de aplicación define las palabras y frases específicas que se conocerá a todos los usuarios de la aplicación, como nombres de entrenamiento o tipos de vehículos. Dado que son parte de la aplicación, se definen en un `AppIntentVocabulary.plist` archivo como parte de la agrupación de la aplicación principal. Además, se deben localizar estas palabras y frases.

Los términos del vocabulario específico de la aplicación deben pertenecer a una de las categorías siguientes:

- Invalidar las opciones.
- Nombres de entrenamiento.

El archivo de vocabulario específico de aplicación contiene dos claves de nivel de raíz:

- `ParameterVocabularies` **Requiere** -define los términos personalizados y los parámetros de la intención de que se aplican a la aplicación.
- `IntentPhrases` **Opcional** -contiene frases de ejemplo con las condiciones personalizadas definidas en el `ParameterVocabularies`.

Cada entrada en el `ParameterVocabularies` debe especificar una cadena de identificador, el término y la intención de que el término se aplica a. Además, un único término es posible que se aplican a varios intentos.

Para obtener una lista completa de los valores aceptables y estructura de los archivos necesarios, consulte Apple [referencia del formato de archivo de aplicación vocabulario](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

Para agregar un `AppIntentVocabulary.plist` archivos al proyecto de aplicación, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga clic en el nombre del proyecto en el **el Explorador de soluciones** y seleccione **agregar** > **nuevo archivo...**   >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "Agregar una lista de propiedades")](implementing-sirikit-images/plist01.png#lightbox)
2. Haga doble clic en el `AppIntentVocabulary.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
3. Haga clic en el **+** para agregar una clave, establezca el **nombre** a `ParameterVocabularies` y **tipo** a `Array`:

    [![](implementing-sirikit-images/plist02.png "Establezca el nombre en ParameterVocabularies y el tipo de matriz")](implementing-sirikit-images/plist02.png#lightbox)
4. Expanda `ParameterVocabularies` y haga clic en el **+** botón y establezca el **tipo** a `Dictionary`:

    [![](implementing-sirikit-images/plist03.png "Establezca el tipo de diccionario")](implementing-sirikit-images/plist03.png#lightbox)
5. Haga clic en el **+** para agregar una nueva clave, establezca el **nombre** a `ParameterNames` y **tipo** a `Array`:

    [![](implementing-sirikit-images/plist04.png "Establezca el nombre en ParameterNames y el tipo de matriz")](implementing-sirikit-images/plist04.png#lightbox)
6. Haga clic en el **+** para agregar una nueva clave con el **tipo** de `String` y el valor como uno de los nombres de parámetro disponibles. Por ejemplo, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "La clave INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05.png#lightbox)
7. Agregar el `ParameterVocabulary` clave para el `ParameterVocabularies` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist06.png "Agregue la clave de ParameterVocabulary a la clave ParameterVocabularies con el tipo de matriz")](implementing-sirikit-images/plist06.png#lightbox)
8. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist07.png#lightbox)
9. Agregar el `VocabularyItemIdentifier` clave con el **tipo** de `String` y especifique un identificador único para el término:

    [![](implementing-sirikit-images/plist08.png "Agregue la clave VocabularyItemIdentifier con el tipo de cadena y especifique un identificador único")](implementing-sirikit-images/plist08.png#lightbox)
10. Agregar el `VocabularyItemSynonyms` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist09.png "Agregue la clave VocabularyItemSynonyms con el tipo de matriz")](implementing-sirikit-images/plist09.png#lightbox)
11. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist10.png#lightbox)
12. Agregar el `VocabularyItemPhrase` clave con el **tipo** de `String` y el término que está definiendo la aplicación:

    [![](implementing-sirikit-images/plist11.png "Agregue la clave VocabularyItemPhrase con el tipo de cadena y el término que se está definiendo la aplicación")](implementing-sirikit-images/plist11.png#lightbox)
13. Agregar el `VocabularyItemPronunciation` clave con el **tipo** de `String` y la pronunciación fonética del término:

    [![](implementing-sirikit-images/plist12.png "Agregue la clave VocabularyItemPronunciation con el tipo de cadena y la pronunciación fonética del término")](implementing-sirikit-images/plist12.png#lightbox)
14. Agregar el `VocabularyItemExamples` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist13.png "Agregue la clave VocabularyItemExamples con el tipo de matriz")](implementing-sirikit-images/plist13.png#lightbox)
15. Agregue algunos `String` claves con los usos de ejemplo del término:

    [![](implementing-sirikit-images/plist14.png "Agregar algunas claves de cadena con los usos de ejemplo del término")](implementing-sirikit-images/plist14.png#lightbox)
16. Repita los pasos anteriores para otros términos personalizados debe definir la aplicación.
17. Contraer el `ParameterVocabularies` clave.
18. Agregar el `IntentPhrases` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist15.png "Agregue la clave IntentPhrases con el tipo de matriz")](implementing-sirikit-images/plist15.png#lightbox)
19. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist16.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist16.png#lightbox)
20. Agregar el `IntentName` clave con el **tipo** de `String` y la intención del ejemplo:

    [![](implementing-sirikit-images/plist17.png "Agregue la clave IntentName con el tipo de cadena y la intención del ejemplo")](implementing-sirikit-images/plist17.png#lightbox)
21. Agregar el `IntentExamples` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist18.png "Agregue la clave IntentExamples con el tipo de matriz")](implementing-sirikit-images/plist18.png#lightbox)
22. Agregue algunos `String` claves con los usos de ejemplo del término:

    [![](implementing-sirikit-images/plist19.png "Agregar algunas claves de cadena con los usos de ejemplo del término")](implementing-sirikit-images/plist19.png#lightbox)
23. Repita los pasos anteriores para cualquier intenciones debe proporcionar ejemplos de uso de la aplicación.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga clic en el nombre del proyecto en el **el Explorador de soluciones** y seleccione **Agregar > nuevo elemento... > Apple > lista de propiedades > Info.plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "Agregar un nuevo Info.plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Haga doble clic en el `AppIntentVocabulary.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
3. Haga clic en el **+** para agregar una clave, establezca el **nombre** a `ParameterVocabularies` y **tipo** a `Array`:

    [![](implementing-sirikit-images/plist02w.png "Establezca el nombre en ParameterVocabularies y el tipo de matriz")](implementing-sirikit-images/plist02w.png#lightbox)
4. Expanda `ParameterVocabularies` y haga clic en el **+** botón y establezca el **tipo** a `Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "Establezca el tipo de diccionario")](implementing-sirikit-images/plist03w.png#lightbox)
5. Haga clic en el **+** para agregar una nueva clave, establezca el **nombre** a `ParameterNames` y **tipo** a `Array`:

    [![](implementing-sirikit-images/plist04w.png "Establezca el nombre en ParameterNames y el tipo de matriz")](implementing-sirikit-images/plist04w.png#lightbox)
6. Haga clic en el **+** para agregar una nueva clave con el **tipo** de `String` y el valor como uno de los nombres de parámetro disponibles. Por ejemplo, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "La clave INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05w.png#lightbox)
7. Agregar el `ParameterVocabulary` clave para el `ParameterVocabularies` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist06w.png "Agregue la clave de ParameterVocabulary a la clave ParameterVocabularies con el tipo de matriz")](implementing-sirikit-images/plist06w.png#lightbox)
8. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist07w.png#lightbox)
9. Agregar el `VocabularyItemIdentifier` clave con el **tipo** de `String` y especifique un identificador único para el término:

    [![](implementing-sirikit-images/plist08w.png "Agregue la clave VocabularyItemIdentifier con el tipo de cadena y especifique un identificador único para el término")](implementing-sirikit-images/plist08w.png#lightbox)
10. Agregar el `VocabularyItemSynonyms` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist09w.png "Agregue la clave VocabularyItemSynonyms con el tipo de matriz")](implementing-sirikit-images/plist09w.png#lightbox)
11. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist10w.png#lightbox)
12. Agregar el `VocabularyItemPhrase` clave con el **tipo** de `String` y el término que está definiendo la aplicación:

    [![](implementing-sirikit-images/plist11w.png "Agregue la clave VocabularyItemPhrase con el tipo de cadena y el término que se está definiendo la aplicación")](implementing-sirikit-images/plist11w.png#lightbox)
13. Agregar el `VocabularyItemPronunciation` clave con el **tipo** de `String` y la pronunciación fonética del término:

    [![](implementing-sirikit-images/plist12w.png "Agregue la clave VocabularyItemPronunciation con el tipo de cadena y la pronunciación fonética del término")](implementing-sirikit-images/plist12w.png#lightbox)
14. Agregar el `VocabularyItemExamples` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist13w.png "Agregue la clave VocabularyItemExamples con el tipo de matriz")](implementing-sirikit-images/plist13w.png#lightbox)
15. Agregue algunos `String` claves con los usos de ejemplo del término:

    [![](implementing-sirikit-images/plist14w.png "Agregar algunas claves de cadena con los usos de ejemplo del término")](implementing-sirikit-images/plist14w.png#lightbox)
16. Repita los pasos anteriores para otros términos personalizados debe definir la aplicación.
17. Contraer el `ParameterVocabularies` clave.
18. Agregar el `IntentPhrases` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist15w.png "Agregue la clave IntentPhrases con el tipo de matriz")](implementing-sirikit-images/plist15w.png#lightbox)
19. Agregue una nueva clave con el **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "Agregue una nueva clave con el tipo de diccionario")](implementing-sirikit-images/plist16w.png#lightbox)
20. Agregar el `IntentName` clave con el **tipo** de `String` y la intención del ejemplo:

    [![](implementing-sirikit-images/plist17w.png "Agregue la clave IntentName con el tipo de cadena y la intención del ejemplo")](implementing-sirikit-images/plist17w.png#lightbox)
21. Agregar el `IntentExamples` clave con el **tipo** de `Array`:

    [![](implementing-sirikit-images/plist18w.png "Agregue la clave IntentExamples con el tipo de matriz")](implementing-sirikit-images/plist18w.png#lightbox)
22. Agregue algunos `String` claves con los usos de ejemplo del término:

    [![](implementing-sirikit-images/plist19w.png "Agregar algunas claves de cadena con los usos de ejemplo del término")](implementing-sirikit-images/plist19w.png#lightbox)
23. Repita los pasos anteriores para cualquier intenciones debe proporcionar ejemplos de uso de la aplicación.

-----

> [!IMPORTANT]
> El `AppIntentVocabulary.plist` se registrará con Siri en la prueba de dispositivos durante el desarrollo y pueden tardar algún tiempo para Siri incorporar el vocabulario personalizado. Como resultado, el evaluador deberá esperar unos minutos antes de intentar probar la aplicación específica vocabulario cuando se ha actualizado.

Para obtener más información, consulte nuestra [referencia de aplicación específica vocabulario](~/ios/platform/sirikit/understanding-sirikit.md) y Apple [especificación de referencia de vocabulario personalizado](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

## <a name="adding-an-intents-extension"></a>Agregar una extensión de Intents

Ahora que la aplicación se ha preparado para adoptar SiriKit, el desarrollador tendrá que agregar las extensiones de las intenciones de uno (o más) a la solución para controlar las intenciones requeridas para la integración de Siri.

Para cada extensión de Intents necesario, realice lo siguiente:

- Agregar un proyecto de extensión Intents a la solución de aplicación de Xamarin.iOS.
- Configurar la extensión Intents `Info.plist` archivo.
- Modifique la clase principal de la extensión de Intents.

Para obtener más información, consulte nuestra [la referencia de extensión Intents](~/ios/platform/sirikit/understanding-sirikit.md) y Apple [crear la referencia de extensión Intents](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1).

### <a name="creating-the-extension"></a>Creación de la extensión

Para agregar una extensión Intents a la solución, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el **nombre de la solución** en el **panel de solución** y seleccione **agregar** > **Agregar nuevo proyecto...** .
2. En el cuadro de diálogo seleccione **iOS** > **extensiones** > **intención extensión** y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/intents05.png "Seleccione la extensión de intención")](implementing-sirikit-images/intents05.png#lightbox)
3. A continuación, escriba un **nombre** la intención de extensión y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/intents06.png "Escriba un nombre para la extensión de intención")](implementing-sirikit-images/intents06.png#lightbox)
4. Por último, haga clic en el **crear** para agregar la extensión de intención a la solución de aplicaciones: 

    [![](implementing-sirikit-images/intents07.png "Agregar la extensión de intención a la solución de aplicaciones")](implementing-sirikit-images/intents07.png#lightbox)
5. En el **el Explorador de soluciones**, haga doble clic en el **referencias** carpeta de la extensión de intención recién creado. Compruebe el nombre del proyecto de biblioteca de código compartido común (que la aplicación creada anteriormente) y haga clic en el **Aceptar** botón: 

    [![](implementing-sirikit-images/intents08.png "Seleccione el nombre del proyecto de biblioteca común de código compartido")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el **nombre de la solución** en el **el Explorador de soluciones** y seleccione **agregar** > **Agregar nuevo proyecto...** .
2. En el cuadro de diálogo seleccione **Visual C# > extensiones de iOS > extensión intención** y haga clic en el **siguiente** botón:

    [![](implementing-sirikit-images/intents05.w157-sml.png "Seleccione la extensión de intención")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. A continuación, escriba un **nombre** la intención de extensión y haga clic en el **Aceptar** botón.
1. En el **el Explorador de soluciones**, haga doble clic en el **referencias** carpeta de la extensión de Intents recién creado y elija **Agregar > referencia**. Compruebe el nombre del proyecto de biblioteca de código compartido común (que la aplicación creada anteriormente) y haga clic en el **Aceptar** botón:

    [![](implementing-sirikit-images/intents08w.png "Seleccione el nombre del proyecto de biblioteca común de código compartido")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Repita estos pasos para el número de extensiones de intención (según [diseñar la aplicación para las extensiones](#Architecting-the-App-for-Extensions) sección anterior) que requiera la aplicación.

### <a name="configuring-the-infoplist"></a>Configuración de Info.plist

Para cada una de las extensiones de intenciones que ha agregado a la solución de la aplicación, debe configurarse en el `Info.plist` archivos para trabajar con la aplicación.

Al igual que cualquier extensión de aplicación típicos, la aplicación tendrá las claves existentes de `NSExtension` y `NSExtensionAttributes`. Para una extensión Intents hay dos nuevos atributos que se deben configurar:

[![](implementing-sirikit-images/intents01.png "Los dos nuevos atributos que se deben configurar")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** : es necesario y consta de una matriz de nombres de clase intención que quiere que la aplicación admitirá a partir de la extensión de la intención.
- **IntentsRestrictedWhileLocked** -es una clave opcional para la aplicación especificar el comportamiento de la extensión de la pantalla de bloqueo. Consta de una matriz de nombres de clase intención que quiere que la aplicación para exigir al usuario iniciar sesión para usar de la intención de extensión.

Para configurar la extensión de intención `Info.plist` de archivos, haga doble clic en el **el Explorador de soluciones** para abrirlo y editarlo. A continuación, cambie a la **origen** ver a continuación, expanda el `NSExtension` y `NSExtensionAttributes` claves en el editor:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "Las claves NSExtension y NSExtensionAttributes en el editor")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "Las claves NSExtension y NSExtensionAttributes en el editor")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Expanda el `IntentsSupported` clave y agregue el nombre de cualquier clase intención será compatible con esta extensión. Para la aplicación de ejemplo MonkeyChat, es compatible con la `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "La clave INSendMessageIntent")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "La clave INSendMessageIntent")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Si la aplicación, opcionalmente, requiere que el usuario se ha iniciado sesión en el dispositivo para usar un propósito determinado, expanda el `IntentRestrictedWhileLocked` clave y agregue los nombres de clase de los intentos que tienen acceso restringido. Para la aplicación de ejemplo MonkeyChat, el usuario debe haber iniciado sesión para enviar un mensaje de chat, por lo que hemos agregado `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "La clave INSendMessageIntent agregada")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "La clave INSendMessageIntent agregada")](implementing-sirikit-images/intents10w.png#lightbox)

-----


Para obtener una lista completa de dominios de intención disponibles, vea Apple [intención de dominios de referencia](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configuración de la clase principal

A continuación, el desarrollador tendrá que configurar la clase principal que actúa como punto de entrada principal para la extensión de intención de Siri. Debe ser una subclase de `INExtension` que se ajusta a la `IINIntentHandler` delegar. Por ejemplo:

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

Hay un método solitario que debe implementar la aplicación en la clase principal de la extensión de intención, el `GetHandler` método. Este método se pasa un intento por SiriKit y la aplicación debe devolver un **controlador intención** que coincide con el tipo de la intención determinado.

Dado que la aplicación de ejemplo MonkeyChat solo controla una intención, se devuelve a sí mismo en el `GetHandler` método. Si la extensión controla más de un intento, el programador agrega una clase para cada tipo de intención y devolver una instancia aquí según el `Intent` pasado al método.

### <a name="handling-the-resolve-stage"></a>Control de la fase de resolución

La fase resolver es donde se aclarar y validar los parámetros de la extensión de intención pasa de Siri y se han establecido a través de la conversación del usuario.

Para cada parámetro que se envía desde Siri, hay un `Resolve` método. La aplicación debe implementar este método para cada parámetro que la aplicación podría necesitar ayuda de Siri para obtener la respuesta correcta del usuario.

En el caso de la aplicación de ejemplo MonkeyChat, la extensión de intención requerirá un uno o más destinatarios enviar el mensaje. Para cada destinatario en la lista, la extensión tendrán que realizar una búsqueda de contacto que puede tener el siguiente resultado:

- Exactamente uno se encuentran coincidencias.
- Se encuentran dos o más contactos coincidentes.
- No se encontró ningún contacto coincidente.

Además, MonkeyChat requiere contenido para el cuerpo del mensaje. Si el usuario no ha proporcionado esta, Siri debe preguntar al usuario para el contenido.

La extensión de intención deberá controlar correctamente cada uno de estos casos.


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

Para obtener más información, consulte nuestra [la referencia de fase resolver](~/ios/platform/sirikit/understanding-sirikit.md) y Apple [Resolving y referencia de control de intenciones](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1).

### <a name="handling-the-confirm-stage"></a>Control de la fase de confirmación

La fase de confirmación es donde la extensión de intención comprueba que tiene toda la información para satisfacer la solicitud del usuario. La aplicación desea enviar confirmación a lo largo de le todos los detalles de apoyo de lo que está a punto de suceder a Siri por lo que se puede confirmar con el usuario que se trata de la acción deseada.

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

Para obtener más información, consulte nuestra [la referencia de fase confirmar](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>La intención de procesamiento

Este es el punto donde la extensión de intención en realidad realiza la tarea para satisfacer la solicitud del usuario y devolver los resultados a Siri, por lo que el usuario pueda estar informado.


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

Para obtener más información, consulte nuestra [la referencia de fase controlar](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Agregar una extensión de interfaz de usuario de Intents

La extensión de interfaz de usuario de Intents opcional presenta la oportunidad de aportar la interfaz de usuario de la aplicación y personalización de marca en la experiencia de Siri y que los usuarios sientan conectado a la aplicación. Con esta extensión de la aplicación puede adoptar los marca como visual y otra información en la transcripción.

[![](implementing-sirikit-images/intentsui01.png "Un ejemplo de salida de extensión de interfaz de usuario de Intents")](implementing-sirikit-images/intentsui01.png#lightbox)

Al igual que la extensión Intents, el desarrollador realizará el siguiente paso para la extensión de interfaz de usuario de Intents:

- Agregar un proyecto de extensión de interfaz de usuario de Intents a la solución de aplicación de Xamarin.iOS.
- Configurar la extensión de interfaz de usuario de Intents `Info.plist` archivo.
- Modifique la clase principal de la extensión de interfaz de usuario de Intents.

Para obtener más información, consulte nuestra [la referencia de extensión de interfaz de usuario de intenciones](~/ios/platform/sirikit/understanding-sirikit.md) y Apple [que proporciona una referencia de la interfaz personalizada](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Creación de la extensión

Para agregar una extensión de interfaz de usuario de Intents a la solución, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el **nombre de la solución** en el **panel de solución** y seleccione **agregar** > **Agregar nuevo proyecto...** .
2. En el cuadro de diálogo seleccione **iOS** > **extensiones** > **extensión de interfaz de usuario de intención** y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/intents11.png "Seleccione la extensión de la intención de la interfaz de usuario")](implementing-sirikit-images/intents11.png#lightbox)
3. A continuación, escriba un **nombre** la intención de extensión y haga clic en el **siguiente** botón: 

    [![](implementing-sirikit-images/intents12.png "Escriba un nombre para la extensión de intención")](implementing-sirikit-images/intents12.png#lightbox)
4. Por último, haga clic en el **crear** para agregar la extensión de intención a la solución de aplicaciones: 

    [![](implementing-sirikit-images/intents13.png "Agregar la extensión de intención a la solución de aplicaciones")](implementing-sirikit-images/intents13.png#lightbox)
5. En el **el Explorador de soluciones**, haga doble clic en el **referencias** carpeta de la extensión de intención recién creado. Compruebe el nombre del proyecto de biblioteca de código compartido común (que la aplicación creada anteriormente) y haga clic en el **Aceptar** botón: 

    [![](implementing-sirikit-images/intents14.png "Seleccione el nombre del proyecto de biblioteca común de código compartido")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el **nombre de la solución** en el **el Explorador de soluciones** y seleccione **agregar** > **Agregar nuevo proyecto...**
2. En el cuadro de diálogo seleccione **iOS** > **extensiones** > **extensión de interfaz de usuario de intención** y haga clic en el **siguiente** botón.
3. A continuación, escriba un **nombre** la intención de extensión y haga clic en el **Aceptar** botón.
5. En el **el Explorador de soluciones**, haga doble clic en el **referencias** carpeta de la extensión de intención recién creado. Compruebe el nombre del proyecto de biblioteca de código compartido común (que la aplicación creada anteriormente) y haga clic en el **Aceptar** botón.
    
-----

### <a name="configuring-the-infoplist"></a>Configuración de Info.plist

Configurar la extensión de interfaz de usuario de Intents `Info.plist` archivo para trabajar con la aplicación.

Al igual que cualquier extensión de aplicación típicos, la aplicación tendrá las claves existentes de `NSExtension` y `NSExtensionAttributes`. Para una extensión Intents hay un nuevo atributo que se debe configurar:

[![](implementing-sirikit-images/intents03.png "Este atributo nuevo que se debe configurar")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** es necesaria y consta de una matriz de nombres de clase intención que desea que la aplicación admitirá a partir de la extensión de la intención.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para configurar la extensión de interfaz de usuario de intención `Info.plist` de archivos, haga doble clic en el **el Explorador de soluciones** para abrirlo y editarlo. A continuación, cambie a la **origen** ver a continuación, expanda el `NSExtension` y `NSExtensionAttributes` claves en el editor:

[![](implementing-sirikit-images/intents04.png "Las claves NSExtension y NSExtensionAttributes en el editor")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para configurar la extensión de interfaz de usuario de intención `Info.plist` de archivos, haga doble clic en el **el Explorador de soluciones** para abrirlo y editarlo. Expanda el `NSExtension` y `NSExtensionAttributes` claves en el editor:

[![](implementing-sirikit-images/intents04w.png "Tthe NSExtension y NSExtensionAttributes claves en el editor")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Expanda el `IntentsSupported` clave y agregue el nombre de cualquier clase intención será compatible con esta extensión. Para la aplicación de ejemplo MonkeyChat, es compatible con la `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "La clave INSendMessageIntent")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "La clave INSendMessageIntent")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Para obtener una lista completa de dominios de intención disponibles, vea Apple [intención de dominios de referencia](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configuración de la clase principal

Configuración de la clase principal que actúa como punto de entrada principal para la extensión de interfaz de usuario de la intención de Siri. Debe ser una subclase de `UIViewController` que se ajusta a la `IINUIHostedViewController` interfaz. Por ejemplo:

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

Siri pasará un `INInteraction` instancia de la clase a la `Configure` método de la `UIViewController` instancia dentro de la extensión de interfaz de usuario de la intención.

La `INInteraction` objeto proporciona tres piezas clave de la información a la extensión:

1. El objeto intención que se está procesando.
2. El objeto de respuesta de la intención de la `Confirm` y `Handle` métodos de extensión de la intención.
3. El estado de interacción que define el estado de la interacción entre la aplicación y Siri.

El `UIViewController` instancia es la clase de entidad de seguridad para la interacción con Siri y porque se hereda de `UIViewController`, tiene acceso a todas las características de UIKit.

Cuando llama Siri el `Configure` método de la `UIViewController` pasa en un contexto de vista que indica que el controlador de vista o bien se hospedará en una tarjeta de mapas o Siri Snippit.

También se pasará Siri en un controlador de finalización que la aplicación debe devolver el tamaño deseado de la vista una vez que la aplicación haya terminado de configurarlo.

### <a name="design-the-ui-in-ios-designer"></a>Diseñar la interfaz de usuario en el Diseñador de iOS

Diseño de interfaz de usuario de la extensión de Intents UI en el Diseñador de iOS. Haga doble clic en la extensión `MainInterface.storyboard` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo. Arrastre todos los elementos de interfaz de usuario necesarios para crear la interfaz de usuario y guarde los cambios.

> [!IMPORTANT]
> Aunque es posible agregar elementos interactivos como `UIButtons` o `UITextFields` a la extensión de interfaz de usuario de intención `UIViewController`, estos están prohibidos estrictamente como la interfaz de usuario de la intención de no interactivo y el usuario no podrá interactuar con ellos.

### <a name="wire-up-the-user-interface"></a>La interfaz de usuario de seguridad de conexión

Con la interfaz de usuario de la extensión de interfaz de usuario de Intents creado en el Diseñador de iOS, edite el `UIViewController` subclase e invalide el `Configure` método tal como se indica a continuación:

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

### <a name="overriding-the-default-siri-ui"></a>Invalidación de la interfaz de usuario de Siri predeterminado

La extensión de interfaz de usuario de Intents siempre se muestran junto con otro contenido Siri como el icono de la aplicación y el nombre en la parte superior de la interfaz de usuario o, según la intención, es posible que se muestran los botones (por ejemplo, envío o cancelar) en la parte inferior.

Hay unas pocas instancias donde la aplicación puede reemplazar la información que Siri muestra al usuario de forma predeterminada, como mensajería o asignaciones donde la aplicación puede reemplazar la experiencia predeterminada con una adaptada a la aplicación.

Si necesita invalidar los elementos del valor predeterminado Siri UI, la extensión de interfaz de usuario de Intents el `UIViewController` subclase tendrá que implementar el `IINUIHostedViewSiriProviding` interfaz y participar en la visualización de un elemento de interfaz determinado.

Agregue el código siguiente a la `UIViewController` subclase para indicar a Siri que la extensión de interfaz de usuario de intención ya muestra el contenido del mensaje:

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Consideraciones

Apple sugiere que el desarrollador tome en cuenta al diseñar e implementar las extensiones de interfaz de usuario de intención de las consideraciones siguientes:

- **Ser consciente de uso de memoria** : porque las extensiones de interfaz de usuario de intención son temporales y solo se muestra durante un breve período, el sistema impone restricciones de memoria más estrictas que se usan con una aplicación completa.
- **Considere la posibilidad de mínimo y máximo de los tamaños de vista** -Asegúrese de que las extensiones de interfaz de usuario de intención parece buena sobre cada tipo de dispositivo de iOS, tamaño y orientación. Además, es posible que el tamaño deseado que la aplicación envía a Siri no pueda tener.
- **Usar Flexible y patrones de diseño adaptable** : una vez más, para asegurarse de que la interfaz de usuario tiene un aspecto excelente en todos los dispositivos.

## <a name="summary"></a>Resumen

En este artículo ha cubierto SiriKit y se muestra cómo se puede agregar a las aplicaciones de Xamarin.iOS para proporcionar servicios que son accesibles para el usuario mediante Siri y la aplicación de mapas de un dispositivo iOS.




## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referencia de Framework de intenciones](https://developer.apple.com/reference/intents)
- [Referencia de Framework de la interfaz de usuario de intenciones](https://developer.apple.com/reference/intentsui)
- [Referencia de la intención de dominios](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
