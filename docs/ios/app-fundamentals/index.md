---
title: Principios de la aplicación de Xamarin.iOS
description: Este documento incluye vínculos a diversas guías que describen los conceptos fundamentales para el desarrollo de Xamarin.iOS, como la seguridad de transporte de aplicación, procesamiento en segundo plano, los eventos y subprocesamiento.
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/21/2017
ms.openlocfilehash: a40227454b597578ff1c1c247b326e523c23493b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110477"
---
# <a name="xamarinios-application-fundamentals"></a>Principios de la aplicación de Xamarin.iOS

En esta sección se proporciona a una guía sobre algunas de las tareas de las cosas o conceptos que los desarrolladores deben tener en cuenta al desarrollar aplicaciones de Xamarin.iOS (anteriormente MonoTouch) más comunes.

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[Accesibilidad](~/ios/app-fundamentals/accessibility.md)

Este documento describe las diversas API y herramientas que pueden usarse para ayudar a crear aplicaciones que son accesibles para tantos usuarios como sea posible.

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)

Este artículo presentan los cambios de seguridad que exige la seguridad de transporte de la aplicación en una aplicación de iOS 9 y qué significa esto para los proyectos de Xamarin.iOS, tratará las opciones de configuración de ATS y explica cómo participar de ATS, si es necesario. Como ATS está habilitado de forma predeterminada, las conexiones no seguras de internet producirán una excepción en aplicaciones de iOS 9 (a menos que haya permitido explícitamente que).

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)

En segundo plano de procesamiento o de procesamiento en segundo plano es el proceso de permitir a las aplicaciones realizar tareas en segundo plano mientras se está ejecutando otra aplicación en primer plano. Esta guía sirve como introducción al procesamiento de iOS en segundo plano.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Creación de aplicaciones de iOS en código](~/ios/app-fundamentals/ios-code-only.md)

En este artículo se explica cómo crear aplicaciones de iOS completamente en código con Visual Studio y Visual Studio para Mac. Se muestra cómo empezar a partir de una plantilla de proyecto vacía para crear una pantalla de aplicación en un controlador mediante la creación de una jerarquía de vistas de UIKit. Luego se explica cómo crear vistas personalizadas que se pueden cargar en un controlador.

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Eventos, protocolos y delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)

Este artículo presentan las tecnologías clave iOS usa para recibir devoluciones de llamada y para rellenar los controles de interfaz de usuario con datos. Estas tecnologías son eventos, protocolos y delegados; en este artículo se explica lo que cada uno de ellos es y cómo cada uno se utiliza desde C#. Muestra cómo Xamarin.iOS utiliza controles de iOS para exponer familiar .NET eventos, así como la forma Xamarin.iOS proporciona compatibilidad con los conceptos de Objective-C como los protocolos y delegados (delegados de Objective-C no debe confundirse con los delegados de C#). En este artículo también proporciona ejemplos que muestran cómo protocolos se utilizan como base para los delegados de Objective-C y en escenarios no delegado.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Trabajar con el sistema de archivos](~/ios/app-fundamentals/file-system.md)

Xamarin.iOS puede usar las mismas clases de System.IO para trabajar con archivos y directorios de iOS que usaría en cualquier aplicación. NET. Sin embargo, a pesar de las conocidas clases y métodos, iOS implementa algunas restricciones en los archivos que se pueden crear o tener acceso a y también proporciona características especiales para ciertos directorios. En este artículo se describe estas restricciones y características y se muestra cómo funciona el acceso de archivo en una aplicación de Xamarin.iOS.

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md)

En este artículo se examina cómo usar imágenes en Xamarin.iOS, imágenes de soporte técnico de la aplicación (por ejemplo, iconos, al cargar imágenes, etc.) y las imágenes dentro de las aplicaciones (como las imágenes que se aplica a los controles). También se describe cómo usar Visual Studio para Mac para incorporar imágenes, así como cómo interactuar con las imágenes desde el código.

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localización](~/ios/app-fundamentals/localization/index.md)

Esta guía explica la adición de codificaciones a una aplicación de Xamarin.iOS para permitir la internacionalización.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Trabajar con listas de propiedades](~/ios/app-fundamentals/index.md)

Este documento presenta Visual Studio para el editor de lista (.plist) de propiedad gráfica y avanzados de Mac para trabajar con Info.plist y Entitlements.plist. Muestran iconos de la configuración y las imágenes de aplicación de iOS de inicio y muestra cómo especificar las funcionalidades de aplicaciones (derechos) de Visual Studio para Mac.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Trabajar con la seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md)

Apple ha realizado varias mejoras en seguridad y privacidad en iOS 10 (y versiones posteriores) que le ayudarán a los desarrolladores a mejorar la seguridad de sus aplicaciones y garantizar la privacidad del usuario final. En este artículo se tratará la implementación de estas características en una aplicación de Xamarin.iOS.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Subprocesamiento](~/ios/app-fundamentals/threading.md)

En este artículo se describe los subprocesos en una aplicación de Xamarin.iOS y trata un poco sobre el grupo de subprocesos. NET, aplicaciones con capacidad de respuesta y recolección de elementos.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Entrada táctil](~/ios/app-fundamentals/touch/index.md)

Pantallas táctiles en muchos de los dispositivos actuales permiten a los usuarios interactuar rápida y eficaz con dispositivos de forma natural e intuitiva. Esta interacción no se limita solo a la detección simple toque: es posible usar gestos también. Por ejemplo, el gesto pinch a zoom es un ejemplo muy común de esta, empujando una parte de la pantalla con dos dedos, que el usuario puede acercar o alejar. Esta guía examina tacto y gestos en iOS.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Trabajar con valores predeterminados del usuario](~/ios/app-fundamentals/user-defaults.md)

La `NSUserDefaults` clase proporciona una manera para iOS, aplicaciones y extensiones para interactuar mediante programación con el sistema predeterminado de todo el sistema. Mediante el sistema tiene como valor predeterminado, el usuario puede configurar el comportamiento de una aplicación o aplicar estilos acordes con sus preferencias (según el diseño de la aplicación). Por ejemplo, para presentar los datos de métrica vs Imperial medidas o seleccionar un tema de la interfaz de usuario determinado.
