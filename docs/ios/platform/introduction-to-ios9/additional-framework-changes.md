---
title: Cambios de marcos de trabajo adicionales iOS 9
description: "Este artículo trata sobre cambios adicionales, secundarios o mejoras en marcos existentes para iOS 9."
ms.topic: article
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 5053d8d0cf9c1c3e6d3282ee8d6e42448e733c2c
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="additional-ios-9-frameworks-changes"></a>Cambios de marcos de trabajo adicionales iOS 9

_Este artículo trata sobre cambios adicionales, secundarios o mejoras en marcos existentes para iOS 9._

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 logotipo")](additional-framework-changes-images/ios9.png#lightbox)

Además de los cambios más importantes en iOS, Apple ha realizado modificaciones y mejoras para varios marcos de trabajo existentes en iOS 9.

## <a name="av-foundation-framework-additions"></a>Adiciones de Framework de Foundation de AV

En el marco de trabajo de AV Foundation, el [AVSpeechSynthesisVoice](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechSynthesisVoice/) clase ahora le permite especificar una voz mediante identificador además de lenguaje.

Por ejemplo, el código siguiente obtiene una lista de todas las voces disponibles:

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

A continuación, puede usar una de las voces de la lista si se establece como el `Voice` propiedad de una instancia de la [AVSpeachUtterance](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechUtterance/) clase.

