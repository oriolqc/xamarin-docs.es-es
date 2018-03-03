---
title: Trabajar con listas de propiedades
description: "Este documento presentan Visual Studio para el editor de lista (.plist) de propiedad gráfica y avanzados de Mac para trabajar con Info.plist y Entitlements.plist. Muestran iconos de configuración y las imágenes de inicio para las aplicaciones de iOS desde dentro de Visual Studio para Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 0d7b4c5a539470a3544d0117251f40fd6bd37f2b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-property-lists"></a>Trabajar con listas de propiedades

_Este documento presentan Visual Studio para el editor de lista (.plist) de propiedad gráfica y avanzados de Mac para trabajar con Info.plist y Entitlements.plist. Muestran iconos de configuración y las imágenes de inicio para las aplicaciones de iOS desde dentro de Visual Studio para Mac._

Visual Studio para Mac incorpora un editor de .plist gráfica que facilita la edición de propiedades de la aplicación y capacidades más fáciles. Visual Studio para Mac tiene dos .plists - `Info.plist` para editar propiedades de la aplicación y los iconos, y `Entitlements.plist` para administrar las capacidades de aplicaciones. Esta guía presenta la Info.plists y proporciona información general sobre cómo trabajar con ellos en Visual Studio para Mac. Para obtener información sobre Entitlements.plist, consulte el [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) guía.

## <a name="infoplist"></a>Info.plist

La lista de propiedades de información ( `Info.plist`) es un archivo de iOS requiere que se proporciona información acerca de la configuración de la aplicación en el sistema. Personalizada de Visual Studio para Mac del `Info.plist` características del editor tres paneles controlados por pestañas en la parte inferior izquierdas de la ventana del editor:

 [ ![](property-lists-images/tabs.png "Las pestañas del editor de Info.plist en la parte inferior izquierda de la ventana del editor")](property-lists-images/tabs.png)

Cada panel controles diferentes propiedades, tal como se describe a continuación:

-  **Panel de la aplicación** -una interfaz gráfica para establecer propiedades de la aplicación, así como iconos y lanzamiento comunes imágenes; especifique backgrounding modos e integración de mapas.
-  **Advanced Panel** -panel avanzadas es el lugar para especificar tipos de documentos admitidos, UTIs y los tipos de dirección URL.
-  **Panel de origen** -el panel de código fuente controla propiedades menos común, así como propiedades personalizadas para la aplicación.


Las tres secciones siguientes investigar las características de cada panel con más detalle.

## <a name="application-panel"></a>Panel de la aplicación

Visual Studio para Mac presenta una interfaz gráfica para su edición comunes `Info.plist` entradas para una aplicación:

1.  Propiedades de la aplicación
1.  Tipos de dispositivos compatibles
1.  Compatibilidad con orientaciones para cada tipo de dispositivo
1.  Estilo y color de la barra de estado
1.  Iconos y pantallas de inicio
1.  Mapas y modos en segundo plano


Estos se describen con más detalle en las secciones siguientes.

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>Destino de la aplicación de iOS

Esta sección contiene información importante que describe la aplicación.
El **identificador** almacenados aquí debe coincidir con el identificador de paquete que se introduce en iTunes Connect (para aplicaciones de la tienda de aplicaciones) y también en la lista de identificadores de aplicación de Portal de aprovisionamiento de iOS y el desarrollo y la distribución de certificados.

 [ ![](property-lists-images/image24.png "Destino de la aplicación de iOS")](property-lists-images/image24.png)

### <a name="device-deployment"></a>Implementación de dispositivos

 [ ![](property-lists-images/deployment.png "Implementación de dispositivos")](property-lists-images/deployment.png)

El dispositivo **implementación** secciones de información se muestran de forma selectiva, dependiendo de la selección en el **dispositivos** lista desplegable en el **destino de la aplicación** sección anterior. El **interfaz principal** desplegable se establece en **MainStoryboard** en aplicaciones controladas por el guión gráfico. Si la interfaz de usuario está escrita completamente en código, a continuación, puede dejarse en blanco.

### <a name="supported-device-orientations"></a>Orientaciones de dispositivos compatibles

 **Admite dispositivos orientaciones** controla la forma en que la aplicación responde a la rotación de dispositivos. Es muy común para las aplicaciones iPhone/iPad admitir solo **vertical**, o todos ellos pero **boca abajo**. Por lo general todas las aplicaciones de iPad excepto juegos deben admitir todas las orientaciones.

### <a name="status-bar-styles"></a>Estilos de barra de estado

El **estilos de barra de estado** sección es una interfaz gráfica para la edición de una aplicación `UIStatusBarStyle`:

 [ ![](property-lists-images/status.png "Estilos de barra de estado")](property-lists-images/status.png)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>Iconos, imágenes de inicio y iTunes ilustración

Encontrará información sobre el uso de iconos, imágenes y material gráfico en el archivo Info.plist de la [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) guía.




### <a name="maps-integration-and-background-modes"></a>Integración de mapas y modos en segundo plano

