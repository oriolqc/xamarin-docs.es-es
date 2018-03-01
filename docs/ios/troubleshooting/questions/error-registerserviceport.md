---
title: "Error del diseñador con RegisterServicePort de iOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 8042ea895de3a623279c9d5f3a5309b990489773
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="ios-designer-error-with-registerserviceport"></a>Error del diseñador con RegisterServicePort de iOS

## <a name="sample-error"></a>Error de ejemplo
> System.AggregateException: Uno o más errores---> System.SystemException: RegisterServicePort (com.xamarin.MTHosting.2a0b1, com.apple.PowerManagement.control): Kernel devuelto: -308 (-308): servidor (ipc/mig) finalizó

## <a name="explanation"></a>Explicación
Errores con `RegisterServicePort` y mensajes de error similares al igual que anteriormente suelen ser un problema con spyware o software malintencionado en el equipo. Considere la posibilidad de la [el comentario de este informe de errores](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) para obtener más información, junto con el vínculo a la [foro de debate de Apple](https://discussions.apple.com/thread/5596008) acerca de cómo quitar una posible infección. 

Para ayudar a diagnosticar el problema, abra la aplicación macOS **consola** y elimine todos los archivos dentro de la **informes de diagnóstico de usuario** sección [http://screencast.com/t/y9i3NKcuMy](http://screencast.com/t/y9i3NKcuMy). A continuación, inicie Visual Studio para Mac y pruebe a utilizar el diseñador. Si los nuevos archivos de registro aparecen en esta sección después de que el diseñador no ha podido inicializar, guarde estos para que podamos analizar.  

Tenga en cuenta lo más importante para que busque este archivo: 
> /usr/lib/libimckit.dylib

Con independencia de los resultados anteriores, si ese archivo existe, el problema de spyware o software malintencionado antes mencionados si está presente en el equipo.  

El siguiente vínculo consta de los pasos para quitar este malware y spyware: [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

