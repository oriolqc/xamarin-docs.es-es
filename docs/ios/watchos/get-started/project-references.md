---
title: Referencias del proyecto
description: Explicación de la relación entre la aplicación iOS, la aplicación de inspección y la extensión de inspección.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: f3573e8b578ca567ea9d7360eb132aead4c24f37
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="project-references"></a>Referencias del proyecto

_Explicación de la relación entre la aplicación iOS, la aplicación de inspección y la extensión de inspección._

Son los tres proyectos en una solución de watchOS *configurada automáticamente* para hacer referencia a entre sí de forma específica para las aplicaciones de watchOS 3 para compilarse e incluirse correctamente. Estas referencias del proyecto y la configuración del identificador de paquete se describe a continuación como referencia.

## <a name="project-references"></a>Referencias del proyecto

Ver las referencias haciendo doble clic en los nodos de referencias para cada proyecto:

- **aplicación de iPhone** referencias **aplicación Watch**

![](project-references-images/catalog-reference1.png "aplicación de iPhone hace referencia a la aplicación de inspección")

- **Ver aplicación** referencias **extensión de la aplicación de inspección**

![](project-references-images/catalog-reference2.png "aplicación de iPhone hace referencia a la aplicación de inspección")


 - El **extensión de la aplicación de inspección** no hace referencia a cualquiera de los demás proyectos

![](project-references-images/catalog-reference3.png "Extensión de la aplicación de inspección no hace referencia a los demás proyectos")



## <a name="bundle-identifiers"></a>Identificadores de agrupación

También debe asegurarse de que su **identificadores de agrupación** son correctos.
Los tres proyectos deben tener la *mismo* prefijo de identificador, con los proyectos de dos inspección tener predefinidos extensiones de `watchkitextension` y `watchkitapp`, como se indica a continuación (para el **WatchKitCatalog** ejemplo:)

 - Xamarin.iOS unificado proyecto: `com.xamarin.WatchKitCatalog`

 - Proyecto de WatchKit extensión- `com.xamarin.WatchKitCatalog.watchkitextension`

 - Proyecto de aplicación de inspección: `com.xamarin.WatchKitCatalog.watchkitapp`

También asegúrese de que estos **Info.plist** configuración es correcta:

 - El proyecto de aplicación de inspección `WKCompanionAppBundleIdentifier` coincide con el identificador de paquete de la aplicación o el contenedor primario (es decir. que se ejecuta en el iPhone);

 - El proyecto de extensión de Kit de inspección **Id. del lote WKApp** coincide con el identificador de paquete. del proyecto de aplicación de inspección

Puede editar los identificadores haciendo doble clic en el **Info.plist** archivo en cada proyecto.

Esta captura de pantalla es la **la extensión de inspección** archivo Info.plist, que muestra la **la aplicación de inspección** identificador así:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
![](project-references-images/infoplist-extension.png "Esta captura de pantalla es el archivo Info.plist de la extensión de inspección")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
![](project-references-images/infoplist-extension-vs.png "Esta captura de pantalla es el archivo Info.plist de la extensión de inspección")

-----

Esta captura de pantalla es la **la aplicación de inspección** archivo Info.plist.
Actual **inspección OS** versión es 8.2, por lo que la **destino de implementación** para la aplicación de inspección debe **8.2**. Tenga en cuenta que si tiene 6.3 Xcode instalado, este valor puede establecerse en 8.3; debe cambiarlo 8.2.

![](project-references-images/infoplist-watchapp.png "El archivo Info.plist de inspección")

El destino de implementación de la aplicación de supervisión puede ser diferente de la extensión de inspección y aplicación iOS.

