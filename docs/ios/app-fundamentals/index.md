---
title: "Principios de la aplicación"
description: Conceptos de las aplicaciones principales
ms.topic: article
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/21/2017
ms.openlocfilehash: f6c0bb75327f0c14d314a0e7ad9d114fd17c2a57
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="application-fundamentals"></a>Principios de la aplicación

Esta sección proporciona a una guía sobre algunas de las tareas de cosas o conceptos que los desarrolladores deben tener en cuenta al desarrollar aplicaciones de Xamarin.iOS (anteriormente MonoTouch) más comunes.

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[Seguridad de transporte de aplicación](~/ios/app-fundamentals/ats.md)

En este artículo se presentan los cambios de seguridad que exige la seguridad de transporte de la aplicación en una aplicación de iOS 9 y qué significa esto para los proyectos de Xamarin.iOS, tratará las opciones de configuración de ATS y explicará cómo desactivación de ATS, si es necesario. Como ATS está habilitado de forma predeterminada, las conexiones de internet no seguras generará una excepción en aplicaciones de iOS 9 (a menos que haya permitido explícitamente lo).


## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)

Fondo de procesamiento o backgrounding es el proceso de permitir que las aplicaciones realizar tareas en segundo plano mientras se está ejecutando otra aplicación en primer plano. Esta guía sirve como una introducción al procesamiento de iOS en segundo plano.


## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Delegados, eventos y protocolos](~/ios/app-fundamentals/delegates-protocols-and-events.md)

En este artículo se presenta la tecnología de iOS clave usada para recibir devoluciones de llamada y para rellenar los controles de interfaz de usuario con datos. Estas tecnologías son eventos, los protocolos y los delegados; Este artículo explica lo que cada uno de ellos es y cómo se usa cada uno en C#. Muestra cómo Xamarin.iOS usa controles de iOS para exponer familiarizado .NET eventos, así como la Xamarin.iOS proporciona compatibilidad para Objective-C conceptos como los protocolos y los delegados (delegados Objective-C no deben confundirse con los delegados de C#). En este artículo también proporciona ejemplos que muestran cómo se utilizan los protocolos tanto como base, para los delegados de Objective-C y en escenarios no delegado.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Subprocesamiento](~/ios/app-fundamentals/threading.md)

En este artículo se describe el subprocesamiento en una aplicación de Xamarin.iOS y habla de un poco el el grupo de subprocesos. NET, aplicaciones con capacidad de respuesta y recolección de elementos.&nbsp;

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Working with Images (Trabajo con imágenes)](~/ios/app-fundamentals/images-icons/index.md)

Este artículo examina cómo utilizar imágenes en Xamarin.iOS, imágenes de soporte técnico de la aplicación (por ejemplo, iconos, cargar imágenes, etc.) y las imágenes dentro de las aplicaciones (por ejemplo, aplicadas a los controles de imágenes). También explica cómo utilizar Visual Studio para Mac para incorporar imágenes, así como cómo interactuar con imágenes desde el código.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Trabajar con listas de propiedades](~/ios/app-fundamentals/index.md)

Este documento presentan Visual Studio para el editor de lista (.plist) de propiedad gráfica y avanzados de Mac para trabajar con Info.plist y Entitlements.plist. Ilustra los iconos de configuración y las imágenes de aplicación de iOS de inicio y muestra cómo especificar las capacidades de aplicaciones (derechos) de Visual Studio para Mac.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Working with the File System (Trabajo con el sistema de archivos)](~/ios/app-fundamentals/file-system.md)

Xamarin.iOS puede utilizar las mismas clases System.IO para trabajar con directorios y archivos de iOS que le gustaría usar en cualquier aplicación. NET. Sin embargo, a pesar de las conocidas clases y métodos, iOS implementa algunas restricciones en los archivos que se pueden crear o acceder y también proporciona características especiales para determinados directorios. En este artículo se describe estas restricciones y características y se muestra cómo funciona el acceso a los archivos en una aplicación de Xamarin.iOS.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Crear aplicaciones de iOS en código](~/ios/app-fundamentals/ios-code-only.md)

Este artículo examina cómo crear aplicaciones de iOS completamente en código usando Visual Studio y Visual Studio para Mac. Se muestra cómo empezar a partir de una plantilla de proyecto vacía para crear una pantalla de aplicación en un controlador mediante la creación de una jerarquía de vistas de UIKit. Luego se explica cómo crear vistas personalizadas que se pueden cargar en un controlador.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Trabajar con valores predeterminados del usuario](~/ios/app-fundamentals/user-defaults.md)

La `NSUserDefaults` clase proporciona una manera de iOS aplicaciones y extensiones para interactuar mediante programación con el sistema predeterminado de todo el sistema. Mediante el sistema tiene como valor predeterminado, el usuario puede configurar el comportamiento de una aplicación o aplicar un estilo para satisfacer sus preferencias (en función del diseño de la aplicación). Por ejemplo, para presentar datos en vs métricas Imperial medidas o seleccionar un tema de la interfaz de usuario determinada.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Entrada táctil](~/ios/app-fundamentals/touch/index.md)

Pantallas táctiles en muchos de los dispositivos actuales permiten a los usuarios interactuar rápida y eficaz con dispositivos de forma natural e intuitiva. Esta interacción no se limita solo a la detección simple toque: es posible usar los movimientos así. Por ejemplo, el movimiento de gesto de acercamiento es un ejemplo muy común de esto: empujando una parte de la pantalla con dos dedos que el usuario puede acercar o alejar. Esta guía examina táctiles y los gestos de iOS.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Trabajar con la seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md)

Apple ha realizado varias mejoras en la seguridad y privacidad en 10 (y versiones posteriores) de iOS que le ayudará a los desarrolladores mejorar la seguridad de sus aplicaciones y garantizar la privacidad del usuario final. En este artículo se tratará la implementación de estas características en una aplicación de Xamarin.iOS.

##  <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localización](~/ios/app-fundamentals/localization/index.md)

Esta guía se describe el la adición de codificaciones a una aplicación Xamarin.iOS para permitir la internacionalización.