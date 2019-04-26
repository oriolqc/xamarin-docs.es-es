---
title: Mi archivo Resource.designer.cs de Android no se actualizará
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/19/2017
ms.openlocfilehash: ba3c2b07e7f35bf9fd84d10b74d034a02ca6a73d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153293"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Mi archivo Resource.designer.cs de Android no se actualizará

> [!NOTE]
> Este problema se resolvió en Xamarin Studio 5.1.4 y versiones posteriores. Sin embargo, si el problema se produce en Visual Studio para Mac, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de salida del registro de compilación.

Un error en la versión 5.1 Xamarin.Studio había dañado previamente archivos .csproj eliminando parcial o totalmente el código xml en el archivo .csproj. Esto provocaría que importantes del sistema (como actualizar el Resource.designer.cs de Android) de la compilación de partes de Android para producir un error. A partir de la 5.1.4 estable de la versión del 15 de julio de, este error se ha corregido; pero en muchos casos, el archivo de proyecto tiene que reparar manualmente, tal como se describe a continuación.


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Dos posibles enfoques para corregir el archivo de proyecto

### <a name="either"></a>Ya sea:

1) Crear un nuevo proyecto de aplicación de Xamarin.Android, establecer todas las propiedades del proyecto para que coincida con el proyecto antiguo y agregar todos los recursos, archivos de código fuente, etc., de nuevo en el proyecto.

### <a name="or"></a>O

2) Realizar una copia de seguridad del archivo de .csproj del proyecto original, ábralo en un editor de texto y vuelven a agregar los elementos que faltan de un archivo .csproj generado correctamente.

### <a name="if-this-does-not-solve-the-problem"></a>Si esto no soluciona el problema

Después de experimentar con estos elementos, es posible que observe que después de volver a agregar los elementos y volver a generar el proyecto, se actualizaría el archivo Resource.designer.cs, pero aún tendrá que cerrar y volver a abrir la solución para obtener la finalización de código para reconocer los nuevos tipos de contenido en Resource.designer.cs. 
