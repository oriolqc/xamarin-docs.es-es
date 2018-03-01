---
title: "Características de la plataforma Android"
description: "Agregar funcionalidad específica de Android a aplicaciones de Xamarin.Forms"
ms.topic: article
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2016
ms.openlocfilehash: d68d84671028ded14b4b885f2c134656fc639f9e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="android-platform-features"></a>Características de la plataforma Android

## <a name="platform-support"></a>Compatibilidad de la plataforma

El valor predeterminado de proyecto de Xamarin.Forms Android utiliza un estilo anterior de renderering de control que era habitual antes de Android 5.0. Las aplicaciones compiladas con la plantilla tienen `FormsApplicationActivity` como la clase base de su actividad principal.

## <a name="material-design-via-appcompat"></a>Diseño material a través de AppCompat

Xamarin.Forms también tiene una función opcional `FormsAppCompatActivity` que utiliza **AppCompat** características proporcionadas por Android para implementar los temas de diseño de Material.

Para agregar temas de diseño de Material para el proyecto de Xamarin.Forms Android, siga el [admiten instrucciones de instalación para AppCompat](appcompat.md)

Este es el **tareas** ejemplo con el valor predeterminado `FormsApplicationActivity`:

[ ![](images/before-appcompat-sml.png "Aplicación de ejemplo de lista de tareas sin AppCompat")](images/before-appcompat.png "aplicación de ejemplo de lista de tareas sin AppCompat")

Y este es el mismo código después de actualizar el proyecto para usar `FormsAppCompatActivity` (y agregar la información del tema adicionales):

[ ![](images/post-appcompat-sml.png "Aplicación de ejemplo de lista de tareas con AppCompat y temas")](images/post-appcompat.png "aplicación de ejemplo de lista de tareas con AppCompat y temas")

> [!NOTE]
> **Tenga en cuenta**: al usar `FormsAppCompatActivity`, el [clases base para algunos representadores personalizados Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) será diferente.


## <a name="related-links"></a>Vínculos relacionados

- [Agregar compatibilidad con Material de diseño](appcompat.md)
