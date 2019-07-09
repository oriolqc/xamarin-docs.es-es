---
title: Xamarin.Forms y servicios Web
description: Esta guía explica cómo comunicarse con servicios web diferente para poder crear, leer, actualizar y eliminar (CRUD) de funcionalidad a una aplicación de Xamarin.Forms. Los temas tratados incluyen la comunicación con servicios ASMX, servicios WCF, servicios REST.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 1cf2714191528c5619b4f877bcb43e80464c44d1
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659012"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Forms y servicios Web

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

En este artículo se ofrece un tutorial de la aplicación de ejemplo de Xamarin.Forms que muestra cómo comunicar con los servicios web diferentes. Los temas tratados incluyen la Anatomía de la aplicación, las páginas, el modelo de datos y la invocación de operaciones del servicio web.

## <a name="consume-an-aspnet-web-service-asmxxamarin-formsdata-cloudweb-servicesasmxmd"></a>[Consumir un servicio Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

Los servicios Web ASP.NET (ASMX) proporcionan la capacidad para crear servicios web que envían mensajes a través de HTTP mediante el protocolo Simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma y lenguaje para crear y obtener acceso a servicios web. Los consumidores de un servicio de ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación usado para implementar el servicio. Sólo necesitan entender cómo enviar y recibir mensajes SOAP. En este artículo se muestra cómo consumir un servicio web ASMX desde una aplicación de Xamarin.Forms.

## <a name="consume-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudweb-serviceswcfmd"></a>[Consumir un servicio Web de Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

WCF es el marco unificado de Microsoft para crear aplicaciones orientadas a servicios. Permite a los desarrolladores crear aplicaciones distribuidas seguras, confiables, transacciones e interoperables. Existen diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero es importante comprender que WCF admite las mismas funcionalidades que proporciona ASMX: los mensajes SOAP a través de HTTP. En este artículo se muestra cómo consumir un servicio SOAP de WCF desde una aplicación de Xamarin.Forms.

## <a name="consume-a-restful-web-servicexamarin-formsdata-cloudweb-servicesrestmd"></a>[Consumir un servicio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

Representational State Transfer (REST) es un estilo de arquitectura para la creación de servicios web. Solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que los exploradores web que se usan para recuperar las páginas web y para enviar datos a los servidores. En este artículo se muestra cómo consumir un servicio web de RESTful desde una aplicación de Xamarin.Forms.
