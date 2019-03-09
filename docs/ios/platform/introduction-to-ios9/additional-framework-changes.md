---
title: Cambios adicionales en iOS 9 marcos de trabajo
description: Este documento describe los cambios de marco de trabajo adicionales introducidos en iOS 9. Describe AVFoundation AVKit y CloudKit.
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f7106c9b23fa71b32043ef8190691d798c36370d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672578"
---
# <a name="additional-ios-9-frameworks-changes"></a>Cambios adicionales en iOS 9 marcos de trabajo

_Este artículo tratan los cambios adicionales y menores o mejoras en los marcos existentes para iOS 9._

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 Logo")](additional-framework-changes-images/ios9.png#lightbox)

Además de los cambios principales en iOS, Apple ha realizado modificaciones y mejoras para varios marcos existentes en iOS 9.

## <a name="avfoundation-framework-additions"></a>Adiciones de AVFoundation Framework

En el marco de trabajo AVFoundation, el [AVSpeechSynthesisVoice](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechSynthesisVoice/) clase ahora le permite especificar una voz por identificador, además de lenguaje.

Por ejemplo, el código siguiente obtiene una lista de todas las voces disponibles:

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

A continuación, puede usar una de las voces de la lista si se establece como el `Voice` propiedad de una instancia de la [AVSpeachUtterance](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechUtterance/) clase.

