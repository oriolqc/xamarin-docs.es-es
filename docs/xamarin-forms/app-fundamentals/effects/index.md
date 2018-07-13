---
title: Efectos de Xamarin.Forms
description: Los efectos permiten a los controles nativos en cada plataforma para personalizarse sin tener que recurrir a la implementación de un representador personalizado.
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 9d859377c40c6fca07e140c50da46d8f30aaae04
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994463"
---
# <a name="xamarinforms-effects"></a>Efectos de Xamarin.Forms

_Interfaces de usuario de Xamarin.Forms se representan mediante los controles nativos de la plataforma de destino, lo que permite a las aplicaciones de Xamarin.Forms conservar la apariencia y comportamiento adecuada para cada plataforma. Los efectos permiten a los controles nativos en cada plataforma para personalizarse sin tener que recurrir a la implementación de un representador personalizado._

## <a name="introduction-to-effectsintroductionmd"></a>[Introducción a los efectos](introduction.md)

Los efectos permiten personalizar los controles nativos de cada plataforma y se suelen usar para pequeños cambios de estilo. En este artículo proporciona una introducción a los efectos, se describe el límite entre los representadores personalizados y de efectos y describe la `PlatformEffect` clase.

## <a name="creating-an-effectcreatingmd"></a>[Creación de un efecto](creating.md)

Efectos simplifican la personalización de un control. En este artículo se muestra cómo crear un efecto que cambia el color de fondo de la [ `Entry` ](xref:Xamarin.Forms.Entry) controlar cuando el control recibe el foco.

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[Pasar parámetros a un efecto](passing-parameters/index.md)

Crear un efecto que se configura a través de parámetros permite el efecto que se volverá a utilizar. Estos artículos se muestra cómo usar las propiedades para pasar parámetros a un efecto y cambiar un parámetro en tiempo de ejecución.

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[Invocación de eventos de un efecto](touch-tracking.md)

Pueden invocar eventos de efectos. En este artículo se muestra cómo crear un evento que implementa el seguimiento de bajo nivel dedo multitoque y señala una solicitud de presiones de toque, movimientos y las versiones.
