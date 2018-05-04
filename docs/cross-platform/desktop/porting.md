---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: Guía de migración de aplicaciones de escritorio
description: Una explicación simple de cómo desacoplar los formularios de Windows existentes o las aplicaciones de WPF para crear aplicaciones multiplataforma para ejecutar en Mac OS, iOS, Android, así como de UWP y Windows 10.
ms.technology: xamarin-crossplatform
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 35ffb9c92b5d3faf48f3e76a6702c2518ca80538
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="desktop-app-porting-guidance"></a>Guía de migración de aplicaciones de escritorio

Mayoría del código de aplicación se puede clasificar en uno de las siguientes áreas:

* Código de interfaz de usuario (p. ej. ventanas y botones)
* 3ª controles de usuario (p. ej. (gráficos)
* Lógica de negocios (p. ej. reglas de validación)
* Acceso y almacenamiento de datos local
* Servicios Web y acceso a datos remotos

Para Windows Forms y WPF aplicaciones escritas con C# (o Visual Basic.NET) una cantidad sorprendente de la lógica de negocios, de acceso datos locales y código de los servicios web se pueden compartir entre plataformas.

## <a name="net-portability-analyzer"></a>Analizador de portabilidad de .NET

Compatibilidad con Visual Studio 2015 y 2017 el [analizador de portabilidad de .NET](https://docs.microsoft.com/en-us/dotnet/articles/standard/portability-analyzer) ([descargar para Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) que puede examinar las aplicaciones existentes y saber la cantidad de código se puede portar "tal cual" a otras plataformas . Puede obtener más información de este [vídeo de Channel 9](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer).

También hay una herramienta de línea de comandos puede descargarse desde [analizador de portabilidad en GitHub](https://github.com/Microsoft/dotnet-apiport) y usado para proporcionar los mismos informes.

## <a name="x-of-my-code-is-portable-what-next"></a>"x % de código es portátil. ¿Qué más?"

Es de esperar que el analizador muestra una gran parte del código es portátil, pero no existe ciertamente va a ser algunas partes de todas las aplicaciones que _no_ moverse a otras plataformas.

Distintos fragmentos de código probablemente entrarán dentro de uno de estos depósitos, explicados con más detalle a continuación:

* Código portable puedan volver a utilizar
* Código que requiere cambios
* Código que no es portátil y necesita volver a escribir

### <a name="re-useable-portable-code"></a>Código portable puedan volver a utilizar

Código de .NET que se escribe con las API disponibles en todas las plataformas se puede tomar multiplataforma sin cambios. Idealmente, podrá mover todo este código en una biblioteca de clases Portable, la biblioteca compartida o la biblioteca estándar de .NET y, a continuación, se prueba dentro de la aplicación existente.

Esa biblioteca compartida, a continuación, puede agregarse a los proyectos de aplicación para otras plataformas (por ejemplo, Android, iOS, Mac OS).

### <a name="code-that-requires-changes"></a>Código que requiere cambios

Algunas API de .NET no estén disponible en todas las plataformas. Si estas API existen en el código, debe volver a escribir estas secciones para que utilicen API multiplataforma.

Ejemplos de este comportamiento son el uso de las API de reflexión que están disponibles en .NET 4.6, pero no están disponibles en todas las plataformas.

Después de volver a haya escrito el código que usa las API portables, debe ser capaz de paquete que el código en una biblioteca compartida y probar dentro de la aplicación existente.

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>Código que no es portátil y necesita volver a escribir

Ejemplos de código que no es probable que sean multiplataforma:

- **Interfaz de usuario** : pantallas de formularios Windows Forms o WPF no se puede usar en proyectos de Android o iOS, por ejemplo. La interfaz de usuario tendrá que volver a escribir, mediante este [controles comparación](~/cross-platform/desktop/controls/index.md) como referencia.

- **Almacenamiento de información específica de la plataforma** -código que se basa en una tecnología específica de la plataforma (por ejemplo, una base de datos local de SQL Server Express). Debe volver a escribir este código utilizando una alternativa entre plataformas (por ejemplo, el código para el motor de base de datos).
Algunas operaciones de sistema de archivos que también necesite ajustar, puesto que UWP tiene ligeramente distintas API para Android e iOS (p. ej. Algunos sistemas de archivos que distinguen mayúsculas de minúsculas y otras no).

- **componentes de entidad 3rd** : comprobar si hay disponibles en otras plataformas 3rd componentes de entidad en las aplicaciones. Otras, como paquetes de NuGet no visuales, estén disponibles pero otros usuarios (especialmente visual controles como reproductores de gráficos o multimedia)

## <a name="tips-for-making-code-portable"></a>Sugerencias para hacer que el código portable

- **Inyección de dependencia** : proporcionar diferentes implementaciones para cada plataforma, y

- **Enfoque por niveles** : si MVVM, MVC, MVP o algún otro modelo que le ayuda a separar el código portable desde el código específico de la plataforma.

- **Mensajería** : puede usar el paso de mensajes en el código anular acoplar las interacciones entre las distintas partes de la aplicación.
