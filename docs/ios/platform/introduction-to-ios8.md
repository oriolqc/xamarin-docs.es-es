---
title: Introducción a iOS 8
description: Con iOS 8, Apple ha proporcionado una gran cantidad de nuevos marcos y API para excitar y deleitan a los desarrolladores. En esta guía le presentaremos estas nuevas API y vea cómo puede beneficiar a iOS 8 los desarrolladores y usuarios.
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 8e95e674f52cb23be769473e27dc8eb785a77820
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116048"
---
# <a name="introduction-to-ios-8"></a>Introducción a iOS 8

_Con iOS 8, Apple ha proporcionado una gran cantidad de nuevos marcos y API para excitar y deleitan a los desarrolladores. En esta guía le presentaremos estas nuevas API y vea cómo puede beneficiar a iOS 8 los desarrolladores y usuarios._

iOS 7 puede cambiar visualmente la interfaz de usuario completas de iOS desde qué usuarios y desarrolladores hubiera acudido a esperar, directamente desde el iPhone primer sistema operativo. IOS 8 sigue teniendo esto proporcionando muchos marcos de trabajo para los desarrolladores, lo que permite a los usuarios controlar casi cualquier aspecto de su vida directamente desde su iPhone. Por ejemplo se pueden analizar la salud y estado con *HealthKit*, los códigos de acceso están obsoletas con el uso de la autenticación biométrica *LocalAuthentication*, *extensiones de aplicación*abrir un canal de comunicación entre aplicaciones de terceros 3ª, y *HomeKit* permite la capacidad de convertir su casa en un hogar del futuro. 

Si estaba iOS 7 sobre deleitar a los usuarios, iOS 8 se centra en deleitar a los desarrolladores con toda una gama de estas nuevas herramientas sabrosas. 

Esta guía presenta las nuevas API para desarrolladores de Xamarin.iOS.  

También hay algunas API que han quedado obsoletas en iOS 8, que se detallan al final de este documento.

## <a name="requirements"></a>Requisitos

Los siguientes son necesarios para crear aplicaciones de iOS 8 en Visual Studio para Mac:

- **Xcode 7 y iOS 8 o posterior** : Xcode y API de iOS más reciente de Apple deben estar instalado y configurado en el equipo del desarrollador.
- **Visual Studio para Mac** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el dispositivo del usuario.
- **iOS 8 dispositivo o simulador** : un dispositivo iOS ejecutando la última versión de iOS 8 para las pruebas.

## <a name="home-and-leisure"></a>Página principal y el ocio

iOS 8 ha ayudado a firmemente vegetales y el dispositivo iOS de Apple directamente en el núcleo de su casa mediante el uso de HomeKit y HealthKit. En esta sección, veremos cómo ambos estos marcos funcionan y cómo pueden integrarse en la aplicación de Xamarin.iOS.

## <a name="homekit"></a>HomeKit

Controlar los dispositivos desde su iPhone no es una nueva aplicación de la tecnología; muchos productos principal conectado pueden controlarse a través de una aplicación de iOS. Sin embargo HomeKit ahora lleva esto un paso más allá mediante la promoción de un protocolo común para los dispositivos de automatización del hogar y mediante la realización de una API pública disponible para determinados fabricantes, como iHome, Philips y Honeywell. Para el usuario, esto significa que puede controlar casi cualquier aspecto de su casa sin problemas desde dentro de una aplicación. Es irrelevante para que sepan que usan una bombilla Hue Philips, o una alarma de anidamiento. Los usuarios también pueden encadenar numerosos procesos domésticos inteligentes juntas en "Escenas".

Con HomeKit, las aplicaciones de terceros y Siri pueden detectar Accesorios y agregarlos a su base de datos de configuración principal personal, editar y actuar sobre estos datos y comunicarse con accesorios y sus servicios para realizar una acción.

### <a name="configuration"></a>Configuración

El diagrama siguiente muestra la jerarquía de la configuración de accesorios de HomeKit básica:

![](introduction-to-ios8-images/image1.png "Este diagrama muestra la jerarquía básica de la configuración de accesorios de HomeKit")
 