El `Info.plist` contiene secciones especial para especificar asignaciones de integración y backgrounding modos. Elegir las opciones que desee para admitir agregará las propiedades necesarias para su aplicación.

 [ ![](property-lists-images/maps.png "Integración de mapas")](property-lists-images/maps.png)

Para obtener más información sobre cómo trabajar con mapas, consulte el Xamarin [iOS mapas](~/ios/user-interface/controls/ios-maps/index.md) guía.

 [ ![](property-lists-images/bging.png "Modos en segundo plano")](property-lists-images/bging.png)

Para obtener más información sobre los modos en segundo plano, consulte la Xamarin [Backgrounding en iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) guía.

## <a name="advanced-panel"></a>Panel avanzadas

El panel Avanzadas controla los tipos de documento y los esquemas de direcciones URL que admite la aplicación.

 [ ![](property-lists-images/image34.png "Panel avanzadas")](property-lists-images/image34.png)

 <a name="Document_Types" />


## <a name="document-types"></a>Tipos de documentos

Para las aplicaciones que admiten la apertura de tipos específicos de archivos, iOS proporciona el `CFBundleDocumentTypes` clave. Si deseamos que nuestra aplicación para admitir ciertos tipos de archivo conocidos: por ejemplo, archivos PDF - se agregaría el valor PDF a la clave. Esta sección proporciona una manera cómoda de escribir los datos que se almacenarán en el `CFBundleDocumentTypes` clave en el `Info.plist` archivo.

Consulte la documentación en [registrar el archivo de tipos de la aplicación admite](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) para obtener más información sobre cómo configurar estos valores.

## <a name="utis"></a>UTIs

En ocasiones, una aplicación debe admitir la apertura de un tipo de archivo personalizado. Por ejemplo, podemos queremos abrir archivos de imagen con una extensión personalizada *.xam*. Para especificar un tipo de archivo personalizados, vamos a crear un UTI - identificador de tipo Universal - personalizado con el `UIExportedTypeDeclarations` clave. La captura de pantalla siguiente muestra cómo crear una UTI personalizado para la extensión .xam:

 [ ![](property-lists-images/uti.png "Editor de UTIs")](property-lists-images/uti.png)

Tipo exportado simplemente como UTIs especificar UTIs personalizados específicos de la aplicación, el *importado tipo UTIs* ( `UIImportedTypeDeclarations` clave) especificar tipos personalizados compatibles pero no pertenece a la aplicación.

Para obtener más información sobre el uso de UTIs personalizados, consulte de Apple [registrar archivo de tipos de la aplicación admite](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) guía.

## <a name="custom-urls"></a>Direcciones URL personalizadas

Un nombre de esquema de dirección URL (también denominado protocolo) es la primera parte de la dirección URL. Por ejemplo, `http://` y `https://` comunes esquemas de dirección URL. Tiene la opción de crear un esquema de dirección URL personalizado para la aplicación. Esquemas de direcciones URL personalizadas se utilizan para comunicar y enviar datos y hacia atrás con otras aplicaciones. Captura de pantalla siguiente muestra la creación de un nuevo esquema de dirección URL personalizado denominado `monkeys://`:

 [ ![](property-lists-images/url.png "Direcciones URL personalizadas")](property-lists-images/url.png)



Para obtener más información sobre la implementación de esquemas de direcciones URL personalizadas, consulte de Apple [sección de implementación de esquemas de direcciones URL personalizadas de esta guía](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>Panel de origen

El **origen** pestaña de la `Info.plist` archivo permite valores personalizados agregar o editar. Visual Studio para Mac proporciona una lista de las propiedades más comunes:

 [ ![](property-lists-images/image31.png "Agregar una nueva propiedad de una lista desplegable")](property-lists-images/image31.png)

Para propiedades conocidas de Visual Studio para Mac será una lista de valores válidos, tal y como se muestra en la captura de pantalla siguiente:

 [ ![](property-lists-images/image32.png "Seleccione un valor de una lista de valores conocidos")](property-lists-images/image32.png)

Visual Studio para Mac también detecta el tipo de propiedad, como se muestra:

 [ ![](property-lists-images/image33.png "Los tipos de propiedades disponibles")](property-lists-images/image33.png)

Revise de Apple [recursos relacionados de la aplicación](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) vínculos para obtener más información sobre las propiedades opcionales.

 <a name="Entitlements" />

## <a name="summary"></a>Resumen

Este artículo se muestra con los editores de .plist gráfica y avanzadas para modificar la configuración de aplicación comunes, así como para especificar iconos e iniciar las imágenes. Que también introdujo el `Entitlements.plist` para agregar y administrar las capacidades de aplicaciones.


## <a name="related-links"></a>Vínculos relacionados

- [IDE](https://developer.xamarin.com/recipes/cross-platform/ide)
- [Recursos relacionados con la aplicación](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Registra el archivo de tipos de la aplicación, es compatible con](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Implementación de esquemas de dirección URL personalizada](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Acerca de los catálogos de activos](https://developer.apple.com/library/ioshttps://developer.xamarin.com/recipes/xcode_help-image_catalog-1.0/Recipe.html)
