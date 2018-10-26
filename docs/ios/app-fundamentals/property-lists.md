---
title: Trabajar con listas de propiedades de Xamarin.iOS
description: Este documento presenta Visual Studio para el editor de lista (.plist) de propiedad gráfica y avanzados de Mac para trabajar con Info.plist y Entitlements.plist. Ilustra establecer iconos e imágenes de inicio para las aplicaciones de iOS desde dentro de Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 7056f7beb623bee32c767a3f2827efa6eb2a6136
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118804"
---
# <a name="working-with-property-lists-in-xamarinios"></a>Trabajar con listas de propiedades de Xamarin.iOS

_Este documento presenta Visual Studio para el editor de lista (.plist) de propiedad gráfica y avanzados de Mac para trabajar con Info.plist y Entitlements.plist. Ilustra establecer iconos e imágenes de inicio para las aplicaciones de iOS desde dentro de Visual Studio para Mac._

Visual Studio para Mac cuenta con un editor de plist gráfica que facilita la edición de propiedades de la aplicación y las capacidades más fácil. Visual Studio para Mac tiene dos .plists - `Info.plist` para editar propiedades de la aplicación y los iconos, y `Entitlements.plist` para administrar las capacidades de la aplicación. Esta guía presenta el Info.plists y proporciona información general de cómo trabajar con ellos en Visual Studio para Mac. Para obtener información sobre Entitlements.plist, consulte el [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) guía.

## <a name="infoplist"></a>Info.plist

