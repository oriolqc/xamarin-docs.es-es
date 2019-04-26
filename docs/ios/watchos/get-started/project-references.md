---
title: las referencias de proyecto de Xamarin de watchOS
description: Este documento describe la relación entre una aplicación de iOS, una aplicación de inspección y una extensión de aplicación de inspección. Describe las referencias de proyecto y la agrupación de los identificadores.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: c900ab714fed2bb1e02367ba39ad3c5a0a76121e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408378"
---
# <a name="watchos-project-references-in-xamarin"></a>las referencias de proyecto de Xamarin de watchOS

_Explicación de la relación entre la aplicación de iOS, la aplicación del reloj y la extensión de inspección._

Son los tres proyectos en una solución de watchOS *configurada automáticamente* que hacen referencia entre sí de forma específica para que las aplicaciones watchOS 3 se compilará y se agrupan correctamente. Estas referencias de proyecto y la configuración del identificador de lote se describe a continuación como referencia.

## <a name="project-references"></a>Referencias del proyecto

Ver las referencias, haga doble clic en los nodos de referencias para cada proyecto:

- **aplicación de iPhone** referencias **aplicación Watch**

![](project-references-images/catalog-reference1.png "aplicación de iPhone hace referencia a la aplicación del reloj")

- **Ver aplicación** referencias **extensión de la aplicación de inspección**

![](project-references-images/catalog-reference2.png "aplicación de iPhone hace referencia a la aplicación del reloj")


 - El **extensión de la aplicación de inspección** no hace referencia a cualquiera de los otros proyectos

![](project-references-images/catalog-reference3.png "Ver la que extensión de la aplicación no hace referencia a los otros proyectos")



## <a name="bundle-identifiers"></a>Identificadores de lote

También deberá asegurarse de que su **identificadores de lote** son correctos.
Deben tener los tres proyectos el *mismo* prefijo de identificador, con los proyectos de dos inspección predefinidos de extensiones de `watchkitextension` y `watchkitapp`, como sigue (para el **WatchKitCatalog** ejemplo):

 - Proyecto unificado de Xamarin.iOS: `com.xamarin.WatchKitCatalog`

 - Proyecto de extensión de WatchKit: `com.xamarin.WatchKitCatalog.watchkitextension`

 - Proyecto de aplicación de inspección: `com.xamarin.WatchKitCatalog.watchkitapp`

Además, asegúrese de que estos **Info.plist** configuración es correcta:

 - El proyecto de aplicación del reloj `WKCompanionAppBundleIdentifier` coincide con el identificador de paquete de la aplicación o el contenedor primario (es decir. lo que se ejecuta en el iPhone);

 - El proyecto de extensión de inspección Kit **Id. del lote de WKApp** coincide con el identificador de paquete. del proyecto de aplicación del reloj

Puede editar los identificadores haciendo doble clic en el **Info.plist** archivo en cada proyecto.

Esta captura de pantalla es la **la extensión de inspección** archivo Info.plist, que muestra la **la aplicación de inspección** identificador así:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
![](project-references-images/infoplist-extension.png "Esta captura de pantalla es el archivo Info.plist de la extensión de inspección")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
![](project-references-images/infoplist-extension-vs.png "Esta captura de pantalla es el archivo Info.plist de la extensión de inspección")

-----

Esta captura de pantalla es la **la aplicación de inspección** archivo Info.plist.
Actual **inspección OS** versión es 8.2, por lo que la **destino de implementación** para la aplicación Watch debería ser **8.2**. Tenga en cuenta que si tiene 6.3 de Xcode está instalado, este valor se puede establecer a 8.3, debe cambiarlo 8.2.

![](project-references-images/infoplist-watchapp.png "El archivo Info.plist de inspección")

El destino de implementación para la aplicación del reloj puede ser diferente de la extensión de inspección y aplicación de iOS.

