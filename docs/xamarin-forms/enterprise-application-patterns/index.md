---
title: "Patrones de aplicación de empresa mediante Xamarin.Forms eBook"
description: "Guía de arquitectura para desarrollar aplicaciones de empresa de Xamarin.Forms adaptables, fácil de mantener y comprobables"
ms.topic: article
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 7ed546ac975ce1956d94d509486e4cfb25d28100
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>Patrones de aplicación de empresa mediante Xamarin.Forms eBook

_Guía de arquitectura para desarrollar aplicaciones de empresa de Xamarin.Forms adaptables, fácil de mantener y comprobables_

![](images/cover-sml.png "Patrones de aplicación de empresa mediante Xamarin.Forms eBook")

Este libro electrónico proporciona instrucciones sobre cómo implementar el patrón Model-View-ViewModel (MVVM), inserción de dependencias, navegación, validación y la administración de configuración, al tiempo que mantiene el acoplamiento flexible. Además, también hay instrucciones acerca de cómo realizar la autenticación y autorización con IdentityServer, acceso a datos desde microservicios en contenedores y las pruebas unitarias.

## <a name="prefaceprefacemd"></a>[Preface](preface.md)

Este capítulo explica el propósito y el ámbito de la guía y a quién está destinado a.

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

Los desarrolladores de aplicaciones empresariales enfrentan a varios desafíos que pueden modificar la arquitectura de la aplicación durante el desarrollo. Por lo tanto, es importante compilar una aplicación para que se puede modificar o extender con el tiempo. Diseñar para tal adaptabilidad puede ser difícil, pero normalmente implica crear particiones de una aplicación en componentes discretos y con acoplamiento flexible que pueden integrarse fácilmente juntos en una aplicación.

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

El modelo Model-View-ViewModel (MVVM) ayuda a separar la lógica de negocios y la presentación de una aplicación desde la interfaz de usuario (UI) de forma clara. Mantener una separación clara entre la lógica de la aplicación y la interfaz de usuario ayuda a solucionar problemas de desarrollo numerosas y puede hacer que una aplicación sea más fácil probar, mantener y desarrollar. Se pueden mejorar enormemente oportunidades de reutilización de código y permite a los desarrolladores y diseñadores de interfaz de usuario más puedan colaborar fácilmente al desarrollar sus respectivos partes de una aplicación.

## <a name="dependency-injectiondependency-injectionmd"></a>[Inserción de dependencias](dependency-injection.md)

Inyección de dependencia permite la separación de tipos concretos desde el código que dependa de estos tipos. Normalmente usa un contenedor que contiene una lista de registros y las asignaciones entre tipos abstractos e interfaces y los tipos concretos que implementan o extienden estos tipos.

Contenedores de inyección de dependencia reducen el acoplamiento entre objetos por lo que proporciona una funcionalidad para crear instancias de clase y administrar su duración en función de la configuración del contenedor. Durante la creación de objetos, el contenedor inserta las dependencias que requiere el objeto en él. Si aún no se han creado esas dependencias, el contenedor crea y sus dependencias resuelve en primer lugar.

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[Comunicación entre débilmente acoplados componentes](communicating-between-loosely-coupled-components.md)

El Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) clase implementa la publicación-suscribirse patrón, que permita la comunicación basada en mensajes entre los componentes que son poco práctico vincular por referencias de objeto y el tipo. Este mecanismo permite publicadores y suscriptores de comunicarse sin necesidad de una referencia a entre sí, lo que ayuda a reducir las dependencias entre los componentes, mientras que permite también componentes desarrollado y probado por separado.

## <a name="navigationnavigationmd"></a>[Navegación](navigation.md)

Xamarin.Forms incluye compatibilidad para la navegación en páginas, que normalmente tiene como resultado de la interacción del usuario con la interfaz de usuario o de la propia aplicación como resultado de cambios de estado interno controlado por la lógica. Sin embargo, la navegación puede ser bastante complicada implementar en aplicaciones que usan el patrón MVVM.

Este capítulo se presentan un `NavigationService` (clase), que se usa para realizar la navegación por modelo basado en la vista de modelos de vista. Colocar en la vista lógica de navegación clases del modelo significa que la lógica puede realizarse a través de las pruebas automatizadas. Además, el modelo de vista, a continuación, puede implementar la lógica para controlar el desplazamiento para asegurarse de que se aplican ciertas reglas de negocios.

## <a name="validationvalidationmd"></a>[Validación](validation.md)

Cualquier aplicación que acepta datos proporcionados por los usuarios debe asegurarse de que la entrada es válida. Sin validación, un usuario puede proporcionar datos a los que hace que la aplicación genere un error. La validación aplica reglas de negocios y evita que un atacante inserte datos malintencionados.

En el contexto de los modelos ViewModel (MVVM) de patrón, un modelo de vista o modelo a menudo se requerirá a realizar la validación de datos y notificar los errores de validación a la vista para que el usuario puede corregirlos.

## <a name="configuration-managementconfiguration-managementmd"></a>[Administración de configuración](configuration-management.md)

Configuración permite la separación de datos que se configura el comportamiento de una aplicación desde el código, lo que permite el comportamiento puede modificarse sin volver a generar la aplicación. Configuración de la aplicación es datos que una aplicación crea y administra y configuración del usuario sea la configuración de una aplicación personalizable que afectan al comportamiento de la aplicación y no requiere frecuente ajuste de nuevo.

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[Microservicios en contenedores](containerized-microservices.md)

Microservicios ofrecen un enfoque de implementación que es adecuado para los requisitos de la agilidad, la escala y la confiabilidad de las aplicaciones modernas en la nube y el desarrollo de aplicaciones. Una de las principales ventajas de microservicios es que puede ser escalado horizontal de forma independiente, lo que significa que se puede escalar un área funcional específica que requiere más procesamiento alimentación eléctrica o red de ancho de banda para admitir la demanda, sin ajuste de escala innecesariamente en áreas de la aplicación que no experimentan una mayor demanda.

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[Autenticación y autorización](authentication-and-authorization.md)

Existen muchos enfoques a la integración de autenticación y autorización en una aplicación de Xamarin.Forms que se comunica con una aplicación web de ASP.NET MVC. En este caso, la autenticación y autorización se realizan con un microservicio de identidad en contenedores que usa IdentityServer 4. IdentityServer es un marco de trabajo de código abierto OAuth 2.0 y OpenID Connect de ASP.NET Core que se integra con la identidad de núcleo de ASP.NET para realizar la autenticación de token de portador.

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[Acceso a datos remotos](accessing-remote-data.md)

Muchas soluciones modernas basadas en web hacen uso de servicios web, hospedadas por servidores web, para proporcionar funcionalidad de cliente remoto a aplicaciones. Las operaciones que expone un servicio web constituyen una API web y aplicaciones de cliente deben ser capaces de usar la API web sin conocer cómo se implementan las operaciones que expone la API o datos.

## <a name="unit-testingunit-testingmd"></a>[Pruebas unitarias](unit-testing.md)

Probar modelos y ver modelos desde aplicaciones de MVVM es idéntico a otras clases de las pruebas, y pueden utilizarse las mismas herramientas y técnicas. Sin embargo, hay determinados patrones que son típicos al modelo y clases de modelo de vista, que se pueden beneficiar de técnicas de pruebas de unidad específica.

## <a name="feedback"></a>Comentarios

Este proyecto es un sitio de la Comunidad, en el que puede publicar preguntas y proporcionar comentarios. El sitio de la Comunidad se encuentra en [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). Como alternativa, pueden recibir un mensaje comentarios acerca de los libros electrónicos a [ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com).


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
