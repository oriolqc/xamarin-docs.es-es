---
title: Datos y servicios en la nube
description: Las aplicaciones de Xamarin.Forms pueden consumir servicios web implementados con una amplia variedad de tecnologías y esta guía examinará cómo hacerlo.
ms.prod: xamarin
ms.assetid: 0601D9D0-C8D2-4C3B-A749-A340BDBF64A4ß
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: f20042b9599f7b4dde699a125e63c5ce435f6bc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="data--cloud-services"></a>Datos y servicios en la nube

_Las aplicaciones de Xamarin.Forms pueden consumir servicios web implementados con una amplia variedad de tecnologías y esta guía examinará cómo hacerlo._

Para obtener una introducción al consumo del servicio web entre plataformas en la plataforma de Xamarin, consulte [Introducción a servicios Web](~/cross-platform/data-cloud/web-services/index.md).

## <a name="understanding-the-samplexamarin-formsdata-cloudwalkthroughmd"></a>[Descripción del ejemplo](~/xamarin-forms/data-cloud/walkthrough.md)

Este artículo ofrece un tutorial de la aplicación de ejemplo de Xamarin.Forms que muestra cómo comunicar con los servicios web diferentes. Los temas tratados son la Anatomía de la aplicación, el modelo de datos, páginas e invocar operaciones del servicio web.

## <a name="consuming-web-servicesxamarin-formsdata-cloudconsumingindexmd"></a>[Consumo de servicios web](~/xamarin-forms/data-cloud/consuming/index.md)

Esta guía demuestra cómo comunicarse con servicios web diferente para poder crear, leer, actualizar y eliminar funcionalidad (CRUD) a una aplicación de Xamarin.Forms. Los temas tratados son comunicarse con [servicios ASMX](consuming/asmx.md), [servicios WCF](consuming/wcf.md), [servicios REST](consuming/rest.md), [aplicaciones móviles de Azure](consuming/azure.md)y [ Servicios Web de Amazon](consuming/aws.md).

## <a name="authenticating-access-to-web-servicesxamarin-formsdata-cloudauthenticationindexmd"></a>[Autenticación del acceso a los servicios web](~/xamarin-forms/data-cloud/authentication/index.md)

Esta guía explica cómo integrar servicios de autenticación en una aplicación de Xamarin.Forms para que los usuarios puedan compartir un back-end al tiempo que sólo tiene acceso a sus propios datos. Los temas tratados son [con la autenticación básica con un servicio REST](authentication/rest.md), [mediante el componente Xamarin.Auth para autenticarse con proveedores de identidades de OAuth](authentication/oauth.md)y el uso de la autenticación integrada que ofrece mecanismos [aplicaciones móviles de Azure](authentication/azure.md), y [Amazon Web Services](authentication/aws.md).

## <a name="synchronizing-data-with-web-servicessyncindexmd"></a>[Sincronización de datos con los servicios web](sync/index.md)

En este artículo se explica cómo agregar la funcionalidad de sincronización sin conexión a una aplicación de Xamarin.Forms. Sincronización sin conexión permite a los usuarios interactuar con una aplicación móvil, ver, agregar o modificar los datos, incluso cuando no hay una conexión de red. Cambios se almacenan en una base de datos local y una vez que el dispositivo está en línea, se pueden sincronizar los cambios con el servicio web.

## <a name="sending-push-notificationspush-notificationsindexmd"></a>[Envío de notificaciones push](push-notifications/index.md)

En este artículo se muestra cómo agregar notificaciones de inserción a una aplicación de Xamarin.Forms. Centros de notificaciones de Azure proporcionan una infraestructura escalable de inserción para enviar notificaciones de inserción móviles desde cualquier back-end para cualquier plataforma móvil, mientras se elimina la complejidad de un back-end tener para comunicarse con sistemas de notificación de plataforma diferente.

## <a name="storing-files-in-the-cloudstorageindexmd"></a>[Almacenamiento de archivos en la nube](storage/index.md)

En este artículo se muestra cómo usar Xamarin.Forms para almacenar datos binarios y texto en el almacenamiento de Azure y cómo obtener acceso a los datos. Almacenamiento de Azure es una solución de almacenamiento en nube escalables que puede utilizarse para almacenar datos estructurados y no estructurados.

## <a name="searching-data-in-the-cloudsearchindexmd"></a>[Búsqueda de datos en la nube](search/index.md)

Este artículo demuestra cómo usar la biblioteca de búsqueda de Microsoft Azure para integrar la búsqueda de Azure en una aplicación de Xamarin.Forms. Búsqueda de Azure es un servicio de nube que proporciona la indización y las capacidades de los datos cargados de consulta. Esto quita los requisitos de infraestructura y la complejidad del algoritmo de búsqueda tradicionalmente asociadas con la implementación de la funcionalidad de búsqueda en una aplicación.

## <a name="storing-data-in-a-document-databasecosmosdbindexmd"></a>[Almacenamiento de datos en una base de datos de documentos](cosmosdb/index.md)

Esta guía muestra cómo utilizar la biblioteca de cliente estándar de .NET de base de datos de Azure Cosmos para integrar una base de datos de documentos de base de datos de Azure Cosmos en una aplicación de Xamarin.Forms. Una base de datos de documentos de base de datos de Azure Cosmos es una base de datos NoSQL que proporciona acceso de latencia baja a documentos JSON, ofrecen un servicio de base de datos rápida, altamente disponible y escalable para aplicaciones que requieren replicación global y escala sin problemas.

## <a name="adding-intelligence-with-cognitive-servicescognitive-servicesindexmd"></a>[Adición de inteligencia con Cognitive Services](cognitive-services/index.md)

Esta guía explica cómo utilizar algunas de las API de servicios de Microsoft cognitivos en una aplicación de Xamarin.Forms. Servicios cognitivos son un conjunto de API, SDK y los servicios disponibles para los desarrolladores para hacer que sus aplicaciones más inteligentes agregando características tales como el reconocimiento facial, reconocimiento de voz y comprensión de lenguaje.