El [AVQueuePlayer](https://developer.xamarin.com/api/type/AVFoundation.AVQueuePlayer/) clase ahora admite una mezcla de multimedia de transmisión por secuencias y basados en archivos de internet en la cola. Las versiones anteriores podrían sólo media de cola del mismo tipo.

Para obtener más información, vea de Apple [AVSpeechSynthesisVoice referencia](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice).

## <a name="avkit-framework-additions"></a>Adiciones de AVKit Framework

Para trabajar con la nueva característica de imagen en imagen (PIP), el marco de trabajo AVKit incluye la nueva `AVPictureInPictureController` y [AVPlayerViewController](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) clases:

- **AVPictureInPictureController** -esta clase permite a una aplicación de iOS 9 responder a los usuarios iniciar la reproducción de un vídeo en una ventana flotante, puede cambiar el tamaño del PIP en un iPad.
- **AVPlayerViewController** -administra un `AVPlayer` controlador que se usa para presentar un vídeo en una ventana flotante, puede cambiar el tamaño del PIP en un iPad.

Para obtener más información, vea nuestra [multitarea para iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) documentación y de Apple [AVPictureInPictureController referencia](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) y [AVPlayerViewController referencia](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>Introducción a servicios Web CloudKit

El marco de trabajo CloudKit simplifica el desarrollo de aplicaciones que iCloud de acceso. Esto incluye la recuperación de datos de la aplicación y activos derechos así como la posibilidad de almacenar información de la aplicación de forma segura. Este kit ofrece a los usuarios un nivel de anonimato permitiendo el acceso a las aplicaciones con su identificadores de iCloud sin compartir información personal.

El nuevo _CloudKit Web Services_ framework proporciona una biblioteca de JavaScript (CloudKit JS) que se puede incorporar en el sitio Web para proporcionar acceso a los mismos datos de CloudKit según y contenido, como la aplicación Xamarin.iOS.

> [!IMPORTANT]
> Para poder tener acceso a, presentar o actualizar el contenido de una base de datos CloudKit CloudKit JS, debe haber definido previamente el esquema de esa base de datos.




Para obtener más información, consulte los siguientes documentos:

- [Introducción a CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) -nuestra introducción al uso de CloudKit en una aplicación de Xamarin.iOS.
- [Inicio rápido de CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) -introducción de Apple para CloudKit.
- [Referencia de JS CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) -documentación de CloudKit JS de Apple.
- [Referencia de servicios Web de CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240) -referencia de Apple que describe la interfaz HTTP a CloudKit.
- [Catálogo de CloudKit: An Introduction to CloudKit (cacao y JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) -aplicación de ejemplo de Apple con CloudKit y CloudKit JS.

## <a name="foundation-framework-additions"></a>Adiciones de Framework de Foundation

En iOS 9, Apple incluye los siguientes cambios en el marco de trabajo de Foundation:

### <a name="changes-to-nsbundle"></a>Cambios en NSBundle

Los siguientes cambios se realizaron en el [NSBundle](https://developer.xamarin.com/api/type/Foundation.NSBundle/) clase IOS 9:

* `GetPreservationPriorityForTag (NSString tag)` -Obtiene la prioridad de conservación actual por los recursos con la etiqueta determinada. Los valores válidos están comprendidos entre `0.0` a `1.0`, primero se purgarán recursos con la prioridad más baja.
* `SetPreservationPriorityForTag (double priority, NSSet tags)` -Establece la prioridad de conservación actual por los recursos con las etiquetas especificados. Los valores válidos están comprendidos entre `0.0` a `1.0`, primero se purgarán recursos con la prioridad más baja.

Para obtener más información, vea de Apple [NSBundle referencia](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle).

### <a name="changes-to-nsprocessinfo"></a>Cambios en NSProcessInfo

Cada proceso que se ejecuta en un dispositivo iOS tiene una sola, _agente de información de proceso_ (PIA). Use la [NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/) clase para proporcionar información sobre la eficacia PIA y control actual y la administración térmica para un proceso determinado.

Por ejemplo, para controlar la finalización automática de un proceso puede usar el código siguiente:

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Para obtener más información, vea de Apple [NSProcessInfo referencia](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo).

### <a name="reacting-to-low-power-mode"></a>Reacción a modo de bajo consumo de energía

Use la `LowPowerModeEnabled` propiedad de la [NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/) clase para determinar si se ha habilitado el modo de bajo consumo en el dispositivo iOS que se ejecuta la aplicación en. Por ejemplo:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Cambios de HealthKit Framework

Apple incluye los siguientes cambios en el [HealthKit](https://developer.xamarin.com/api/namespace/HealthKit/) framework en iOS 9:

- Soporte técnico para el seguimiento de eliminación de entradas en la base de datos de HealthKit y eliminación en masa. Vea de Apple [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) y [HKHealthStore Class Reference](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) para obtener más información.
- Nuevas categorías de seguimiento y las características se agregaron a la `HKQuantityTypeIdentifier` clase (como `UVExposure`) y a la `HKCategoryTypeIdentifier` clase (como `OvulationTestResult`). Vea de Apple [HealthKit constantes referencia](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710) para obtener más información.

Visite nuestro [Introducción a HealthKit](~/ios/platform/healthkit.md) documentación para obtener más información sobre cómo trabajar con HealthKit en Xamarin.iOS.

## <a name="local-authentication-framework-changes"></a>Cambios de marco de trabajo de autenticación local

Apple incluye los siguientes cambios en el [autenticación Local](https://developer.xamarin.com/api/namespace/LocalAuthentication/) framework en iOS 9:

- Mediante el `EvaluateAccessControl` y `EvaluatePolicy` métodos de la [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) (clase), ahora puede intentos de reutilización Touch ID hace coincidir el desbloqueo correcta anterior.
- La capacidad de obtener una lista de los dedos registradas actualmente.
- Compatibilidad con seguimiento cuando se agrega o se quita de la autenticación con un dedo.
- La capacidad para usar _contexto de autenticación_ en llamadas de llaves y soporte técnico para evaluar el control de acceso a llaveros enumera.
- La capacidad para cancelar un mensaje de usuario desde el código.

Visite nuestro [Introducción a Touch ID](~/ios/platform/touchid.md) documentación para obtener más información sobre cómo trabajar con Touch ID en Xamarin.iOS.

### <a name="lacontext-changes"></a>Cambios de LAContext

Los siguientes cambios se realizaron en el [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) clase IOS 9:

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -devuelve la cantidad máxima de tiempo que se puede reutilizar una autenticación de Id. de entrada táctil.
- **EvaluatedPolicyDomainState** : Obtiene o establece el estado de una directiva evaluada.
- **MaxBiometryFailures** -se ha depreciado en iOS 9.
- **TouchIdAuthenticationAllowableReuseDuration** Obtiene o establece la cantidad de tiempo que se puede reutilizar una autenticación de Id. de entrada táctil.
- **EvaluateAccessControl** : de forma asincrónica se evalúa como una directiva de autenticación.
- **Invalidar** -invalida una autenticación de Id. de táctil determinado.
- **IsCredentialSet** -devuelve `true` si las credenciales están establecidas actualmente.
- **SetCredentialType** establece el tipo de credencial proporcionada.

Vea de Apple [LAContext referencia](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) para obtener más detalles.

## <a name="mapkit-framework-changes"></a>Cambios de MapKit Framework

Apple incluye los siguientes cambios en el [MapKit](https://developer.xamarin.com/api/namespace/MapKit/) framework en iOS 9:

- MapKit ahora proporciona compatibilidad para iniciar la aplicación mapa directamente en direcciones de tránsito y para realizar consultas en tránsito tiempo estimado de llegada (ETA) utilizando el [MKLaunchOptions](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) y [MKDirections](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) clases.
- Los resultados de búsqueda devueltos por MapKit y [CLGeocoder](https://developer.xamarin.com/api/type/CoreLocation.CLGeocoder/) clase también puede proporcionar la zona de horaria del resultado.
- Ahora puede personalizar completamente las anotaciones de mapa presentado por la aplicación de iOS con el `DetailCalloutAccessoryView` propiedad de la [MKAnnotationView](https://developer.xamarin.com/api/type/MapKit.MKAnnotationView/) clase.

Visite nuestro [iOS mapas](~/ios/user-interface/controls/ios-maps/index.md) y [Tutorial: explorar las anotaciones y superposiciones en MapKit](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) documentación para obtener más información sobre cómo trabajar con mapas y las anotaciones de Xamarin.iOS y Apple [Referencia CLGeocoder](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) para obtener más información.

## <a name="passkit-framework-additions"></a>Adiciones de PassKit Framework

Apple incluye los siguientes cambios en el [PassKit](https://developer.xamarin.com/api/namespace/PassKit/) framework en iOS 9:

- Apple Pay ahora es compatible con almacén de débito y tarjetas de crédito junto con las tarjetas de detección. Consulte la **redes de pago** sección de Apple [PKPaymentRequest Class Reference](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) para obtener más información.
- Desde directamente dentro de una aplicación Xamarin.iOS, ahora puede agregar redes de pago y los emisores de tarjeta para pagar de Apple. Vea de Apple [PKAddPaymentPassViewController Class Reference](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) para obtener más detalles.

Visite nuestro [Introducción a PassKit](~/ios/platform/passkit.md) documentación para obtener más información sobre cómo trabajar con PassKit en Xamarin.iOS.

## <a name="safari-services-framework-additions"></a>Adiciones de marco de trabajo de servicios de Safari

Apple incluye los siguientes cambios en el [Safari servicios](https://developer.xamarin.com/api/namespace/SafariServices/) framework en iOS 9:

- Ahora puede usar la nueva [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) clase para mostrar contenido web dentro de una aplicación de Xamarin.iOS. Proporciona la capacidad de compartir datos del sitio Web y las cookies con la aplicación de Safari e incluye varias características de Safari (como lector y autorrelleno). [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) características un **realiza** botón que se devolverá los usuarios a la aplicación cuando haya terminado de ver el contenido web.

Dado que la [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) clase se adapta para mostrar una sola página de contenido web, puede utilizar para reemplazar cualquiera [WKWebKit](https://developer.xamarin.com/api/type/WebKit.WKWebView/) o [UIWebView](https://developer.xamarin.com/api/type/UIKit.UIWebView/)controles dentro de las aplicaciones existentes de Xamarin.iOS.

### <a name="displaying-a-website"></a>Mostrar un sitio Web

El código siguiente es un ejemplo de llamada a un [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) desde dentro de otro controlador de vista:

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Cambios de UIKit Framework

Apple ha incluido muchas mejoras a varios elementos de la [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) framework IOS 9. Las secciones siguientes detallan esos cambios.

### <a name="3d-touch-events"></a>Eventos Touch 3D

Familiarizado con iOS 9 y el iPhone 6s y iPhone 6s Plus, 3D Touch agrega movimientos confidenciales de presión a las aplicaciones de iOS. Como resultado, si la aplicación se ejecuta en iOS 9 (o posterior) y el dispositivo iOS es capaz de apoyo 3D Touch, hará que los cambios en la presión de la `TouchesMoved` evento. 

Debido a este cambio de comportamiento, las aplicaciones iOS deben estar preparadas para la `TouchesMoved` eventos que se debe invocar más a menudo, aunque la X / coordenadas Y no han cambiado. 

Para obtener más información, vea nuestra [Introducción a la entrada táctil 3D](~/ios/platform/3d-touch.md) guía.

### <a name="document-open-in-place-functionality"></a>Funcionalidad de Open in situ de documentos

Mediante el uso del `FinishedLaunching (application, launchOptions)` o `WillFinishLaunching (Application, launchOptions)` métodos de la [UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/) (clase), ahora puede abrir un documento y modificarlo en su lugar (en lugar de trabajar en una copia).

Para admitir la nueva funcionalidad de open in situ, agregue el `LSSupportsOpeningDocumentsInPlace` clave a la aplicación de Xamarin.iOS **Info.plist** archivo con un valor de `YES`.

Vea de Apple [UIApplicationDelegate referencia](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) para obtener más detalles.

### <a name="enhanced-touch-events"></a>Eventos Touch mejorada

Apple ha desarrollado varias mejoras a los eventos Touch en iOS 9. Éstos incluyen la capacidad para usar predicción táctil y obtener acceso a los últimos retoques intermedios entre las actualizaciones de la pantalla.

Vea de Apple [Guía de control de eventos para iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) para obtener más detalles.

### <a name="fetching-tailored-content"></a>Obtener contenido adaptado

El nuevo `NSDataAsset` clase permite que una aplicación de Xamarin.iOS va a recuperar contenido adaptado a la memoria y los gráficas capacidades del dispositivo iOS que se está ejecutando en.

### <a name="new-layout-anchors"></a>Los nuevos delimitadores de diseño

El nuevo `NSLayoutAnchor` y `NSLayoutDimension` clases de delimitador de diseño funcionan con las nuevas propiedades de anclaje de la [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) clase (como `LeadingAnchor` y `WidthAnchor`) para facilitar el diseño en iOS 9.

Visite nuestro [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) documentación para obtener más información acerca de cómo trabajar con clases de tamaño y diseño automático en una aplicación de Xamarin.iOS y de Apple [NSLayoutAnchor referencia](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor), [ Referencia de NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) y [UIView referencia](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) para obtener más información.

### <a name="new-readable-content-margins"></a>Nuevos márgenes contenidos legibles

El nuevo `UILayoutGuide` clase puede usarse para proporcionar los márgenes de contenido puede leer y definir las regiones de dibujo para el contenido dentro de una vista. Vea de Apple [UILayoutGuide referencia](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) para obtener más información.

### <a name="text-input-in-notifications-modifications"></a>Entrada de texto en las modificaciones de las notificaciones

El [UIUserNotificationAction](https://developer.xamarin.com/api/type/UIKit.UIUserNotificationAction/) clase tiene un nuevo `Behavior` propiedad que puede usarse para admitir la entrada de texto de las notificaciones.

### <a name="uiapplicationdelegate-changes"></a>Cambios de UIApplicationDelegate

Mientras no formalmente en desuso por Apple, que sugieren reemplazar todas las llamadas a la `FinishedLaunching (UIApplication application)` método de la [UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/) clase con cualquiera el `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` o `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` métodos.

Vea de Apple [UIApplicationDelegate referencia](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) para obtener más detalles.

### <a name="uikit-dynamics-changes"></a>Cambios de Dynamics UIKit

Apple incluye los siguientes cambios en la dinámica UIKit en iOS 9:

- Dynamics ahora ofrece compatibilidad con los límites de la colisión no rectangulares.
- El nuevo personalizable `UIFieldBehavior` clase se usa para admitir distintos tipos de campo.
- Tipos de archivos adjuntos adicionales se han agregado a la `UIAttachmentBehavior` clase.

Vea de Apple [UIAttachment referencia](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) para obtener más detalles.

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView y UIDatePicker cambios

Antes de iOS 9, la [UIPickerView](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) y [UIDatePicker](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/) controles eran invariable y automáticamente se cambia el tamaño para llenar el ancho de su contenedor (normalmente el ancho del dispositivo iOS la aplicación estuvo ejecutando).

En iOS 9, este cambio de tamaño automático deja de producirse y los controles se representa con un ancho de punto de 320 en todos los dispositivos de iOS, independientemente del tamaño de pantalla y la orientación.

Para corregir esta situación, utilice diseño automático y las clases de tamaño para anclar el ancho del control con los bordes del contenedor primario (vista) y especificar la altura requerida. Visite nuestro [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) documentación para obtener más información acerca de cómo trabajar con clases de tamaño y diseño automático en una aplicación de Xamarin.iOS.

### <a name="new-uitextinputassistantitem-class"></a>Nueva clase de UITextInputAssistantItem

Use la nueva `UITextInputAssistantItem` clase a grupos de botones de barra de diseño en un _barra de accesos directos_. La barra de acceso directo es un área nueva que esté disponible en el teclado programable para proporcionar métodos abreviados de escritura.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introducción a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [What's New en iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
