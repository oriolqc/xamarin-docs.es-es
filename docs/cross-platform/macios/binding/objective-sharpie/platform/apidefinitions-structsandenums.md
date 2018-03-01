---
title: ApiDefinitions & StructsAndEnums archivos
ms.topic: article
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 0dedc6f574fbe2f2bf4ffaf4e70fb972670e9a8c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions & StructsAndEnums archivos

Al objetivo Sharpie se ha ejecutado correctamente, genera `Binding/ApiDefinitions.cs` y `Binding/StructsAndEnums.cs` archivos.
Estos dos archivos se agregan a un proyecto de enlace en Visual Studio para Mac o se pasa directamente a la `btouch` o `bmac` herramientas para generar el enlace final.

En *algunos* casos estos archivos generados podrían ser todo lo que necesita, sin embargo, con más frecuencia el programador deberá modificar manualmente estos archivos para corregir cualquier problema que no se pudo controlar automáticamente por la herramienta (por ejemplo, los marcados generan con un [ `Verify` atributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Algunos de los siguientes pasos son:

- **Ajustar nombres**: en ocasiones, deseará ajustar los nombres de métodos y clases para que coincida con las instrucciones de diseño de .NET Framework.
- **Métodos o propiedades**: la heurística utilizada por objetivo Sharpie a veces se encargarán de asumir un método se convierta en una propiedad. En este momento, puede decidir si este es el comportamiento previsto o no.
- **Enlazar eventos**: puede vincular las clases con las clases de delegado y generar eventos automáticamente para aquellos.
- **Enlazar notificaciones**: no es posible extraer el contrato de API de notificaciones de los archivos de encabezado puro, será necesario un viaje a la documentación de API. Si desea que las notificaciones fuertemente tipadas, debe actualizar el resultado.
- **Selección de la API**: en este punto, puede proporcionar constructores adicionales, agregue métodos (para permitir la sintaxis de inicialización en la construcción de C#), la sobrecarga de operadores e implementar sus propias interfaces en el archivo de definiciones adicionales.

Consulte la [enlace una API](~/cross-platform/macios/binding/objective-c-libraries.md) descripción para ver cómo encajan estos archivos en el proceso de enlace, como se muestra en el diagrama siguiente:

![](apidefinitions-structsandenums-images/binding-flowchart.png "En este diagrama se muestra el proceso de enlace")

Hacer referencia a la [referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md) para obtener más información sobre el contenido de estos archivos.

