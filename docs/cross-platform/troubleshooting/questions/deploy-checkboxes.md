---
title: Implementar casillas deshabilitadas en el Administrador de configuración
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: ab825ba4d28ca8768e5c633fc3779828638a498d
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919519"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>Implementar casillas deshabilitadas en el Administrador de configuración

A partir de Xamarin 3.5, los proyectos de Xamarin.iOS se implementan automáticamente cada vez que pulsa el botón **Iniciar** de barra de herramientas o se selecciona el elemento de menú **Depurar > Iniciar depuración**. Aun así, deberá establecer el proyecto de aplicación de Xamarin.iOS deseado como **Proyecto de inicio** antes de ejecutar cualquiera de esos comandos.

Debido a esto, las casillas de **Implementar** están deshabilitadas a propósito en el Administrador de configuración de Visual Studio para los proyectos de Xamarin.iOS:

![](deploy-checkboxes-images/configuration.png "Administrador de configuración de Visual Studio que muestra la casilla de verificación 'Implementar' deshabilitado para un proyecto de Xamarin.iOS en Xamarin 3.5")

Este cambio elimina un error que podía aparecer en versiones anteriores de Xamarin (versión 3.3 y anteriores) cuando no se establecía la implementación del proyecto de aplicación de Xamarin.iOS:

![](deploy-checkboxes-images/error.png "Cuadro de diálogo de error: el proyecto iPhoneApp1 debe implementarse antes de que se puede iniciar. Compruebe que esté seleccionado el proyecto para implementarse en el Administrador de configuración de la solución.")
