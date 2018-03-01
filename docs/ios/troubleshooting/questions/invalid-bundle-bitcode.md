---
title: "Error al enviar a la tienda de aplicaciones: \"Agrupación no válido - opciones no permitidas para incrustar en bitcode se detectan en el envío\""
ms.topic: article
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b8ce643d392945e7e746c28b13063a2b0b7ebb48
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Error al enviar a la tienda de aplicaciones: "Agrupación no válido - opciones no permitidas para incrustar en bitcode se detectan en el envío"

watchOS tvOS las aplicaciones y _requieren_ bitcode al que se envían a la tienda de aplicaciones. Al generar y enviar aplicaciones watchOS y tvOS con Xcode 8.3 o una versión anterior, puede producirse el error siguiente (a través de la notificación por correo electrónico) al intentar cargar en la tienda de aplicaciones:

>Paquete no válido: la aplicación no se puede procesar porque opciones no permitidas para incrustar en bitcode se detectan en el envío. Es probable que no se está creando la aplicación con la cadena de herramientas proporcionada en Xcode.

La solución a este problema consiste en compilar las aplicaciones con Xcode 9 y la versión más reciente de Xamarin.iOS.
