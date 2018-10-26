---
title: ¿Cómo se puede establecer variables de entorno en tiempo de ejecución Mono para proyectos de iOS en Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/31/2017
ms.openlocfilehash: ba74e316f706e5bf22f973de4dad38d94ccd0db9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116945"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>¿Cómo se puede establecer variables de entorno en tiempo de ejecución Mono para proyectos de iOS en Xamarin Studio?

Si tiene que establecer variables de entorno de cualquier tiempo de ejecución de Mono, se pueden establecer el **opciones de proyecto > Ejecutar > General** página.

Nota: Variables de entorno de recolección de elementos para SGen (MONO\_GC\_PARAMS) conjunto de este modo solo se usará cuando se inicie desde Xamarin Studio. Si inicia la aplicación desde el dispositivo, se omitirá la configuración de Sgen. 

Para configurar una variable de entorno para una aplicación de forma permanente, deberá agregar esto como un argumento de mtouch adicionales (para todas las configuraciones relevantes):

```csharp
   --setenv=NAME=VALUE
```

Para ver las variables de entorno que se pueden establecer, consulte la página man Mono: [ http://docs.go-mono.com/?link=man%3amono(1) ](http://docs.go-mono.com/?link=man%3amono(1)) consulte la sección: `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Establecer variables de entorno para un proyecto")
