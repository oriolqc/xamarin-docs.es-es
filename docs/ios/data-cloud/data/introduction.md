---
title: Introducción al almacenamiento de datos en aplicaciones de Xamarin.iOS
description: Este documento describe diversos medios de almacenamiento de datos en una aplicación de Xamarin.iOS y proporciona información específica sobre las ventajas de SQLite.
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 5df001926512b182c19ccfcf896057e88f5a597a
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650244"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>Introducción al almacenamiento de datos en aplicaciones de Xamarin.iOS

## <a name="when-to-use-a-database"></a>Cuándo usar una base de datos

Mientras aumentan las capacidades de almacenamiento y procesamiento de dispositivos móviles, teléfonos y tabletas todavía retrase en comparación con su escritorio &amp; homólogos portátil. Por este motivo es que vale la pena realizar algún tiempo para planear la arquitectura de almacenamiento de datos para la aplicación en lugar de simplemente suponiendo que una base de datos es la mejor solución todo el tiempo. Hay una serie de diferentes opciones que se adapten a distintos requisitos, tales como:

-  **Preferencias** – iOS ofrece un mecanismo integrado para almacenar pares de clave-valor simple de datos. Si va a almacenar pequeñas cantidades de datos (por ejemplo, información de personalización) o de configuración de usuario simple, a continuación, usar las características nativas de la plataforma para almacenar este tipo de información. Para iOS también se puede sacar partido de la sincronización de iCloud para estos datos, tanto para la copia de seguridad y sincronización para los usuarios con varios dispositivos.
-  **Archivos de texto** – proporcionados por el usuario o las memorias caché de contenido (p ej. descargan. HTML) se puede almacenar directamente en el sistema de archivos. Utilice una convención de nomenclatura de archivos adecuada para ayudarle a organizar los archivos y buscar los datos.
-  **Serializa los archivos de datos** : los objetos se pueden almacenar como XML o JSON en el sistema de archivos. .NET framework incluye las bibliotecas que permiten serializar y deserializar objetos fácil. Usar nombres apropiados para organizar los archivos de datos.
-  **Base de datos** – SQLite el motor de base de datos es iOS disponibles y es útil para almacenar datos estructurados que necesita para consultar, ordenar o manipular. Almacenamiento de base de datos es adecuado para listas de datos con muchas propiedades.
-  **Archivos de imagen** : aunque es posible almacenar datos binarios en la base de datos en un dispositivo móvil, se recomienda almacenarlas directamente en el sistema de archivos. Si es necesario puede almacenar los nombres de archivo en una base de datos para asociar la imagen con otros datos. Cuando se trabaja con imágenes grandes, o una gran cantidad de imágenes, es recomendable planear una estrategia de almacenamiento en caché que elimina los archivos que ya no necesita para evitar el consumo de espacio de almacenamiento de todas las del usuario.


Si una base de datos es el mecanismo de almacenamiento correcto para la aplicación, el resto de este documento describe cómo usar SQLite en la plataforma Xamarin.

## <a name="advantages-of-using-a-database"></a>Ventajas del uso de una base de datos

Hay una serie de ventajas de utilizar una base de datos SQL en su aplicación móvil:

-  Las bases de datos SQL permiten el almacenamiento eficiente de datos estructurados.
-  Datos específicos se pueden extraer con consultas complejas.
-  Se pueden ordenar los resultados de la consulta.
-  Se pueden agregar los resultados de la consulta.
-  Los desarrolladores con conocimientos de la base de datos pueden usar sus conocimientos para diseñar el código de acceso a datos y la base de datos.
-  El modelo de datos del componente de servidor de una aplicación conectada se puede volver a usar (en su totalidad o parcialmente) en la aplicación móvil.


## <a name="sqlite-database-engine"></a>Motor de base de datos SQLite

SQLite es un motor de base de datos de código abierto que se ha adoptado por Apple para su plataforma móvil. El motor de base de datos de SQLite está integrado en iOS, por lo que no hay ningún trabajo adicional para que los desarrolladores aprovechar las ventajas de la misma. SQLite se adapta perfectamente al desarrollo móvil entre plataformas porque:

-  El motor de base de datos es pequeño, rápido y facilidad de portabilidad.
-  Una base de datos se almacena en un único archivo, que es fácil de administrar en dispositivos móviles.
-  Es fácil de usar el formato de archivo entre plataformas: si 32 o 64 bits y los sistemas de big - o little-endian.
-  Implementa la mayoría de los estándar SQL92.


Como SQLite está diseñado para ser pequeño y rápido, hay algunas advertencias sobre su uso:

-  No se admite alguna sintaxis de combinación externa.
-  Se admiten ADDCOLUMN y sólo cambiar el nombre de tabla. No se puede realizar otras modificaciones en el esquema.
-  Las vistas son de solo lectura.


Puede aprender más acerca de SQLite en el sitio Web - [SQLite.org](http://SQLite.org) : sin embargo, toda la información que necesita usar SQLite con Xamarin está contenida en este documento y ejemplos de asociados. El motor de base de datos de SQLite está integrado en todas las versiones de iOS.
Aunque no se tratan en este capítulo, SQLite también está disponible para su uso en aplicaciones de Windows y Windows Phone.

## <a name="windows-and-windows-phone"></a>Windows y Windows Phone

También se puede usar SQLite en plataformas Windows, aunque estas plataformas no se tratan en este documento.
Obtenga más información en el [Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) y [Tasky Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky) casos prácticos y revise [blog de Tim Heuer](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).



## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recetas de datos](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
