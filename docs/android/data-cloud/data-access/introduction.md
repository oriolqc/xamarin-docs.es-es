---
title: "Introducción"
ms.topic: article
ms.prod: xamarin
ms.assetid: FDAC0771-4749-4758-865A-F1BD190CA54B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/28/2017
ms.openlocfilehash: bd670a07d09f9ff7892122ff4307ac4b900a580c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="introduction"></a>Introducción

## <a name="when-to-use-a-database"></a>Cuándo se debe utilizar una base de datos

Mientras se estén incrementando las capacidades de almacenamiento y procesamiento de dispositivos móviles, teléfonos y tabletas todavía retrasen sus homólogos de escritorio y portátiles. Por esta razón merece la pena tarda algún tiempo para planear la arquitectura de almacenamiento de datos de la aplicación en lugar de simplemente suponiendo que una base de datos es la respuesta correcta de todo el tiempo. Hay una serie de opciones diferentes que se ajusten a los requisitos diferentes, como:

-  **Preferencias** – Android ofrece un mecanismo integrado para almacenar pares de clave-valor simples de datos. Si va a almacenar valores de configuración de usuario simple o pequeñas cantidades de datos (por ejemplo, información de personalización), a continuación, usar las características nativas de la plataforma para almacenar este tipo de información.
-  **Archivos de texto** – proporcionados por el usuario o las memorias caché de descargan de contenido (p. ej. HTML) se puede almacenar directamente en el sistema de archivos. Utilice una convención de nomenclatura de archivos adecuada para ayudarle a organizar los archivos y buscar los datos.
-  **Serializa los archivos de datos** : objetos pueden conservarse como XML o JSON en el sistema de archivos. .NET framework incluye las bibliotecas que permiten serializar y deserializar objetos fácil. Utilice nombres que correspondan a organizar los archivos de datos.
-  **Base de datos** : SQLite el motor de base de datos está disponible en la plataforma Android, y se estructura útiles para almacenar datos que necesite consultar, ordenar o manipular de otras maneras. Almacenamiento de base de datos es adecuado para listas de datos con muchas de las propiedades.
-  **Archivos de imagen** : aunque es posible almacenar datos binarios en la base de datos en un dispositivo móvil, se recomienda almacenarlas directamente en el sistema de archivos. Si es necesario puede almacenar los nombres de archivo en una base de datos para asociar la imagen con otros datos. Cuando se trabaja con imágenes grandes, o una gran cantidad de imágenes, es recomendable planear una estrategia de almacenamiento en caché que elimina los archivos que ya no se necesita para evitar el consumo de espacio de almacenamiento de todos los del usuario.

Si una base de datos es el mecanismo de almacenamiento de información adecuada para la aplicación, el resto de este documento explica cómo utilizar código en la plataforma de Xamarin.

## <a name="advantages-of-using-a-database"></a>Ventajas de utilizar una base de datos

Hay una serie de ventajas de utilizar una base de datos SQL en su aplicación móvil:

-  Bases de datos SQL permiten eficaz almacenamiento de datos estructurados.
-  Datos específicos se pueden extraer con consultas complejas.
-  Se pueden ordenar los resultados de la consulta.
-  Se pueden agregar los resultados de la consulta.
-  Los desarrolladores con conocimientos de la base de datos pueden utilizar sus conocimientos para diseñar el código de acceso y base de datos.
-  El modelo de datos desde el componente de servidor de una aplicación conectada puede volver a usar (en su totalidad o en parte) en la aplicación móvil.


## <a name="sqlite-database-engine"></a>Motor de base de datos de SQLite

SQLite es un motor de base de datos de código abierto que se ha optado por Google para su plataforma móvil. El motor de base de datos de SQLite está integrado en ambos sistemas operativos, así que no hay ningún trabajo adicional para que los desarrolladores aprovecharse de ella. SQLite se adapta perfectamente al desarrollo móvil multiplataforma porque:

-  El motor de base de datos es pequeña, rápida y fácilmente portátil.
-  Una base de datos se almacena en un único archivo, que es fácil de administrar en dispositivos móviles.
-  El formato de archivo es fácil de usar en las distintas plataformas: si 32 o 64 bits y los sistemas de big - o little-endian.
-  Implementa la mayoría de los estándar SQL92.


Dado código está diseñado para ser pequeño y rápido, existen algunas advertencias sobre su uso:

-  No se admiten alguna sintaxis de combinación externa.
-  Se admiten ADDCOLUMN y sólo cambiar el nombre de tabla. No se puede realizar otras modificaciones en el esquema.
-  Las vistas son de solo lectura.


Se puede obtener más información acerca de SQLite en el sitio Web - [SQLite.org](http://SQLite.org) ; sin embargo, toda la información que necesita usar SQLite con Xamarin contenida en este documento y ejemplos de asociados. El motor de base de datos de SQLite compatible con Android desde Android 2.
Aunque no se tratan en este capítulo, SQLite también está disponible para su uso en aplicaciones de Windows y Windows Phone.

## <a name="windows-and-windows-phone"></a>Windows y Windows Phone

SQLite también puede usarse en plataformas de Windows, aunque esas plataformas no se tratan en este documento.
Obtenga más información en el [Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) y [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) casos prácticos y revise [blog de Tim Heuer](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).


## <a name="related-links"></a>Vínculos relacionados

- [DataAccess Basic (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avanzada (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Datos de Android recetas](https://developer.xamarin.com/recipes/android/data/)
- [Acceso a datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
