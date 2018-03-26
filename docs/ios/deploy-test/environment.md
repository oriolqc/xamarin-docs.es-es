---
title: Entorno
ms.topic: article
ms.prod: xamarin
ms.assetid: 9801644A-89BB-4491-AD28-7F3B97D2CD62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: c46d3aa151d4a596ca3da881a3c4e5e38bb41cbd
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2018
---
# <a name="environment"></a>Entorno

El *entorno de ejecución* es el conjunto de variables de entorno que influyen en la ejecución del programa. Las variables de entorno se pueden establecer temporalmente en las propiedades del proyecto o permanentemente mediante la definición de argumentos adicionales en la herramienta de empaquetado mtouch.

## <a name="temporary-environment-variables"></a>Variables de entorno temporales

Las variables de entorno temporales se definen en la ventana **Propiedades**/**Opciones** del proyecto en la sección **Ejecutar > General**. Estas variables de entorno solo tienen efecto si la aplicación se inicia con Visual Studio para Mac; si la aplicación se inicia manualmente al puntear en ella, estas variables de entorno no se definen.

## <a name="permanent-environment-variables"></a>Variables de entorno permanentes

Las variables de entorno permanentes se establecen mediante la definición de argumentos adicionales en la herramienta de empaquetado mtouch. Estas variables de entorno se compilan en el archivo ejecutable y se definirán incluso si la aplicación no se inicia desde Visual Studio para Mac.

## <a name="example"></a>Ejemplo

```csharp
# log all exceptions to the device log
--setenv:MONO_TRACE=E:all

# to pass multipe environment variables, use --setenv multiple times
--setenv:MONO_TRACE=E:all --setenv:GC_DONT_GC=1
```

