---
title: Localización de Xamarin.Forms
description: El marco de trabajo de localización integrado de .NET se puede utilizar para compilar aplicaciones multilingües multiplataforma con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/13/2018
ms.openlocfilehash: 29624eeccc8542b3296774f6b77480b664bee478
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="localization"></a>Localización

_El marco de trabajo de localización integrado de .NET se puede utilizar para compilar aplicaciones multilingües multiplataforma con Xamarin.Forms._

## <a name="string-and-image-localizationtextmd"></a>[Cadena y la localización de imagen](text.md)

El mecanismo integrado para la localización de aplicaciones .NET utiliza [RESX (archivos)](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx) y las clases en el `System.Resources` y `System.Globalization` los espacios de nombres. Los archivos RESX que contiene las cadenas traducidas se incrustan en el ensamblado de Xamarin.Forms, junto con una clase generada por el compilador que proporciona acceso fuertemente tipado para las traducciones. A continuación, se puede recuperar el texto traducido en código.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Localización de derecha a izquierda](right-to-left.md)

Dirección de flujo es la dirección en la que el ojo examina los elementos de interfaz de usuario en la página. Localización de derecha a izquierda agrega compatibilidad para la dirección del flujo de derecha a izquierda para aplicaciones de Xamarin.Forms.
