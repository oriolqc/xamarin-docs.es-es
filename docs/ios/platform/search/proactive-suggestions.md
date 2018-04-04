---
title: Introducción a sugerencias automático
description: Este artículo muestra cómo utilizar sugerencias automático en la aplicación de Xamarin.iOS para la interacción de unidad al permitir que el sistema de forma proactiva presentar automáticamente información útil al usuario.
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 5b06dbf0e8e108616adb4f77910267aaa1ac71f4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-proactive-suggestions"></a>Introducción a sugerencias automático

_Este artículo muestra cómo utilizar sugerencias automático en la aplicación de Xamarin.iOS para la interacción de unidad al permitir que el sistema de forma proactiva presentar automáticamente información útil al usuario._

Nuevo para iOS 10, formas de noticias presente sugerencias automático para que los usuarios ponerse en contacto con una aplicación de Xamarin.iOS proactivamente presente información útil automáticamente al usuario en los momentos adecuados.

iOS 10 presenta nuevas formas de contratación conducir a la aplicación al permitir que el sistema de forma proactiva presentar información útil automáticamente al usuario en los momentos adecuados. Al igual que iOS 9 proporciona la capacidad de Agregar búsqueda en profundidad la aplicación con servicios, entrega y Siri sugerencias (consulte [nuevas API de búsqueda](~/ios/platform/search/index.md)), con iOS 10 una aplicación puede exponer la funcionalidad que se puede presentar al usuario por el sistema desde el ubicaciones siguientes:

- El selector de la aplicación
- La pantalla de bloqueo
- CarPlay
- Asignaciones
- Interacciones de Siri
- Sugerencias de QuickType

La aplicación expone esta funcionalidad en el sistema mediante un conjunto de tecnologías como `NSUserActivity`, marcado web, servicios de núcleo, MapKit, el Reproductor de Media y UIKit. Además, al proporcionar soporte de sugerencia automático de la aplicación, obtiene una integración más profunda Siri de forma gratuita.

## <a name="location-based-suggestions"></a>Sugerencias basadas en ubicación

Nuevo en iOS 10, la `NSUserActivity` clase incluye una `MapItem` propiedad que permite al desarrollador proporcionar información de ubicación que se puede usar en otros contextos. Por ejemplo, si la aplicación muestra opiniones sobre restaurantes, el desarrollador puede establecer el `MapItem` propiedad a la ubicación del restaurante que está viendo el usuario en la aplicación. Si el usuario cambia a la aplicación de mapas, ubicación del restaurante está automáticamente disponible.

Si la aplicación admite la búsqueda de la aplicación, puede usar los nuevos componentes de la dirección de la `CSSearchableItemAttributesSet` clase para especificar las ubicaciones que el usuario que desee visitar. Estableciendo la `MapItem` propiedad, las demás propiedades son rellena de forma automática.

Además de establecer el `Latitude` y `Longitude` de las propiedades de componente de dirección, se recomienda que la aplicación proporcione la `NamedLocation` y `PhoneNumbers` propiedades demasiado, por lo que Siri puede iniciar una llamada a la ubicación.

## <a name="web-markup-based-suggestions"></a>Sugerencias basadas en Web marcado

