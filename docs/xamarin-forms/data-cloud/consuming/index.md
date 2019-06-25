---
title: Consumo de servicios web
description: Esta guía muestra cómo comunicar con servicios web diferente para poder crear, leer, actualizar y eliminar (CRUD) de funcionalidad a una aplicación de Xamarin.Forms. Los temas tratados incluyen la comunicación con servicios ASMX, WCF services, servicios REST y Azure Mobile Apps.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 163b82590e95a1587836502883cd5f9f2afbe19c
ms.sourcegitcommit: 6e04246207aa743820029e8c217a43cfdd24f991
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2019
ms.locfileid: "67352109"
---
# <a name="consuming-web-services"></a>Consumo de servicios web

_Esta guía muestra cómo comunicar con servicios web diferente para poder crear, leer, actualizar y eliminar (CRUD) de funcionalidad a una aplicación de Xamarin.Forms. Los temas tratados incluyen la comunicación con servicios ASMX, WCF services, servicios REST y Azure Mobile Apps._

## <a name="consuming-an-aspnet-web-service-asmxxamarin-formsdata-cloudconsumingasmxmd"></a>[Consumir un servicio Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)

Los servicios Web ASP.NET (ASMX) proporcionan la capacidad para crear servicios web que envían mensajes a través de HTTP mediante el protocolo Simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma y lenguaje para crear y obtener acceso a servicios web. Los consumidores de un servicio de ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación usado para implementar el servicio. Sólo necesitan entender cómo enviar y recibir mensajes SOAP. En este artículo se muestra cómo consumir un servicio web ASMX desde una aplicación de Xamarin.Forms.

## <a name="consuming-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudconsumingwcfmd"></a>[Consumir un servicio Web de Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md)

WCF es el marco unificado de Microsoft para crear aplicaciones orientadas a servicios. Permite a los desarrolladores crear aplicaciones distribuidas seguras, confiables, transacciones e interoperables. Existen diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero es importante comprender que WCF admite las mismas funcionalidades que proporciona ASMX: los mensajes SOAP a través de HTTP. En este artículo se muestra cómo consumir un servicio SOAP de WCF desde una aplicación de Xamarin.Forms.

## <a name="consuming-a-restful-web-servicexamarin-formsdata-cloudconsumingrestmd"></a>[Consumir un servicio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)

Representational State Transfer (REST) es un estilo de arquitectura para la creación de servicios web. Solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que los exploradores web que se usan para recuperar las páginas web y para enviar datos a los servidores. En este artículo se muestra cómo consumir un servicio web de RESTful desde una aplicación de Xamarin.Forms.

## <a name="consuming-an-azure-mobile-appxamarin-formsdata-cloudconsumingazuremd"></a>[Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)

Azure Mobile Apps le permiten desarrollar aplicaciones con escalables back-ends hospedados en Azure App Service, con compatibilidad con autenticación móvil, sincronización sin conexión y las notificaciones de inserción. En este artículo, que solo es aplicable a Azure Mobile Apps que use un back-end de Node.js, explica cómo consultar, insertar, actualizar y eliminar datos almacenados en una tabla en una instancia de Azure Mobile Apps.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a los servicios web](~/cross-platform/data-cloud/web-services/index.md)
- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
