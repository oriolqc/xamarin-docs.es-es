---
title: Patrones de aplicación empresarial utilizando el libro electrónico de Xamarin.Forms
description: Este libro electrónico proporciona una guía de arquitectura para el desarrollo de Xamarin.Forms adaptables, fáciles de mantener y probar las aplicaciones empresariales.
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: f972a32f8daf920f2121e5aa56923c0f3a7f808a
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528447"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>Patrones de aplicación empresarial utilizando el libro electrónico de Xamarin.Forms

_Guía de arquitectura para el desarrollo de Xamarin.Forms adaptables, fáciles de mantener y probar las aplicaciones empresariales_

![](images/cover-sml.png "Patrones de aplicación empresarial utilizando el libro electrónico de Xamarin.Forms")

Este libro electrónico proporciona instrucciones sobre cómo implementar el patrón Model-View-ViewModel (MVVM), la inserción de dependencias, navegación, validación y la administración de configuración, manteniendo el acoplamiento flexible. Además, también hay una guía sobre cómo realizar la autenticación y autorización con IdentityServer, acceso a datos de microservicios en contenedores y las pruebas unitarias.

## <a name="prefaceprefacemd"></a>[Prefacio](preface.md)

Este capítulo explica el propósito y el ámbito de la guía y quién lo está destinado a.

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

Los desarrolladores de aplicaciones empresariales enfrentan a varios desafíos que pueden modificar la arquitectura de la aplicación durante el desarrollo. Por lo tanto, es importante compilar una aplicación para que se puede modificar o extendido con el tiempo. Diseño de la capacidad de dichos adaptación puede ser difícil, pero normalmente implica dividir una aplicación en componentes discretos y con acoplamiento flexible que pueden integrarse fácilmente entre sí en una aplicación.

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

El patrón Model-View-ViewModel (MVVM) ayuda a separar la lógica de negocios y la presentación de una aplicación desde su interfaz de usuario (UI). Mantener una separación clara entre la interfaz de usuario y la lógica de aplicación ayuda a abordar numerosos problemas de desarrollo y hacer más fácil probar una aplicación, mantener y evolucionar. Se pueden mejorar enormemente las oportunidades de reutilización de código y permite a los desarrolladores y diseñadores de interfaz de usuario más colaboran fácilmente al desarrollar sus respectivos partes de una aplicación.

## <a name="dependency-injectiondependency-injectionmd"></a>[Inserción de dependencias](dependency-injection.md)

Inserción de dependencias permite la separación de tipos concretos desde el código que dependa de estos tipos. Normalmente usa un contenedor que contiene una lista de registros y las asignaciones entre tipos e interfaces abstractos y los tipos concretos que implementan o amplían estos tipos.

Contenedores de inserción de dependencia reducen el acoplamiento entre objetos por lo que proporciona una funcionalidad para crear instancias de clase y administra su duración en función de la configuración del contenedor. Durante la creación de objetos, el contenedor inserta las dependencias que requiere el objeto en él. Si aún no se han creado esas dependencias, el contenedor crea y resuelve primero sus dependencias.

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[Comunicación entre componentes débilmente acoplados](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) clase implementa la publicación-patrón, que permite la comunicación basada en mensajes entre los componentes que no son convenientes para vincular mediante referencias de objeto y el tipo de suscripción. Este mecanismo permite a los publicadores y suscriptores transmitir sin tener una referencia entre sí, lo que ayuda a reducir las dependencias entre componentes, mientras que permite que los componentes que se desarrollan y prueban de forma independiente.

## <a name="navigationnavigationmd"></a>[Navegación](navigation.md)

Xamarin.Forms incluye compatibilidad con la navegación de página, que normalmente da como resultado de la interacción del usuario con la interfaz de usuario o de la aplicación, como resultado de los cambios de estado controlado por la lógica interna. Sin embargo, navegación puede ser difícil de implementar en las aplicaciones que usan el patrón MVVM.

Este capítulo presenta un `NavigationService` (clase), que se usa para realizar la navegación de model first de vista de modelos de vista. Colocar en la vista lógica de navegación clases de modelo significa que la lógica puede realizarse a través de pruebas automatizadas. Además, el modelo de vista, a continuación, puede implementar la lógica para controlar el desplazamiento para garantizar que se apliquen determinadas reglas empresariales.

## <a name="validationvalidationmd"></a>[Validación](validation.md)

Cualquier aplicación que acepte entradas de los usuarios debe asegurarse de que la entrada sea válida. Sin validación, un usuario puede proporcionar datos que hagan que la aplicación produzca un error. La validación aplica reglas de negocio e impide que un atacante inserte datos malintencionados.

En el contexto de Model-View-ViewModel (MVVM) de patrón, un modelo de vista o modelo a menudo se requerirá para realizar la validación de datos y señalar los errores de validación a la vista para que el usuario puede corregirlos.

## <a name="configuration-managementconfiguration-managementmd"></a>[Administración de configuraciones](configuration-management.md)

La configuración permite la separación de datos que se configura el comportamiento de una aplicación desde el código, lo que permite el comportamiento que se puede cambiar sin volver a generar la aplicación. Configuración de la aplicación es datos que una aplicación crea y administra y configuración de usuario es la configuración de una aplicación personalizable que afectan al comportamiento de la aplicación y no requiere el ajuste de volver a frecuentes.

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[Microservicios en contenedores](containerized-microservices.md)

Los Microservicios ofrecen un enfoque al desarrollo de aplicaciones e implementación que se adapte a los requisitos de agilidad, escalabilidad y confiabilidad de aplicaciones modernas en la nube. Una de las principales ventajas de los microservicios es que pueden ser escaladas horizontalmente de forma independiente, lo que significa que se puede escalar un área funcional específica que requiere más procesamiento de energía o ancho de banda para admitir la demanda, sin escalado innecesariamente áreas de la aplicación que no está experimentando una mayor demanda.

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[Autenticación y autorización](authentication-and-authorization.md)

Existen muchos enfoques para integrar la autenticación y autorización en una aplicación de Xamarin.Forms que se comunica con una aplicación web ASP.NET MVC. En este caso, la autenticación y autorización se realizan con un microservicio en contenedor de identidad que usa 4 IdentityServer. IdentityServer es un marco de código abierto de OAuth 2.0 y OpenID Connect para ASP.NET Core que se integra con ASP.NET Core Identity para realizar la autenticación de token de portador.

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[Acceso a datos remotos](accessing-remote-data.md)

Muchas soluciones modernas basadas en web hacen uso de servicios web, hospedadas por servidores web, para proporcionar funcionalidad de cliente remoto a aplicaciones. Las operaciones que expone un servicio web constituyen una API web y aplicaciones de cliente deben ser capaces de usar la API web sin necesidad de saber cómo se implementan los datos o las operaciones que expone la API.

## <a name="unit-testingunit-testingmd"></a>[Pruebas unitarias](unit-testing.md)

Probar los modelos y los modelos de vista de las aplicaciones MVVM es idéntica a las pruebas de otras clases, y se pueden usar las mismas herramientas y técnicas. Sin embargo, hay algunos patrones típicos de modelo y las clases de modelo de vista, que pueden beneficiarse de las técnicas de pruebas de unidad específica.

## <a name="feedback"></a>Comentarios

Este proyecto tiene un sitio de la Comunidad, en el que puede publicar preguntas y proporcionar comentarios. El sitio de la Comunidad se encuentra en [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). Como alternativa, se pueden enviar comentarios sobre el libro electrónico a [ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com).


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
