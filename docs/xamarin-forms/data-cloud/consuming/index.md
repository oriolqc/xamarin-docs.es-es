---
title: Consumir servicios Web
description: "Esta guía demuestra cómo comunicarse con servicios web diferente para poder crear, leer, actualizar y eliminar funcionalidad (CRUD) a una aplicación de Xamarin.Forms. Los temas tratados son comunicarse con ASMX servicios, WCF services, REST servicios, aplicaciones móviles de Azure y servicios Web de Amazon."
ms.topic: article
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 411ceaa372aef7aec51e3fa691996c2d7538c590
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="consuming-web-services"></a>Consumir servicios Web

_Esta guía demuestra cómo comunicarse con servicios web diferente para poder crear, leer, actualizar y eliminar funcionalidad (CRUD) a una aplicación de Xamarin.Forms. Los temas tratados son comunicarse con ASMX servicios, WCF services, REST servicios, aplicaciones móviles de Azure y servicios Web de Amazon._

## <a name="consuming-an-aspnet-web-service-asmxxamarin-formsdata-cloudconsumingasmxmd"></a>[Consumir un servicio Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)

Los servicios Web ASP.NET (ASMX) proporcionan la capacidad para crear servicios web que envían mensajes a través de HTTP mediante el protocolo Simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma e independientes del lenguaje para crear y obtener acceso a servicios web. Los consumidores de un servicio ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación utilizado para implementar el servicio. Solo necesitan saber cómo enviar y recibir mensajes SOAP. Este artículo demuestra cómo consumir un servicio web ASMX desde una aplicación de Xamarin.Forms.

## <a name="consuming-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudconsumingwcfmd"></a>[Consumir un servicio Web de Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md)

WCF es el marco unificado de Microsoft para compilar aplicaciones orientadas a servicios. Permite a los desarrolladores crear aplicaciones distribuidas seguras, confiables, transacciones e interoperables. Existen diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero es importante comprender que WCF admite las mismas capacidades que proporciona ASMX: mensajes SOAP a través de HTTP. Este artículo demuestra cómo consumir un servicio SOAP de WCF desde una aplicación de Xamarin.Forms.

## <a name="consuming-a-restful-web-servicexamarin-formsdata-cloudconsumingrestmd"></a>[Consumir un servicio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)

Representational State Transfer (REST) es un estilo de arquitectura para la creación de servicios web. Solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que los exploradores web que se utilizan para recuperar las páginas web y para enviar datos a los servidores. Este artículo demuestra cómo consumir un servicio web RESTful desde una aplicación de Xamarin.Forms.

## <a name="consuming-an-azure-mobile-appxamarin-formsdata-cloudconsumingazuremd"></a>[Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)

Aplicaciones móviles de Azure le permiten desarrollar aplicaciones con back-ends de escalable hospedado en el servicio de aplicación de Azure, con compatibilidad para la autenticación de dispositivos móvil, sincronización sin conexión y las notificaciones de inserción. En este artículo, que sólo es aplicable a las aplicaciones móviles de Azure que usan un back-end de Node.js, explica cómo consultar, insertar, actualizar y eliminar los datos almacenados en una tabla en una instancia de aplicaciones móviles de Azure.

## <a name="consuming-an-amazon-simpledb-servicexamarin-formsdata-cloudconsumingawsmd"></a>[Consumir un servicio de SimpleDB de Amazon](~/xamarin-forms/data-cloud/consuming/aws.md)

Amazon SimpleDB es un servicio web que proporciona la capacidad de almacenar y consultar datos en la nube de Amazon. En este artículo se explica cómo usar el SDK de AWS para .NET para consultar, crear y reemplazar y eliminar los datos almacenados en un servicio de SimpleDB.


## <a name="related-links"></a>Vínculos relacionados

- [Introducción a servicios Web](~/cross-platform/data-cloud/web-services/index.md)
- [Introducción al soporte técnico de Async](~/cross-platform/platform/async.md)
