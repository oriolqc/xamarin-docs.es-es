---
title: Localización de Xamarin.Forms
description: El marco de trabajo de localización integrada de .NET puede utilizarse para generar aplicaciones multilingües multiplataforma con Xamarin.Forms. Se pueden localizar texto e imágenes y las aplicaciones pueden admitir una dirección de flujo de derecha a izquierda.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: 71033e935a2d3a4be88dbcc5d975938771484640
ms.sourcegitcommit: b60a37587aad8a0bfa8a522d88d22fa672002443
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285578"
---
# <a name="xamarinforms-localization"></a>Localización de Xamarin.Forms

_El marco de trabajo de localización integrada de .NET puede utilizarse para generar aplicaciones multilingües multiplataforma con Xamarin.Forms._

## <a name="string-and-image-localizationtextmd"></a>[Localización de cadenas e imágenes](text.md)

El mecanismo integrado para la localización de aplicaciones de .NET utiliza [archivos RESX](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) y las clases en el `System.Resources` y `System.Globalization` espacios de nombres. Los archivos RESX, que contiene las cadenas traducidas se incrustan en el ensamblado de Xamarin.Forms, junto con una clase generada por el compilador que proporciona acceso fuertemente tipado para las traducciones. A continuación, se puede recuperar el texto traducido en código.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localización de derecha a izquierda](right-to-left.md)

Dirección del flujo es la dirección en la que se analizan los elementos de interfaz de usuario en la página por el ojo. Localización de derecha a izquierda, agrega compatibilidad para la dirección del flujo de derecha a izquierda para las aplicaciones de Xamarin.Forms.
