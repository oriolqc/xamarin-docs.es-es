---
title: Aspectos básicos de la aplicación de Xamarin.Forms
description: Exploramos los aspectos básicos del desarrollo de aplicaciones de Xamarin.Forms, incluidos todos los conceptos principales necesarios, a través de toques finales, como la localización y accesibilidad.
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2018
ms.openlocfilehash: 015a7dfa3d55b411e6626ae2ac70fd2930661e65
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898997"
---
# <a name="xamarinforms-application-fundamentals"></a>Aspectos básicos de la aplicación de Xamarin.Forms

## <a name="accessibilityaccessibilityindexmd"></a>[Accesibilidad](accessibility/index.md)

Sugerencias para incorporar características accesibles (como por ejemplo, la compatibilidad con herramientas de lectura de pantalla) con Xamarin.Forms.

## <a name="app-classapplication-classmd"></a>[Clase de aplicación](application-class.md)

La clase `Application` es el punto de partida para Xamarin.Forms: todas las aplicaciones necesitan implementar una subclase `App` para establecer la página inicial. También proporciona la recopilación `Properties` para el almacenamiento de datos sencillo. Se puede definir en C# o XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Ciclo de vida de la aplicación](app-lifecycle.md)

La clase `Application` y los métodos `OnStart`, `OnSleep` y `OnResume`, así como los eventos de navegación modal, permiten controlar los eventos del ciclo de vida de la aplicación con código personalizado.

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

## <a name="filesfilesmd"></a>[Archivos](files.md)

El control de archivos con Xamarin.Forms puede lograrse mediante código en una biblioteca .NET Standard o usando recursos insertados.

## <a name="gesturesgesturesindexmd"></a>[Gestos](gestures/index.md)

La clase [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) de Xamarin.Forms admite los gestos de pulsar, reducir y desplazar lateralmente en controles de interfaz de usuario.

## <a name="localizationlocalizationindexmd"></a>[Localización](localization/index.md)

El marco de trabajo de localización integrado de .NET puede usarse para generar aplicaciones multilingües multiplataforma con Xamarin.Forms.

## <a name="local-databasesdatabasesmd"></a>[Bases de datos locales](databases.md)

Xamarin.Forms admite aplicaciones de base de datos que usan el motor de base de datos de SQLite, lo que permite cargar y guardar los objetos en código compartido.

## <a name="messaging-centermessaging-centermd"></a>[Centro de mensajería](messaging-center.md)

`MessagingCenter` de Xamarin.Forms permite que los modelos de vista y otros componentes se comuniquen sin tener que saber nada sobre los demás, salvo un sencillo contrato de mensajería.

## <a name="navigationnavigationindexmd"></a>[Navegación](navigation/index.md)

Xamarin.Forms proporciona una serie de experiencias de navegación de páginas diferente, en función del tipo de `Page` que se use.

## <a name="shellshellmd"></a>[Shell](shell.md)

Xamarin.Forms Shell es un contenedor para aplicaciones, que proporciona características fundamentales de la interfaz de usuario que requieren la mayoría de las aplicaciones, lo que le permite centrarse en la carga de trabajo principal de la aplicación.

## <a name="templatestemplatesindexmd"></a>[Templates](templates/index.md) (Plantillas [C++])

Las plantillas de control proporcionan la capacidad de aplicar temas y cambiar el tema de las páginas de aplicación fácilmente en tiempo de ejecución, mientras que las plantillas de datos proporcionan la capacidad de definir la presentación de datos en los controles admitidos.

## <a name="triggerstriggersmd"></a>[Desencadenadores](triggers.md)

Actualice los controles respondiendo a los cambios de propiedades y eventos en XAML.


## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
