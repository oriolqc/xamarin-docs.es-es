---
title: Resumen del capítulo 9. Llamadas a API específicas de la plataforma
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen del capítulo 9. Llamadas a API específicas de la plataforma'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 3aec84ec6598a45bb989d4bbc1705fd797382755
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057014"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Resumen del capítulo 9. Llamadas a API específicas de la plataforma

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

A veces es necesario ejecutar el código que varía según la plataforma. Este capítulo explora las técnicas.

## <a name="preprocessing-in-the-shared-asset-project"></a>Preprocesamiento en el proyecto de recurso compartido

Un proyecto de recurso compartido de Xamarin.Forms pueden ejecutar código diferente para cada plataforma mediante las directivas de preprocesador de C# `#if`, `#elif`, y `endif`. Esto se demuestra en [ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Captura de pantalla triple de variable con formato de párrafo](images/ch09fg01-small.png "modelo del dispositivo y el sistema operativo")](images/ch09fg01-large.png#lightbox "modelo del dispositivo y el sistema operativo")

Sin embargo, el código resultante puede ser poco atractivo y difícil de leer.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Clases paralelas en el proyecto de activos compartidos

Un enfoque más estructurado para la ejecución de código específico de plataforma en SAP se muestra en el [ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) ejemplo. Cada uno de los proyectos de plataforma contiene una clase con el mismo nombre y los métodos, pero se implementa para esa plataforma determinada. SAP, a continuación, simplemente crea una instancia de la clase y llama al método.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService y la biblioteca de clases Portable

> [!NOTE] 
> Bibliotecas de clases portables se han reemplazado por las bibliotecas de .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas de .NET estándares.

Una biblioteca normalmente no puede acceder a las clases de proyectos de aplicación. Esta restricción se parece a evitar que la técnica mostrada en **PlatInfoSap2** desde que se va a usar en una biblioteca. Sin embargo, Xamarin.Forms contiene una clase denominada [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) que usa la reflexión de .NET para tener acceso a las clases públicas en el proyecto de aplicación de la biblioteca.

La biblioteca debe definir un `interface` con los miembros que debe usar en cada plataforma. A continuación, cada una de las plataformas contiene una implementación de dicha interfaz. La clase que implementa la interfaz debe identificarse con un [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) en el nivel de ensamblado.

La biblioteca, a continuación, utiliza el modelo genérico [ `Get` ](xref:Xamarin.Forms.DependencyService.Get*) método `DependencyService` para obtener una instancia de la clase de la plataforma que implementa la interfaz.

Esto se muestra en el [ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) ejemplo.

## <a name="platform-specific-sound-generation"></a>Generación de sonido específica de la plataforma

El [ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) ejemplo agrega sonidos para el **MonkeyTap** programa mediante el acceso a las instalaciones de generación de sonido en cada plataforma.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 9 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Ejemplos del capítulo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Servicio de dependencia](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
