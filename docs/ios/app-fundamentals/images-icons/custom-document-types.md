---
title: Iconos de documento personalizadas en Xamarin.iOS
description: Este plano de artículo incluido y administrar un recurso de imagen en una aplicación de Xamarin.iOS que se usará como un icono de tipo de documento personalizado.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/23/2017
ms.openlocfilehash: 155847b4f5f6b3e6070f1afb6219db2d3789a075
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258171"
---
# <a name="custom-document-icons-in-xamarinios"></a>Iconos de documento personalizadas en Xamarin.iOS

_Este plano de artículo incluido y administrar un recurso de imagen en una aplicación de Xamarin.iOS que se usará como un icono de tipo de documento personalizado._

Si una aplicación de Xamarin.iOS admite la carga de un tipo de documento en particular, el desarrollador puede proporcionar iconos que va a usar el sistema cuando encuentra ese tipo de documento, como cuando un usuario se mantiene presionado un dato adjunto en el *aplicación de correo* como se muestra aquí:

 [![](custom-document-types-images/17.png "Un ejemplo de iconos de tipo de documento")](custom-document-types-images/17.png#lightbox)

El programador puede agregar información de tipo de documento para un formato de archivo de la aplicación es capaz de apertura mediante la inclusión de entradas de diccionario para el `CFBundleTypeName` cadena y `LSItemContentTypes` matriz en la aplicación `Info.plist`. Van de los iconos para el tipo de documento el `CFBundleTypeIconFiles` matriz. Si no se proporciona un icono de documento, derivará iOS uno desde el icono de la aplicación.
Pueden proporcionar iconos para diversos tamaños, optimizados para las diferentes resoluciones de dispositivo. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para asignar estos valores en Visual Studio para Mac, use el **tipos de documento** sección la **avanzadas** ficha la `Info.plist` editor para agregar el tipo de documento y asignar los iconos de imagen a él. Por ejemplo, esta es una captura de pantalla que muestra el registro para la compatibilidad con PDF:

 [![](custom-document-types-images/18.png "La sección tipos de documento en la ficha Opciones avanzadas en el editor \"Info.plist\"")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para asignar estos valores en Visual Studio, use el **tipos de documento** sección la **avanzadas** ficha la `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Abra la sección tipos de documento en la ficha Opciones avanzadas")

Haga clic en el **Agregar tipo de documento** botón y rellene los campos obligatorios:

![](custom-document-types-images/doc02w.png "El formulario Agregar tipo de documento")

-----


Para obtener más información acerca de los tipos de documento, vea Apple [referencia de identificadores de tipo uniforme](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) y [temas de programación de la interacción del documento para iOS](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).


## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icono personalizado y directrices para la creación de imagen](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