9 agregado a la capacidad de incluir el marcado de los datos estructurados en el sitio Web que enriquece el contenido que los usuarios ven en los resultados de búsqueda de Spotlight y Safari de iOS (consulte [búsqueda con marcado Web](~/ios/platform/search/web-markup.md)). iOS 10 agrega la posibilidad de incluir marcado basada en ubicación (como [la tabla](http://schema.org/PostalAddress) tal como se define por [Schema.org](http://schema.org/)) para mejorar aún más la experiencia del usuario. Por ejemplo, si una vista de los usuarios una ubicación marcado como página en el sitio Web, el sistema puede sugerir la misma ubicación cuando abran mapas.

## <a name="text-based-suggestions"></a>Sugerencias basadas en texto

Se ha expandido UIKit en iOS 10 para incluir la [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) propiedad de la [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) clase para especificar el significado semántico del contenido en un área de texto. Con esta información en su lugar, el sistema puede seleccionar normalmente automáticamente el tipo de teclado correspondiente, mejorar las sugerencias de Autocorrección e integrar proactivamente información de otras aplicaciones y sitios Web.

Por ejemplo, si el usuario escribe texto en un campo de texto marcado `UITextContentType.FullStreetAddress`, el sistema puede sugerir rellenado automáticamente el campo con la ubicación que el usuario estaba viendo recientemente.

## <a name="media-based-suggestions"></a>Sugerencias basadas en medios

Si la aplicación reproduce medios mediante el [MPPlayableContentManager](https://developer.xamarin.com/api/type/MediaPlayer.MPPlayableContentManager/) API, iOS 10 permite a los usuarios ver las carátulas de álbum y reproducir archivos multimedia a través de la aplicación en la pantalla de bloqueo.

## <a name="contextual-siri-reminders"></a>Avisos de Siri contextuales

Permite al usuario usar Siri para realizar rápidamente un aviso para ver el contenido que están viendo actualmente en la aplicación en una fecha posterior. Por ejemplo, si una revisión de restaurantes estaban viendo en la aplicación, ya que podrían invocar Siri y diga *"Recordármelo sobre esto cuando llegue a casa."* Siri generaría el recordatorio con un vínculo a la revisión en la aplicación.

## <a name="contact-based-suggestions"></a>Sugerencias basadas en Póngase en contacto con

Permite que la aplicación contactos (y póngase en contacto con información relacionada) que aparezca en el **póngase en contacto con** aplicación en el dispositivo iOS junto con todos los contactos de los usuarios almacenados en el sistema.

## <a name="ride-sharing-based-suggestions"></a>Invalidar sugerencias en función de uso compartido

Si una aplicación de uso compartido de transporte usa el [MKDirectionsRequest](https://developer.xamarin.com/api/type/MapKit.MKDirectionsRequest/) API, iOS 10 presentará como una opción en el selector de la aplicación a veces cuando el usuario es probable que se desee el transporte. La aplicación también debe registrarse como una aplicación de uso compartido de conducción especificando el `MKDirectionsModeRideShare` para el [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) clave en su `Info.plist` archivo.

Si la aplicación solo admite el uso compartido de transporte, la sugerencia de sistema comenzaría con *"Conseguir transporte para..."*, si se admiten otros tipos de dirección de enrutamiento (por ejemplo, paseo o bicicleta), el sistema usará *"Obtener direcciones para..."*

> [!IMPORTANT]
> El [MKMapItem](https://developer.xamarin.com/api/type/MapKit.MKMapItem/) objeto que recibe la aplicación no se puede incluir la información de longitud y latitud y requerirá codificación geográfica.

## <a name="implementing-proactive-suggestions"></a>Implementación de sugerencias automático

Agregando la sugerencia automático compatibilidad a una aplicación de Xamarin.iOS es normalmente tan fácil como implementar algunas API o expandir en algunas API que ya se puede implementar la aplicación.

Sugerencias automático funcionan con las aplicaciones de tres maneras principales:

- **`NSUserActivity` y Schema.org**  -  `NSUserActivity` ayuda a que el sistema entender la información que el usuario está trabajando actualmente con pantalla. Schema.org agrega capacidades similares a las páginas web.
- **Sugerencias de ubicación** : si la aplicación ofrece o utiliza información de ubicación según, estas formas nuevas de la oferta de extensión API para compartir esta información entre aplicaciones.
- **Sugerencias de la aplicación de medios** : el sistema puede aumentar el nivel de la aplicación y su contenido multimedia en función del contexto de la interacción del usuario con el dispositivo iOS.

Y es compatible con la aplicación mediante la implementación de los siguientes:

- **Entrega**  -  `NSUserActivity` se agregó en iOS 8 para admitir la entrega, que permite al desarrollador iniciar una actividad en un dispositivo, a continuación, continúe en otro (vea [Introducción a la entrega](~/ios/platform/handoff.md)).
- **Noticias destacadas búsqueda** -iOS 9 agrega la capacidad para promover el contenido de las aplicaciones desde los resultados de la búsqueda de Spotlight con `NSUserActivity` (consulte [búsqueda de Spotlight Core](~/ios/platform/search/corespotlight.md)).
- **Contextual recordatorios de Siri** : en iOS 10, `NSUserActivity` se ha ampliado para permitir que Siri crear rápidamente un aviso para ver el contenido, el usuario está viendo actualmente en la aplicación en una fecha posterior.
- **Sugerencias de ubicación** -iOS 10 mejora `NSUserActivity` para capturar ubicaciones ver dentro de la aplicación y promoverlos en muchos lugares en todo el sistema.
- **Solicitudes de Siri contextual**  -  `NSUserActivity` proporciona contexto para la información mostrada Siri dentro de la aplicación para que el usuario pueda obtener direcciones o en lugar de ser una llamada Siri invocar desde dentro de la aplicación.
- **Póngase en contacto con interacciones** : nuevo en 10, iOS `NSUserActivity` permite las aplicaciones de las comunicaciones se promueva de una tarjeta de contacto (en la aplicación de contactos) como un método alternativo de comunicación.

Todas estas características tienen algo en común, todas utilizan `NSUserActivity` en un formulario u otro para proporcionar su funcionalidad. 

## <a name="nsuseractivity"></a>NSUserActivity

Como se indicó anteriormente, `NSUserActivity` ayuda a que el sistema entender la información que el usuario está trabajando actualmente con pantalla. `NSUserActivity` un estado atenuado almacena en caché mecanismo para capturar la actividad del usuario mientras navegan por la aplicación. Por ejemplo, si en una aplicación de restaurante:

[![](proactive-suggestions-images/activity02.png "El estado de ligera NSUserActivity mecanismo el almacenamiento en caché")](proactive-suggestions-images/activity02.png#lightbox)

Con las siguientes interacciones:

1. Cuando el usuario trabaja con la aplicación, un `NSUserActivity` se crea para volver a crear el estado de la aplicación más tarde.
2. Si el usuario busca un restaurante, se sigue el mismo patrón de creación de actividades.
3. Y, de nuevo, cuando el usuario ve un resultado. En este último caso, el usuario está viendo una ubicación y en iOS 10, el sistema es más consciente de ciertos conceptos (por ejemplo, las interacciones de ubicación o comunicación).

Eche un vistazo más de cerca en la última pantalla:

[![](proactive-suggestions-images/activity03.png "Los detalles de NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Aquí es la creación de la aplicación un `NSUserActivity` y se ha rellenado con información para volver a crear el estado más tarde. La aplicación también incluye algunos metadatos como el nombre y la dirección de la ubicación. Con esta actividad creada, la aplicación permite iOS saber que representa el estado del usuario actual.

La aplicación, a continuación, decide si la actividad se anuncian por aire para la entrega, guarda como un valor temporal para obtener sugerencias de ubicación o se agregan al índice de servicios en el dispositivo para mostrar en los resultados de la búsqueda.

Para obtener más información sobre la búsqueda de Spotlight y de entrega, vea nuestra [Introducción a la entrega](~/ios/platform/handoff.md) y [iOS 9 nuevas API de búsqueda](~/ios/platform/search/index.md) guías.

### <a name="creating-an-activity"></a>Creación de una actividad

Antes de crear una actividad, un identificador de tipo de actividad necesitará crearse para identificarlo. El identificador de tipo de actividad es una cadena corta que se agrega a la `NSUserActivityTypes` matriz de la aplicación `Info.plist` archivo usado para identificar de forma única un determinado tipo de actividad de usuario. Habrá una entrada en la matriz por cada actividad que es compatible con la aplicación y se expone en la búsqueda de la aplicación. Consulte nuestro [crear la referencia de los identificadores de tipo de actividad](~/ios/platform/search/nsuseractivity.md) para obtener más detalles.

Un vistazo a un ejemplo de una actividad:

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Inform system of Activity
activity.BecomeCurrent();
```

Se crea una nueva actividad con un identificador de tipo de actividad. A continuación, se crean algunos metadatos que definen la actividad para que este estado se puede restaurar en una fecha posterior. A continuación, la actividad se asignó un título descriptivo y adjunta a la información de usuario. Por último, algunas capacidades se habilitan y se envía la actividad en el sistema.

El código anterior se puede mejorar aún más para incluir los metadatos que proporciona el contexto para la actividad mediante la realización de los siguientes cambios:

```csharp
...

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Inform system of Activity
activity.BecomeCurrent();
```

Si el desarrollador tiene un sitio Web que es capaz de mostrar la misma información que la aplicación, la aplicación puede contener la dirección URL y el contenido se puede mostrar en otros dispositivos que no tienen la aplicación instalada (a través de entrega):

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Restauración de una actividad

Para responder al usuario puntee en un resultado de búsqueda (`NSUserActivity`) para la aplicación, editar la **AppDelegate.cs** archivo e invalide el `ContinueUserActivity` método. Por ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

El desarrollador debe asegurarse de esto es el mismo identificador de tipo de actividad (`com.xamarin.platform`) como la actividad que creó anteriormente. La aplicación usa la información almacenada en el `NSUserActivity` para restaurar el estado en donde se dejó el usuario.

### <a name="benefits-of-creating-an-activity"></a>Ventajas de la creación de una actividad

Con la mínima cantidad de código presentado anteriormente, la aplicación es ahora pueden aprovechar de tres nuevas características de iOS 10:

- **Handoff**
- **Búsqueda de Spotlight**
- **Avisos de Siri contextuales**

En la siguiente sección se echa un vistazo a la hora de habilitar dos otras nuevas características de iOS 10:

- **Sugerencias de ubicación**
- **Solicitudes de Siri contextuales**

### <a name="location-based-suggestions"></a>Sugerencias basadas en ubicación 

Tome como ejemplo de la aplicación de búsqueda de restaurante anterior. Si ha implementado `NSUserActivity` y rellena correctamente todos los metadatos y atributos, el usuario podrá hacer lo siguiente:

1. Buscar un restaurante en la aplicación que les gustaría usar para cumplir con un amigo en.
2. Cuando el usuario se mueve fuera de la aplicación mediante el selector de la aplicación de multitarea, el sistema mostrará automáticamente una sugerencia (en la parte inferior de la pantalla) para obtener instrucciones al restaurante con su aplicación de navegación favoritos.
3. Si el usuario cambia a la aplicación de mensajes y comience a escribir *"Vamos a cumplir en"*, el teclado QuickType le sugerirá automáticamente pegarlo en la dirección del restaurante.
4. Si el usuario cambia a la aplicación de mapas, dirección del restaurante se sugiere automáticamente como un destino.
5. Esto funciona incluso para las aplicaciones de terceros 3rd (que admiten `NSUserActivity`), por lo que el usuario puede cambiar a una aplicación de uso compartido de viaje y automáticamente la dirección del restaurante se sugiere así como un destino no existe.
6. También proporciona contexto al Siri, por lo que el usuario puede invocar Siri dentro de la aplicación del restaurante y pida *"Obtener direcciones..."* y Siri le proporcionará instrucciones al restaurante el usuario está viendo.

Toda la funcionalidad anterior tiene algo en común, todos ellos indicarán donde procede originalmente la sugerencia de. En el caso del ejemplo anterior, es la aplicación de revisión de restaurantes ficticios.

iOS 10 se ha mejorado para habilitar esta funcionalidad en una aplicación a través de varias pequeñas modificaciones y adiciones a marcos existentes:

- `NSUserActivity` tiene campos adicionales para capturar la información de ubicación que se ve dentro de la aplicación.
- Se realizaron varias adiciones a MapKit y CoreSpotlight para capturar la ubicación.
- Se ha agregado la funcionalidad compatible con la ubicación Siri, mapas, teclados, multitarea y otras aplicaciones en el sistema.

Para implementar las sugerencias de ubicación según, comience con el mismo código de actividad presentado anteriormente:

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");

// Inform system of Activity
activity.BecomeCurrent();
```

Si la aplicación está utilizando MapKit, resulta tan sencillo como la adición de la asignación actual `MKMapItem` a la actividad:

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Si la aplicación no está utilizando MapKit, puede adoptar la búsqueda de la aplicación y especificar los nuevos atributos para la ubicación siguientes:

```csharp
// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
...

attributes.NamedLocation = "Apple Inc.";
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

Eche un vistazo en el código anterior en detalle. En primer lugar, se requiere el nombre de la ubicación en todos los casos:

```csharp
attributes.NamedLocation = "Apple Inc.";
```

A continuación, la descripción de texto en función de la ubicación es necesaria para las instancias de basado en texto (por ejemplo, el teclado QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitud y longitud son opcionales, pero asegúrese de que el usuario se enruta a la ubicación exacta de que la aplicación desea enviarlos, por lo que se debe incluir:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Al establecer los números de teléfono, la aplicación puede obtener acceso a Siri por lo que el usuario puede invocar Siri desde la aplicación diciendo algo parecido, *"Llamar a esta ubicación"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por último, la aplicación puede indicar si la instancia es adecuada para la navegación y llamadas de teléfono:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Implementar interacciones contacto

Nuevo en 10 de iOS, aplicaciones de comunicación se integran plenamente en la aplicación de contactos de la tarjeta de contacto. Para las aplicaciones que han implementado las interacciones de contacto, el usuario puede agregar información de la aplicación dada a personas concretas en sus contactos. Y si, por ejemplo, pulse el botón de acción rápida en la parte superior de una tarjeta para enviar un mensaje, la aplicación adjunta aparecerá como una opción para enviar el mensaje.

Si se selecciona una aplicación de 3, puede recordar y presentado como el modo predeterminado para el mensaje a la persona dada la próxima vez que el usuario desea volver a ponerse en contacto con ellos.

Interacciones contacto se implementan en la aplicación con `NSUserActivity` y el nuevo marco de calidades introducidas en iOS 10. Para obtener más información sobre cómo trabajar con propósitos, visite nuestro [descripción de conceptos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) y [SiriKit implementar](~/ios/platform/sirikit/implementing-sirikit.md) guías.

#### <a name="donating-interactions"></a>Donación de interacciones

Eche un vistazo a cómo la aplicación puede donar interacciones:

[![](proactive-suggestions-images/activity04.png "Información general de las interacciones de donación")](proactive-suggestions-images/activity04.png#lightbox)

La aplicación crea un `INInteraction` objeto que contiene un **intención** (`INIntent`), **participantes** y **metadatos**. El **intención** representa una acción del usuario, como realizar una llamada de vídeo o enviar un mensaje de texto. El **participantes** incluyen las personas que reciben la comunicación. El **metadatos** define información adicional, como enviar correctamente el mensaje, etcetera.

El desarrollador nunca crea directamente una instancia de `INIntent` o `INIntentResponse`, usará una de las clases de secundarios específicos (basadas en la tarea de la aplicación es llevar a cabo en nombre del usuario) que heredan de estas clases primarias. Por ejemplo, `INSendMessageIntent` y `INSendMessageIntentResponse` para enviar un mensaje de texto. 

Una vez completa la interacción, llame a la `DonateInteraction` método para informar al sistema que la interacción está disponible para su uso.

Cuando el usuario interactúa con la aplicación de la tarjeta de contacto, obtiene incluirse la interacción con un `NSUserActivity`, que, a continuación, se usa para iniciar la aplicación:

[![](proactive-suggestions-images/activity05.png "Obtiene incluirse la interacción con un NSUserActivity que se usa para iniciar la aplicación")](proactive-suggestions-images/activity05.png#lightbox)

Eche un vistazo en el siguiente ejemplo de un intento de mensaje de envío:

```csharp
using System;
using Foundation;
using UIKit;
using Intents;

namespace MonkeyNotification
{
    public class DonateInteraction
    {
        #region Constructors
        public DonateInteraction ()
        {
        }
        #endregion

        #region Public Methods
        public void SendMessageIntent (string text, INPerson from, INPerson[] to)
        {

            // Create App Activity
            var activity = new NSUserActivity ("com.xamarin.message");

            // Define details
            var info = new NSMutableDictionary ();
            info.Add (new NSString ("message"), new NSString (text));

            // Populate Activity
            activity.Title = "Sent MonkeyChat Message";
            activity.UserInfo = info;

            // Enable capabilities
            activity.EligibleForSearch = true;
            activity.EligibleForHandoff = true;
            activity.EligibleForPublicIndexing = true;

            // Inform system of Activity
            activity.BecomeCurrent ();

            // Create message Intent
            var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);

            // Create Intent Reaction
            var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);

            // Create interaction
            var interaction = new INInteraction (intent, response);

            // Donate interaction to the system
            interaction.DonateInteraction ((err) => {
                // Handle donation error
                ...
            });
        }
        #endregion
    }
}
```

Se trata de este código en detalle, crea y rellena una instancia de `NSUserActivity` (como se muestra en el [creación de una actividad](#Creating-an-Activity) sección anterior). A continuación, crea una instancia de `INSendMessageIntent` (que hereda de `INIntent`) y lo rellena con los detalles del mensaje que se va a enviar:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

Un `INSendMessageIntentResponse` se crea y se pasa el `NSUserActivity` creado anteriormente:

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

Un `INInteraction` se crea a partir de la intención del mensaje de envío (`INSendMessageIntent`) y respuesta (`INSendMessageIntentResponse`) acaba de crear:

```csharp
var interaction = new INInteraction (intent, response);
```

Por último, el sistema es la notificación de la interacción entre:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

Se pasó un controlador de finalización en donde la aplicación puede responder a la donación finalizará correctamente o no.

### <a name="activities-best-practices"></a>Prácticas recomendadas de actividades

Apple sugiere las siguientes prácticas recomendadas al trabajar con actividades:

- Use `NeedsSave` para las actualizaciones de la carga diferida.
- Asegúrese de mantener una referencia segura a la actividad actual.
- Transferencia de pequeñas cargas que incluyen suficiente información para restaurar el estado.
- Asegúrese de que los identificadores de tipo de actividad único y descriptivo usando la notación DNS inversa para especificarlos. 

## <a name="schemaorg"></a>Schema.org

Como se indicó anteriormente, `NSUserActivity` ayuda a que el sistema entender la información que el usuario está trabajando actualmente con pantalla. Schema.org agrega capacidades similares a las páginas web.

Schema.org puede proporcionar los mismos tipos de interacciones de ubicación basándose en el sitio Web. Apple diseñado las sugerencias de ubicación nueva para que funcione igual de bien cuando se ve en Safari como lo hacen en una aplicación nativa.

Algunos antecedentes Schema.org:

- Proporciona un estándar de vocabulario de marcado de web abierta.
- Funciona mediante la inclusión de metadatos estructurados en páginas web.
- Hay más de 500 esquemas que representan los distintos conceptos disponibles.
- Mediante la implementación, en el sitio Web, el desarrollador puede adquirir algunas de las ventajas del uso de `NSUserActivity` en una aplicación nativa.

Los esquemas están organizados en un árbol como estructura, donde los tipos específicos como *restaurante*, heredar de tipos más genéricos como *empresa Local*. Para obtener más información, vea [Schema.org](http://schema.org).

Por ejemplo, si la página web incluye los siguientes datos:

```xml
<script type="application/ld+json>
{
    "@context":"http://schema.org",
    "@type":"Restaurant",
    "telephone":"(415) 781-1111",
    "url":"https://www.yanksing.com",
    "address":{
        "@type":"PostalAddress",
        "streetAddress":"101 Spear St",
        "addressLocality":"San Francisco",
        "postalCode":"94105",
        "addressRegion":"CA"
    },
    "aggregateRating":{
        "@type":"AggregateRating",
        "ratingValue":"3.5",
        "reviewCount":"2022"
    }
}
</script>
```

Si el usuario visitó esta página en Safari y, a continuación, cambia a otra aplicación, la información de ubicación de la página se capturan y se ofrece como una sugerencia de ubicación en otras partes del sistema (como se muestra en las actividades anteriores).

Safari extraerá nada en una página web que se adhiere a cualquiera de las propiedades de esquema siguiente:

- **PostalAddress**
- **GeoCoordinates**
- Una propiedad de teléfono.

Para obtener más información, vea nuestra [búsqueda con marcado Web](~/ios/platform/search/web-markup.md) guía.

## <a name="consuming-location-suggestions"></a>Consumir sugerencias de ubicación

En esta sección se tratará consumiendo sugerencias de ubicación que proceden de otras partes del sistema (por ejemplo, la aplicación de asignaciones) o con otras aplicaciones de terceros 3rd.

Hay dos formas principales de que la aplicación pueda utilizar sugerencias de ubicación:

- A través del teclado QuickType.
- Directamente al consumir la información de ubicación en las aplicaciones de enrutamientos.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>Sugerencias de ubicación y el teclado QuickType

Si la aplicación trabaja con direcciones en formatos de texto en función, la aplicación puede aprovechar las ventajas de sugerencias de ubicación mediante la UI de QuickType. iOS 10 expande la UI QuickType con las siguientes características:

- La aplicación puede agregar sugerencias acerca de la intención semántica para campos de texto en la interfaz de usuario.
- La aplicación puede obtener sugerencias automático en la aplicación.
- La aplicación puede beneficiarse de Autocorrección mejorada.

El nuevo `TextContentType` propiedad de los controles de campo de texto de iOS 10 permite al desarrollador definir la intención semántica para el valor que el usuario se va a escribir en un campo determinado. Por ejemplo:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Indicaría hasta que el sistema que la aplicación espera que el usuario escriba una dirección completa en el campo determinado. Esto permitirá que el teclado QuickType para proporcionar sugerencias de ubicación de manera automática en el teclado cuando el usuario escribe un valor en este campo.

Los siguientes son algunos de los tipos más comunes disponibles para el desarrollador en la `UITextContentType` clase estática:

* `Name`
* `GivenName`
* `FamilyName`
* `Location`
* `FullStreetAddress`
* `AddressCityAndState`
* `TelephoneNumber`
* `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>Aplicaciones de enrutamientos y sugerencias de ubicaciones

En esta sección se echa un vistazo a la hora de consumir sugerencias de ubicación directamente desde dentro de una aplicación de enrutamiento. Para que la aplicación de enrutamiento agregar esta funcionalidad, el desarrollador aprovecharán existente `MKDirectionsRequest` marco de trabajo como se indica a continuación:

- Para promover la aplicación en la multitarea.
- Para registrar la aplicación como una aplicación de enrutamiento.
- Para controlar iniciar la aplicación con un MapKit `MKDirectionsRequest` objeto.
- Ofrecer a iOS la capacidad para obtener información acerca de la aplicación para el usuario en los momentos oportunos, según la interacción del usuario.

Cuando se inicia la aplicación con un MapKit `MKDirectionsRequest` objeto, debe iniciar automáticamente lo que proporciona las instrucciones de usuario a la ubicación solicitada, o presentar una interfaz de usuario que hace más fácil para que el usuario empiece a obtener direcciones. Por ejemplo:


```csharp
using System;
using Foundation;
using UIKit;
using MapKit;
using CoreLocation;

namespace MonkeyChat
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate, IUISplitViewControllerDelegate
    {
        ...
        
        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            if (MKDirectionsRequest.IsDirectionsRequestUrl (url)) {
                var request = new MKDirectionsRequest (url);
                var coordinate = request.Destination?.Placemark.Location?.Coordinate;
                var address = request.Destination.Placemark.AddressDictionary;
                if (coordinate.IsValid()) {
                    var geocoder = new CLGeocoder ();
                    geocoder.GeocodeAddress (address, (place, err) => {
                        // Handle the display of the address

                    });
                }
            }

            return true;
        }
    }       
}
```

Eche un vistazo a este código en detalle. Comprueba para ver si se trata de una solicitud de destino válida:

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

Si lo es, crea un `MKDirectionsRequest` desde la dirección URL:

```csharp
var request = new MKDirectionsRequest(url);
```

Nuevo en iOS 10, la aplicación se puede enviar una dirección que no tiene coordenadas de replicación geográfica, en las que hacen que el desarrollador debe codificar la dirección:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>Sugerencias de la aplicación de medios

Si la aplicación controla cualquier tipo de medio como una aplicación podcast o un transmisión por secuencias contenido multimedia como audio o vídeo, con iOS 10, puede sacar partido de sugerencias de medios en el sistema.

Para las aplicaciones que controlan la media, iOS es compatible con los siguientes comportamientos:

- iOS promueve aplicaciones que el usuario es probable que use en función de su comportamiento anterior.
- Se presentan sugerencias relacionadas con la aplicación en servicios y la vista de hoy en día.
- Si la aplicación cumple uno de los siguientes desencadenadores, podría ser elevada a una sugerencia de pantalla de bloqueo:
    - Después de conectarse a auriculares o un dispositivo Bluetooth, realiza una conexión.
    - Después de obtener en un automóvil.
    - Después de que llegan en el hogar o trabajo. 

Al incluir una llamada de API sencilla en iOS 10, el programador puede crear una experiencia de pantalla de bloqueo más atractiva para los usuarios de la aplicación de medios. Mediante el uso de la `MPPlayableContentManager` clase para administrar la reproducción de multimedia, los controles de medios completos (por ejemplo, aquellas presentada por la aplicación de música) se mostrará en la pantalla de bloqueo de la aplicación.


```csharp
using System;
using MediaPlayer;
using UIKit;

namespace MonkeyPlayer
{
    public class PlayableContentDelegate : MPPlayableContentDelegate
    {
        #region Constructors
        public PlayableContentDelegate ()
        {
        }
        #endregion

        #region Override methods
        public override void InitiatePlaybackOfContentItem (MPPlayableContentManager contentManager, Foundation.NSIndexPath indexPath, Action<Foundation.NSError> completionHandler)
        {
            // Access the media item to play
            var item = LoadMediaItem (indexPath);

            // Populate the lock screen
            PopulateNowPlayingItem (item);

            // Prep item to be played
            var status = PreparePlayback (item);

            // Call completion handler
            completionHandler (null);
        }
        #endregion

        #region Public Methods
        public MPMediaItem LoadMediaItem (Foundation.NSIndexPath indexPath)
        {
            var item = new MPMediaItem ();

            // Load item from media store
            ...

            return item;
        }

        public void PopulateNowPlayingItem (MPMediaItem item)
        {
            // Get Info Center and album art
            var infoCenter = MPNowPlayingInfoCenter.DefaultCenter;
            var albumArt = (item.Artwork == null) ? new MPMediaItemArtwork (UIImage.FromFile ("MissingAlbumArt.png")) : item.Artwork;

            // Populate Info Center
            infoCenter.NowPlaying.Title = item.Title;
            infoCenter.NowPlaying.Artist = item.Artist;
            infoCenter.NowPlaying.AlbumTitle = item.AlbumTitle;
            infoCenter.NowPlaying.PlaybackDuration = item.PlaybackDuration;
            infoCenter.NowPlaying.Artwork = albumArt;
        }

        public bool PreparePlayback (MPMediaItem item)
        {
            // Prepare media item for playback
            ...

            // Return results
            return true;
        }
        #endregion
    }
}
```

## <a name="summary"></a>Resumen

En este artículo ha tratado sugerencias automático y se ha explicado cómo el programador puede usarlos para tráfico de unidad a la aplicación de Xamarin.iOS. Trata el paso para implementar sugerencias automático y presentan las instrucciones de uso.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
