---
title: Sugerencias automático
description: Este artículo muestra cómo usar las sugerencias automático en una aplicación watchOS 3 para la interacción de unidad al permitir que el sistema de forma proactiva presentar automáticamente información útil al usuario.
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: f22be43f814865c3c14e12aa2aec3a8dbce09b7a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="proactive-suggestions"></a>Sugerencias automático

_Este artículo muestra cómo usar las sugerencias automático en una aplicación watchOS 3 para la interacción de unidad al permitir que el sistema de forma proactiva presentar automáticamente información útil al usuario._


Nuevo elemento a watchOS 3, formas de noticias presente sugerencias automático para que los usuarios ponerse en contacto con la aplicación Xamarin.iOS proactivamente presente información útil automáticamente al usuario en los momentos adecuados.


## <a name="about-proactive-suggestions"></a>Acerca de sugerencias automático

Nueva a 3, watchOS `NSUserActivity` incluye un `MapItem` propiedad que permite que la aplicación proporcionar información de ubicación que se puede usar en otros contextos. Por ejemplo, si el hotel aplicación muestra revisa y proporciona un `MapItem` ubicación, si el usuario cambió a la aplicación de mapas, la ubicación del hotel que estaban viendo estaría disponible.

La aplicación expone esta funcionalidad en el sistema mediante un conjunto de tecnologías como `NSUserActivity`, MapKit, el Reproductor de Media y UIKit. Además, al proporcionar soporte de sugerencia automático de la aplicación, obtiene una integración más profunda Siri de forma gratuita.

## <a name="location-based-suggestions"></a>Sugerencias basadas en ubicación

Nuevo para watchOS 3, el `NSUserActivity` clase incluye una `MapItem` propiedad que permite al desarrollador proporcionar información de ubicación que se puede usar en otros contextos. Por ejemplo, si la aplicación muestra opiniones sobre restaurantes, el desarrollador puede establecer el `MapItem` propiedad a la ubicación del restaurante que está viendo el usuario en la aplicación. Si el usuario cambia a la aplicación de mapas, ubicación del restaurante está automáticamente disponible.

Si la aplicación admite la búsqueda de la aplicación, puede usar los nuevos componentes de la dirección de la `CSSearchableItemAttributesSet` clase para especificar las ubicaciones que el usuario que desee visitar. Estableciendo la `MapItem` propiedad, las demás propiedades son rellena de forma automática.

Además de establecer el `Latitude` y `Longitude` de las propiedades de componente de dirección, se recomienda que la aplicación proporcione la `NamedLocation` y `PhoneNumbers` propiedades demasiado, por lo que Siri puede iniciar una llamada a la ubicación.

## <a name="contextual-siri-reminders"></a>Avisos de Siri contextuales

Permite al usuario usar Siri para realizar rápidamente un aviso para ver el contenido que están viendo actualmente en la aplicación en una fecha posterior. Por ejemplo, si una revisión de restaurantes estaban viendo en la aplicación, ya que podrían invocar Siri y diga *"Recordármelo sobre esto cuando llegue a casa."* Siri generaría el recordatorio con un vínculo a la revisión en la aplicación.

## <a name="implementing-proactive-suggestions"></a>Implementación de sugerencias automático

Agregando la sugerencia automático soporte técnico para la aplicación de Xamarin.iOS es normalmente tan fácil como implementar algunas API o expandir en algunas API que ya se puede implementar la aplicación.

Sugerencias automático funcionan con las aplicaciones de tres maneras principales:

- **`NSUserActivity`** -Ayuda entender la información que el usuario está trabajando actualmente con pantalla del sistema.
- **Sugerencias de ubicación** : si la aplicación ofrece o utiliza información de ubicación según, estas formas nuevas de la oferta de extensión API para compartir esta información entre aplicaciones.

Y es compatible con la aplicación mediante la implementación de los siguientes:

- **Contextual recordatorios de Siri** : en iOS 10, `NSUserActivity` se ha ampliado para permitir que Siri crear rápidamente un aviso para ver el contenido que están viendo actualmente en la aplicación en una fecha posterior.
- **Sugerencias de ubicación** -iOS 10 mejora `NSUserActivity` para capturar ubicaciones ver dentro de la aplicación y promoverlos en muchos lugares en todo el sistema.
- **Solicitudes de Siri contextual**  -  `NSUserActivity` proporciona contexto para la información mostrada Siri dentro de la aplicación para que el usuario pueda obtener direcciones o en lugar de ser una llamada Siri invocar desde dentro de la aplicación.

Todas estas características tienen algo en común, todas utilizan `NSUserActivity` en un formulario u otro para proporcionar su funcionalidad. 

## <a name="nsuseractivity"></a>NSUserActivity

Como se indicó anteriormente, `NSUserActivity` ayuda a que el sistema entender la información que el usuario está trabajando actualmente con pantalla. `NSUserActivity` un estado atenuado almacena en caché mecanismo para capturar la actividad del usuario mientras navegan por la aplicación. Por ejemplo, si la aplicación del restaurante:

[![](proactive-suggestions-images/activity02.png "La aplicación del restaurante")](proactive-suggestions-images/activity02.png#lightbox)

Con las siguientes interacciones:

1. Cuando el usuario trabaja con la aplicación, un `NSUserActivity` se crea para volver a crear el estado de la aplicación más tarde.
2. Si el usuario busca un restaurante, se sigue el mismo patrón de creación de actividades.
3. Y, de nuevo, cuando el usuario ve un resultado. En este último caso, el usuario está viendo una ubicación y en iOS 10, el sistema es más consciente de ciertos conceptos (por ejemplo, las interacciones de ubicación o comunicación).

Eche un vistazo más de cerca en la última pantalla:

[![](proactive-suggestions-images/activity03.png "La carga de NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

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

Asegúrese de que es el mismo identificador de tipo de actividad (`com.xamarin.platform`) como la actividad que creó anteriormente. La aplicación usa la información almacenada en el `NSUserActivity` para restaurar el estado en donde se dejó el usuario.

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
4. Si el usuario cambia a la aplicación de mapas, dirección del restaurante se sugiere automáticamente como un destino.
5. Esto funciona incluso para las aplicaciones de terceros 3rd (que admiten `NSUserActivity`), por lo que el usuario puede cambiar a una aplicación de uso compartido de viaje y automáticamente la dirección del restaurante se sugiere así como un destino no existe.
6. También proporciona contexto al Siri, por lo que el usuario puede invocar Siri dentro de la aplicación del restaurante y pida *"Obtener direcciones..."* y Siri le proporcionará instrucciones al restaurante el usuario está viendo.

Toda la funcionalidad anterior tiene algo en común, todos ellos indicarán donde procede originalmente la sugerencia de. En el caso del ejemplo anterior, es la aplicación de revisión de restaurantes ficticios.

watchOS 3 se ha mejorado para habilitar esta funcionalidad en una aplicación a través de varias pequeñas modificaciones y adiciones a marcos existentes:

- `NSUserActivity` tiene campos adicionales para capturar la información de ubicación que se ve dentro de la aplicación.
- Se realizaron varias adiciones a MapKit y CoreSpotlight para capturar la ubicación.
- Se ha agregado la funcionalidad compatible con la ubicación Siri, mapas, multitarea y otras aplicaciones en el sistema.

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

A continuación, el texto de descripción basándose en necesarios para el texto según instancias (por ejemplo, el teclado QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

La latitud y longitud son opcionales, pero asegúrese de que el usuario se enruta a la ubicación exacta que desea enviarlos a la aplicación:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Al establecer los números de teléfono, la aplicación puede obtener acceso a Siri por lo que el usuario puede invocar Siri desde la aplicación diciendo algo parecido, * "Llamar a esta ubicación":

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por último, la aplicación puede indicar si la instancia es adecuada para la navegación y llamadas de teléfono:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>Prácticas recomendadas de actividades

Apple sugiere las siguientes prácticas recomendadas al trabajar con actividades:

- Use `NeedsSave` para las actualizaciones de la carga diferida.
- Asegúrese de mantener una referencia segura a la actividad actual.
- Transferencia de pequeñas cargas que incluyen suficiente información para restaurar el estado.
- Asegúrese de que los identificadores de tipo de actividad único y descriptivo usando la notación DNS inversa para especificarlos. 

## <a name="consuming-location-suggestions"></a>Consumir sugerencias de ubicación

En esta sección se tratará consumiendo sugerencias de ubicación que proceden de otras partes del sistema (por ejemplo, la aplicación de asignaciones) o con otras aplicaciones de terceros 3rd.

## <a name="routing-apps-and-locations-suggestions"></a>Aplicaciones de enrutamientos y sugerencias de ubicaciones

En esta sección se echa un vistazo a la hora de consumir sugerencias de ubicación directamente desde dentro de una aplicación de enrutamiento. Para que la aplicación de enrutamiento agregar esta funcionalidad, el desarrollador aprovecharán existente `MKDirectionsRequest` marco de trabajo como se indica a continuación:

- Para promover la aplicación en la multitarea.
- Para registrar la aplicación como una aplicación de enrutamiento.
- Para controlar iniciar la aplicación con un MapKit `MKDirectionsRequest` objeto.
- Dan watchOS la capacidad para obtener información acerca de la aplicación en función de interacción del usuario.

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

Nuevo en watchOS 3, la aplicación se puede enviar una dirección que no tiene coordenadas de replicación geográfica, en las que hacen que el desarrollador debe codificar la dirección:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>Resumen

En este artículo ha tratado sugerencias automático y se ha explicado cómo el programador puede usarlos para tráfico de unidad a una aplicación de Xamarin.iOS para watchOS. Trata el paso para implementar sugerencias automático y presentan las instrucciones de uso.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Guía de programación de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