La lista de propiedades de información ( `Info.plist`) es un archivo de iOS necesarios que proporciona información sobre la configuración de la aplicación en el sistema. Personalizada de Visual Studio para Mac del `Info.plist` características del editor de tres paneles controlados por fichas en la parte inferior izquierdos de la ventana del editor:

 [![](property-lists-images/tabs.png "Las pestañas del editor Info.plist en la parte inferior izquierda de la ventana del editor")](property-lists-images/tabs.png#lightbox)

Cada panel controla propiedades diferentes, tal como se describe a continuación:

-  **Panel de la aplicación** -una interfaz gráfica para establecer propiedades de la aplicación, así como iconos y lanzamiento comunes imágenes; especifique la integración de maps y procesamiento en segundo plano de los modos.
-  **Advanced Panel** -el panel avanzado es el lugar para especificar los tipos de documento admitidos, UTI y tipos de dirección URL.
-  **Panel de origen** -el panel de código fuente controla propiedades menos comunes, así como propiedades personalizadas para la aplicación.


Las tres secciones siguientes, investigue las características de cada panel con más detalle.

## <a name="application-panel"></a>Panel de la aplicación

Visual Studio para Mac incluye una interfaz gráfica para la edición habitual `Info.plist` entradas para una aplicación:

1.  Propiedades de la aplicación
1.  Tipos de dispositivos compatibles
1.  Orientaciones de compatibilidad para cada tipo de dispositivo
1.  Estilo y color de la barra de estado
1.  Iconos y pantallas de inicio
1.  Mapas y modos en segundo plano


Estos métodos se describen con más detalle en las secciones siguientes.

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>Destino de la aplicación iOS

Esta sección contiene información importante que describe la aplicación.
El **identificador** almacenados aquí debe coincidir con el identificador de paquete que se ha especificado en iTunes Connect (para aplicaciones de App Store) y también en la lista de identificadores de aplicación de Portal de aprovisionamiento de iOS y certificados de desarrollo y distribución.

 [![](property-lists-images/image24.png "Destino de la aplicación iOS")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>Implementación del dispositivo

 [![](property-lists-images/deployment.png "Implementación del dispositivo")](property-lists-images/deployment.png#lightbox)

El dispositivo **implementación** secciones de información que se muestran de forma selectiva, según la selección en el **dispositivos** lista desplegable en el **destino de la aplicación** sección anterior. El **interfaz principal** desplegable se establece en **MainStoryboard** en aplicaciones dirigidas a guión gráfico. Si la interfaz de usuario está escrita completamente en código, a continuación, esto puede dejarse en blanco.

### <a name="supported-device-orientations"></a>Orientaciones de dispositivos compatibles

 **Admite las orientaciones de dispositivos** controla cómo responde la aplicación a la rotación de dispositivos. Es muy común para las aplicaciones de iPhone/iPad admitir sólo **vertical**, o todo, pero **revés**. Por lo general, todas las aplicaciones de iPad, excepto los juegos deben admitir todas las orientaciones.

### <a name="status-bar-styles"></a>Estilos de barra de estado

El **estilos de barra de estado** sección es una interfaz gráfica para la edición de una aplicación `UIStatusBarStyle`:

 [![](property-lists-images/status.png "Estilos de barra de estado")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>Iconos, imágenes de inicio e ilustraciones de iTunes

Puede encontrar información sobre el uso de iconos, imágenes y material gráfico en el archivo Info.plist en el [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) guía.




### <a name="maps-integration-and-background-modes"></a>Integración de MAPS y modos en segundo plano

El `Info.plist` contiene secciones especiales para especificar la integración de maps y procesamiento en segundo plano de los modos. Elegir las opciones que quiera admitir agregará las propiedades necesarias para su aplicación.

 [![](property-lists-images/maps.png "Integración de mapas")](property-lists-images/maps.png#lightbox)

Para obtener más información sobre cómo trabajar con mapas, consulte el Xamarin [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md) guía.

 [![](property-lists-images/bging.png "Modos en segundo plano")](property-lists-images/bging.png#lightbox)

Para obtener más información acerca de los modos en segundo plano, consulte el Xamarin [procesamiento en segundo plano en iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) guía.

## <a name="advanced-panel"></a>Panel avanzado

El panel Avanzadas controla los tipos de documento y los esquemas de dirección URL que admite la aplicación.

 [![](property-lists-images/image34.png "Panel avanzado")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>Tipos de documento

Para las aplicaciones que admiten la apertura de determinados tipos de archivos, que proporciona iOS la `CFBundleDocumentTypes` clave. Si deseamos que nuestra aplicación para admitir ciertos tipos de archivo conocidos: por ejemplo, archivos PDF: se recomienda agregar el valor PDF a la clave. Esta sección proporciona una manera cómoda de escribir los datos que se almacenarán en el `CFBundleDocumentTypes` clave en el `Info.plist` archivo.

Consulte la documentación sobre [registrar el archivo de tipos de su aplicación admite](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) para obtener más información sobre cómo configurar estos valores.

## <a name="utis"></a>Uti

A veces, una aplicación necesita admitir la apertura de un tipo de archivo personalizado. Por ejemplo, nos conviene abrir archivos de imagen con una extensión personalizada *.xam*. Para especificar un tipo de archivo personalizados, vamos a crear una UTI - identificador de tipo Universal - personalizado mediante la `UIExportedTypeDeclarations` clave. La captura de pantalla siguiente muestra cómo crear un UTI personalizado para la extensión .xam:

 [![](property-lists-images/uti.png "UTI de Editor")](property-lists-images/uti.png#lightbox)

UTI de tipos exportado simplemente como especificar UTI personalizadas específicas de la aplicación, el *importado UTI de tipos* ( `UIImportedTypeDeclarations` key) especificar tipos personalizados admiten pero no pertenece a la aplicación.

Para obtener más información sobre el uso de UTI personalizado, consulte Apple [registrando archivo de tipos de su aplicación admite](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) guía.

## <a name="custom-urls"></a>Direcciones URL personalizadas

Un nombre de esquema de dirección URL (también denominado protocolo) es la primera parte de la dirección URL. Por ejemplo, `http://` y `https://` son combinaciones de URL comunes. Tiene la opción de crear una combinación de dirección URL personalizada para la aplicación. Esquemas de direcciones URL personalizadas se utilizan para comunicar e intercambiar datos con otras aplicaciones. Captura de pantalla siguiente muestra la creación de un nuevo esquema de dirección URL personalizado denominado `monkeys://`:

 [![](property-lists-images/url.png "Direcciones URL personalizadas")](property-lists-images/url.png#lightbox)



Para obtener más información sobre la implementación de esquemas de dirección URL personalizadas, consulte Apple [sección implementar esquemas de dirección URL personalizada de esta guía](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>Panel de origen

El **origen** pestaña de la `Info.plist` archivo permite que los valores personalizados ser agregado o editado. Visual Studio para Mac proporciona una lista de las propiedades más comunes:

 [![](property-lists-images/image31.png "Agregar una nueva propiedad en una lista desplegable")](property-lists-images/image31.png#lightbox)

Para propiedades conocidas de Visual Studio para Mac lo una lista de valores válidos, como se muestra en la siguiente captura de pantalla siguiente:

 [![](property-lists-images/image32.png "Seleccione un valor de una lista de valores conocidos")](property-lists-images/image32.png#lightbox)

Visual Studio para Mac también detecta el tipo de propiedad, como se muestra:

 [![](property-lists-images/image33.png "Los tipos de propiedad disponibles")](property-lists-images/image33.png#lightbox)

Revise Apple [recursos relacionados de la aplicación](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) vínculos para obtener más información sobre las propiedades opcionales.

 <a name="Entitlements" />

## <a name="summary"></a>Resumen

En este artículo se muestra con los editores de .plist gráfica y avanzado para modificar la configuración de aplicación comunes, así como para especificar iconos y las imágenes de inicio. Se introdujo también el `Entitlements.plist` para agregar y administrar las capacidades de la aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [IDE](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [Recursos relacionados con la aplicación](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Registra el archivo de tipos que admite la aplicación](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Implementar esquemas de dirección URL personalizada](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Referencia del formato de catálogo activo](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
