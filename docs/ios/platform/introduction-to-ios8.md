---
title: Introducción a iOS 8
description: Con iOS 8, Apple ha proporcionado una gran cantidad de API para excitar y satisfacen los desarrolladores y marcos de trabajo de nuevo. En esta guía le presentaremos estas nuevas API y vea cómo iOS 8 puede beneficiar a los desarrolladores y usuarios.
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 2f57547356adcbafd01851bc54e42a14454ccd6a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30781121"
---
# <a name="introduction-to-ios-8"></a>Introducción a iOS 8

_Con iOS 8, Apple ha proporcionado una gran cantidad de API para excitar y satisfacen los desarrolladores y marcos de trabajo de nuevo. En esta guía le presentaremos estas nuevas API y vea cómo iOS 8 puede beneficiar a los desarrolladores y usuarios._

iOS 7 cambiar visualmente la interfaz de usuario completas de iOS de qué usuarios y desarrolladores tenían pueda esperar, directamente desde el iPhone primer sistema operativo. IOS 8 continúa con esto proporcionando muchos marcos de trabajo para los desarrolladores, lo que permite a los usuarios controlar casi todos los aspectos de su vida recta desde su iPhone. Por ejemplo mantenimiento e idoneidad se pueden analizar con *HealthKit*, códigos de acceso están obsoletas con el uso de la autenticación biométrica *LocalAuthentication*, *extensiones de aplicación*abrir un canal de comunicación entre aplicaciones de terceros 3ª, y *HomeKit* permite convertir su casa en un hogar del futuro. 

Si estaba iOS 7 sobre mimar a los usuarios, iOS 8 se centra en los desarrolladores mimar con toda una gama de estas nuevas herramientas tasty. 

Esta guía presenta las nuevas API para desarrolladores de Xamarin.iOS.  

También hay algunas API que han quedado obsoletas en iOS 8, que se detallan al final de este documento.

## <a name="requirements"></a>Requisitos

Se necesita lo siguiente para crear aplicaciones de iOS 8 en Visual Studio para Mac:

- **Xcode 7 y iOS 8 o versiones más recientes** : Xcode y API de iOS más reciente de Apple deben instalarse y configurarse en el equipo del desarrollador.
- **Visual Studio para Mac** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el dispositivo de usuario.
- **Dispositivo 8 o el simulador de iOS** : un dispositivo iOS ejecutando la última versión de iOS 8 para las pruebas.

## <a name="home-and-leisure"></a>Hogar y ocio

iOS 8 ha ayudado a firmemente la planta Apple y el dispositivo iOS directamente en el núcleo de la casa mediante el uso de HomeKit y HealthKit. En esta sección, veremos cómo estos nuevos marcos de trabajo y cómo pueden integrarse en la aplicación Xamarin.iOS.

## <a name="homekit"></a>HomeKit

Controlar los dispositivos desde su iPhone no es una nueva aplicación de la tecnología; muchos productos principal conectado pueden controlarse a través de una aplicación de iOS. Sin embargo HomeKit ahora da un paso adicional mediante la promoción de un protocolo común para los dispositivos de automatización del hogar y mediante la realización de una API pública disponibles para ciertos fabricantes, como iHome, Philips y Honeywell. Para el usuario, esto significa que pueden controlar casi todos los aspectos de su casa sin problemas desde dentro de una aplicación. Es irrelevante para que sepa que están usando una bombilla Philips matiz o una alarma de nivel de anidamiento. Los usuarios también pueden encadenar numerosos procesos domésticos inteligentes juntas en "Plano".

Con HomeKit, aplicaciones de terceros y Siri pueden detectar Accesorios y agregarlos a su base de datos de configuración de inicio personal, editar y actuar sobre estos datos y comunicarse con sus servicios para realizar una acción y accesorios.

### <a name="configuration"></a>Configuración

El diagrama siguiente muestra la jerarquía básica de la configuración de accesorios HomeKit:

![](introduction-to-ios8-images/image1.png "Este diagrama muestra la jerarquía básica de la configuración de accesorios HomeKit")
 
