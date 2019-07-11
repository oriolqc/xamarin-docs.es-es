---
title: Aspectos básicos de la aplicación de Xamarin.Forms
description: Exploramos los aspectos básicos del desarrollo de aplicaciones de Xamarin.Forms, incluidos todos los conceptos principales necesarios, a través de toques finales, como la localización y accesibilidad.
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: 61d9c5b1c7b81bfc8a20932ce1d188cd0cb0f980
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650440"
---
# <a name="xamarinforms-application-fundamentals"></a>Aspectos básicos de la aplicación de Xamarin.Forms

## <a name="accessibilityaccessibilityindexmd"></a>[Accesibilidad](accessibility/index.md)

Sugerencias para incorporar características accesibles (como por ejemplo, la compatibilidad con herramientas de lectura de pantalla) con Xamarin.Forms.

## <a name="app-classapplication-classmd"></a>[Clase de aplicación](application-class.md)

La clase `Application` es el punto de partida para Xamarin.Forms: todas las aplicaciones necesitan implementar una subclase `App` para establecer la página inicial. También proporciona la recopilación `Properties` para el almacenamiento sencillo de datos. Se puede definir en C# o XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Ciclo de vida de la aplicación](app-lifecycle.md)

La clase `Application` y los métodos `OnStart`, `OnSleep` y `OnResume`, así como los eventos de navegación modal, permiten controlar los eventos del ciclo de vida de la aplicación con código personalizado.

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[Indexación de la aplicación y vinculación en profundidad](deep-linking.md)

La indexación de la aplicación permite a aplicaciones que de lo contrario se olvidarían tras unos pocos usos mantener su pertinencia al aparecer en los resultados de la búsqueda. La vinculación en profundidad permite a las aplicaciones responder a un resultado de la búsqueda que contiene datos de aplicación, normalmente al navegar a una página a la que se hace referencia a partir de un vínculo profundo.

## <a name="behaviorsbehaviorsindexmd"></a>[Comportamientos](behaviors/index.md)

Los controles de la interfaz de usuario se pueden extender fácilmente sin incluir subclases mediante comportamientos para agregar funcionalidad.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Representadores personalizados](custom-renderer/index.md)

Los representadores personalizados permiten a los desarrolladores reemplazar la representación predeterminada de los controles de Xamarin.Forms para personalizar su apariencia y comportamiento en cada plataforma (usando SDK nativos si lo prefiere).

## <a name="data-bindingdata-bindingindexmd"></a>[Enlace de datos](data-binding/index.md)

El enlace de datos vincula las propiedades de dos objetos para que los cambios en una propiedad se reflejen automáticamente en la otra propiedad. El enlace de datos es una parte integral de la arquitectura de aplicación Model-View-ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)).

## <a name="dependency-servicedependency-serviceindexmd"></a>[Servicio de dependencia](dependency-service/index.md)

`DependencyService` proporciona un localizador simple para que pueda incluir código para interfaces en el código compartido y proporcionar implementaciones específicas de la plataforma que se resuelvan automáticamente, lo que facilita la referencia a funcionalidades específicas de la plataforma en Xamarin.Forms.

## <a name="effectseffectsindexmd"></a>[Efectos](effects/index.md)

Con los efectos se pueden personalizar los controles nativos de cada plataforma y normalmente se usan para pequeños cambios de estilo.

## <a name="gesturesgesturesindexmd"></a>[Gestos](gestures/index.md)

La clase [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) de Xamarin.Forms admite los gestos de pulsar, reducir y desplazar lateralmente en controles de interfaz de usuario.

## <a name="localizationlocalizationindexmd"></a>[Localización](localization/index.md)

El marco de trabajo de localización integrado de .NET puede usarse para generar aplicaciones multilingües multiplataforma con Xamarin.Forms.

## <a name="messaging-centermessaging-centermd"></a>[Centro de mensajería](messaging-center.md)

`MessagingCenter` de Xamarin.Forms permite que los modelos de vista y otros componentes se comuniquen sin tener que saber nada sobre los demás, salvo un sencillo contrato de mensajería.

## <a name="navigationnavigationindexmd"></a>[Navegación](navigation/index.md)

Xamarin.Forms proporciona una serie de experiencias de navegación de páginas diferentes, en función del tipo de `Page` que se use.

## <a name="shellshellindexmd"></a>[Shell](shell/index.md)

Xamarin.Forms Shell reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de aplicaciones móviles. Esto incluye una experiencia de usuario de navegación común, un esquema de navegación basado en URI y un controlador de búsqueda integrada.

## <a name="templatestemplatesindexmd"></a>[Templates](templates/index.md) (Plantillas [C++])

Las plantillas de control proporcionan la capacidad de aplicar temas y cambiar el tema de las páginas de aplicación fácilmente en tiempo de ejecución, mientras que las plantillas de datos proporcionan la capacidad de definir la presentación de datos en los controles admitidos.

## <a name="triggerstriggersmd"></a>[Desencadenadores](triggers.md)

Actualice los controles respondiendo a los cambios de propiedades y eventos en XAML.
