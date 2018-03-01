---
title: "Principios de la aplicación"
description: Conceptos de las aplicaciones principales
ms.topic: article
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 9e4e7705e1ca29b6abf716a48ae3fa0e7c1a19ec
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="application-fundamentals"></a>Principios de la aplicación

Esta sección proporciona a una guía sobre algunas de las tareas de cosas o conceptos que los desarrolladores deben tener en cuenta al desarrollar aplicaciones móviles más comunes.

##  <a name="building-cross-platform-applicationscross-platformapp-fundamentalsbuilding-cross-platform-applicationsindexmd"></a>[Compilar entre aplicaciones de la plataforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)

Eligiendo Xamarin y mantener algunas cosas en cuenta al diseñar y desarrollar las aplicaciones móviles, puede obtener código enorme compartir código entre plataformas móviles, reducir el tiempo de comercialización, aprovechar talent existente, satisfacer la demanda de cliente para el acceso móvil, y reducir la complejidad de multiplataforma. &nbsp;Este documento describen las instrucciones clave para observar las siguientes ventajas para las aplicaciones de utilidad y la productividad.

## <a name="code-sharing-optionscode-sharingmd"></a>[Opciones de uso compartido de código](code-sharing.md)

Obtenga información acerca del código de diferentes opciones disponibles para los proyectos de Xamarin, incluidas las bibliotecas de clases portables (PCLs), proyectos compartidos y bibliotecas estándar de .NET de uso compartido.


## <a name="accessibilityaccessibilitymd"></a>[Accesibilidad](accessibility.md)

Sugerencias para la creación de aplicaciones accesibles.


## <a name="localizationlocalizationmd"></a>[Localización](localization.md)

Directrices para hacer que las aplicaciones basadas en la configuración regional que se pueden traducir a varios idiomas.


##  <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[Bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md)

Proyectos de biblioteca de clases Portable le permiten crear y distribuir ensamblados que contienen código compartido que se ejecuta en varias plataformas. Para crear una biblioteca de clases Portable (o "PCL") selecciona qué plataformas de destino y, después, escribir el código en un subjuego de .NET Framework que está disponible en el perfil definido para esas plataformas. Este documento describe cómo crear y usar PCLs con Xamarin.

##  <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)

Los proyectos compartidos le permiten escribir el código común que se hace referencia mediante un número de proyectos de aplicación diferente. El código se compila como parte de cada proyecto que hace referencia y puede incluir directivas de compilador para ayudar a incorporar la funcionalidad específica de la plataforma en la base de código compartido. Este artículo describe cómo funcionan los proyectos compartidos y cómo crearlos y utilizarlos con los proyectos de Xamarin.

##  <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

Estándar de .NET es una opción nueva para compartir código entre plataformas. Funciona de forma similar a las bibliotecas de clases portables; código se basa en una versión específica (actualmente 1.0 a 1.6) y, a continuación, puede ser utilizado por otros proyectos que admitan ese nivel o superior. Proyectos de .NET estándar son compatibles con Xamarin Studio 6.2, Visual Studio para Windows y Visual Studio para Mac.

##  <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[Proyectos de NuGet: Bibliotecas multiplataforma para uso compartido de código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

Se pueden generar automáticamente los paquetes de NuGet de PCL o .NET proyectos estándares; y proyectos compartidos pueden empaquetarse en paquetes de NuGet "gancho" con el tipo de proyecto de NuGet independiente. Esta sección explica cómo crear paquetes de NuGet para cada escenario de uso compartido de código.

##  <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[Creación manual de paquetes de NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Sugerencias para crear paquetes de NuGet que funcionan con la plataforma de Xamarin.

##  <a name="cross-platform-data-accessxamarin-formsdata-cloudindexmd"></a>[Acceso de datos de plataforma entre](~/xamarin-forms/data-cloud/index.md)

Mayoría de las aplicaciones tiene algún requisito para guardar los datos en el dispositivo de forma local. A menos que la cantidad de datos es pequeña trivial, esto normalmente requiere una base de datos y una capa de datos en la aplicación para administrar el acceso de la base de datos. iOS y Android que el motor de base de datos de SQLite "integrado" y acceso para almacenar y recuperar los datos se simplifica con la plataforma de Xamarin. El [acceso a datos Android](~/android/data-cloud/data-access/index.md), [acceso a datos de iOS](~/ios/data-cloud/data/index.md), y [Xamarin.Forms el acceso a datos](~/xamarin-forms/data-cloud/index.md) guías proporcionan ejemplos de cómo obtener acceso a código en cada plataforma.


##  <a name="transport-layer-securitytransport-layer-securitymd"></a>[Seguridad de la capa de transporte](transport-layer-security.md)

Información sobre la correcta implementación de SSL/TLS selectingthe para asegurar la conectividad de red de la aplicación.


##  <a name="notificationsxamarin-formsdata-cloudpush-notificationsindexmd"></a>[Notificaciones](~/xamarin-forms/data-cloud/push-notifications/index.md)

Aplicaciones móviles de utilizan notificaciones como una forma discreta de informar al usuario que se ha producido algún evento específico de la aplicación. Las notificaciones se utilizan normalmente para notificar al usuario del estado de un proceso de aplicación que se ejecuta en segundo plano. Un ejemplo de esto podría estar descargando un archivo grande. Este archivo puede tardar mucho tiempo en descargar, por lo que debe producir esta actividad en segundo plano. Una vez completada la descarga, se informa al usuario del hecho mediante una notificación.
Además, notificación áreas no limita a aplicaciones locales. También es posible que las aplicaciones de servidor publicar notificaciones a las aplicaciones móviles. Este artículo tratará sobre cómo utilizar las notificaciones en iOS y Android.
