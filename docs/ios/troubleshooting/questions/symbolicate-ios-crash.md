---
title: "¿Dónde puedo encontrar el archivo de .dSYM a symbolicate registros de bloqueo de iOS?"
ms.topic: article
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 223e1977bb6229760d6428fdca2f5372b1e25c23
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>¿Dónde puedo encontrar el archivo de .dSYM a symbolicate registros de bloqueo de iOS?

Al compilar aplicaciones de iOS desde visual studio, el archivo .dSYM que puede usarse para symbolicate informes de bloqueo se termina en el host de compilación en la ruta de acceso:
```
    /Users/<username>/Library/Caches/Xamarin/mtbs/builds/<appname>/<guid>/bin/iPhone/<configuration>
```

Tenga en cuenta que la `~/Library` carpeta está oculta de forma predeterminada en Finder, por lo que si necesita utilizar del buscador **vaya > Ir a la carpeta** menú y escriba: `~/Library/Caches/Xamarin/mtbs/builds/` para abrir la carpeta.  

Como alternativa puede mostrar el `~/Library` carpeta utilizando el **Mostrar opciones de vista** panel de su carpeta principal. Si selecciona la carpeta principal en la barra lateral en Finder y use el menú buscador **Vista > Mostrar opciones de vista** (o cmd-j), verá una casilla para **mostrar la carpeta de biblioteca**.


### <a name="see-also"></a>Vea también
- Informes de bloqueo de pasos extendidos para symbolicating iOS: [http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Desmitificación iOS registros de bloqueo de aplicación](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
