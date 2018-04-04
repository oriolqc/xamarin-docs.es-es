---
title: Características de la plataforma de Windows
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: ab6b12738028b4f3439629f334ed5429244f4d5a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="windows-platform-features"></a>Características de la plataforma de Windows

Desarrollo de aplicaciones de Xamarin.Forms para plataformas de Windows, requiere Visual Studio. El [página requisitos](~/xamarin-forms/get-started/installation.md) contiene más información sobre los requisitos previos.

![](images/allhanselman.png "Aplicaciones de Xamarin.Forms que se ejecutan en Windows")

## <a name="platform-support"></a>Compatibilidad de la plataforma

Las plantillas de Xamarin.Forms disponibles en Visual Studio contienen un proyecto de Windows de forma predeterminada:

* **Aplicaciones de la plataforma de Windows universal** -también se pueden optimizar las aplicaciones de Xamarin.Forms para Windows 10. Pueden ejecutar las aplicaciones universales (UWP) en teléfonos, Tablet PC y dispositivos de escritorio.

Si ha instalado las opciones de desarrollo correcto en Visual Studio, también es posible [agregar](installation/index.md) estos tipos para compatibilidad con versiones anteriores de Windows de proyectos:

* **Windows 8.1** : puede implementar aplicaciones de Xamarin.Forms para Tablet PC y usar controles de WinRT de proyectos de escritorio factores de forma que una aplicación de Windows 8.1.
* **Windows Phone 8.1** -Xamarin.Forms es totalmente compatible con la plataforma de Windows Phone 8.1 mediante WinRT. La apariencia y funcionamiento de las aplicaciones con el soporte técnico de Windows Phone 8.1 pueden ser diferente a las aplicaciones de Windows Phone Xamarin.Forms anteriores que se basaban en Silverlight.


> [!NOTE]
> Compatibilidad con 1.x y 2.x de Xamarin.Forms _Silverlight de Windows Phone 8_ desarrollo de aplicaciones, sin embargo está en desuso este tipo de proyecto.


## <a name="getting-started"></a>Introducción

Vaya a **archivo > Nuevo > proyecto** en Visual Studio y elija uno de los **multiplataforma > aplicación vacía (Xamarin.Forms)** plantillas para empezar a trabajar.

Las soluciones anteriores de Xamarin.Forms, o las creadas en macOS, no tendrá todos los proyectos de Windows mencionados anteriormente (pero deben agregarse manualmente).
Si desea tener como destino la plataforma de Windows no se encuentra en la solución, visite la [instrucciones de instalación](installation/index.md) para agregar el tipo/s de proyectos de las ventanas deseadas.


## <a name="samples"></a>Muestras

[Todas las muestras](https://github.com/xamarin/xamarin-forms-book-preview-2) de libro de Petzold [ *crear aplicaciones móviles con Xamarin.Forms* ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) incluir los proyectos de plataforma Universal de Windows (para Windows 10), Windows 8.1 y Windows Phone 8.1.

El ["Scott Hanselman" demo aplicación](https://github.com/jamesmontemagno/Hanselman.Forms) está disponible por separado y también incluye proyectos de Apple Watch y se ocupa de Android (con Xamarin.iOS y Xamarin.Android respectivamente, Xamarin.Forms no se ejecuta en estas plataformas).


## <a name="related-links"></a>Vínculos relacionados

- [Proyectos de instalación de Windows](~/xamarin-forms/platform/windows/installation/index.md)
