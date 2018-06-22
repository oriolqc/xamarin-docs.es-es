---
title: Interfaces de usuario de iOS
description: Vínculos de este documento guías que describen cómo crear interfaces de usuario en aplicaciones de Xamarin.iOS. Las guías vinculadas tratan la API de apariencia, crear objetos de la interfaz de usuario, las opciones de diseño y mucho más.
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: a51d3f57106a282ed72b45dedf356739244e247f
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790372"
---
# <a name="user-interfaces-in-ios"></a>Interfaces de usuario de iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[Appearance API](introduction-to-the-appearance-api.md)

iOS permite muchos atributos visuales de los controles de interfaz de usuario mediante las APIs UIAppearance aplicarles un tema.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Creación de objetos de la interfaz de usuario](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Grupos de Apple relacionadas con elementos de la funcionalidad en "marcos", que equivale a los espacios de nombres de Xamarin.iOS. `UIKit` es el espacio de nombres que contiene todos los controles de interfaz de usuario para iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Opciones de diseño](~/ios/user-interface/ios-ui/layout-options.md)

Hay dos mecanismos diferentes para controlar el diseño cuando se cambia el tamaño o girar una vista: cambiar automáticamente el tamaño y diseño automático.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Provisión de comentarios hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

Este artículo explican los nuevos tipos de comentarios hápticos disponible en iOS 10 y cómo implementarlas en Xamarin.iOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Trabajo con el subproceso de la interfaz de usuario](~/ios/user-interface/ios-ui/ui-thread.md)

El código debe realizar solo subproceso de cambios en los controles en la página principal de la interfaz de usuario (o interfaz de usuario). Las actualizaciones de la interfaz de usuario que se producen en un subproceso diferente (por ejemplo, un subproceso de devolución de llamada o en segundo plano) no pueden obtener representa en la pantalla, o incluso podrían provocar un bloqueo.




