---
title: Introducción a las sugerencias proactivas en Xamarin.iOS
description: Este artículo muestra cómo usar sugerencias proactivas en la aplicación de Xamarin.iOS para generar participación permitiendo que el sistema de forma proactiva presentar automáticamente información útil al usuario.
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2ab0147f918b36dc47ef6eed7d9bf1b6295d9733
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870409"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>Introducción a las sugerencias proactivas en Xamarin.iOS

_Este artículo muestra cómo usar sugerencias proactivas en la aplicación de Xamarin.iOS para generar participación permitiendo que el sistema de forma proactiva presentar automáticamente información útil al usuario._

Nuevo en iOS 10 y formas de noticias presente sugerencias proactivas para que los usuarios interactúen con una aplicación Xamarin.iOS presente de forma proactiva información útil automáticamente al usuario en los momentos adecuados.

iOS 10 presenta nuevas formas de conducción engagement a la aplicación al permitir que el sistema de forma proactiva presentar información útil automáticamente al usuario en los momentos adecuados. Igual que iOS 9 proporciona la capacidad de agregar una búsqueda en profundidad a la aplicación mediante Spotlight, entrega y sugerencias de Siri (consulte [nuevas API de búsqueda](~/ios/platform/search/index.md)), con iOS 10 una aplicación puede exponer la funcionalidad que se puede presentar al usuario por el sistema desde el las siguientes ubicaciones:

- El modificador de la aplicación
- La pantalla de bloqueo
- CarPlay
- Asignaciones
- Interacciones de Siri
- Sugerencias de QuickType

La aplicación expone esta funcionalidad al sistema mediante un conjunto de tecnologías como `NSUserActivity`, marcado web, Core Spotlight, MapKit, Media Player y UIKit. Además, al proporcionar compatibilidad con sugerencias proactivas para la aplicación, obtiene una integración más profunda Siri de forma gratuita.

## <a name="location-based-suggestions"></a>Sugerencias basadas en ubicación

Nuevo en iOS 10 y el `NSUserActivity` clase incluye un `MapItem` propiedad que permite al desarrollador proporcionar información de ubicación que se puede usar en otros contextos. Por ejemplo, si la aplicación muestra opiniones sobre restaurantes, el desarrollador puede establecer el `MapItem` propiedad a la ubicación del restaurante que el usuario está viendo en la aplicación. Si el usuario cambia a la aplicación de mapas, la ubicación del restaurante está automáticamente disponible.

Si la aplicación admite la búsqueda de la aplicación, pueden usar los nuevos componentes de la dirección de la `CSSearchableItemAttributesSet` clase para especificar las ubicaciones que el usuario que desee visitar. Estableciendo el `MapItem` propiedad, las demás propiedades son en rellenar automáticamente.

Además de establecer el `Latitude` y `Longitude` de las propiedades de componente de dirección, se recomienda que la aplicación proporcione la `NamedLocation` y `PhoneNumbers` propiedades demasiado, por lo que Siri puede iniciar una llamada a la ubicación.

## <a name="web-markup-based-suggestions"></a>Sugerencias basadas en marcado Web

