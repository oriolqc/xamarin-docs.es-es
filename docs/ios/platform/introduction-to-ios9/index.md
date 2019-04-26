---
title: Introducción a iOS 9
description: Este artículo presenta todas las características disponibles en iOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: d0ad25a1ecff7262b9b4b41a5f9d73a5931bbd1c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946239"
---
# <a name="introduction-to-ios-9"></a>Introducción a iOS 9

_Este artículo presenta todas las características disponibles en iOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.iOS._

![](images/ios9-sml.png "El logotipo de iOS 9")

Apple ha agregado varias nuevas API y servicios en iOS 9, junto con muchas mejoras en las características existentes.

## <a name="3d-touch"></a>Entrada táctil 3D

Nuevo a iOS 9 y el iPhone 6s y iPhone 6s Plus, 3D Touch agrega gestos confidenciales presión a las aplicaciones de iOS. Con 3D Touch, una aplicación de iPhone ahora es capaz de indicar no solo a la que el usuario toca la pantalla del dispositivo, puede también detectan cuánto presión está ejerciendo el usuario y responder a los niveles de presión diferentes.

3D Touch proporciona las siguientes características a la aplicación:

- **Sensibilidad a la presión** : aplicaciones ahora se pueden medir hard o claro, el usuario toca la pantalla y beneficiarse de dicha información. Por ejemplo, una aplicación de dibujo puede hacer que una línea más gruesa o más delgados en función de lo difícil el usuario está tocando la pantalla.
- **Inspeccionar y Pop** -la aplicación ahora puede permitir que el usuario interactúe con sus datos sin tener que salir de su contexto actual. Al pulsar el disco duro en la pantalla, pueden *Peek* en el elemento les interesa (por ejemplo, para obtener una vista previa de un mensaje). Si presiona más difícil, pueden *Pop* en el elemento.
- **Acciones rápidas** -pensar de acciones rápidas, como los menús contextuales que pueden ser sacado de la copia de seguridad cuando un usuario del botón secundario en un elemento de una aplicación de escritorio. Con acciones rápidas, puede agregar comunes, rápido y fácil a los métodos abreviados de acceso a las funciones en la aplicación desde el icono de pantalla de inicio en el dispositivo iOS.

Para obtener más información, consulte nuestra [Introducción a 3D Touch](~/ios/platform/3d-touch.md) guía.

## <a name="app-transport-security"></a>Seguridad de transporte de aplicación

Nuevo a iOS 9, App Transport Security (ATS) aplica las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación. ATS garantiza que todas las comunicaciones de internet se ajustan para proteger la conexión de los procedimientos recomendados, impidiendo así la revelación accidental de información confidencial, ya sea directamente a través de la aplicación o una biblioteca que lo consume.

Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10.11 (El capitán), todas las conexiones mediante [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) o [NSUrlSession](xref:Foundation.NSUrlSession) estará sujeto a Requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Para obtener más información acerca de ATS, consulte nuestra [App Transport Security](~/ios/app-fundamentals/ats.md) guía.

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>Multitarea para iPad

Con iOS 9, Apple ha agregado compatibilidad con multitarea ejecuta dos aplicaciones al mismo tiempo en un hardware específico iPad. Como resultado, las aplicaciones de Xamarin.iOS ya no pueden asumir que son la única aplicación que se ejecuta en un momento dado o que tienen acceso a la pantalla completa o los recursos del dispositivo.

Multitarea para iPad es compatible a través de las siguientes características:

- **Diapositiva sobre** -permite al usuario ejecutar temporalmente una segunda aplicación de iOS en una diapositiva panel (ya sea en el lado derecho o izquierdo de la pantalla según la dirección del idioma) que abarca aproximadamente el 25% de la aplicación principal que se está ejecutando actualmente. Deslice a través solo está disponible en un iPad Pro, iPad Air, iPad 2 de aire, iPad 2 Mini, Mini 3 iPad o iPad Mini 4.
- **Vista en dos paneles** -en hardware iPad admitidas (iPad Air 2, 4 Mini iPad e iPad solo para Pro), el usuario puede seleccionar una segunda aplicación y ejecutarlo en paralelo con la aplicación que se está ejecutando en un modo de pantalla dividida. El usuario puede controlar el porcentaje de la pantalla principal que ocupa cada aplicación.
- **Imagen en imagen** : para las aplicaciones que se reproduzcan reproducir contenido de vídeo, el vídeo puede ahora en una ventana movible y redimensionable que flota sobre las demás aplicaciones que se están ejecutando actualmente en el dispositivo iOS. El usuario tiene control total sobre el tamaño y posición de esta ventana. Imagen en imagen sólo está disponible en un iPad Pro, iPad Air, iPad 2 de aire, iPad 2 Mini, Mini 3 iPad o iPad Mini 4.

