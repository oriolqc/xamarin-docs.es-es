---
title: Proveedores de contenido de cómo funcionan
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: df4c2e10e34c308e4fadb44fba9c6a14714ae1b9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108943"
---
# <a name="how-content-providers-work"></a>Proveedores de contenido de cómo funcionan

Hay dos clases implicadas en un `ContentProvider` interacción:

- **ContentProvider** &ndash; implementa una API que expone un conjunto de datos de una manera estándar. Los métodos principales son Query, Insert, Update y Delete.

- **ContentResolver** &ndash; un proxy estático que se comunica con un `ContentProvider` para tener acceso a sus datos, ya sea de la misma aplicación o desde otra aplicación.

Un proveedor de contenido normalmente está respaldado por una base de datos de SQLite, pero la API significa que usar código no es necesario saber nada sobre el SQL subyacente. Las consultas se realizan a través de un Uri utilizando constantes para hacer referencia a nombres de columna (para reducir las dependencias de la estructura de datos subyacentes) y un `ICursor` se devuelve para que el código usado recorrer en iteración.


## <a name="consuming-a-contentprovider"></a>Consumo de un ContentProvider

`ContentProviders` exponer su funcionalidad a través de un Uri que está registrado en el **AndroidManifest.xml** de la aplicación que publica los datos. Es una convención que el Uri y las columnas de datos que se exponen deben estar disponibles como constantes para que sea fácil enlazar a los datos. Integrado de Android `ContentProviders` proporcionan clases de comodidad con constantes que hacen referencia a la estructura de datos en el [ `Android.Providers` ](https://developer.xamarin.com/api/namespace/Android.Provider/) espacio de nombres.



### <a name="built-in-providers"></a>Proveedores integrados

Android le proporciona acceso a una amplia gama de sistemas y el uso de datos de usuario `ContentProviders`:

- *Explorador* &ndash; marcadores y el historial del explorador (requiere el permiso `READ_HISTORY_BOOKMARKS` o `WRITE_HISTORY_BOOKMARKS`).

- *CallLog* &ndash; llamadas recientes realizados o recibidos con el dispositivo.

- *Contactos* &ndash; detallada información de la lista de contactos del usuario, incluidas las personas, teléfonos, fotos y grupos.

- *MediaStor* &ndash; contenido del dispositivo del usuario: audio (álbumes, intérpretes, géneros, listas de reproducción), (incluidas las vistas en miniatura) de imágenes y vídeo.

- *Configuración de* &ndash; preferencias y la configuración para todo el sistema.

- *UserDictionary* &ndash; contenido del diccionario definido por el usuario usa para la entrada de texto predictivo.

- *Correo de voz* &ndash; historial de los mensajes de correo de voz.



## <a name="classes-overview"></a>Información general de clases

Las clases primarias utilizadas cuando se trabaja con un `ContentProvider` se muestra aquí:

[![Diagrama de clases de aplicación de proveedor de contenido y las interacciones de aplicación Consuming](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

En este diagrama, el `ContentProvider` implementa las consultas y registra el identificador URI que otras aplicaciones que se usan para buscar datos. El `ContentResolver` actúa como un 'proxy' para el `ContentProvider` (consultar, insertar, actualizar y eliminar métodos). El `SQLiteOpenHelper` contiene los datos utilizados por el `ContentProvider`, pero no se expone directamente a la que usan aplicaciones.
El `CursorAdapter` pasa del cursor devuelto por la `ContentResolver` para mostrar en un `ListView`. El `UriMatcher` es una clase auxiliar que analiza el URI al procesar consultas.

El propósito de cada clase se describe a continuación:

- **ContentProvider** &ndash; implementar métodos de esta clase abstracta para exponer los datos. La API está disponible para otras clases y las aplicaciones a través del atributo de identificador Uri que se agrega a la definición de clase.

- **SQLiteOpenHelper** &ndash; ayuda a implementa el almacén de datos SQLite que se expone mediante el `ContentProvider`.

- **UriMatcher** &ndash; Use `UriMatcher` en su `ContentProvider` implementación para ayudar a administrar los identificadores URI que se usan para consultar el contenido.

- **ContentResolver** &ndash; consumiendo el código usa un `ContentResolver` para tener acceso a un `ContentProvider` instancia. Juntas, las dos clases se encargan de los problemas de comunicación entre procesos, lo que permite compartir fácilmente entre aplicaciones datos. Usar código nunca se crea un `ContentProvider` clase de forma explícita; en su lugar, se tiene acceso a los datos mediante la creación de un cursor basado en un Uri expuesto por el `ContentProvider` aplicación.

- **CursorAdapter** &ndash; Use `CursorAdapter` o `SimpleCursorAdapter` para mostrar los datos de acceso a través de un `ContentProvider`.

El `ContentProvider` API permite que los consumidores realizar diversas operaciones en los datos, como:

-  Consultar datos para devolver listas o registros individuales.
-  Modificar los registros individuales.
-  Agregar nuevos registros.
-  Eliminar registros.

Este documento contiene un ejemplo que usa proporcionada por el sistema `ContentProvider`, así como un ejemplo sencillo de solo lectura que implementa un personalizado `ContentProvider`.

