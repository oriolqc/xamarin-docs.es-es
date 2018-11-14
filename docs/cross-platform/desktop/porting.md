---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: Guía de portabilidad de aplicación de escritorio
description: Una explicación simple de cómo desacoplar los formularios de Windows existentes o las aplicaciones de WPF para crear aplicaciones multiplataforma para ejecutarse en macOS, iOS, Android, así como para UWP y Windows 10.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 3d3af9c78b7486e7ebfb063a3cb00fabdbd0f5b7
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617545"
---
# <a name="desktop-app-porting-guidance"></a>Guía de portabilidad de aplicación de escritorio

La mayoría de código de aplicación se puede clasificar en una de las áreas siguientes:

* Código de interfaz de usuario (p ej. ventanas y botones)
* controles de terceros 3rd (p ej. gráficos)
* Lógica de negocios (p ej. reglas de validación)
* Acceso y almacenamiento de datos local
* Servicios Web y acceso a datos remotos

Para aplicaciones de Windows Forms y WPF escritas con C# (o Visual Basic.NET) una cantidad sorprendente de la lógica de negocios, el acceso a datos locales y el código de servicios web se puede compartir entre plataformas.

## <a name="net-portability-analyzer"></a>Analizador de portabilidad de .NET

Compatibilidad de Visual Studio 2015 y 2017 el [.NET Portability Analyzer](https://docs.microsoft.com/dotnet/articles/standard/portability-analyzer) ([descargar para Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) que se puede examinar las aplicaciones existentes y saber cuánto código se puede portar "tal cual" a otras plataformas . Puede obtener más información de este [vídeo de Channel 9](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer).

También hay una herramienta de línea de comandos que puede descargarse desde [analizador de portabilidad en GitHub](https://github.com/Microsoft/dotnet-apiport) y se usa para proporcionar los mismos informes.

## <a name="x-of-my-code-is-portable-what-next"></a>"x % de mi código es portable. ¿Qué sigue?"

Espero que el analizador muestra una gran parte del código es portable, pero sin duda habrá que algunas partes de todas las aplicaciones que _no_ se puede mover a otras plataformas.

Diferentes fragmentos de código probablemente entrarán dentro de uno de estos depósitos, explicados con más detalle a continuación:

* Puede volver a usar código portable
* Código que requiere cambios
* Código que no es portable y requiere volver a escribir

### <a name="re-useable-portable-code"></a>Puede volver a usar código portable

Código de .NET que se escribe en las API disponibles en todas las plataformas se puede realizar entre plataformas sin cambios. Idealmente, podrá mover todo este código a una biblioteca de clases Portable, la biblioteca compartida o la biblioteca estándar de .NET y, después, probarla dentro de la aplicación existente.

Esa biblioteca compartida, a continuación, se puede agregar a proyectos de aplicación para otras plataformas (por ejemplo, Android, iOS, Mac OS).

### <a name="code-that-requires-changes"></a>Código que requiere cambios

Algunas API de .NET pueden no estar disponible en todas las plataformas. Si estas API existen en el código, deberá volver a escribir esas secciones para que utilicen API multiplataforma.

Ejemplos de esto son usan de API de reflexión que están disponibles en .NET 4.6, pero no están disponibles en todas las plataformas.

Después de volver a que ha escrito el código mediante las API de portátiles, podrá empaquetar ese código en una biblioteca compartida y la prueba dentro de la aplicación existente.

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>Código que no es portable y requiere volver a escribir

Ejemplos de código que no es probable que sea multiplataforma:

- **Interfaz de usuario** – pantallas de Windows Forms o WPF no se puede usar en proyectos de Android o iOS, por ejemplo. La interfaz de usuario debe volver a escribir, mediante este [comparación de los controles](~/cross-platform/desktop/controls/index.md) como referencia.

- **Almacenamiento de información específica de la plataforma** -código que se basa en una tecnología específica de la plataforma (por ejemplo, una base de datos local de SQL Server Express). Deberá volver a escribir mediante una alternativa entre plataformas (por ejemplo, SQLite para el motor de base de datos).
Algunas operaciones de sistema de archivos que también deba ajustar, puesto que UWP tiene una API ligeramente diferente para Android e iOS (p ej. Algunos sistemas de archivos distinguen mayúsculas de minúsculas y otros usuarios no).

- **componentes de terceros 3rd** : comprobar si hay disponibles en otras plataformas 3rd componentes de terceros en sus aplicaciones. Algunas, como los paquetes de NuGet no visual, es posible que estén disponibles pero otros usuarios (especialmente visual controles como reproductores multimedia o de gráficos)

## <a name="tips-for-making-code-portable"></a>Sugerencias para hacer que el código sea portátil

- **Inserción de dependencias** : proporcionar diferentes implementaciones para cada plataforma, y

- **Enfoque por niveles** : indica si MVVM, MVC, MVP o algún otro patrón que le ayuda a separar el código portable desde el código específico de plataforma.

- **Mensajería** : puede usar el paso de mensajes en el código anular acoplar las interacciones entre las distintas partes de la aplicación.