Para empezar a usar HomeKit, los desarrolladores deben asegurarse de que su perfil de aprovisionamiento tiene el servicio de HomeKit seleccionado. Apple también ha proporcionado a los desarrolladores un simulador del complemento HomeKit para Xcode. Se puede encontrar en el [Centro para desarrolladores de Apple](https://developer.apple.com/downloads/index.action), en `Hardware IO Tools for Xcode`. 

Para obtener más información, consulte nuestra [HomeKit](~/ios/platform/homekit.md) guía.

## <a name="healthkit"></a>HealthKit

HealthKit es un marco que se introdujo en iOS 8 que proporciona un almacén de datos centralizado, coordinado y seguro para obtener información relacionada con el mantenimiento. El sistema operativo garantiza la privacidad y seguridad de información de estado y, con la aplicación de mantenimiento, un panel para el usuario. Con el permiso del usuario, las aplicaciones pueden leer y escribir una amplia variedad de información de estado.

Para obtener más información sobre su uso en la aplicación de Xamarin.iOS, consulte el [Introducción a HealthKit](~/ios/platform/healthkit.md) guía.

## <a name="extending-iphone-functionality"></a>Extender la funcionalidad de iPhone
Con iOS8, los desarrolladores que se han conseguido un mayor control sobre quién puede usar su aplicación y una mayor capacidad para abrir más comunicación entre las aplicaciones de terceros. Características como extensiones de aplicación y el selector de documentos abren un mundo de posibilidades de uso de aplicaciones en el ecosistema de Apple.

### <a name="app-extensions"></a>Extensiones de aplicación
Extensiones de aplicación, para simplificar en exceso, son una forma para las aplicaciones de terceros para comunicarse entre sí. Para mantener altos estándares de seguridad y mantener la integridad de las aplicaciones en espacio aislado, no ocurre esta comunicación directamente entre aplicaciones. En su lugar, se lleva a cabo por un *extensión* en el medio.

El primer paso para crear una extensión de la aplicación es definir el punto de extensión correcto, esto es importante garantizar el comportamiento y la disponibilidad de las API correctas. Para crear una extensión de la aplicación en Visual Studio para Mac, puede agregarlo a una aplicación existente mediante la adición de un nuevo proyecto a la solución.

En el **nuevo proyecto** cuadro de diálogo navegar a **C#**  >  **iOS** > **Unified API**  >  **Extensiones**, tal y como se muestra en la captura de pantalla siguiente:

![](introduction-to-ios8-images/image2.png "Crear una nueva extensión")
 
El cuadro de diálogo nuevo proyecto proporciona siete nuevas plantillas de proyecto para crear extensiones de aplicación y, a continuación se comentan. Tenga en cuenta que muchas de las extensiones se relacionan con otro nuevas API en iOS, por ejemplo, el selector de documentos:

- **Acción** : Esto permite a los desarrolladores crear botones de acción personalizado único que permite a los usuarios realizan determinadas tareas
- **Teclado personalizado** : Esto permite a los desarrolladores agregar a la gama de creado en los teclados de Apple mediante la adición de su propios uno personalizado. El teclado más popular, Swype usa esto para poner su teclado para iOS.
- **Selector de documentos** – contiene un controlador de vista de selector de documentos que permite a los usuarios tener acceso a archivos que estén fuera del recinto de seguridad de la aplicación.
- **Proveedor de archivos de selector de documentos** : Esto proporciona un almacenamiento seguro para los archivos mediante el selector de documentos.
- **Edición de fotos** : esta se expande en los filtros y ya proporcionadas por Apple de la aplicación fotos para ofrecer a los usuarios más control y más opciones al editar sus fotografías de las herramientas de edición.
- **Hoy en día** : Esto da a las aplicaciones la capacidad para mostrar widgets en la sección de hoy del centro de notificaciones.

Para obtener más información sobre el uso de extensiones de aplicación de Xamarin, consulte el [Introducción a las extensiones de aplicación](~/ios/platform/extensions.md) guía.

### <a name="touch-id"></a>Id. de toque

Id. de toque se introdujo en iOS 7 como un medio para autenticar al usuario, similar a un código de acceso. Sin embargo, se limitaba a desbloquear el dispositivo, utilizando el Store de la aplicación, mediante iTunes y autenticar sólo la cadena de claves de iCloud 

Ahora hay dos formas de usar Touch ID como mecanismo de autenticación en aplicaciones de iOS 8 mediante la API de autenticación Local. Actualmente no es posible usar la autenticación Local para autenticar de forma remota.

En primer lugar, ayuda a los servicios existentes de la cadena de claves mediante el uso de nuevos Keychain Access Control Lists (ACL). Datos de cadena de claves se pueden desbloqueadas con la autenticación correcta de una huella digital de los usuarios.

En segundo lugar, LocalAuthentication proporciona dos métodos para autenticar su aplicación localmente. Los desarrolladores deben usar `CanEvaluatePolicy` para determinar si el dispositivo es capaz de Aceptar Touch ID y, a continuación, `EvaluatePolicy` para iniciar la operación de autenticación.

Para obtener más información sobre Touch ID y obtenga información sobre cómo integrarla en una aplicación de Xamarin.iOS, consulte el [Introducción a TouchID](~/ios/platform/touchid.md) guías.

### <a name="document-picker"></a>Selector de documentos

Funciona de selector de documentos con una unidad de iCloud a los usuarios para permitir al usuario que abra los archivos que se han creado en una aplicación diferente, importarán y manipulan y exportación revertir a intentarlo. Esto crea un flujo de trabajo intuitivo y, por tanto, una experiencia mucho mejor, para los usuarios. sincronización de iCloud lleva esto un paso más, los cambios realizados en una aplicación también se reflejará coherente en todos los dispositivos.

Para obtener información sobre el selector de documentos con más detalle y para obtener información sobre cómo integrarla en una aplicación de Xamarin.iOS, consulte el [Introducción para el selector de documentos](~/ios/platform/document-picker.md) guía.

### <a name="handoff"></a>HANDOFF

Entrega, que forma parte de la característica de continuidad de mayor tamaño, da un paso más hacia la integración de iOS y OS X. Esto incluye AirDrop multiplataforma, la capacidad de realizar llamadas de iPhone, SMS en el iPad y Mac y mejoras en tethering desde su iPhone.

Entrega funciona con iOS 8 y Yosemite y requiere una cuenta de iCloud para iniciar sesión en los distintos dispositivos que desea usar. Debería funcionar con aplicaciones de Apple más preinstaladas, incluidas Safari, iWork, mapas, calendarios y contactos.

Para obtener más información, consulte nuestra [Handoff](~/ios/platform/handoff.md) guía.

## <a name="unified-storyboards"></a>Guiones gráficos unificados
iOS 8 incluye una nueva más fácil de usar el mecanismo para crear la interfaz de usuario, el guión gráfico unificado. Con un solo guión gráfico para cubrir todos los tamaños de pantalla de hardware diferente, se pueden crear vistas rápidas y con capacidad de respuesta en un estilo de "diseño de una vez, usan muchas" es true.

Antes de iOS8, los desarrolladores usaban `UIInterfaceOrientation` para distinguir entre los modos vertical y horizontal, y `UIInterfaceIdiom` para distinguir entre los dispositivos iOS. En iOS8 ya no es necesario crear guiones gráficos independientes para los dispositivos iPhone y iPad: orientación y el dispositivo se determinan mediante el uso de *las clases de tamaño*.

Todos los dispositivos se definen mediante una clase de tamaño, en la vertical y el eje horizontal, y hay dos tipos de clases de tamaño en iOS 8:

- **Regular** -se trata de un tamaño de pantalla de gran tamaño (por ejemplo, un iPad) o un gadget que da la impresión de un tamaño grande (por ejemplo, una UIScrollView
- **Compact** -se trata de los dispositivos más pequeños (por ejemplo, el iPhone). Este tamaño tiene en cuenta la orientación del dispositivo.

Si los dos conceptos se usan juntos, el resultado es una cuadrícula de 2 x 2 que define los posibles tamaños diferentes que se pueden usar en las orientaciones diferentes, tal como se muestra en el diagrama siguiente:

![](introduction-to-ios8-images/image3.png "Un diagrama que representa la cuadrícula de 2 x 2 que define los posibles tamaños diferentes que pueden usarse en ambos las orientaciones diferentes")
 
Para obtener más información acerca de las clases de tamaño, consulte el [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md).

## <a name="photo-kit"></a>Kit de fotos
Kit de foto es un nuevo marco que permite a las aplicaciones para la biblioteca de imágenes de sistema de consulta y crear interfaces de usuario personalizadas para ver y modificar su contenido. Incluye una serie de clases que representan la imagen y activos de vídeo, así como las colecciones de recursos, como carpetas y álbumes.

Para obtener más información, consulte nuestra [PhotoKit](~/ios/platform/photokit.md) guía.

## <a name="games"></a>Juegos

<a name="scenekit" />

### <a name="scene-kit"></a>Kit de escena

Kit de escena es un gráfico de escena 3D API que simplifica el trabajo con gráficos 3D. Apareció por primera vez en OS X 10.8 y ha llegado a iOS 8. Con el Kit de escena crear visualizaciones 3D envolventes y juegos en 3D ocasionales no requiere experiencia en OpenGL. A partir de los conceptos comunes de gráfico de escena, Scene Kit abstrae las complejidades de OpenGL y OpenGL ES, facilitando en gran medida 3D de agregar contenido a una aplicación. Sin embargo, si es un experto de OpenGL, Scene Kit tiene mayor compatibilidad para enlazar directamente con OpenGL también. También incluye numerosas características que complementan los gráficos 3D, como física y se integra perfectamente con otros marcos de Apple, por ejemplo, Core Animation, Core imagen y Sprite Kit.

Para obtener más información, consulte nuestra [SceneKit](~/ios/platform/gaming/scenekit.md) documentación.

<a name="spritekit" />

### <a name="sprite-kit"></a>Sprite Kit

Sprite Kit, el marco de juego 2D de Apple, tiene algunas nuevas características interesantes en iOS 8 y OS X Yosemite. Estos incluyen la integración con Scene Kit sombreador, iluminación, sombras, restricciones, generación de un mapa normal y la compatibilidad con las mejoras de leyes físicas. En concreto, las nuevas características de leyes físicas facilitan agregar efectos realistas a un juego.

Para obtener más información, consulte nuestra [SpriteKit](~/ios/platform/gaming/spritekit.md) documentación.

## <a name="other-changes"></a>Otros cambios
Así como los cambios más importantes en iOS 8 que se han descrito anteriormente, Apple ha actualizado además muchos marcos de trabajo existentes. Se detallan a continuación:

- **[Principales imagen](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)**  : Apple ha ampliado en su marco de procesamiento de imagen mediante la adición de una mejor compatibilidad para la detección de regiones rectangulares, y los códigos QR dentro de las imágenes. Mike Bluestein ahonda en su blog titulada post [detección de imágenes en iOS 8](http://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>Interfaces API en desuso
Con todas las mejoras realizadas en iOS 8, han en varias API en desuso. A continuación se detallan algunas de ellas.

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)**  : los métodos y propiedades que se usa para registrar las notificaciones remotas han en desuso. Estos son registerForRemoteNotificationTypes y enabledRemoteNotificationTypes.
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)**  – rasgos y las clases de tamaño reemplazaron los métodos y propiedades que se usan para describir la orientación de la interfaz. Hacer referencia a la [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obtener más información sobre cómo usarlas.

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)**  : se ha reemplazado por UISearchController en iOS8.

## <a name="summary"></a>Resumen
En este artículo hemos analizado algunas de las nuevas características introducidas por Apple en iOS 8.



## <a name="related-links"></a>Vínculos relacionados

- [UIKitEnhancements (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/UIKitEnhancements)
- [Introducción a las extensiones de aplicación](~/ios/platform/extensions.md)
- [Introduction to CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) (Introducción a CloudKit)
- [Introducción al selector de documentos](~/ios/platform/document-picker.md)
- [Introducción a HealthKit](~/ios/platform/healthkit.md)
- [Introducción a los controles manuales de la cámara](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [Introducción a TouchID](~/ios/platform/touchid.md)
- [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md)
