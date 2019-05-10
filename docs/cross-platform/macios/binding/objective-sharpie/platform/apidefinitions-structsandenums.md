---
title: ApiDefinitions y StructsAndEnums archivos
description: Este documento describe los archivos ApiDefinitions.cs y StructsAndEnums.cs que genera Sharpie objetivo. Estos archivos se usan para tener acceso al código de Objective-C desde C#.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 3b991f6105c6053f473b049d195aaef63cbcdd57
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977672"
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions y StructsAndEnums archivos

Cuando el objetivo Sharpie se ha ejecutado correctamente, genera `Binding/ApiDefinitions.cs` y `Binding/StructsAndEnums.cs` archivos.
Estos dos archivos se agregan a un proyecto de enlace en Visual Studio para Mac o pasa directamente a la `btouch` o `bmac` herramientas para producir el enlace final.

En *algunos* casos estos archivos generados pueden ser todo lo que necesita, sin embargo, con más frecuencia el desarrollador tendrá que modificar manualmente estos archivos para corregir cualquier problema que no se puede controlar automáticamente la herramienta (por ejemplo, los marcados generan con un [ `Verify` atributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Algunos de los siguientes pasos son:

- **Ajuste los nombres**: A veces desea ajustar los nombres de métodos y clases para que coincida con las instrucciones de diseño de .NET Framework.
- **Métodos o propiedades**: La heurística utilizada por objetivo Sharpie a veces, seleccionará un método para convertirse en una propiedad. En este momento, podría decidir si este es el comportamiento previsto o no.
- **Enlazar eventos**: Puede vincular sus clases con las clases de delegado y generar automáticamente los eventos para aquellos.
- **Enlazar notificaciones**: No es posible extraer el contrato de API de notificaciones de los archivos de encabezado puro, esto requerirá un viaje a la documentación de API. Si desea que las notificaciones fuertemente tipadas, deberá actualizar el resultado.
- **Protección de API**: En este momento, puede proporcionar constructores adicionales, agregue los métodos (para permitir C# sintaxis de inicialización en construcción), operador de sobrecarga e implementar sus propias interfaces en el archivo de definiciones adicionales.

Consulte la [una API de enlace](~/cross-platform/macios/binding/objective-c-libraries.md) descripción para ver cómo encajan estos archivos en el proceso de enlace, tal como se muestra en el diagrama siguiente:

![](apidefinitions-structsandenums-images/binding-flowchart.png "El proceso de enlace se muestra en este diagrama")

Hacer referencia a la [referencias de los tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md) para obtener más información sobre el contenido de estos archivos.

