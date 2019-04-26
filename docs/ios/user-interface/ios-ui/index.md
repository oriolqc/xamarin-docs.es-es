---
title: Interfaces de usuario en iOS
description: Este documento incluye vínculos a guías que describen cómo crear interfaces de usuario de la aplicación de Xamarin.iOS. Las guías vinculadas abarcan la API de apariencia, creación de objetos de la interfaz de usuario, las opciones de diseño y mucho más.
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: efb88ada8a4b4c36dd49de137eb64acd63552968
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382341"
---
# <a name="user-interfaces-in-ios"></a>Interfaces de usuario en iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[Appearance API](introduction-to-the-appearance-api.md)

iOS permite muchos atributos visuales de los controles de interfaz de usuario de ser temáticas con las APIs UIAppearance.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Creación de objetos de la interfaz de usuario](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Grupos de Apple relacionados con partes de funcionalidad en "marcos", que equivalen a los espacios de nombres de Xamarin.iOS. `UIKit` es el espacio de nombres que contiene todos los controles de interfaz de usuario para iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Opciones de diseño](~/ios/user-interface/ios-ui/layout-options.md)

Hay dos mecanismos diferentes para controlar el diseño cuando se cambia el tamaño o girar una vista: Cambiar automáticamente el tamaño y el diseño automático.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Provisión de comentarios hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

En este artículo se trata los nuevos tipos de comentarios hápticos disponible en iOS 10 y cómo implementarlos en Xamarin.iOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Trabajo con el subproceso de la interfaz de usuario](~/ios/user-interface/ios-ui/ui-thread.md)

El código solo debe hacer el subproceso de los cambios realizados en los controles en la página principal de la interfaz de usuario (o la interfaz de usuario). Las actualizaciones de la interfaz de usuario que se producen en un subproceso diferente (por ejemplo, un subproceso en segundo plano o de devolución de llamada) no pueden obtener representa en la pantalla o incluso podrían provocar un bloqueo.




