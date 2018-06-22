---
title: Introducción a iOS 9
description: Este artículo detallan todas las características disponibles en iOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 10ed9154b92e6f13dd71f83cf4fed47585dc795f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30781277"
---
# <a name="introduction-to-ios-9"></a>Introducción a iOS 9

_Este artículo detallan todas las características disponibles en iOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.iOS._

![](images/ios9-sml.png "El logotipo de iOS 9")

Apple ha agregado varias nuevas API y servicios en iOS 9 junto con muchas mejoras en las características existentes.

## <a name="3d-touch"></a>Táctil 3D

Familiarizado con iOS 9 y el iPhone 6s y iPhone 6s Plus, 3D Touch agrega movimientos confidenciales de presión a las aplicaciones de iOS. Con 3D Touch, una aplicación de iPhone ahora es capaz de no solo indican que el usuario toca la pantalla del dispositivo, puede también tienen sentido cuánto presión es ejerciendo el usuario y responder a los niveles de presión diferentes.

Táctil 3D proporciona las siguientes características a la aplicación:

- **Sensibilidad a la presión** : aplicaciones ahora pueden medir de forma rígida o claro el usuario toca la pantalla y beneficiarse de dicha información. Por ejemplo, una aplicación de dibujo puede realizar una línea más gruesa o más delgados basándose en la presión, el usuario es tocar la pantalla.
- **Consultar y Pop** -la aplicación ahora puede permitir que el usuario interactúe con sus datos sin tener que desplazarse fuera de su contexto actual. Si presiona de disco duro en la pantalla, puede *Peek* en el elemento que le interesa (por ejemplo, una vista previa de un mensaje). Si presiona más difícil, pueden *Pop* en el elemento.
- **Acciones rápidas** -pensar de acciones rápidas como los menús contextuales que pueden ser extrae telefónico cuando un usuario hace clic con un botón en un elemento de una aplicación de escritorio. Usar acciones rápidas, puede agregar comunes, rápida y sencilla a los accesos directos de acceso a las funciones en la aplicación desde el icono de la pantalla de inicio en el dispositivo iOS.

Para obtener más información, visite nuestro [Introducción a la entrada táctil 3D](~/ios/platform/3d-touch.md) guía.

## <a name="app-transport-security"></a>Seguridad de transporte de la aplicación

Nuevo en iOS 9, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación. ATS garantiza que todas las comunicaciones de internet se ajustan para proteger la conexión de los procedimientos recomendados, lo que evita que la divulgación accidental de información confidencial, ya sea directamente a través de la aplicación o una biblioteca que está consumiendo.

Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10.11 (El capitán), todas las conexiones con [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) o [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) estará sujeto a Requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Para obtener más información acerca de ATS, visite nuestro [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md) guía.

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>Multitarea para iPad

Con iOS 9, Apple incorpora compatibilidad con multitarea dos aplicaciones en ejecución al mismo tiempo en un hardware específico iPad. Como resultado, las aplicaciones de Xamarin.iOS ya no se pueden asumir que son la única aplicación que se ejecuta en un momento dado o que tienen acceso a los recursos del dispositivo o pantalla completa.

Multitarea para iPad es compatible a través de las siguientes características:

- **Diapositiva sobre** -permite al usuario que ejecute temporalmente una segunda aplicación de iOS en una diapositiva hacia afuera panel (ya sea en el lado derecho o izquierdo de la pantalla basándose en la dirección de idioma) que cubre aproximadamente un 25% de la aplicación principal que se está ejecutando actualmente. Deslice sobre solo está disponible en un iPad Pro iPad aire, iPad 2 de aire, iPad 2 Mini, iPad Mini 3 o iPad Mini 4.
- **Vista en dos paneles** -en hardware iPad admitidos (iPad aire 2, 4 Mini iPad e iPad Pro sólo), el usuario puede seleccionar una segunda aplicación y ejecutar en paralelo con la aplicación que se está ejecutando en un modo de pantalla de división. El usuario puede controlar el porcentaje de la pantalla principal que se ocupa de cada aplicación.
- **Imagen en imagen** : para las aplicaciones que se reproduce contenido de vídeo de reproducción, el vídeo puede ahora en una ventana de tamaño variable y que puede moverse flotará sobre las otras aplicaciones que se están ejecutando actualmente en el dispositivo iOS. El usuario tiene control total sobre el tamaño y la posición de esta ventana. Imagen en imagen sólo está disponible en un iPad Pro iPad aire, iPad 2 de aire, iPad 2 Mini, iPad Mini 3 o iPad Mini 4.

Para obtener más información acerca de las nuevas capacidades de multitarea de iOS 9, consulte nuestro [multitarea para iPad](~/ios/platform/multitasking.md) guía.

