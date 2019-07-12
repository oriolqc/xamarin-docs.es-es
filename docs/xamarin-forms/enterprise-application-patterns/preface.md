---
title: Prólogo al desarrollo de aplicaciones de empresa
description: Este capítulo proporciona un prefacio a patrones de aplicación empresarial mediante Xamarin.Forms.
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 82f1455ff5e5ac06b50664e1d4d533d4964b7a0e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831126"
---
# <a name="preface-to-enterprise-app-development"></a>Prólogo al desarrollo de aplicaciones de empresa

Este libro electrónico proporciona instrucciones sobre la creación de aplicaciones empresariales de desarrollo multiplataforma con Xamarin.Forms. Xamarin.Forms es un Kit de herramientas de interfaz de usuario multiplataforma que permite a los desarrolladores crear fácilmente diseños de interfaz que se pueden compartir entre plataformas, incluidas iOS, Android y la plataforma Universal de Windows (UWP) de usuario nativa. Proporciona una solución completa para la empresa a empleado (B2E), negocio a negocio (B2B) y Business a las aplicaciones de consumidor (B2C) que proporciona la capacidad de compartir código entre todas las plataformas de destino y para ayudar a reducir el costo total de propiedad (TCO).

La guía proporciona una guía de arquitectura para desarrollar aplicaciones de empresa de Xamarin.Forms adaptables, fáciles de mantener y fácil de probar. Se proporciona orientación sobre cómo implementar MVVM, inserción de dependencias, navegación, validación y la administración de configuración, manteniendo el acoplamiento flexible. Además, también hay una guía sobre cómo realizar la autenticación y autorización con IdentityServer, acceso a datos de microservicios en contenedores y las pruebas unitarias.

La guía incluye código fuente para el [aplicación móvil de eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)y el código fuente de la [aplicación de referencia eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers). La aplicación móvil de eShopOnContainers es una aplicación multiplataforma enterprise desarrollada con Xamarin.Forms, que se conecta a una serie de microservicios en contenedores conocidos como aplicación de referencia de eShopOnContainers. Sin embargo, la aplicación móvil de eShopOnContainers puede configurarse para consumir datos de servicios ficticios para aquellos que desean evitar la implementación de los microservicios en contenedores.

## <a name="whats-left-out-of-this-guides-scope"></a>Lo que queda fuera del ámbito de esta guía

Esta guía está dirigida a los lectores que ya están familiarizados con Xamarin.Forms. Para obtener una introducción detallada a Xamarin.Forms, consulte el [Xamarin.Forms documentación](~/xamarin-forms/index.yml), y [Creating Mobile Apps with Xamarin.Forms](https://aka.ms/xamebook).

La guía es complementaria a [Microservicios de. NET: Arquitectura de aplicaciones .NET en contenedor](https://aka.ms/microservicesebook), que se centra en desarrollar e implementar microservicios en contenedores. Incluyen otras guías que vale la pena leer [diseño de la arquitectura y desarrollo de aplicaciones Web modernas con ASP.NET Core y Microsoft Azure](https://aka.ms/WebAppEbook), [en contenedores Docker Application Lifecycle con Microsoft Platform y herramientas](https://aka.ms/dockerlifecycleebook), y [plataforma de Microsoft y herramientas de desarrollo de aplicaciones móviles](https://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>¿Quién debe usar esta guía

Los destinatarios de esta guía son principalmente a los desarrolladores y arquitectos que deseen obtener información sobre cómo diseñar e implementar aplicaciones empresariales de desarrollo multiplataforma con Xamarin.Forms.

Otros destinatarios secundarios son decisiones técnicas que le gustaría recibir información general sobre arquitectura y tecnología antes de decidir qué enfoque para seleccionar para el desarrollo de aplicaciones empresariales multiplataforma con Xamarin.Forms.

## <a name="how-to-use-this-guide"></a>Cómo usar esta guía

Esta guía se centra en la creación de aplicaciones empresariales de desarrollo multiplataforma con Xamarin.Forms. Por lo tanto, se debe leer en su totalidad para ofrecer una base de la descripción de estas aplicaciones y sus consideraciones técnicas. La guía, junto con su aplicación de ejemplo, también puede servir como punto de inicio o la referencia para crear una nueva aplicación de empresa. Use la aplicación de ejemplo asociada como una plantilla para la nueva aplicación, o para ver cómo organizar los componentes de una aplicación. A continuación, consulte esta guía para una guía de arquitectura.

No dude en reenviar a esta guía a los miembros del equipo para ayudar a garantizar una comprensión común de desarrollo de aplicaciones empresariales multiplataforma con Xamarin.Forms. Tener todo el mundo principios subyacentes y trabajar desde un conjunto común de terminología ayudará a garantizar una aplicación coherente de los modelos arquitectónicos y los procedimientos recomendados.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
