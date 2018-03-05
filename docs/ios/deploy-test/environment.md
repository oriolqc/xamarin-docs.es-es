---
title: Entorno
ms.topic: article
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ef5cfa2a9eee0d5d01922e5b7b3f89a209396c56
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="environment"></a>Entorno

El *entorno de ejecución* es el conjunto de variables de entorno que influyen en la ejecución del programa. Las variables de entorno se pueden establecer temporalmente en las propiedades del proyecto o permanentemente mediante la definición de argumentos adicionales en la herramienta de empaquetado mtouch.

## <a name="temporary-environment-variables"></a>Variables de entorno temporales

Las variables de entorno temporales se definen en la ventana **Propiedades**/**Opciones** del proyecto en la sección **Ejecutar > General**. Estas variables de entorno solo tienen efecto si la aplicación se inicia con Visual Studio para Mac; si la aplicación se inicia manualmente al puntear en ella, estas variables de entorno no se definen.

## <a name="permanent-environment-variables"></a>Variables de entorno permanentes

Las variables de entorno permanentes se establecen mediante la definición de argumentos adicionales en la herramienta de empaquetado mtouch. Estas variables de entorno se compilan en el archivo ejecutable y se definirán incluso si la aplicación no se inicia desde Xamarin Studio.

## <a name="example"></a>Ejemplo

```csharp
# log all exceptions to the device log
--setenv:MONO_TRACE=E:all

# to pass multipe environment variables, use --setenv multiple times
--setenv:MONO_TRACE=E:all --setenv:GC_DONT_GC=1
```