Para obtener más información acerca de las nuevas capacidades de multitarea de iOS 9, consulte nuestra [multitarea para iPad](~/ios/platform/multitasking.md) guía.

## <a name="new-contacts-and-contacts-ui-frameworks"></a>Nuevos contactos y los marcos de interfaz de usuario de contactos

Con la introducción de iOS 9, Apple ha lanzado dos nuevos marcos de trabajo, [contactos](xref:Contacts) y [ContactsUI](xref:ContactsUI), que reemplace la libreta de direcciones existente y marcos de interfaz de usuario de libreta de direcciones usan por iOS 8 y versiones anteriores.

Estos marcos de trabajo nuevo, orientada a objetos proporcionan lo siguiente:

- **Contactos** – Xamarin.iOS proporciona acceso a información de contacto del usuario. Dado que la mayoría de las aplicaciones solo requieren acceso de solo lectura, este marco de trabajo se ha optimizado para el acceso seguro y de solo lectura de subproceso.
- **ContactsUI** : elementos de interfaz de usuario de Xamarin.iOS proporciona para mostrar, editar, seleccione y crear contactos en dispositivos iOS.

Para obtener más información, consulte nuestra [contactos y la interfaz de usuario de contactos](~/ios/platform/contacts.md) documentación.


## <a name="new-search-apis"></a>Nuevas API de búsqueda

Se ha ampliado la búsqueda en iOS 9 para proporcionar excelentes nuevas formas de acceder a la información dentro de la aplicación de Xamarin.iOS. Mediante las nuevas API de búsqueda, puede realizar el contenido de la aplicación que se puede buscar a través de Spotlight y Safari resultados de la búsqueda, entrega y avisos de Siri y sugerencias. Esto permite un acceso rápido a los usuarios a las actividades y la información en profundidad dentro de la aplicación.

Además, las nuevas API de búsqueda facilitan la integración de búsqueda en la aplicación sin experiencia en la implementación anterior de búsqueda. Por este motivo, notificaciones de Apple que suele tardar unas horas en que el contenido de una aplicación iOS 9 universalmente mediante la búsqueda de la aplicación de búsqueda.

Para obtener más información, consulte nuestra [mejoras en la búsqueda](~/ios/platform/search/index.md) documentación.

## <a name="new-stack-view"></a>Nueva vista de pila

