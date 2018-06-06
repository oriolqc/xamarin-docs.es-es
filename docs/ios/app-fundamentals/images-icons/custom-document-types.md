---
title: Iconos de documento personalizadas en Xamarin.iOS
description: Esta artículo cubre incluidos y administrar un recurso de imagen en una aplicación Xamarin.iOS para usarse como un icono de tipo de documento personalizado.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/23/2017
ms.openlocfilehash: 415500050e4e957ca635c4b18004716dd44a162d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784156"
---
# <a name="custom-document-icons-in-xamarinios"></a>Iconos de documento personalizadas en Xamarin.iOS

_Esta artículo cubre incluidos y administrar un recurso de imagen en una aplicación Xamarin.iOS para usarse como un icono de tipo de documento personalizado._

Si una aplicación de Xamarin.iOS admite la carga de un tipo de documento en particular, el desarrollador puede proporcionar iconos que se va a usar el sistema cuando encuentra ese tipo de documento, como cuando un usuario mantiene presionado un dato adjunto en el *aplicación de correo electrónico* como se muestra aquí:

 [![](custom-document-types-images/17.png "Un ejemplo de iconos de tipo de documento")](custom-document-types-images/17.png#lightbox)

El programador puede agregar información de tipo de documento para un formato de archivo de la aplicación es capaz de apertura mediante la inclusión de entradas del diccionario para la `CFBundleTypeName` cadena y `LSItemContentTypes` matriz en la aplicación `Info.plist`. Los iconos para el tipo de documento ir en la `CFBundleTypeIconFiles` matriz. Si no se proporcionó un icono de documento, iOS derivará uno desde el icono de la aplicación.
Pueden proporcionar iconos para varios tamaños, optimizados para las diferentes resoluciones de dispositivo. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para asignar estos valores en Visual Studio para Mac, use la **tipos de documentos** sección en la la **avanzadas** ficha la `Info.plist` editor para agregar el tipo de documento y asignar los iconos de imagen a él. Por ejemplo, aquí tiene una captura de pantalla que muestra el registro para la compatibilidad con PDF:

 [![](custom-document-types-images/18.png "La sección tipos de documento en la ficha Opciones avanzadas en el editor 'Info.plist'")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para asignar estos valores en Visual Studio, use el **tipos de documentos** sección en el **avanzadas** pestaña en el `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Abra la sección tipos de documento en la ficha Opciones avanzadas")

Haga clic en el **Agregar tipo de documento** botón y rellene los campos obligatorios:

![](custom-document-types-images/doc02w.png "La forma de agregar tipo de documento")

-----


Para obtener más información acerca de los tipos de documento, consulte de Apple [referencia de identificadores de tipo uniforme](http://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) y [temas de programación de la interacción del documento para iOS](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).


## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con imágenes (ejemplo)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icono personalizado y directrices para la creación de imágenes](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
