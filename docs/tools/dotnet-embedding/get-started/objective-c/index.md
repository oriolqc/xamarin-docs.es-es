---
title: Introducción a Objective-c.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 96afe6bbfd1d9c6f4fd5ca3b7358ef0b89da30bb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-objective-c"></a>Introducción a Objective-c.

Se trata de la página de introducción para Objective-C, que cubre los conceptos básicos de todas las plataformas admitidas.


## <a name="requirements"></a>Requisitos

Para usar la incrustación de .NET con Objective-C necesitará un equipo Mac que ejecuta:

* macOS 10.12 (Sierra) o posterior
* Xcode 8.3.2 o posterior
* [Mono 5.0](http://www.mono-project.com/download/)

Puede instalar [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/) para editar y compilar el código de C#.


Notas:

* Las versiones anteriores de macOS, Xcode y Mono _podría_ el trabajo, pero se ha comprobado y no compatibles;
* Generación de código puede realizarse en Windows, pero solo es posible compilarlo en un equipo Mac donde está instalado Xcode;


## <a name="installation"></a>Instalación

El paso siguiente consiste en descargar e instalar .NET incrustar en el equipo Mac.

* [Package](https://dl.xamarin.com/embeddinator/Xamarin.Embeddinator-4000-0.2.0.79.pkg)
* [Notas de la versión](https://github.com/mono/Embeddinator-4000/tree/master/docs/releases)

Como alternativa es posible compilar desde nuestra [repositorio de git](https://github.com/mono/Embeddinator-4000/tree/objc), consulte el [que han contribuido](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md) documento para obtener instrucciones.

El programa de instalación es un instalador estándar pkg según:

![Introducción de instalador](images/install1.png)
![programa de instalación instala tipo](images/install2.png)
![resumen de instalador](images/install3.png)

Una vez instalado a través del instalador, después de iniciar una nueva sesión de terminal, puede usar el `objcgen` comando.
En caso contrario, siempre puede ejecutar la herramienta a través de su ruta de acceso absoluta: `/Library/Frameworks/Xamarin.Embeddinator-4000.framework/Commands/objcgen`.

## <a name="platforms"></a>Plataformas

Objective-C es un lenguaje que se usa normalmente para escribir aplicaciones de Mac OS, iOS, tvOS y watchOS; y la embeddinator admite todas esas plataformas. Trabajar con cada plataforma implica algunas diferencias clave y estos se explican [aquí](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Crear una aplicación de macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) es más fácil, ya que no implica tantos pasos adicionales, como configurar la identidad, provisining perfiles, simuladores y dispositivos. Se recomienda comenzar con el documento de macOS anteriores a la de iOS.

### <a name="ios--tvos"></a>iOS / tvOS

Asegúrese de que ya están configurados para desarrollar aplicaciones de iOS antes de intentar crear uno mediante el embeddinator. El [siguiendo instrucciones](~/tools/dotnet-embedding/get-started/objective-c/ios.md) se supone que ya ha creado e implementa una aplicación de iOS desde su equipo.

Compatibilidad con tvOS es análogo al funcionamiento de iOS, usando solo tvOS proyectos en el IDE (Visual Studio y Xcode) en lugar de proyectos de iOS.

> [!NOTE]
> Nota: La compatibilidad con watchOS estará disponible en una versión futura y será muy similar a iOS/tvOS.


## <a name="further-reading"></a>Información adicional

* [Características de .NET incrustar específicas Objective-c.](~/tools/dotnet-embedding/objective-c/index.md)
* [Prácticas recomendadas para Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Limitaciones de incrustación de .NET](~/tools/dotnet-embedding/limitations.md)
* [En el proyecto de código abierto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Códigos de error y descripciones](~/tools/dotnet-embedding/errors.md)
* [Plataformas de destino](~/tools/dotnet-embedding/objective-c/platforms.md)


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de tiempo (iOS y Mac OS)](https://github.com/jamesmontemagno/embeddinator-weather)
