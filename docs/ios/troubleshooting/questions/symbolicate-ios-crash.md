---
title: ¿Dónde puedo encontrar el archivo de .dSYM a symbolicate registros de bloqueo de iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/09/2018
ms.openlocfilehash: 60d897be8739ff5b78a322bc4ea3f43011785bb5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
ms.locfileid: "33920662"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>¿Dónde puedo encontrar el archivo de .dSYM a symbolicate registros de bloqueo de iOS?

Al compilar una aplicación de iOS con Visual Studio para Mac o 2017 de Visual Studio, el archivo de .dSYM que se necesita para symbolicate informes de bloqueo se colocará en la misma jerarquía de directorio que el archivo de proyecto de la aplicación (.csproj). La ubicación exacta depende de la configuración de compilación del proyecto:

- Si ha habilitado compilaciones específico del dispositivo, el el .dSYM puede encontrarse en el siguiente directorio:

    **&lt;directorio del proyecto&gt;/bin/&lt;plataforma&gt;/&lt;configuración&gt;/device-builds /&lt;dispositivo&gt; - &lt; versión del sistema operativo&gt;/**

    Por ejemplo:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Si no ha habilitado compilaciones específico del dispositivo, el .dSYM puede encontrarse en el siguiente directorio:

    **&lt;directorio del proyecto&gt;/bin/&lt;plataforma&gt;/&lt;configuración&gt;/**

    Por ejemplo:

    **TestApp/bin/iPhone/lanzamiento /**

> [!NOTE]
> Como parte del proceso de compilación, 2017 de Visual Studio copia el archivo .dSYM desde el host de compilación de Mac para Windows. Si no ve un archivo .dSYM en Windows, asegúrese de que ha establecido la configuración de compilación de la aplicación para [crear un archivo .ipa](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Vea también

- [Symbolicating iOS bloqueará los archivos (Xamarin.iOS)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Desmitificación iOS registros de bloqueo de aplicación](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

