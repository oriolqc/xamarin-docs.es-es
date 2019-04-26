---
title: Información general sobre la arquitectura
description: Este documento describe la arquitectura de Xamarin Workbooks, examinar cómo funcionan conjuntamente el agente interactivo y un cliente interactivo.
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 352e8d0191512184ffd7d82fa0dfa0bc79fa24ca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61257430"
---
# <a name="architecture-overview"></a>Información general sobre la arquitectura

Xamarin Workbooks incluye dos componentes principales que deben funcionar conjuntamente entre sí: _Agente_ y _cliente_.

## <a name="interactive-agent"></a>Agente interactivo

El componente del agente es un ensamblado de específicos de la plataforma pequeño que se ejecuta en el contexto de una aplicación .NET.

Xamarin Workbooks proporciona a las aplicaciones "vacías" pregeneradas para varias plataformas como iOS, Android, Mac y WPF. Estas aplicaciones hospedan explícitamente el agente.

Durante la inspección en directo (Inspector de Xamarin), el agente se inserta mediante el depurador IDE en una aplicación existente como parte del desarrollo regular & depuración de flujo de trabajo.

## <a name="interactive-client"></a>Cliente interactivo

El cliente es un shell nativo (cacao en Mac, WPF en Windows) que hospeda una superficie del explorador web para presentar la interfaz/REPL de libro. Desde una perspectiva SDK, todas las integraciones de cliente se implementan en JavaScript y CSS.

El cliente es responsable de (a través de Roslyn) para compilar el código fuente en ensamblados pequeños y enviarlos a través de la ejecución del agente conectada. Resultados de ejecución se envían al cliente para la representación. Cada celda de un libro, genera un ensamblado que hace referencia al ensamblado de la celda anterior.

Dado que un agente puede ejecutarse en cualquier tipo de plataforma .NET y tiene acceso a ningún sitio en la aplicación en ejecución, debe tener cuidado para serializar los resultados de una manera independiente de la plataforma.
