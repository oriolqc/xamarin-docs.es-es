---
title: "No se actualizará mi archivo Resource.designer.cs Android"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/19/2017
ms.openlocfilehash: b169bcc64af15de3d87bfb7f8059b4251f1a3ad9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>No se actualizará mi archivo Resource.designer.cs Android

> [!NOTE]
> **Nota:** este problema se ha resuelto en Xamarin Studio 5.1.4 y versiones posteriores. Sin embargo, si el problema se produce en Visual Studio para Mac, registre una [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de los resultados de registro de compilación.

Un error en Xamarin.Studio 5.1 previamente había dañado archivos .csproj eliminando parcial o totalmente el código xml en el archivo .csproj. Esto provocaría que importante partes de Android que el sistema (por ejemplo, para actualizar la Resource.designer.cs Android) de compilación genere un error. A partir de la 5.1.4 estable de la versión en el 15 de julio, este error se ha corregido; pero en muchos casos, el archivo de proyecto tiene que repararse manualmente, tal como se describe a continuación.


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Dos métodos posibles para corregir el archivo de proyecto

### <a name="either"></a>Ya sea:

1) Cree un nuevo proyecto de aplicación de Xamarin.Android, establezca todas las propiedades de proyecto para que coincida con el proyecto anterior y agregar todos los recursos, archivos de código fuente, etc. de nuevo en el proyecto.

### <a name="or"></a>O

2) Hacer una copia de seguridad del archivo de .csproj del proyecto original, a continuación, abrirlo en un editor de texto y vuelven a agregar los elementos que faltan desde un archivo .csproj generado correctamente.

### <a name="if-this-does-not-solve-the-problem"></a>Si esto no soluciona el problema

Después de experimentar con estos elementos, puede observar después de volver a agregar los elementos y volver a generar el proyecto, el archivo Resource.designer.cs actualizaría, pero todavía tendrá que cerrar y volver a abrir la solución para obtener la finalización de código para que reconozca los nuevos tipos de contenido en Resource.designer.cs. 