Para empezar a trabajar con HomeKit, los desarrolladores necesiten para asegurarse de que su perfil de aprovisionamiento tiene el servicio de HomeKit seleccionado. Apple también ha proporcionado a los desarrolladores un simulador del complemento HomeKit para Xcode. Se puede encontrar en el [Centro para desarrolladores de Apple](https://developer.apple.com/downloads/index.action), en `Hardware IO Tools for Xcode`. 

Para obtener más información, vea nuestra [HomeKit](~/ios/platform/homekit.md) guía.

## <a name="healthkit"></a>HealthKit

HealthKit es un marco que se introdujo en iOS 8 que proporciona un almacén de datos centralizado, seguro y coordinada de información relacionados con el estado. El sistema operativo garantiza la privacidad y seguridad de información de estado y, a la aplicación de mantenimiento, un panel para el usuario. Con el permiso del usuario, las aplicaciones pueden leer y escribir una amplia variedad de información de estado.

Para obtener más información sobre cómo utilizar esto en la aplicación de Xamarin.iOS, consulte el [Introducción a HealthKit](~/ios/platform/healthkit.md) guía.

## <a name="extending-iphone-functionality"></a>Extender la funcionalidad de iPhone
Con iOS8, los desarrolladores que se tienen un mayor control sobre quién puede usar su aplicación y una mayor capacidad para abrir más comunicación entre las aplicaciones de terceros. Características como selector de documento y extensiones de aplicación abre todo un mundo de posibilidades de uso de aplicaciones en el ecosistema de Apple.

### <a name="app-extensions"></a>Extensiones de aplicaciones
Las extensiones de aplicación, para simplificar en exceso, son una manera para que las aplicaciones de terceros para comunicarse entre sí. Para mantener altos estándares de seguridad y mantener la integridad de las aplicaciones en un espacio aislado, esta comunicación no se presenta directamente entre aplicaciones. En su lugar, se lleva a cabo por un *extensión* en la parte central.

El primer paso para crear una extensión de la aplicación consiste en definir el punto de extensión correcto: Esto es importante para garantizar el comportamiento y la disponibilidad de las API correcta. Para crear una extensión de la aplicación en Visual Studio para Mac, puede agregarlo a una aplicación existente mediante la adición de un nuevo proyecto a la solución.

En el **nuevo proyecto** diálogo navegar a **C#** > **iOS** > **API unificada**  >   **Extensiones**, tal y como se muestra en la captura de pantalla siguiente:

![](introduction-to-ios8-images/image2.png "Crear una nueva extensión")
 
El cuadro de diálogo nuevo proyecto proporciona siete nuevas plantillas de proyecto para crear extensiones de aplicación y se describen a continuación. Tenga en cuenta que muchas de las extensiones se relacionan con otro nuevas API de iOS, por ejemplo, el selector de documento:

- **Acción** : Esto permite a los desarrolladores crear botones de acción personalizado único que permite a los usuarios realizan determinadas tareas
- **Teclado** : Esto permite a los programadores agregar al intervalo de integrada en los teclados de Apple agregando su propios uno personalizado. El teclado popular, Swype lo utiliza para poner su teclado para iOS.
- **Selector de documento** – contiene un controlador de vista de selector de documento que permite a los usuarios tener acceso a archivos que están fuera del espacio aislado de la aplicación.
- **Proveedor de selector de archivos de documento** : Esto proporciona un almacenamiento seguro de archivos mediante el selector de documento.
- **Edición de fotografías** : esta se expande en los filtros y herramientas ya proporcionadas por Apple en la aplicación fotos para proporcionar a los usuarios más control y más opciones al editar sus fotos de edición.
- **Hoy en día** : Esto proporciona a las aplicaciones la capacidad para mostrar widgets en la sección de hoy en día del centro de notificaciones.

Para obtener más información sobre el uso de extensiones de aplicaciones de Xamarin, consulte el [Introducción a las extensiones de aplicación](~/ios/platform/extensions.md) guía.

### <a name="touch-id"></a>Id. de entrada táctil

Id. de entrada táctil se introdujo en iOS 7 como un medio para autenticar al usuario, similar a un código de acceso. Sin embargo, se limitaba a desbloquear el dispositivo, uso de la tienda de aplicaciones, utilizando iTunes y autenticar sólo la cadena de claves de iCloud 

Ahora hay dos maneras de utilizar Touch ID como mecanismo de autenticación en aplicaciones de iOS 8 mediante la API de autenticación Local. Actualmente no es posible utilizar la autenticación Local para autenticar de manera remota.

En primer lugar, ayuda a los servicios de cadenas de claves existentes mediante el uso de nuevos llaveros Control listas de acceso (ACL). Datos de cadena de claves se pueden desbloqueadas con la autenticación correcta de los usuarios mediante las huellas digitales.

En segundo lugar, LocalAuthentication proporciona dos métodos para autenticar su aplicación localmente. Los desarrolladores deben extremar `CanEvaluatePolicy` para determinar si el dispositivo es capaz de Aceptar Touch ID y, a continuación, `EvaluatePolicy` para iniciar la operación de autenticación.

Para obtener más información en Touch ID y para obtener información sobre cómo integrar en una aplicación de Xamarin.iOS, consulte el [Introducción a TouchID](~/ios/platform/touchid.md) guías.

### <a name="document-picker"></a>Selector de documento

Documento selector funciona con una unidad de iCloud a los usuarios para permitir al usuario abrir los archivos que se han creado en una aplicación distinta, importarán y manipulan y exportación revertir nuevo. Esto crea un flujo de trabajo intuitivo y, por tanto, una experiencia mucho mejor, para los usuarios. Sincronizando iCloud toma un paso adicional, cualquier cambio realizado en una aplicación también se reflejará forma coherente en todos los dispositivos.

Para obtener información sobre el selector de documento en mayor profundidad y para obtener información sobre cómo integrar en una aplicación Xamarin.iOS, hacen referencia a la [Introducción para el selector de documento](~/ios/platform/document-picker.md) guía.

### <a name="handoff"></a>Handoff

Entrega, que forma parte de la característica de continuidad mayor, toma un paso más hacia la integración de iOS y OS X. Esto incluye AirDrop multiplataforma, la capacidad de realizar llamadas de iPhone, SMS en el mejoras en tethering desde su iPhone, iPad y Mac.

Entrega funciona con iOS 8 y Yosemite y requiere una cuenta de iCloud para iniciar sesión en los distintos dispositivos que desea usar. Debe funcionar con aplicaciones de Apple más previamente instaladas, incluido Safari, iWork, mapas, calendarios y contactos.

Para obtener más información, vea nuestra [entrega](~/ios/platform/handoff.md) guía.

## <a name="unified-storyboards"></a>Guiones gráficos unificados
incluye un nuevo iOS 8 más fácil utilizar el mecanismo para crear la interfaz de usuario, el guión gráfico unificado. Con un solo guión gráfico para cubrir todos los tamaños de pantalla de hardware diferente, se pueden crear vistas rápidas y con capacidad de respuesta en un estilo "diseñar una vez y utilizar muchas" true.

Antes de iOS8, los desarrolladores usaban `UIInterfaceOrientation` para distinguir entre los modos vertical y horizontal, y `UIInterfaceIdiom` para distinguir entre los dispositivos de iOS. En iOS8 ya no es necesario crear guiones gráficos independientes para los dispositivos iPhone y iPad, orientación y el dispositivo se determinan mediante el uso de *clases de tamaño*.

Todos los dispositivos se define mediante una clase de tamaño, en vertical y el eje horizontal, y hay dos tipos de clases de tamaño en iOS 8:

- **Regular** -se trata de un tamaño de pantalla de gran tamaño (por ejemplo, un iPad) o un gadget que da la impresión de un tamaño grande (por ejemplo, un UIScrollView
- **Compact** -se trata de dispositivos más pequeños (por ejemplo, el iPhone). Este tamaño tiene en cuenta la orientación del dispositivo.

Si los dos conceptos se usan juntos, el resultado es una cuadrícula de 2 x 2 que define los posibles tamaños diferentes que pueden utilizarse en las orientaciones que no son iguales, tal como se muestra en el diagrama siguiente:

![](introduction-to-ios8-images/image3.png "Un diagrama que represente la cuadrícula de 2 x 2 que define los posibles tamaños diferentes en los que se pueden usar en ambos las orientaciones que no son iguales")
 
Para obtener más información acerca de las clases de tamaño, consulte la [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md).

## <a name="photo-kit"></a>Kit de fotografías
Kit de foto es un nuevo marco que permite a las aplicaciones para la biblioteca de imágenes de sistema de consulta y crear interfaces de usuario personalizadas para ver y modificar su contenido. Incluye una serie de clases que representan la imagen y recursos de vídeo, así como las colecciones de recursos como álbumes y carpetas.

Para obtener más información, vea nuestra [PhotoKit](~/ios/platform/photokit.md) guía.

## <a name="games"></a>Juegos

<a name="scenekit" />

### <a name="scene-kit"></a>Kit de escena

Kit de escena es un gráfico de escena 3D API que simplifica el trabajo con gráficos 3D. Apareció por primera vez en OS X 10.8 y ha llegado a iOS 8. Con el Kit de escena crear visualizaciones 3D envolventes y juegos 3D ocasionales no requiere tener conocimientos de OpenGL. Basándose en los conceptos de gráfico de escena comunes, Kit de escena abstrae las complejidades de OpenGL y OpenGL ES, facilitando muy 3D de agregar contenido a una aplicación. Sin embargo, si es un experto de OpenGL, Kit de escena tiene mayor compatibilidad para enlazar directamente con OpenGL así. También incluye varias características que complementan a los gráficos 3D, como física y se integra perfectamente con otros marcos de Apple, como animación de núcleo, imagen Core y Sprite Kit.

Para obtener más información, vea nuestra [SceneKit](~/ios/platform/gaming/scenekit.md) documentación.

<a name="spritekit" />

### <a name="sprite-kit"></a>Kit de Sprite

Kit de Sprite, el marco de juego 2D de Apple, tiene algunas nuevas características interesantes de iOS 8 y OS X Yosemite. Estos incluyen la integración con el Kit de escena, compatibilidad con el sombreador, iluminación, sombras, restricciones, la generación de asignación normal y mejoras de ciencias físicas. En particular, las nuevas características de ciencias físicas asegurarse muy fácil agregar efectos realistas a un juego.

Para obtener más información, vea nuestra [SpriteKit](~/ios/platform/gaming/spritekit.md) documentación.

## <a name="other-changes"></a>Otros cambios
Así como los cambios más importantes en iOS 8 descritas anteriormente, Apple además actualizó muchos marcos de trabajo existentes. Estos se detallan a continuación:

- **[Principales imagen](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)**  : Apple se desarrolla su marco de trabajo de procesamiento de imagen mediante la adición de una mejor compatibilidad con la detección de regiones rectangulares y códigos QR dentro de las imágenes. Mike Bluestein se explora en su blog post titulada " [imagen de detección en iOS 8](http://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>Interfaces API en desuso
Con todas las mejoras realizadas en iOS 8, que en una serie de interfaces API en desuso. A continuación se detallan algunas de ellas.

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)**  : los métodos y propiedades que se usan para registrar las notificaciones remotas han en desuso. Se trata de registerForRemoteNotificationTypes y enabledRemoteNotificationTypes.
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)**  : clases de tamaño y rasgos reemplazaron los métodos y propiedades que se usan para describir la orientación de la interfaz. Hacer referencia a la [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) para obtener más información sobre cómo usar estos.

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)**  : se ha reemplazado por UISearchController en iOS8.

## <a name="summary"></a>Resumen
En este artículo hemos examinado algunas de las nuevas características introducidas por Apple en iOS 8.



## <a name="related-links"></a>Vínculos relacionados

- [UIKitEnhancements (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/UIKitEnhancements)
- [Introducción a las extensiones de aplicación](~/ios/platform/extensions.md)
- [Introduction to CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) (Introducción a CloudKit)
- [Introducción al selector de documento](~/ios/platform/document-picker.md)
- [Introducción a HealthKit](~/ios/platform/healthkit.md)
- [Introducción a los controles de cámara Manual](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [Introducción a TouchID](~/ios/platform/touchid.md)
- [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