## <a name="new-contacts-and-contacts-ui-frameworks"></a>Nuevos contactos y marcos de interfaz de usuario de contactos

Con la introducción de iOS 9, Apple ha lanzado dos marcos nueva, [contactos](https://developer.xamarin.com/api/namespace/Contacts/) y [ContactsUI](https://developer.xamarin.com/api/namespace/ContactsUI/), que reemplaza la libreta de direcciones existente y marcos de interfaz de usuario de libreta de direcciones usan por iOS 8 y versiones anteriores.

Estos marcos de trabajo nuevo, orientado a objetos proporcionan lo siguiente:

- **Contactos** – Xamarin.iOS proporciona acceso a información de contacto del usuario. Porque la mayoría de las aplicaciones solo requiere acceso de solo lectura, este marco de trabajo se ha optimizado para el acceso a un subproceso seguro, de solo lectura.
- **ContactsUI** : elementos de interfaz de usuario de Xamarin.iOS proporciona para mostrar, editar, seleccionar y crear contactos en dispositivos iOS.

Para obtener más información, consulte nuestro [contactos y la interfaz de usuario de contactos](~/ios/platform/contacts.md) documentación.


## <a name="new-search-apis"></a>Nuevas API de búsqueda

Se ha expandido la búsqueda en iOS 9 para proporcionar excelentes nuevas formas de acceder a la información dentro de la aplicación Xamarin.iOS. Con las nuevas API de búsqueda, puede realizar el contenido de la aplicación puede buscar a través de servicios y Safari resultados de la búsqueda, entrega y avisos de Siri y sugerencias. Esto permite un acceso rápido a los usuarios a las actividades y obtener información detallada dentro de la aplicación.

Además, las nuevas API de búsqueda facilitan la integración de búsqueda en la aplicación sin experiencia en la implementación anterior de búsqueda. Por este motivo, Apple notificaciones que suele tardar unas horas para que el contenido de una aplicación iOS 9 universalmente permite realizar búsquedas mediante la búsqueda de la aplicación.

Para obtener más información, vea nuestra [mejoras en la búsqueda](~/ios/platform/search/index.md) documentación.

## <a name="new-stack-view"></a>Nueva vista de pila

El control de vista de pila ([UIStackView](https://developer.xamarin.com/api/type/UIKit.UIStackView/)) aprovecha la eficacia de diseño automático y las clases de tamaño para administrar una pila de subvistas (de forma horizontal o vertical) que responde dinámicamente al tamaño de pantalla y la orientación del dispositivo iOS.

Mediante el control de vista de pila, la cantidad de trabajo necesario para una interfaz de usuario se reduce en gran medida de diseño. El diseño de todas las subvistas adjunta a una vista de pila se administran automáticamente según las propiedades de desarrollador definido como eje, distribución, alineación y el espaciado.

Para obtener más información, vea nuestra [Introducción a la vista de pila](~/ios/user-interface/controls/uistackview.md) documentación.


## <a name="collection-view-changes"></a>Cambios de la vista de colección

En iOS 9, la vista de colección ([UICollectionView](https://developer.xamarin.com/api/type/UIKit.UICollectionView/)) ahora admite arrastre reordenación de elementos de fábrica mediante la adición de un nuevo reconocedor de movimiento predeterminado y varios métodos auxiliares de nuevo.

Con estos nuevos métodos, puede implementar arrastrar para reordenar en la vista de colección fácilmente y tiene la posibilidad de personalizar la apariencia de elementos durante cualquier fase del proceso de reordenación.

Para obtener más información acerca de los cambios de la vista de colección para iOS 9, consulte nuestro [cambios de la vista de colección](~/ios/user-interface/controls/uicollectionview.md) guía.

## <a name="game-enhancements"></a>Mejoras de juego

Con iOS 9, Apple ha realizado varias mejoras tecnológicas a las API de juegos que facilitan la implementación de audio y gráficos de juegos en su aplicación de Xamarin.iOS. Esto incluye la facilidad de desarrollo a través de los marcos de trabajo de alto nivel y el aprovechamiento de la potencia de GPU del dispositivo iOS para mejorar la velocidad y la capacidad de gráfico con las mejoras de bajo nivel.

Esto incluye GameplayKit, ReplayKit, E/S de modelo, MetalKit y los sombreadores de rendimiento de sistema operativo junto con características nuevas y mejoradas del sistema operativo, SceneKit y SpriteKit.

Para obtener más información, vea nuestra [mejoras de juego](~/ios/platform/gaming/index.md) documentación.

## <a name="homekit-framework-changes"></a>Cambios de HomeKit Framework

El [HomeKit](https://developer.xamarin.com/api/namespace/HomeKit/) framework, que se incluye en iOS 8, proporciona la capacidad de configurar y controlar diversas accesorios de HomeKit habilitada (por ejemplo, luces automatizadas, cerraduras y abren el puerta artículos) desde una aplicación de Xamarin.iOS. Además de ser fáciles de instalar y configurar, Accesorios HomeKit pueden controlarse mediante comandos de voz Siri.

En iOS 9, Apple ha simplificaba el programa de instalación, los tipos de accesorios compatible y proporcionado más interacciones accesorios (por ejemplo, para controlar un accesorio de forma remota a través de iCloud) amplía el filtro.

Para obtener más información, consulte nuestro [Introducción a HomeKit](~/ios/platform/homekit.md), [aplicación de ejemplo de iOS de HomeKitIntro](https://developer.xamarin.com/samples/monotouch/HomeKit/HomeKitIntro/) y de Apple [HomeKit](https://developer.apple.com/homekit/) documentación.

## <a name="handoff-framework-changes"></a>Cambios de Framework de entrega

Entrega (también conocido como continuidad) se introdujo por Apple iOS 8 y OS X Yosemite (10.10) como una manera para que el usuario iniciar una actividad en uno de sus dispositivos (iOS o Mac) y siga esa misma actividad en otro de sus dispositivos (según se identifica por iClou del usuario d. cuenta).

Entrega que se haya expandido en iOS 9 para también admiten nuevas capacidades de búsqueda mejoradas. Para obtener más información, vea nuestra [mejoras en la búsqueda](~/ios/platform/search/index.md) documentación. Para obtener más información sobre el uso de entrega, vea nuestra [Introducción a la entrega](~/ios/platform/handoff.md) documentación.

## <a name="new-extension-points"></a>Nuevos puntos de extensión

En iOS 8, Apple realizados en las extensiones, las bibliotecas que se presentan por el sistema operativo en contextos estándares, como en el centro de notificaciones, cuando el usuario solicita un teclado o cuando está editando una foto.

Con iOS 9, Apple es ampliar la compatibilidad de extensión al proporcionar varios nueva _puntos de extensión_ que definir directivas de uso y proporciona las API para trabajar dentro de un área determinado como sigue:

- **Nuevo punto de extensión de la unidad de Audio** : usar este punto de extensión para proporcionar efectos de audio, instrumentos musicales, generadores de sonido, etc. para su uso en otras aplicaciones de host de la unidad de sonido (por ejemplo, GarageBand). Este punto de extensión también permite vender _unidades de Audio_ (audio complementos) en la tienda de aplicaciones.
- **Nuevo punto de extensión de mantenimiento del índice** : usar este punto de extensión para admitir la indización de datos de la aplicación sin necesidad de volver a iniciarla una aplicación.
- **Nuevos puntos de extensión de red** (se requiere el permiso especial de Apple):
    - **Extensión del proveedor de Proxy de aplicación** : Use este punto de extensión para implementar un proxy de red de cliente transparente personalizada.
    - **Proveedor de datos de filtro o extensión del proveedor de Control de filtro** -usar estos puntos de extensión para implementar en dispositivos de filtrado de contenido de red dinámica.
    - **Extensión del proveedor de túnel de paquete** : usar este punto de extensión para implementar una VPN personalizada de cliente de protocolo de túnel.
- **Nuevos puntos de extensión de Safari**:
    - **Extensión de bloqueo de contenido** : usar este punto de extensión para definir una lista de contenido bloqueado que no se mostrará cuando el usuario explora la web.
    - **Compartido vínculos extensión** : Use este punto de extensión para habilitar la visualización de contenido de la aplicación de Safari que comparten los vínculos.

Para obtener más información, vea nuestra [Introducción a las extensiones](~/ios/platform/extensions.md) y de Apple [Guía de programación de extensión de aplicación](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214) documentación.

## <a name="keychain-enhancements"></a>Mejoras de la cadena de claves

En iOS 9, Apple ha mejorado la cadena de claves para proporcionar un nuevo tipo de clave de cifrado para el Enclave seguros y más opciones de protección del elemento como se indica a continuación:

- Una nueva restricción Touch ID que invalida a elementos de la cadena de claves cuando se modifica la base de datos de huellas digitales.
- Nuevas restricciones que permiten crear solo entradas de la lista de Control de acceso con Touch ID o código de acceso.
- Un nuevo contexto de autenticación que permite invocar la autenticación independiente de `SecItem` llamadas.
- Obtener acceso a la entropía de la lista de Control (mediante la opción de contraseña de la aplicación) para el cifrado de elemento de cadena de claves proporcionada por la aplicación.
- Compatibilidad para generar y utilizar claves dentro del enclave seguro (a través de la `kSecAttrTokenIDSecureEnclave` atributo).

Para obtener más información, vea nuestra [Introducción a Touch ID](~/ios/platform/touchid.md) documentación.


## <a name="right-to-left-language-support"></a>Compatibilidad con idiomas de derecha a izquierda

En iOS 9, Apple ha hecho presentar una interfaz de usuario volteados más fácil que nunca proporcionando compatibilidad completa para los idiomas de derecha a izquierda. Entre estas estructuras se incluyen las siguientes:

- Estándar [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) controles va a invertir automáticamente según la configuración de idioma y configuración regional de los dispositivos iOS de derecha a izquierda.
- El [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) clase proporciona atributos que le permiten definir cómo debe aparecer una vista determinada cuando voltean de derecha a izquierda.
- La capacidad para voltear una imagen mediante programación utilizando la [FlipsForRightToLeftLayoutDirection](https://developer.xamarin.com/api/property/UIKit.UIImage.FlipsForRightToLeftLayoutDirection/) propiedad de la [UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/) clase.

Para obtener más información, vea de Apple [idiomas de derecha a izquierda compatibles con](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) documentación.



## <a name="additional-framework-changes"></a>Cambios de Framework adicionales

Además de los cambios más importantes que hemos tratado anteriormente, Apple ha realizado modificaciones y mejoras para varios marcos de trabajo existentes para iOS 9, incluido lo siguiente:

- Marco de trabajo AV Foundation
- AVKit Framework
- CloudKit Framework
- Framework de Foundation
- Marco de trabajo de entrega
- HealthKit Framework
- HomeKit Framework
- Marco de autenticación local
- MapKit Framework
- PassKit Framework
- El marco de trabajo de Safari Services
- UIKit Framework

Para obtener más información, vea nuestra [adicionales iOS 9 Framework cambios](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) documentación.

## <a name="deprecated-apis-and-functions"></a>Las funciones y las API en desuso

Las siguientes API y funciones en iOS 9, Apple ha quedado en desuso:

- **Dirección Libreta & interfaz de usuario de libreta de direcciones** -estas API se han reemplazado por los marcos de interfaz de usuario de contacto y póngase en contacto con. Para obtener más información, consulte nuestro [contactos y la interfaz de usuario de contactos](~/ios/platform/contacts.md) documentación.
- **CBCentralManager** - la `RetrievePeripherals` y `RetrieveConnectedPeripherals` métodos de la `CBCentralManager` clase se han quitado en iOS 9. Llamar a estos métodos hará que una aplicación se bloquee al emparejar un accesorio o al iniciar la aplicación.
- **FetchAllChanges** - la `FetchAllChanges` de la `CKFetchRecordChangesOperation` clase se ha depreciado y se quitará en iOS 9.
- **El Reproductor de Media** -marco para el Reproductor multimedia está en desuso en iOS 9. Utilice AVKit o las API de Foundation de AV en su lugar.

Para obtener una lista completa de elementos obsoletos de API específicas, consulte de Apple [iOS 9.0 API Diffs](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) documentación.

## <a name="ios-9-sample-apps"></a>Aplicaciones de ejemplo de iOS 9

Tenemos algunos [iOS 9 específica ejemplos](https://developer.xamarin.com/samples/ios/iOS9/) para empezar a trabajar:

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://developer.xamarin.com/samples/monotouch/ios9/MetalPerformanceShadersHelloWorld/)
- [MusicMotion](https://developer.xamarin.com/samples/monotouch/ios9/MusicMotion/)
- [PhotoProgress](https://developer.xamarin.com/samples/monotouch/ios9/PhotoProgress/)
- [SegueCatalog](https://developer.xamarin.com/samples/monotouch/ios9/SegueCatalog/)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

Consulte también las partes de iOS de estos ejemplos (Asistente para Mac OS X versiones procedentes!):

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introducción a la entrada táctil 3D](~/ios/platform/3d-touch.md)
- [Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)
- [Multitarea para iPad](~/ios/platform/multitasking.md)
- [Contactos y contactos de interfaz de usuario](~/ios/platform/contacts.md)
- [Nuevas API de búsqueda](~/ios/platform/search/index.md)
- [Introducción a la vista de pila](~/ios/user-interface/controls/uistackview.md)
- [Cambios de la vista de colección](~/ios/user-interface/controls/uicollectionview.md)
- [Mejoras de juegos](~/ios/platform/gaming/index.md)
- [Introducción a HomeKit](~/ios/platform/homekit.md)
- [Introducción a la entrega](~/ios/platform/handoff.md)
- [Cambios adicionales en la plataforma iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [Solución de problemas](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [What's New en iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Actualizar las aplicaciones de Xamarin.iOS a iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
