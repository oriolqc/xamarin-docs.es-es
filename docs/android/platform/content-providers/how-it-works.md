---
title: Trabajo de administración de contenido de proveedores
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b4c674176be5af09d6b780d79923737a364d1591
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="how-content-providers-work"></a>Trabajo de administración de contenido de proveedores

Hay dos clases implicadas en un `ContentProvider` interacción:

- **ContentProvider** &ndash; implementa una API que expone un conjunto de datos de una manera estándar. Los métodos principales son consultas, Insert, Update y Delete.

- **ContentResolver** &ndash; un proxy estático que se comunica con un `ContentProvider` para tener acceso a sus datos, ya sea desde dentro de la misma aplicación o desde otra aplicación.

Un proveedor de contenido normalmente está respaldado por una base de datos de SQLite, pero la API significa que utilizar código no es necesario saber nada sobre el SQL subyacente. Las consultas se realizan a través de un Uri con constantes para hacer referencia a nombres de columna (para reducir las dependencias en la estructura de datos subyacente) y un `ICursor` se devuelve para que el código usado recorrer en iteración.


## <a name="consuming-a-contentprovider"></a>Consumir un ContentProvider

`ContentProviders` exponer su funcionalidad a través de un Uri que se ha registrado en el **AndroidManifest.xml** de la aplicación que publica los datos. Es una convención que el Uri y las columnas de datos que se exponen deben estar disponibles como constantes que resulte sencillo enlazar a los datos. Integrados del Android `ContentProviders` todo proporciona clases de comodidad con constantes que hacen referencia a la estructura de datos en el [ `Android.Providers` ](https://developer.xamarin.com/api/namespace/Android.Provider/) espacio de nombres.



### <a name="built-in-providers"></a>Proveedores integrados

Android le proporciona acceso a una amplia gama de sistemas y el uso de datos de usuario `ContentProviders`:

- *Explorador* &ndash; marcadores y el historial de exploración (requiere el permiso `READ_HISTORY_BOOKMARKS` o `WRITE_HISTORY_BOOKMARKS`).

- *CallLog* &ndash; llamadas recientes realizados o recibe con el dispositivo.

- *Contactos* &ndash; obtener información de la lista de contactos del usuario, como personas, teléfonos, fotos y grupos.

- *MediaStor* &ndash; contenido del dispositivo de usuario: audio (álbumes, intérpretes, géneros, listas de reproducción), (incluidas las vistas en miniatura) de imágenes y vídeo.

- *Configuración de* &ndash; preferencias y opciones de dispositivo de todo el sistema.

- *UserDictionary* &ndash; contenido del diccionario definido por el usuario usa para la entrada de texto predictivo.

- *Correo de voz* &ndash; historial de mensajes de correo de voz.



## <a name="classes-overview"></a>Información general de clases

Las clases principales que se usan al trabajar con un `ContentProvider` se muestran a continuación:

[![Diagrama de clases de aplicación de proveedor de contenido y las interacciones de la aplicación de Consuming](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

En este diagrama, el `ContentProvider` implementa las consultas y registra el URI que otras aplicaciones que se usan para buscar datos. El `ContentResolver` actúa como un 'proxy' para el `ContentProvider` (consultar, insertar, actualizar y eliminar métodos). El `SQLiteOpenHelper` contiene los datos utilizados por el `ContentProvider`, pero no se expone directamente al consumir las aplicaciones.
El `CursorAdapter` pasa del cursor devuelto por la `ContentResolver` para mostrar en un `ListView`. El `UriMatcher` es una clase auxiliar que analiza el URI al procesar las consultas.

El propósito de cada clase se describe a continuación:

- **ContentProvider** &ndash; implementar métodos de esta clase abstracta para exponer los datos. La API están disponible para otras clases y las aplicaciones mediante el atributo de identificador Uri que se agrega a la definición de clase.

- **SQLiteOpenHelper** &ndash; le ayuda a implementar el almacén de datos de SQLite expuesto por la `ContentProvider`.

- **UriMatcher** &ndash; Use `UriMatcher` en su `ContentProvider` implementación para ayudar a administrar el URI que se usan para consultar el contenido.

- **ContentResolver** &ndash; utilizar código usa un `ContentResolver` para tener acceso a un `ContentProvider` instancia. Las dos clases conjuntamente ocupan de los problemas de comunicación entre procesos, permitir que los datos pueden compartir con facilidad entre aplicaciones. Utilizar código no crea nunca una `ContentProvider` clase explícitamente; en su lugar, se tiene acceso a los datos mediante la creación de un cursor basado en un Uri expuesto por la `ContentProvider` aplicación.

- **CursorAdapter** &ndash; Use `CursorAdapter` o `SimpleCursorAdapter` para mostrar los datos que se tiene acceso a través de un `ContentProvider`.

El `ContentProvider` API permite a los consumidores realizar diversas operaciones en los datos, como:

-  Consultar datos para devolver listas o registros individuales.
-  Modificar los registros individuales.
-  Agregar nuevos registros.
-  Eliminar registros.

Este documento contiene un ejemplo que utiliza proporcionada por el sistema `ContentProvider`, así como un ejemplo sencillo de sólo lectura que implementa un personalizado `ContentProvider`.

