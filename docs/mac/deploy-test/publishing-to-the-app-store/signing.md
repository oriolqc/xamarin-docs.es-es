---
title: Firmar con el identificador de desarrollador
description: "En esta guía se explican los pasos para firmar una aplicación Xamarin.Mac con Developer ID para su publicación."
ms.topic: article
ms.prod: xamarin
ms.assetid: cf7b733b-e08f-4f56-a233-264b29ee4c97
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 46d5527a33b82a795029f62900e782d644671f0d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="sign-with-developer-id"></a>Firmar con el identificador de desarrollador

Si el desarrollador tiene previsto distribuir una aplicación directamente a los usuarios de macOS, Apple recomienda que firme el código con su identificador de desarrollador para que se pueda instalarse en sistemas macOS con el **equipo selector** habilitado. Si la aplicación no se ha firmado, el **equipo selector** impedirá con un mensaje de alerta que los usuarios realicen la instalación (es posible omitir esta restricción si se mantiene presionada la tecla Control durante el inicio).

Obtenga más información sobre [Identificador del desarrollador y equipo selector](https://developer.apple.com/resources/developer-id/) y [Distribuir fuera del Mac App Store](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) en el sitio web de Apple.

## <a name="code-signing-options"></a>Opciones de firma de código

Para compilar una aplicación para la implementación directamente en los usuarios (NO a través de Mac App Store), establezca que la **configuración de firma** use el **identificador de desarrollador**. Asegúrese de editar la configuración de **lanzamiento**.

 [![](signing-images/config02.png "Opciones de la firma de Mac")](signing-images/config02.png#lightbox)


## <a name="build"></a>Compilar

Antes de compilar, asegúrese de que ha seleccionado la configuración correcta y elija crear un paquete de instalación en la configuración de **Mac Build** (Compilación de Mac):

[![](signing-images/config03.png "Opciones de compilación")](signing-images/config03.png#lightbox)

Al compilar la aplicación, se le solicitará al desarrollador que use los dos certificados:

 [![](signing-images/image57.png "Permitir el acceso a llaves")](signing-images/image57.png#lightbox)

 [![](signing-images/image58.png "Permitir el acceso a llaves")](signing-images/image58.png#lightbox)

Una vez que se ha compilado la aplicación, el desarrollador puede hacer clic con el botón derecho en el proyecto y seleccionar **Abrir carpeta contenedora** para buscar el archivo de paquete (en el directorio `bin/Release`). Este archivo de paquete incluye un instalador para la aplicación, por lo que se puede distribuir a cualquier usuario de macOS para su instalación.

 [![](signing-images/image59.png "Selección del paquete de aplicación en el buscador")](signing-images/image59.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [Instalación](~//mac/get-started/installation.md)
- [Ejemplo de Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuir las aplicaciones en Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guía de herramientas: firma de código de la aplicación](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/resources/developer-id/)
