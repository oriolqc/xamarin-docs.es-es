---
title: Información general sobre la arquitectura
description: Este documento describe la arquitectura de los libros de Xamarin, examinar cómo funcionan conjuntamente el agente interactivo y el cliente interactivo.
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 3cd0d3e8cc47dd7fa43dfa0b910c9f09740fcc9a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794012"
---
# <a name="architecture-overview"></a>Información general sobre la arquitectura

Los libros de Xamarin ofrece dos componentes principales que deben trabajar conjuntamente entre sí: _agente_ y _cliente_.

## <a name="interactive-agent"></a>Agente interactivo

El componente agente es un ensamblado de específico de la plataforma pequeño que se ejecuta en el contexto de una aplicación. NET.

Los libros de Xamarin proporciona a las aplicaciones "vacías" pregeneradas durante un número de plataformas, como iOS, Android, Mac y WPF. Estas aplicaciones explícitamente hospedan al agente.

Durante la inspección en vivo (Xamarin Inspector), el agente se aplica mediante el depurador IDE en una aplicación existente como parte del desarrollo regular & depuración de flujo de trabajo.

## <a name="interactive-client"></a>Cliente interactivo

El cliente es un shell nativo (cacao en Mac, WPF en Windows) que hospeda una superficie del explorador web para presentar la interfaz de libro/REPL. Desde una perspectiva SDK, todas las integraciones de cliente se implementan en JavaScript y CSS.

El cliente es responsable (a través de Roslyn) para compilar código fuente en ensamblados pequeños y enviarlos a través de la ejecución del agente conectada. Resultados de ejecución se envían al cliente para la representación. Cada celda de un libro, genera un ensamblado que hace referencia al ensamblado de la celda anterior.

Dado que un agente puede ejecutarse en cualquier tipo de plataforma. NET y tiene acceso a ningún sitio en la aplicación en ejecución, se debe tener cuidado para serializar los resultados de una manera independiente de la plataforma.