iOS 9 que se agrega a la posibilidad de incluir marcado datos estructurados en el sitio Web que enriquece el contenido que ven los usuarios en los resultados de búsqueda de Spotlight y Safari (consulte [búsqueda con marcado Web](~/ios/platform/search/web-markup.md)). iOS 10 agrega la posibilidad de incluir marcado basado en la ubicación (como [PostalAddress](http://schema.org/PostalAddress) tal como se define por [Schema.org](http://schema.org/)) para mejorar aún más la experiencia del usuario. Por ejemplo, si una vista de los usuarios una ubicación marcada como página en el sitio Web, el sistema puede sugerir la misma ubicación cuando abran mapas.

## <a name="text-based-suggestions"></a>Sugerencias basadas en texto

Se ha ampliado UIKit en iOS 10 a fin de incluir la [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) propiedad de la [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) clase para especificar el significado semántico del contenido en un área de texto. Con esta información en su lugar, puede seleccionar normalmente automáticamente el tipo de teclado correspondiente en el sistema, mejorar las sugerencias de Autocorrección y proactivamente integrar información desde otras aplicaciones y sitios Web.

Por ejemplo, si el usuario escribe texto en un campo de texto marcado `UITextContentType.FullStreetAddress`, el sistema puede sugerir el relleno automático del campo con la ubicación en que estaba viendo recientemente.

## <a name="media-based-suggestions"></a>Sugerencias basadas en medios

Si la aplicación reproduce medios mediante el [MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) API, iOS 10 permite a los usuarios ver las carátulas de álbum y reproducir archivos multimedia a través de la aplicación en la pantalla de bloqueo.

## <a name="contextual-siri-reminders"></a>Avisos de Siri contextuales

Permite al usuario usar Siri para crear rápidamente un recordatorio para ver el contenido que están viendo actualmente en la aplicación en una fecha posterior. Por ejemplo, si una revisión de restaurantes estaban viendo en la aplicación, ya que podrían invocar Siri y diga *"Recordarme esto cuando llegue a casa."* Siri generaría el recordatorio de con un vínculo a la revisión en la aplicación.

## <a name="contact-based-suggestions"></a>Sugerencias basadas en contacto

Permite que la aplicación contactos (y póngase en contacto con información relacionada) aparecen en la **póngase en contacto con** aplicación en el dispositivo iOS, junto con todos los contactos de los usuarios almacenados en el sistema.

## <a name="ride-sharing-based-suggestions"></a>Sugerencias basadas en el manejo de uso compartido

Si usa una aplicación de uso compartido de andar el [MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) API, iOS 10 presentará como una opción en el selector de la aplicación a veces cuando el usuario es probable que se desee un viaje. La aplicación también debe estar registrada como una aplicación de uso compartido de andar especificando el `MKDirectionsModeRideShare` para el [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) clave en su `Info.plist` archivo.

Si la aplicación solo admite el uso compartido de andar, la sugerencia de sistema comenzaría con *"Obtener un viaje a..."*, si se admiten otros tipos de dirección de enrutamiento (por ejemplo, Walking o bicicleta), el sistema usará *"Obtener indicaciones de..."*

> [!IMPORTANT]
> El [MKMapItem](xref:MapKit.MKMapItem) objeto que recibe la aplicación no puede incluir información de longitud y latitud y requerirán la geocodificación.

## <a name="implementing-proactive-suggestions"></a>Implementación de sugerencias proactivas

Agregando la sugerencia proactiva soporte técnico a una aplicación de Xamarin.iOS es normalmente tan fácil como implementar algunas API o expandir en algunas API que ya se puede implementar la aplicación.

Sugerencias proactivas trabajar con las aplicaciones de tres maneras principales:

- **`NSUserActivity` y Schema.org**  -  `NSUserActivity` ayuda al sistema a comprender qué información que el usuario está trabajando actualmente en pantalla. Schema.org agrega funcionalidades similares a las páginas web.
- **Sugerencias de ubicación** : si la aplicación ofrece o consume información basados en la ubicación, estas nuevas formas de API extensión oferta para compartir esta información entre aplicaciones.
- **Sugerencias de aplicaciones multimedia** : el sistema puede aumentar el nivel de la aplicación y su contenido multimedia basado en el contexto de la interacción del usuario con el dispositivo iOS.

Y es compatible con la aplicación mediante la implementación de los siguientes:

- **Entrega**  -  `NSUserActivity` se agregó en iOS 8 para admitir la entrega, que permite al desarrollador iniciar una actividad en un dispositivo y, después, continúa en otra (consulte [Introducción a la entrega](~/ios/platform/handoff.md)).
- **Búsqueda de Spotlight** -iOS 9 agregó la capacidad para promover el contenido de la aplicación desde los resultados de búsqueda de Spotlight mediante `NSUserActivity` (consulte [búsqueda de Spotlight Core](~/ios/platform/search/corespotlight.md)).
- **Avisos de Siri contextuales** : en iOS 10 y `NSUserActivity` se ha ampliado para permitir que Siri crear rápidamente un recordatorio para ver el contenido que el usuario está viendo actualmente en la aplicación en una fecha posterior.
- **Sugerencias de ubicación** -iOS 10 mejora `NSUserActivity` para capturar las ubicaciones que se ve dentro de la aplicación y promoverlas en muchos lugares del sistema.
- **Las solicitudes de Siri contextuales**  -  `NSUserActivity` proporciona contexto a la información mostrada Siri dentro de la aplicación para que el usuario puede obtener direcciones o colocar una llamada de invocación Siri desde dentro de la aplicación.
- **Póngase en contacto con interacciones** : nuevo en iOS 10 y `NSUserActivity` permite que las aplicaciones de comunicaciones se promoviera de una tarjeta de contacto (en la aplicación contactos) como método de comunicación alternativo.

Todas estas características tienen algo en común, todos ellos utilizan `NSUserActivity` en una forma u otra, para proporcionar su funcionalidad. 

## <a name="nsuseractivity"></a>NSUserActivity

Como se indicó anteriormente, `NSUserActivity` ayuda al sistema a comprender qué información que el usuario está trabajando actualmente en pantalla. `NSUserActivity` un estado atenuado almacena en caché el mecanismo para capturar la actividad del usuario mientras navegan por la aplicación. Por ejemplo, vea una aplicación de restaurante:

[![](proactive-suggestions-images/activity02.png "El estado de peso ligero NSUserActivity mecanismo de almacenamiento en caché")](proactive-suggestions-images/activity02.png#lightbox)

Con las interacciones de la siguientes:

1. Mientras el usuario trabaja con la aplicación, un `NSUserActivity` se crea para volver a crear el estado de la aplicación más adelante.
2. Si el usuario busca un restaurante, se sigue el mismo patrón de creación de actividades.
3. Y, de nuevo, cuando el usuario ve un resultado. En este último caso, el usuario está viendo una ubicación y en iOS 10, el sistema es más consciente de ciertos conceptos (por ejemplo, las interacciones de ubicación o comunicación).

Eche un vistazo más de cerca en la última pantalla:

[![](proactive-suggestions-images/activity03.png "Los detalles de NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Aquí es la creación de la aplicación un `NSUserActivity` y se ha rellenado con información para volver a crear el estado más tarde. La aplicación también incluye algunos metadatos como el nombre y la dirección de la ubicación. Con esta actividad creada, la aplicación permite iOS saber que representa el estado del usuario actual.

La aplicación, a continuación, decide si la actividad se anunciará por transmisión terrestre para su entrega, guarda como un valor temporal para sugerencias de ubicación o agregar al índice de Spotlight en el dispositivo para mostrar en los resultados de búsqueda.

Para obtener más información sobre la búsqueda de Spotlight y de entrega, vea nuestra [Introducción a la entrega](~/ios/platform/handoff.md) y [iOS 9 nuevas API de búsqueda](~/ios/platform/search/index.md) guías.

### <a name="creating-an-activity"></a>Creación de una actividad

Antes de crear una actividad, será necesario un identificador de tipo de actividad que se ha creado para identificarla. El identificador de tipo de actividad es una cadena corta que se agrega a la `NSUserActivityTypes` matriz de la aplicación `Info.plist` archivo usado para identificar de forma única un determinado tipo de actividad de usuario. Habrá una entrada en la matriz para cada actividad que la aplicación admite y se expone a la aplicación de búsqueda. Consulte nuestra [referencia de identificadores de tipo de actividad crear](~/ios/platform/search/nsuseractivity.md) para obtener más detalles.

Ver un ejemplo de una actividad:

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

Se crea una nueva actividad con un identificador de tipo de actividad. A continuación, algunos metadatos que definen la actividad se crean por lo que este estado se puede restaurar en una fecha posterior. A continuación, la actividad tiene un título significativo y conectada a la información del usuario. Por último, algunas funcionalidades están habilitadas y se envía la actividad en el sistema.

El código anterior se podría mejorar aún más para incluir los metadatos que proporciona contexto a la actividad mediante la realización de los siguientes cambios:

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

Si el desarrollador tiene un sitio Web que es capaz de mostrar la misma información que la aplicación, la aplicación puede incluir la dirección URL y el contenido se puede mostrar en otros dispositivos que no tienen la aplicación instalada (a través de Handoff):

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Restauración de una actividad

Para responder al usuario pulsar en un resultado de búsqueda (`NSUserActivity`) para la aplicación, edite el **AppDelegate.cs** archivo e invalidar la `ContinueUserActivity` método. Por ejemplo:

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

El desarrollador tendrá que asegurarse de esto es el mismo identificador de tipo de actividad (`com.xamarin.platform`) como la actividad que creó anteriormente. La aplicación usa la información almacenada en el `NSUserActivity` para restaurar el estado a donde se dejó el usuario.

### <a name="benefits-of-creating-an-activity"></a>Ventajas de crear una actividad

Con la cantidad mínima de código anterior, la aplicación ahora es capaz de aprovechar las ventajas de las tres nuevas características de iOS 10:

- **HANDOFF**
- **Búsqueda de Spotlight**
- **Avisos de Siri contextuales**

La siguiente sección tardará un vistazo a habilitar dos otras nuevas características de iOS 10:

- **Sugerencias de ubicación**
- **Solicitudes de Siri contextuales**

### <a name="location-based-suggestions"></a>Sugerencias basadas en ubicación 

Tome como ejemplo la aplicación de búsqueda de restaurante anterior. Si ha implementado `NSUserActivity` y rellenado correctamente todos los metadatos y los atributos, el usuario podría hacer lo siguiente:

1. Buscar un restaurante en la aplicación que les gustaría para cumplir con un amigo en.
2. Cuando el usuario se mueve fuera de la aplicación mediante el modificador de la aplicación de multitarea, el sistema mostrará automáticamente una sugerencia (en la parte inferior de la pantalla) para obtener instrucciones al restaurante con su aplicación de navegación favorito.
3. Si el usuario cambia a la aplicación de mensajes y empieza a escribir *"Quedemos a"*, el teclado QuickType sugerirá automáticamente al pegar la dirección del restaurante.
4. Si el usuario cambia a la aplicación de mapas, dirección del restaurante se sugiere automáticamente como un destino.
5. Esto funciona incluso para las aplicaciones de terceros 3rd (que admiten `NSUserActivity`), por lo que el usuario puede cambiar a una aplicación de uso compartido de andar y dirección del restaurante se sugiere automáticamente como un destino allí también.
6. También proporciona contexto a Siri, por lo que el usuario puede invocar Siri dentro de la aplicación del restaurante y solicitar *"Obtener direcciones..."* y Siri le proporcionará instrucciones para el restaurante el usuario está viendo.

Toda la funcionalidad anterior tiene una cosa en común, todos ellos indican donde procede originalmente la sugerencia de. En el caso del ejemplo anterior, es la aplicación de revisión de restaurantes ficticia.

iOS 10 se ha mejorado para habilitar esta funcionalidad para una aplicación a través de varias pequeñas modificaciones y adiciones a los marcos existentes:

- `NSUserActivity` tiene campos adicionales para capturar la información de ubicación que se ve dentro de la aplicación.
- Se realizaron varias adiciones MapKit y CoreSpotlight para capturar la ubicación.
- Se agregó la funcionalidad compatible con la ubicación de Siri, mapas, teclados, multitarea y otras aplicaciones dentro del sistema.

Para implementar sugerencias basadas en la ubicación, comience con el mismo código de actividad presentado anteriormente:

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

Si la aplicación usa MapKit, es tan sencillo como agregar el objeto map actual `MKMapItem` a la actividad:

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Si la aplicación no está utilizando MapKit, puede adoptar la búsqueda de la aplicación y especificar los siguientes atributos nuevos para la ubicación:

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

Eche un vistazo en el código anterior en detalle. En primer lugar, se requiere el nombre de la ubicación en todas las instancias:

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

La latitud y longitud son opcionales, pero asegúrese de que el usuario se enruta a la ubicación exacta de que la aplicación desea enviarlos, por lo que deben incluirse:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Al establecer los números de teléfono, la aplicación puede obtener acceso a Siri por lo que el usuario puede invocar desde la aplicación Siri con algo como, *"Llamar a este lugar"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por último, la aplicación puede indicar si la instancia es adecuada para la navegación y las llamadas de teléfono:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Implementación de las interacciones de contacto

Nuevo en iOS 10, las aplicaciones de comunicación están profundamente en la aplicación de contactos de la tarjeta de contacto. Las aplicaciones que se han implementado las interacciones de contacto, el usuario puede agregar información de la aplicación dada a personas específicas de sus contactos. Y si, por ejemplo, aparezcan en el botón de acción rápida en la parte superior de una tarjeta para enviar un mensaje, se mostrará la aplicación adjunta como una opción para enviar el mensaje desde.

Si se selecciona una aplicación de terceros 3rd, puede recordar y presenta como el modo predeterminado para el mensaje a la persona dada la próxima vez que el usuario desea ponerse en contacto con ellos.

Póngase en contacto con interacciones se implementan en la aplicación con `NSUserActivity` y el nuevo marco de intenciones introducida en iOS 10. Para obtener más información sobre cómo trabajar con las intenciones, consulte nuestra [descripción de conceptos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) y [SiriKit implementar](~/ios/platform/sirikit/implementing-sirikit.md) guías.

#### <a name="donating-interactions"></a>Interacciones de donación

Eche un vistazo a cómo la aplicación puede realizar una donación interacciones:

[![](proactive-suggestions-images/activity04.png "Información general de las interacciones de donación")](proactive-suggestions-images/activity04.png#lightbox)

La aplicación crea un `INInteraction` objeto que contiene un **intención** (`INIntent`), **participantes** y **metadatos**. El **intención** representa una acción del usuario como una llamada de vídeo o enviar un mensaje de texto. El **participantes** incluyen las personas que reciben la comunicación. El **metadatos** define información adicional, como enviar correctamente el mensaje, etcetera.

El desarrollador nunca crea directamente una instancia de `INIntent` o `INIntentResponse`, usará una de las clases de objeto secundario concreto (según la tarea es llevar a cabo la aplicación en nombre del usuario) que heredan de estas clases primarias. Por ejemplo, `INSendMessageIntent` y `INSendMessageIntentResponse` para enviar un mensaje de texto. 

Una vez que se completa la interacción, llame a la `DonateInteraction` método para informar al sistema que está disponible para usarse la interacción.

Cuando el usuario interactúa con la aplicación desde la tarjeta de contacto, obtiene incluye la interacción con un `NSUserActivity`, que, a continuación, se usa para iniciar la aplicación:

[![](proactive-suggestions-images/activity05.png "Obtiene incluye la interacción con un NSUserActivity que se usa para iniciar la aplicación")](proactive-suggestions-images/activity05.png#lightbox)

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

Mirar este código en detalle, crea y rellena una instancia de `NSUserActivity` (como se muestra en el [creación de una actividad](#creating-an-activity) sección anterior). A continuación, crea una instancia de `INSendMessageIntent` (que hereda de `INIntent`) y lo rellena con los detalles del mensaje que se va a enviar:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

Un `INSendMessageIntentResponse` se crea y pasa el `NSUserActivity` creado anteriormente:

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

Un `INInteraction` se crea a partir de la intención del mensaje de envío (`INSendMessageIntent`) y la respuesta (`INSendMessageIntentResponse`) acaba de crear:

```csharp
var interaction = new INInteraction (intent, response);
```

Por último, el sistema es la notificación de la interacción:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

Se pasa un controlador de finalización donde la aplicación puede responder a la donación que sigue o no.

### <a name="activities-best-practices"></a>Prácticas recomendadas de actividades

Apple sugiere las siguientes prácticas recomendadas al trabajar con actividades:

- Use `NeedsSave` para las actualizaciones de la carga diferida.
- Asegúrese de mantener una referencia fuerte a la actividad actual.
- Solo se transfieren las cargas pequeñas que incluyan suficiente información para restaurar el estado.
- Asegúrese de que los identificadores de tipo de actividad único y descriptivo mediante el uso de la notación DNS inversa especificarlos. 

## <a name="schemaorg"></a>Schema.org

Como se indicó anteriormente, `NSUserActivity` ayuda al sistema a comprender qué información que el usuario está trabajando actualmente en pantalla. Schema.org agrega funcionalidades similares a las páginas web.

Schema.org puede proporcionar los mismos tipos de interacciones basadas en la ubicación en el sitio Web. Apple diseñado las sugerencias de ubicación nueva para que funcione tan bien cuando se ven en Safari que lo hacen en una aplicación nativa.

Algunas nociones Schema.org:

- Proporciona un estándar de vocabulario de marcado de web abierta.
- Funciona mediante la inclusión de metadatos estructurados en páginas web.
- Hay más de 500 esquemas que representan los distintos conceptos disponibles.
- Por su implementación en el sitio Web, el desarrollador puede adquirir algunas de las ventajas de usar `NSUserActivity` en una aplicación nativa.

Los esquemas se organizan en un árbol como estructura, donde los tipos específicos, como *restaurante*, heredar de tipos más genéricos como *empresarial Local*. Para obtener más información, consulte [Schema.org](http://schema.org).

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

Si el usuario ha visitado esta página en Safari y, a continuación, cambia a otra aplicación, la información de ubicación de la página se captura y se ofrece como una sugerencia de la ubicación en otras partes del sistema (como se muestra en las actividades anteriores).

Safari extraerá ningún elemento en una página web que se adhiere a cualquiera de las propiedades de esquema siguiente:

- **PostalAddress**
- **GeoCoordinates**
- Una propiedad de teléfono.

Para obtener más información, consulte nuestra [búsqueda con marcado Web](~/ios/platform/search/web-markup.md) guía.

## <a name="consuming-location-suggestions"></a>Consumo de sugerencias de ubicación

En esta sección se tratará consumiendo sugerencias de ubicación que proceder de otras partes del sistema (por ejemplo, la aplicación mapas) o con otras aplicaciones de terceros 3rd.

Hay dos formas principales de que la aplicación puede consumir las sugerencias de ubicación:

- A través del teclado QuickType.
- Directamente al consumir la información de ubicación en las aplicaciones de enrutamientos.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>Las sugerencias de ubicación y el teclado QuickType

Si la aplicación está relacionada con las direcciones de formatos basado en texto, la aplicación puede sacar partido de las sugerencias de ubicación mediante la UI de QuickType. iOS 10 expande la UI QuickType con las siguientes características:

- La aplicación puede agregar sugerencias sobre el intento semántico para los campos de texto en la interfaz de usuario.
- La aplicación puede obtener sugerencias proactivas en la aplicación.
- La aplicación puede beneficiarse de Autocorrección mejorada.

El nuevo `TextContentType` propiedad de los controles de campo de texto en iOS 10 permite al programador definir la intención semántica para el valor que el usuario se va a escribir en un campo determinado. Por ejemplo:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Podría indicar al sistema que la aplicación espera que el usuario escriba una dirección completa en el campo especificado. Esto permitirá que el teclado QuickType proporcionar automáticamente sugerencias de ubicación en el teclado cuando el usuario escribe un valor en este campo.

Los siguientes son algunos de los tipos más comunes disponibles para el desarrollador en el `UITextContentType` clase estática:

* `Name`
* `GivenName`
* `FamilyName`
* `Location`
* `FullStreetAddress`
* `AddressCityAndState`
* `TelephoneNumber`
* `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>Aplicaciones de enrutamientos y las sugerencias de ubicaciones

En esta sección tardará un vistazo al consumo de sugerencias de ubicación directamente desde dentro de una aplicación de enrutamiento. Para que la aplicación de enrutamiento agregar esta funcionalidad, el desarrollador aprovechará existente `MKDirectionsRequest` framework como sigue:

- Para promover la aplicación en la multitarea.
- Para registrar la aplicación como una aplicación de enrutamiento.
- Para controlar el inicio de la aplicación con un MapKit `MKDirectionsRequest` objeto.
- Para conceder la capacidad para obtener información sugerir la aplicación al usuario en los momentos adecuados, de iOS según la interacción del usuario.

Cuando se inicia la aplicación con un MapKit `MKDirectionsRequest` objeto, debe iniciar lo que proporciona las instrucciones de usuario a la ubicación solicitada automáticamente o presentar una interfaz de usuario que facilita empezar a recibir instrucciones al usuario. Por ejemplo:


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

Nuevo en iOS 10, la aplicación se puede enviar una dirección que no tienen coordenadas geográficas, en los que hacen que el desarrollador debe codificar la dirección:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>Sugerencias de aplicaciones multimedia

Si la aplicación controla cualquier tipo de medio como una aplicación de podcast o un streaming de contenido multimedia, como audio o vídeo, con iOS 10, puede sacar partido de sugerencias de medios en el sistema.

Para las aplicaciones que controlan la media, iOS admite los siguientes comportamientos:

- iOS promueve las aplicaciones que el usuario es probable que use en su comportamiento anterior.
- Sugerencias relacionadas con la aplicación se presentará en Spotlight y la vista de hoy en día.
- Si la aplicación cumple uno de los siguientes desencadenadores, es posible que se eleva a una sugerencia de pantalla de bloqueo:
    - Después de insertar auriculares o un dispositivo Bluetooth realiza una conexión.
    - Después de obtener en un automóvil.
    - Después de que llegan en el hogar o trabajo. 

Al incluir una sola llamada API en iOS 10, el desarrollador puede crear una experiencia de pantalla de bloqueo más atractiva para los usuarios de la aplicación de medios. Mediante el uso de la `MPPlayableContentManager` clase para administrar la reproducción multimedia, controles de medios completos (como aquellos presentada por la aplicación de música) aparecerá en la pantalla de bloqueo para la aplicación.


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

En este artículo ha cubierto sugerencias proactivas y se ha mostrado cómo los desarrolladores pueden usar para dirigir el tráfico a la aplicación de Xamarin.iOS. Trata el paso para implementar sugerencias proactivas y presentan las directrices de uso.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
