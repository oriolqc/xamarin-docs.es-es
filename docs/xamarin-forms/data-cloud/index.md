---
title: Datos y servicios en la nube
description: Las aplicaciones de Xamarin.Forms pueden consumir servicios web implementados utilizando una amplia variedad de tecnologías y esta guía examinará cómo hacerlo.
ms.prod: xamarin
ms.assetid: 0601D9D0-C8D2-4C3B-A749-A340BDBF64A4ß
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 6e67820fa83ddea46f934b4eaedde2c6334f9cc6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61327511"
---
# <a name="data--cloud-services"></a>Datos y servicios en la nube

_Las aplicaciones de Xamarin.Forms pueden consumir servicios web implementados utilizando una amplia variedad de tecnologías y esta guía examinará cómo hacerlo._

Para obtener una introducción al consumo del servicio web entre plataformas en la plataforma Xamarin, consulte [Introducción a servicios Web](~/cross-platform/data-cloud/web-services/index.md).

## <a name="understanding-the-samplexamarin-formsdata-cloudwalkthroughmd"></a>[Descripción del ejemplo](~/xamarin-forms/data-cloud/walkthrough.md)

En este artículo se ofrece un tutorial de la aplicación de ejemplo de Xamarin.Forms que muestra cómo comunicar con los servicios web diferentes. Los temas tratados incluyen la Anatomía de la aplicación, las páginas, el modelo de datos y la invocación de operaciones del servicio web.

## <a name="consuming-web-servicesxamarin-formsdata-cloudconsumingindexmd"></a>[Consumo de servicios web](~/xamarin-forms/data-cloud/consuming/index.md)

Esta guía muestra cómo comunicar con servicios web diferente para poder crear, leer, actualizar y eliminar (CRUD) de funcionalidad a una aplicación de Xamarin.Forms. Los temas tratados incluyen la comunicación con [servicios ASMX](consuming/asmx.md), [servicios WCF](consuming/wcf.md), [servicios REST](consuming/rest.md), y [Azure Mobile Apps](consuming/azure.md).

## <a name="authenticating-access-to-web-servicesxamarin-formsdata-cloudauthenticationindexmd"></a>[Autenticación del acceso a los servicios web](~/xamarin-forms/data-cloud/authentication/index.md)

Esta guía explica cómo integrar servicios de autenticación en una aplicación de Xamarin.Forms para permitir a los usuarios compartir un back-end al tiempo que sólo tiene acceso a sus propios datos. Los temas tratados incluyen [mediante la autenticación básica con un servicio REST](authentication/rest.md), [mediante el componente Xamarin.Auth para autenticarse con proveedores de identidades de OAuth](authentication/oauth.md)y el uso de la autenticación integrada que ofrece mecanismos [Azure Mobile Apps](authentication/azure.md).

## <a name="synchronizing-data-with-web-servicessyncindexmd"></a>[Sincronización de datos con los servicios web](sync/index.md)

En este artículo se explica cómo agregar la funcionalidad de sincronización sin conexión a una aplicación de Xamarin.Forms. La sincronización sin conexión permite a los usuarios interactuar con una aplicación móvil, ver, agregar y modificar los datos incluso cuando no hay una conexión de red. Estos cambios se almacenan en una base de datos local y una vez que el dispositivo está en línea, se pueden sincronizar los cambios con el servicio web.

## <a name="sending-push-notificationspush-notificationsindexmd"></a>[Envío de notificaciones push](push-notifications/index.md)

En este artículo se muestra cómo agregar notificaciones de inserción a una aplicación de Xamarin.Forms. Azure Notification Hubs proporciona una infraestructura de inserción escalable para enviar notificaciones de inserción móviles desde cualquier back-end a cualquier plataforma móvil, mientras se elimina la complejidad de un back-end de tener que comunicarse con sistemas de notificación de plataforma diferente.

## <a name="storing-files-in-the-cloudstorageindexmd"></a>[Almacenamiento de archivos en la nube](storage/index.md)

En este artículo se muestra cómo usar Xamarin.Forms para almacenar datos binarios y texto en el almacenamiento de Azure y cómo tener acceso a los datos. Azure Storage es una solución de almacenamiento en la nube escalable que puede usarse para almacenar datos estructurados y no estructurados.

## <a name="searching-data-in-the-cloudsearchindexmd"></a>[Búsqueda de datos en la nube](search/index.md)

En este artículo se muestra cómo usar la biblioteca de Microsoft Azure Search para integrar la búsqueda de Azure en una aplicación de Xamarin.Forms. Azure Search es un servicio en la nube que proporciona la indización y consulta las capacidades de los datos cargados. Esto quita los requisitos de infraestructura y la complejidad de algoritmo de búsqueda tradicionalmente asociada con la implementación de la funcionalidad de búsqueda en una aplicación.

## <a name="storing-data-in-a-document-databasecosmosdbindexmd"></a>[Almacenamiento de datos en una base de datos de documentos](cosmosdb/index.md)

Esta guía muestra cómo usar la biblioteca de cliente estándar de .NET de Azure Cosmos DB para integrar una base de datos de documentos de Azure Cosmos DB en una aplicación de Xamarin.Forms. Una base de datos de documentos de Azure Cosmos DB es una base de datos NoSQL que proporciona acceso de baja latencia a los documentos JSON, que ofrece un servicio de base de datos rápido, alta disponibilidad y escalabilidad para aplicaciones que requieren replicación global y escala sin problemas.

## <a name="adding-intelligence-with-cognitive-servicescognitive-servicesindexmd"></a>[Adición de inteligencia con Cognitive Services](cognitive-services/index.md)

Esta guía explica cómo usar algunas de las API de Microsoft Cognitive Services en una aplicación de Xamarin.Forms. Cognitivas Services son un conjunto de API, SDK y servicios disponibles para los desarrolladores para que sus aplicaciones más inteligentes mediante la adición de características como reconocimiento facial, reconocimiento de voz y comprensión del lenguaje.
