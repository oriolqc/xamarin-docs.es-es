---
title: Efectos
description: Interfaces de usuario de Xamarin.Forms se representan mediante los controles nativos de la plataforma de destino, permita que las aplicaciones de Xamarin.Forms conservar la apariencia y funcionamiento adecuada para cada plataforma. Efectos de permitir que los controles nativos en cada plataforma para personalizarse sin tener que recurrir a la implementación de un representador personalizado.
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 63d7750294321a28dbde833f72fe6b7277ada397
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="effects"></a>Efectos

_Interfaces de usuario de Xamarin.Forms se representan mediante los controles nativos de la plataforma de destino, permita que las aplicaciones de Xamarin.Forms conservar la apariencia y funcionamiento adecuada para cada plataforma. Efectos de permitir que los controles nativos en cada plataforma para personalizarse sin tener que recurrir a la implementación de un representador personalizado._

## <a name="introduction-to-effectsintroductionmd"></a>[Introducción a los efectos](introduction.md)

Efectos de permitir que los controles nativos en cada plataforma para personalizarse y se utilizan normalmente para los cambios de estilo pequeño. Este artículo proporciona una introducción a los efectos, se describen el límite entre los representadores personalizados y efectos y describe el `PlatformEffect` clase.

## <a name="creating-an-effectcreatingmd"></a>[Crear un efecto](creating.md)

Efectos simplifican la personalización de un control. En este artículo se muestra cómo crear un efecto que cambia el color de fondo de la [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar cuando el control recibe el foco.

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[Pasar parámetros a un efecto](passing-parameters/index.md)

Crear un efecto que se configura a través de parámetros permite que el efecto reutilizar. Estos artículos muestran cómo utilizar propiedades para pasar parámetros a un efecto y cambiar un parámetro en tiempo de ejecución.

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[Invocación de eventos de un efecto](touch-tracking.md)

Efectos pueden invocar a eventos. Este artículo muestra cómo crear un evento que implementa el seguimiento de bajo nivel dedo multitoque e indica una aplicación para pulsaciones táctil, se mueve y versiones.