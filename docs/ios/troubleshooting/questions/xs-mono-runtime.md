---
title: "¿Cómo se puede establecer variables de entorno en tiempo de ejecución de Mono para los proyectos de iOS en Xamarin Studio?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/31/2017
ms.openlocfilehash: 6032ea89aa54719cc4b0fdde67e67f1ec8fb183b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>¿Cómo se puede establecer variables de entorno en tiempo de ejecución de Mono para los proyectos de iOS en Xamarin Studio?

Si tiene que establecer variables de entorno de cualquier runtime para Mono, se pueden establecer el **Project Options > Ejecutar > General** página.

Nota: Variables de entorno de recolección de elementos para SGen (MONO\_GC\_PARAMS) conjunto de esta manera solo se usará cuando se inicie desde Xamarin Studio. Si se inicia la aplicación desde el dispositivo, se omitirá la configuración de Sgen. 

Para configurar una variable de entorno para una aplicación de forma permanente, debe agregarlo como un argumento adicional mtouch (para todas las configuraciones relevantes):

```csharp
   --setenv=NAME=VALUE
```

Para ver las variables de entorno que se pueden establecer, consulte la página de man Mono: [http://docs.go-mono.com/?link=man%3amono (1)](http://docs.go-mono.com/?link=man%3amono(1)) consulte la sección: `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Establecer variables de entorno para un proyecto")