El control de vista de pila ([UIStackView](xref:UIKit.UIStackView) aprovecha la eficacia del diseño automático y las clases de tamaño para administrar una pila de subvistas (horizontal o verticalmente) que responde de forma dinámica al tamaño de pantalla y la orientación del dispositivo iOS.

Mediante el control de vista de pila, la cantidad de trabajo necesarios para una interfaz de usuario se reduce en gran medida el diseño. El diseño de todas las subvistas asociada a una vista de pila se administran automáticamente en función de propiedades definido por el desarrollador como eje, distribución, alineación y espaciado.

Para obtener más información, consulte nuestra [Introducción a la vista de pila](~/ios/user-interface/controls/uistackview.md) documentación.


## <a name="collection-view-changes"></a>Cambios de la vista de colección

En iOS 9, la vista de colección ([UICollectionView](xref:UIKit.UICollectionView) ahora admite arrastra la reordenación de elementos desde el principio mediante la adición de un reconocedor de movimiento predeterminado nuevo y varios métodos auxiliares de nuevo.

Con estos nuevos métodos, puede implementar arrastrar para volver a ordenar en la vista de colección fácilmente y tiene la opción de personalizar la apariencia de los elementos durante cualquier fase del proceso de reordenación.

Para obtener más información acerca de los cambios de la vista de colección para iOS 9, consulte nuestra [los cambios de la vista de colección](~/ios/user-interface/controls/uicollectionview.md) guía.

## <a name="game-enhancements"></a>Mejoras de juegos

Con iOS 9, Apple ha realizado varias mejoras tecnológicas a las API de juegos que resulte más fácil implementar gráficos de juegos y audio en la aplicación de Xamarin.iOS. Estos incluyen la facilidad de desarrollo a través de marcos de alto nivel y aprovechar la eficacia de GPU de su dispositivo iOS para mejorar la velocidad y capacidades gráficas con mejoras de bajo nivel.

Esto incluye GameplayKit, ReplayKit, modelo de E/S, MetalKit y sombreadores de rendimiento del sistema operativo junto con las características nuevas y mejoradas de Metal, SceneKit y SpriteKit.

Para obtener más información, consulte nuestra [juego mejoras](~/ios/platform/gaming/index.md) documentación.

## <a name="homekit-framework-changes"></a>Cambios en la plataforma HomeKit

El [HomeKit](xref:HomeKit) framework, que se introdujo en iOS 8, proporciona la capacidad de configurar y controlar diversas accesorios de HomeKit habilitado (por ejemplo, las luces automatizadas, cerraduras y puertas del garaje) desde una aplicación de Xamarin.iOS. Además de ser fácil de instalar y configurar, Accesorios de HomeKit pueden controlarse a través de comandos de voz Siri.

En iOS 9, Apple ha facilitado el programa de instalación, expande los tipos de accesorios admite y proporciona más interacciones accesorios (por ejemplo, para controlar un accesorio de forma remota a través de iCloud).

Para obtener más información, consulte nuestra [Introducción a HomeKit](~/ios/platform/homekit.md), [HomeKitIntro iOS aplicación de ejemplo](https://developer.xamarin.com/samples/monotouch/HomeKit/HomeKitIntro/) y Apple [HomeKit](https://developer.apple.com/homekit/) documentación.

## <a name="handoff-framework-changes"></a>Cambios de la plataforma de entrega

HANDOFF (también conocido como continuidad) se introdujo por Apple en iOS 8 y OS X Yosemite (10.10) como una forma para que el usuario iniciar una actividad en uno de sus dispositivos (iOS o Mac) y continuar con esa misma actividad en otro de sus dispositivos (que se identifica con iClou del usuario d. cuenta).

Entrega se expandió en iOS 9 para también admiten nuevas capacidades de búsqueda mejoradas. Para obtener más información, consulte nuestra [mejoras en la búsqueda](~/ios/platform/search/index.md) documentación. Para obtener más información sobre el uso de entrega, vea nuestra [Introducción a la entrega](~/ios/platform/handoff.md) documentación.

## <a name="new-extension-points"></a>Nuevos puntos de extensión

En iOS 8, Apple ha incorporado las extensiones, las bibliotecas que se presentan por el sistema operativo en contextos estándares, como en el centro de notificaciones cuando el usuario solicita un teclado, o cuando está editando una foto.

Con iOS 9, Apple es ampliar la compatibilidad de extensión, ya que proporciona varias nuevas _puntos de extensión_ que definir directivas de uso y proporcionan API para trabajar en un área determinada como sigue:

- **Nuevo punto de extensión de unidad de Audio** : Use este punto de extensión para proporcionar los efectos de audio, instrumentos musicales, generadores de sonido, etc. para su uso en otras aplicaciones de host de la unidad de Audio (por ejemplo, GarageBand). Este punto de extensión también permite vender _unidades de Audio_ (audio plug-ins) en la aplicación de Store.
- **Nuevo punto de extensión de mantenimiento de índice** : Use este punto de extensión para admitir la reindización de datos de la aplicación sin necesidad de volver a iniciarla una aplicación.
- **Nuevos puntos de extensión de red** (se requiere el permiso especial de Apple):
    - **Extensión de proveedor de la aplicación Proxy** : Use este punto de extensión para implementar un proxy de red transparente de cliente personalizada.
    - **Proveedor de datos de filtro / extensión de proveedor de Control de filtro** -usar estos puntos de extensión para implementar en el dispositivo de filtrado de contenido dinámico de red.
    - **Extensión de proveedor de túnel paquetes** : Use este punto de extensión para implementar una VPN personalizada de cliente de protocolo de túnel.
- **Nuevos puntos de extensión de Safari**:
    - **Extensión de bloqueo de contenido** : Use este punto de extensión para definir una lista de contenido bloqueado que no se mostrará cuando el usuario explora la web.
    - **Compartir vínculos extensión** : Use este punto de extensión para habilitar la visualización del contenido de la aplicación en los vínculos compartidos de Safari.

Para obtener más información, consulte nuestra [Introducción a las extensiones](~/ios/platform/extensions.md) y Apple [Guía de programación de extensión de aplicación](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214) documentación.

## <a name="keychain-enhancements"></a>Mejoras de la cadena de claves

En iOS 9, Apple ha mejorado la cadena de claves para proporcionar un nuevo tipo de clave de cifrado para el Enclave seguro y más opciones de protección del elemento como sigue:

- Una restricción de Touch ID que invalida los elementos de la cadena de claves cuando se modifica la base de datos de la huella digital.
- Las restricciones nuevas que permiten crear entradas de la lista de Control de acceso con Touch ID o código de acceso solo.
- Un nuevo contexto de autenticación que permite invocar independiente de la autenticación `SecItem` llamadas.
- Obtener acceso a la entropía de la lista de Control (con la opción de contraseña de la aplicación) para el cifrado de cadenas de claves de aplicación proporcionado por el elemento.
- Compatibilidad para generar y utilizar claves dentro el enclave seguro (a través de la `kSecAttrTokenIDSecureEnclave` atributo).

Para obtener más información, consulte nuestra [Introducción a Touch ID](~/ios/platform/touchid.md) documentación.


## <a name="right-to-left-language-support"></a>Compatibilidad con idiomas de derecha a izquierda

En iOS 9, Apple ha hecho de presentar una interfaz de usuario volteado más fácil que nunca, ya que proporciona compatibilidad completa para los idiomas de derecha a izquierda. Entre estas estructuras se incluyen las siguientes:

- Estándar [UIKit](xref:UIKit) controles va a invertir automáticamente según la configuración de idioma y de dispositivos iOS de derecha a izquierda.
- El [UIView](xref:UIKit.UIView) clase proporciona atributos que le permiten definir cómo debe aparecer una vista determinada cuando voltean de derecha a izquierda.
- La capacidad para voltear una imagen mediante programación utilizando la [FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection) propiedad de la [UIImage](xref:UIKit.UIImage) clase.

Para obtener más información, consulte Apple [idiomas de derecha a izquierda auxiliares](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) documentación.



## <a name="additional-framework-changes"></a>Cambios de marco de trabajo adicionales

Además de los cambios más importantes que hemos tratado anteriormente, Apple ha realizado modificaciones y mejoras para varios marcos existentes para iOS 9, incluido lo siguiente:

- Marco de AV Foundation
- AVKit Framework
- CloudKit Framework
- Marco de Foundation
- Marco de trabajo de entrega
- Framework HealthKit
- Marco de HomeKit
- Marco de autenticación local
- MapKit Framework
- Marco de PassKit
- Marco de servicios de Safari
- Marco UIKit

Para obtener más información, consulte nuestra [cambios adicionales en iOS 9 Framework](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) documentación.

## <a name="deprecated-apis-and-functions"></a>Las funciones y las API en desuso

Apple ha dejado de las siguientes API y funciones en iOS 9:

- **Libro y la interfaz de usuario de libreta de direcciones de direcciones** -estas API se han reemplazado por los marcos de contacto y póngase en contacto con la interfaz de usuario. Para obtener más información, consulte nuestra [contactos y la interfaz de usuario de contactos](~/ios/platform/contacts.md) documentación.
- **CBCentralManager** : la `RetrievePeripherals` y `RetrieveConnectedPeripherals` métodos de la `CBCentralManager` clase se han quitado en iOS 9. Llamar a estos métodos hará que una aplicación se bloquee al emparejar un accesorio o iniciar la aplicación.
- **FetchAllChanges** : la `FetchAllChanges` de la `CKFetchRecordChangesOperation` se ha depreciado de clase y se quitará en iOS 9.
- **Media Player** -framework el Reproductor ha quedado obsoleto en iOS 9. Use AVKit o AV Foundation API en su lugar.

Para obtener una lista completa de elementos obsoletos de API específicas, consulte Apple [iOS 9.0 diferencias de API](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) documentación.

## <a name="ios-9-sample-apps"></a>Aplicaciones de ejemplo de iOS 9

Tenemos algunos [ejemplos específicos del 9 de iOS](https://developer.xamarin.com/samples/ios/iOS9/) para empezar a trabajar:

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://developer.xamarin.com/samples/monotouch/ios9/MetalPerformanceShadersHelloWorld/)
- [MusicMotion](https://developer.xamarin.com/samples/monotouch/ios9/MusicMotion/)
- [PhotoProgress](https://developer.xamarin.com/samples/monotouch/ios9/PhotoProgress/)
- [SegueCatalog](https://developer.xamarin.com/samples/monotouch/ios9/SegueCatalog/)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

Consulte también las partes de iOS de estos ejemplos (complementaria Mac OS X versiones procedentes!):

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introducción a 3D Touch](~/ios/platform/3d-touch.md)
- [Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)
- [Multitarea para iPad](~/ios/platform/multitasking.md)
- [Contactos y contactos de la interfaz de usuario](~/ios/platform/contacts.md)
- [Nuevas API de búsqueda](~/ios/platform/search/index.md)
- [Introducción a la vista de pila](~/ios/user-interface/controls/uistackview.md)
- [Cambios de la vista de colección](~/ios/user-interface/controls/uicollectionview.md)
- [Mejoras de juegos](~/ios/platform/gaming/index.md)
- [Introducción a HomeKit](~/ios/platform/homekit.md)
- [Introducción a la entrega](~/ios/platform/handoff.md)
- [Cambios adicionales en la plataforma iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [Solución de problemas](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Actualizar las aplicaciones de Xamarin.iOS para IOS 9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
