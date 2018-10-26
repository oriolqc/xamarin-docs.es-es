---
title: 'Error al enviar a App Store: "Agrupación no válida: las opciones no permitidas para insertar en bitcode se detectan en el envío"'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 867ad29abfa6a38971b60ac9ebf181905949dafd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106993"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Error al enviar a App Store: "Agrupación no válida: las opciones no permitidas para insertar en bitcode se detectan en el envío"

watchOS aplicaciones y tvOS _requieren_ bitcode cuándo se envían a la aplicación de Store. Al crear y enviar aplicaciones para watchOS y tvOS utilizando Xcode 8.3 o una versión anterior, puede producirse el error siguiente (a través de la notificación por correo electrónico) al intentar cargar en la aplicación de Store:

>Agrupación no válida: la aplicación no se puede procesar porque las opciones no permitidas para insertar en bitcode se detectan en el envío. Es probable que no se está compilando la aplicación con la cadena de herramientas proporcionada en Xcode.

La solución a este problema consiste en compilar las aplicaciones con Xcode 9 y la última versión de Xamarin.iOS.