El [AVQueuePlayer](https://developer.xamarin.com/api/type/AVFoundation.AVQueuePlayer/) clase ahora admite una mezcla de multimedia de transmisión por secuencias y basados en archivos de internet en la cola. Las versiones anteriores podrían sólo media de cola del mismo tipo.

Para obtener más información, consulte Apple [AVSpeechSynthesisVoice referencia](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice).

## <a name="avkit-framework-additions"></a>Adiciones de AVKit Framework

Para trabajar con la nueva característica de imagen en imagen (PIP), el marco de trabajo AVKit incluye la nueva `AVPictureInPictureController` y [AVPlayerViewController](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) clases:

- **AVPictureInPictureController** -esta clase permite que una aplicación de iOS 9 responder a que el usuario que inicia la reproducción de un vídeo en una ventana PIP flotante, puede cambiar el tamaño en un iPad.
- **AVPlayerViewController** -administra un `AVPlayer` controlador usado para presentar un vídeo en una ventana PIP flotante, puede cambiar el tamaño en un iPad.

Para obtener más información, consulte nuestra [multitarea para iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) documentación y Apple [AVPictureInPictureController referencia](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) y [AVPlayerViewController referencia](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>Introducción a CloudKit Web Services

El marco de trabajo de CloudKit simplifica el desarrollo de aplicaciones que iCloud acceso. Esto incluye la recuperación de datos de la aplicación y derechos de activos así como ser capaz de almacenar información de la aplicación de forma segura. Este kit ofrece a los usuarios un nivel de anonimato permitiendo el acceso a las aplicaciones con su identificadores de iCloud sin compartir información personal.

El nuevo _CloudKit Web Services_ framework proporciona una biblioteca de JavaScript (CloudKit JS) que se puede incorporar en su sitio Web para proporcionar acceso a los mismos datos de CloudKit basado y contenido que la aplicación de Xamarin.iOS.

> [!IMPORTANT]
> Antes de que puede acceder, presentar o actualizar el contenido de una base de datos CloudKit CloudKit JS, debe haber definido previamente el esquema de esa base de datos.




Para obtener más información, consulte los siguientes documentos:

- [Introducción a CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) -nuestra introducción al uso directo de CloudKit en una aplicación de Xamarin.iOS.
- [Inicio rápido de CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) -introducción de Apple a CloudKit.
- [Referencia de JS CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) -la documentación de Apple CloudKit JS.
- [Referencia de servicios Web de CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240) -referencia de Apple que se describe la interfaz HTTP a CloudKit.
- [Catálogo de CloudKit: Una introducción a CloudKit (Cocoa y JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) -aplicación de ejemplo de Apple mediante CloudKit y CloudKit JS.

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

## <a name="foundation-framework-additions"></a>Adiciones de Framework Foundation

En iOS 9, Apple incluye los siguientes cambios en el marco de Foundation:

### <a name="changes-to-nsbundle"></a>Cambios realizados en NSBundle

Los siguientes cambios se realizaron a la [NSBundle](xref:Foundation.NSBundle) clase IOS 9:

* `GetPreservationPriorityForTag (NSString tag)` — Obtiene la prioridad de conservación actual por los recursos con la etiqueta determinada. Los valores válidos están comprendidos entre `0.0` a `1.0`, primero se purgarán los recursos con la prioridad más baja.
* `SetPreservationPriorityForTag (double priority, NSSet tags)` : Establece la prioridad de conservación actual por los recursos con las etiquetas especificados. Los valores válidos están comprendidos entre `0.0` a `1.0`, primero se purgarán los recursos con la prioridad más baja.

Para obtener más información, consulte Apple [NSBundle referencia](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle).

### <a name="changes-to-nsprocessinfo"></a>Cambios realizados en NSProcessInfo

Cada proceso que se ejecuta en un dispositivo iOS tiene una sola, _agente de información de proceso_ (PIA). Use la [NSProcessInfo](xref:Foundation.NSProcessInfo) clase para proporcionar información sobre la eficacia PIA y el control actual y la administración térmica para un proceso determinado.

Por ejemplo, para controlar la finalización automática de un proceso puede utilizar el código siguiente:

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Para obtener más información, consulte Apple [NSProcessInfo referencia](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo).

### <a name="reacting-to-low-power-mode"></a>Reacción a modo de bajo consumo de energía

Use la `LowPowerModeEnabled` propiedad de la [NSProcessInfo](xref:Foundation.NSProcessInfo) clase para determinar si se ha habilitado el modo de bajo consumo en el dispositivo iOS que se está ejecutando la aplicación. Por ejemplo:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Cambios en la plataforma HealthKit

Apple incluye los siguientes cambios en el [HealthKit](https://developer.xamarin.com/api/namespace/HealthKit/) framework en iOS 9:

- Soporte técnico para el seguimiento de eliminación de entradas en la base de datos HealthKit y eliminación en masa. Consulte Apple [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) y [referencia de clase HKHealthStore](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) para obtener más información.
- Se han agregado nuevas categorías de seguimiento y las características para la `HKQuantityTypeIdentifier` clase (como `UVExposure`) y a la `HKCategoryTypeIdentifier` clase (como `OvulationTestResult`). Consulte Apple [HealthKit constantes referencia](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710) para obtener más información.

Consulte nuestra [Introducción a HealthKit](~/ios/platform/healthkit.md) documentación para obtener más información sobre cómo trabajar con HealthKit en Xamarin.iOS.

## <a name="local-authentication-framework-changes"></a>Cambios de marco de trabajo de autenticación local

Apple incluye los siguientes cambios en el [autenticación Local](https://developer.xamarin.com/api/namespace/LocalAuthentication/) framework en iOS 9:

- Mediante el `EvaluateAccessControl` y `EvaluatePolicy` métodos de la [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) (clase), ahora puede intentos de reutilización coincide con Touch ID de desbloqueo correcto anterior.
- La capacidad de obtener una lista de los dedos registradas actualmente.
- Soporte técnico para realizar el seguimiento cuando se agrega o quita de la autenticación de un dedo.
- La capacidad de usar _contexto de autenticación_ en las llamadas de la cadena de claves y soporte técnico para evaluar el control de acceso a llaves se enumeran.
- La capacidad para cancelar un mensaje de usuario desde el código.

Consulte nuestra [Introducción a Touch ID](~/ios/platform/touchid.md) documentación para obtener más información sobre cómo trabajar con Touch ID en Xamarin.iOS.

### <a name="lacontext-changes"></a>LAContext cambios

Los siguientes cambios se realizaron a la [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) clase IOS 9:

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -devuelve la cantidad máxima de tiempo que se puede reutilizar una autenticación de Id. de toque.
- **EvaluatedPolicyDomainState** : Obtiene o establece el estado de una directiva evaluado.
- **MaxBiometryFailures** -se ha depreciado en iOS 9.
- **TouchIdAuthenticationAllowableReuseDuration** Obtiene o establece la cantidad de tiempo que se puede reutilizar una autenticación de Id. de toque.
- **EvaluateAccessControl** : de forma asincrónica se evalúa como una directiva de autenticación.
- **Invalidar** -invalida una autenticación de Id. de toque determinado.
- **IsCredentialSet** -devuelve `true` si las credenciales están establecidas actualmente.
- **SetCredentialType** establece el tipo de credencial proporcionada.

Consulte Apple [LAContext referencia](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) para obtener más detalles.

## <a name="mapkit-framework-changes"></a>Cambios en la plataforma MapKit

Apple incluye los siguientes cambios en el [MapKit](https://developer.xamarin.com/api/namespace/MapKit/) framework en iOS 9:

- MapKit proporciona ahora compatibilidad para iniciar la aplicación de mapa directamente en las direcciones de tránsito y para realizar consultas en tránsito tiempo estimado de llegada (ETA) utilizando el [MKLaunchOptions](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) y [MKDirections](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) clases.
- Los resultados de búsqueda devueltos por MapKit y [CLGeocoder](https://developer.xamarin.com/api/type/CoreLocation.CLGeocoder/) clase también puede proporcionar la zona de horaria del resultado.
- Ahora puede personalizar completamente las anotaciones de mapa presentada por la aplicación iOS mediante el `DetailCalloutAccessoryView` propiedad de la [MKAnnotationView](https://developer.xamarin.com/api/type/MapKit.MKAnnotationView/) clase.

Consulte nuestra [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md) y [Tutorial: explorar las anotaciones y superposiciones en MapKit](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) documentación para obtener más información sobre cómo trabajar con mapas y las anotaciones en Xamarin.iOS y Apple [Referencia CLGeocoder](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) para obtener más información.

## <a name="passkit-framework-additions"></a>Marco de PassKit adiciones

Apple incluye los siguientes cambios en el [PassKit](https://developer.xamarin.com/api/namespace/PassKit/) framework en iOS 9:

- Apple Pay ahora es compatible con almacén de débito y tarjetas de crédito junto con las tarjetas Discover. Consulte la **redes de pago** sección de Apple [referencia de clase PKPaymentRequest](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) para obtener más información.
- Desde directamente dentro de una aplicación de Xamarin.iOS, ahora puede agregar las redes de pago y los emisores de tarjeta para Apple Pay. Consulte Apple [referencia de clase PKAddPaymentPassViewController](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) para obtener más detalles.

Consulte nuestra [Introducción a PassKit](~/ios/platform/passkit.md) documentación para obtener más información sobre cómo trabajar con PassKit en Xamarin.iOS.

## <a name="safari-services-framework-additions"></a>Adiciones de marco de trabajo de servicios de Safari

Apple incluye los siguientes cambios en el [Safari servicios](https://developer.xamarin.com/api/namespace/SafariServices/) framework en iOS 9:

- Ahora puede usar el nuevo [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) clase para mostrar el contenido web en una aplicación de Xamarin.iOS. Proporciona la capacidad de compartir cookies y datos del sitio Web con la aplicación de Safari e incluye varias características de Safari (por ejemplo, lector y el relleno automático). [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) características un **realiza** botón que se devolverá los usuarios a la aplicación cuando hayan terminado de ver el contenido web.

Dado que el [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) clase está diseñada para mostrar una sola página de contenido web, debe considerar el uso para reemplazar cualquier [WKWebKit](xref:WebKit.WKWebView) o [UIWebView](xref:UIKit.UIWebView)controles dentro de las aplicaciones de Xamarin.iOS existentes.

### <a name="displaying-a-website"></a>Visualización de un sitio Web

El código siguiente es un ejemplo de llamada un [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) desde dentro de otro controlador de vista:

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Cambia el marco UIKit

Apple ha incluido muchas mejoras para varios elementos de la [UIKit](xref:UIKit) framework para iOS 9. Las siguientes secciones se detallará esos cambios.

### <a name="3d-touch-events"></a>Eventos de función táctil 3D

Nuevo a iOS 9 y el iPhone 6s y iPhone 6s Plus, 3D Touch agrega gestos confidenciales presión a las aplicaciones de iOS. Como resultado, si la aplicación se ejecuta en iOS 9 (o posterior) y el dispositivo iOS es capaz de apoyo de 3D Touch, hará que los cambios en la presión de la `TouchesMoved` evento.

Debido a este cambio de comportamiento, las aplicaciones de iOS deben estar preparadas para la `TouchesMoved` eventos que se debe invocar con más frecuencia, incluso si la X / coordenadas Y no han cambiado.

Para obtener más información, consulte nuestra [Introducción a 3D Touch](~/ios/platform/3d-touch.md) guía.

### <a name="document-open-in-place-functionality"></a>Funcionalidad de documento abierto en contexto

Mediante el uso del `FinishedLaunching (application, launchOptions)` o `WillFinishLaunching (Application, launchOptions)` métodos de la [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) (clase), ahora puede abrir un documento y modificarlo en su lugar (en lugar de trabajar en una copia).

Para admitir la nueva funcionalidad de open in situ, agregue el `LSSupportsOpeningDocumentsInPlace` clave a la aplicación de Xamarin.iOS **Info.plist** archivo con un valor de `YES`.

Consulte Apple [UIApplicationDelegate referencia](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) para obtener más detalles.

### <a name="enhanced-touch-events"></a>Eventos de función táctil mejorada

Apple ha desarrollado varias mejoras a los eventos Touch en iOS 9. Éstos incluyen la capacidad para usar predicción de toque y para obtener acceso a los toques intermedios entre las actualizaciones de pantalla.

Consulte Apple [Guía de control de eventos para iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) para obtener más detalles.

### <a name="fetching-tailored-content"></a>Recuperación de contenido personalizado

El nuevo `NSDataAsset` clase permite capturar contenido adaptado a la memoria y las capacidades gráficas del dispositivo iOS que se está ejecutando en una aplicación de Xamarin.iOS.

### <a name="new-layout-anchors"></a>Los nuevos delimitadores de diseño

El nuevo `NSLayoutAnchor` y `NSLayoutDimension` funcionan las clases de anclaje de diseño con las nuevas propiedades de delimitador de la [UIView](xref:UIKit.UIView) clase (como `LeadingAnchor` y `WidthAnchor`) para facilitar la distribución de iOS 9.

Consulte nuestra [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentación para obtener más información sobre cómo trabajar con diseño automático y las clases de tamaño en una aplicación de Xamarin.iOS y Apple [NSLayoutAnchor referencia](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor), [ Referencia de NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) y [UIView referencia](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) para obtener más información.

### <a name="new-readable-content-margins"></a>Márgenes contenidos legibles

El nuevo `UILayoutGuide` clase puede usarse para proporcionar los márgenes de contenido puede leer y definir las regiones de dibujo para el contenido dentro de una vista. Consulte Apple [UILayoutGuide referencia](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) para obtener más información.

### <a name="text-input-in-notifications-modifications"></a>Entrada de texto en las modificaciones de las notificaciones

El [UIUserNotificationAction](xref:UIKit.UIUserNotificationAction) tiene una nueva clase `Behavior` propiedad que se puede usar para admitir la entrada de texto de las notificaciones.

### <a name="uiapplicationdelegate-changes"></a>UIApplicationDelegate cambios

Mientras no formalmente en desuso por Apple, que sugieren reemplazar todas las llamadas a la `FinishedLaunching (UIApplication application)` método de la [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) clase con cualquiera el `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` o `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` métodos.

Consulte Apple [UIApplicationDelegate referencia](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) para obtener más detalles.

### <a name="uikit-dynamics-changes"></a>Cambios de UIKit Dynamics

En iOS 9, Apple incluye los siguientes cambios en la dinámica de UIKit:

- Dynamics proporciona ahora compatibilidad con los límites de la colisión no rectangulares.
- El nuevo, personalizable `UIFieldBehavior` clase se usa para admitir distintos tipos de campo.
- Tipos de datos adjuntos adicionales se han agregado a la `UIAttachmentBehavior` clase.

Consulte Apple [UIAttachment referencia](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) para obtener más detalles.

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView y UIDatePicker cambios

Antes de iOS 9, el [UIPickerView](xref:UIKit.UIPickerView) y [UIDatePicker](xref:UIKit.UIDatePicker) controles eran invariable y cambiarían de tamaño automáticamente para llenar el ancho de su contenedor (normalmente el ancho del dispositivo iOS era de la aplicación está ejecutando).

En iOS 9, este cambio de tamaño automático deja de producirse y los controles se representará con un ancho de 320 punto en todos los dispositivos iOS, independientemente del tamaño de pantalla y la orientación.

Para corregir esta situación, utilice el diseño automático y las clases de tamaño para anclar el ancho del control a los bordes del contenedor primario (vista) y especifique la altura requerida. Consulte nuestra [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentación para obtener más información sobre cómo trabajar con diseño automático y las clases de tamaño en una aplicación de Xamarin.iOS.

### <a name="new-uitextinputassistantitem-class"></a>Nueva clase UITextInputAssistantItem

Use la nueva `UITextInputAssistantItem` clase a grupos de botones de barra de diseño en un _barra de accesos directos_. La barra de acceso directo es una nueva área que está disponible en el teclado en pantalla para proporcionar los métodos abreviados de escritura.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
