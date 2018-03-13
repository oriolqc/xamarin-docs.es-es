---
title: "Resumen de capítulo 9. Llamadas a la API específica de la plataforma"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 84650c930445172d27520129123d493253851642
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Resumen de capítulo 9. Llamadas a la API específica de la plataforma

A veces es necesario ejecutar el código que varía según la plataforma. En este capítulo se explora las técnicas.

## <a name="preprocessing-in-the-shared-asset-project"></a>Preprocesamiento en el proyecto activo compartido

Un proyecto de recurso compartido de Xamarin.Forms pueden ejecutar código diferente para cada plataforma que use las directivas de preprocesador de C# `#if`, `#elif`, y `endif`. Esto se demuestra en [ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Captura de pantalla triple de variable con el formato párrafo](images/ch09fg01-small.png "modelo del dispositivo y el sistema operativo")](images/ch09fg01-large.png#lightbox "modelo del dispositivo y el sistema operativo")

Sin embargo, el código resultante puede ser desagradables y difícil de leer.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Clases paralelas en el proyecto de recurso compartido

Un enfoque más estructurado para la ejecución de código específico de plataforma en el protocolo de SAP se muestra en el [ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) ejemplo. Cada uno de los proyectos de plataforma contiene una clase con el mismo nombre y los métodos, pero se implementa para esa plataforma en particular. El protocolo de SAP, a continuación, simplemente crea una instancia de la clase y llama al método.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService y la biblioteca de clases Portable

Una biblioteca normalmente no puede obtener acceso a clases en proyectos de aplicaciones. Esta restricción se parece a evitar la técnica que se muestra en **PlatInfoSap2** desde que se va a usar en una PCL. Sin embargo, Xamarin.Forms contiene una clase denominada [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) que usa la reflexión de .NET para tener acceso a las clases públicas en el proyecto de aplicación desde la PCL.

Debe definir la PCL un `interface` con los miembros que se debe usar en cada plataforma. A continuación, cada una de las plataformas contiene una implementación de dicha interfaz. La clase que implementa la interfaz debe identificarse con un [DependencyAttribute](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyAttribute/) en el nivel de ensamblado.

La PCL, a continuación, utiliza la interfaz genérica [ `Get` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DependencyService.Get{T}/p/Xamarin.Forms.DependencyFetchTarget/) método `DependencyService` para obtener una instancia de la clase de plataforma que implementa la interfaz.

Esto se muestra en el [ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) ejemplo.

## <a name="platform-specific-sound-generation"></a>Generación de sonido específica de la plataforma

El [ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) ejemplo agrega pitidos a la **MonkeyTap** programa mediante el acceso a los servicios de generación de sonido en cada plataforma.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 9 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Ejemplos de capítulo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Servicio de dependencia](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
