---
title: ¿Dónde puedo encontrar el archivo .dSYM para resolver símbolos de registros de bloqueo de iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/09/2018
ms.openlocfilehash: 0b8f3aa736cba6e70fbf346766499c23a9bbe270
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61418228"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>¿Dónde puedo encontrar el archivo .dSYM para resolver símbolos de registros de bloqueo de iOS?

Al compilar una aplicación de iOS con Visual Studio para Mac o Visual Studio 2017, el archivo .dSYM que se necesita para resolver símbolos de los informes de bloqueo se colocarán en la misma jerarquía de directorio que el archivo del proyecto de la aplicación (.csproj). La ubicación exacta depende de la configuración de compilación del proyecto:

- Si ha habilitado las compilaciones específicas del dispositivo, el .dSYM puede encontrarse en el directorio siguiente:

    **&lt;directorio del proyecto&gt;/bin /&lt;plataforma&gt;/&lt;configuración&gt;/device-builds /&lt;dispositivo&gt; - &lt; versión del sistema operativo&gt;/**

    Por ejemplo:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Si no ha habilitado las compilaciones específicas del dispositivo, el .dSYM puede encontrarse en el directorio siguiente:

    **&lt;directorio del proyecto&gt;/bin /&lt;plataforma&gt;/&lt;configuración&gt;/**

    Por ejemplo:

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> Como parte del proceso de compilación, Visual Studio 2017 copia el archivo .dSYM desde el host de compilación de Mac para Windows. Si no ve un archivo .dSYM en Windows, asegúrese de que ha configurado las opciones de compilación de la aplicación a [crear un archivo .ipa](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Vea también

- [Symbolicating iOS Crash archivos (Xamarin.iOS)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Desmitificación de los registros de bloqueo de aplicación de iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

