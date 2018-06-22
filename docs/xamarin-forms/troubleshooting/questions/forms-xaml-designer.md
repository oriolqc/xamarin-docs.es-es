---
title: ¿Por qué no funciona el Diseñador XAML de Visual Studio para los archivos XAML de Xamarin.Forms?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 43088beba6c6a86330cac164856be98d88f07fe2
ms.sourcegitcommit: 4f646dc5c51db975b2936169547d625c78a22b30
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/25/2018
ms.locfileid: "34546156"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>¿Por qué no funciona el Diseñador XAML de Visual Studio para los archivos XAML de Xamarin.Forms?

Xamarin.Forms no admite actualmente diseñadores visuales para archivos XAML. Por este motivo, al intentar abrir un archivo XAML Forms en Visual Studio *Diseñador de la interfaz de usuario de XAML* o *Diseñador de la interfaz de usuario de XAML con codificación*, se produce el siguiente mensaje de error:

> "No se puede abrir el archivo con el editor seleccionado. Elija otro editor."

Esta limitación se describe en el [Introducción](~/xamarin-forms/xaml/xaml-basics/index.md#Overview) sección de la [conceptos básicos de XAML de Xamarin.Forms](~/xamarin-forms/xaml/xaml-basics/index.md) guía:

> "No existe todavía no es un diseñador visual para generar XAML en aplicaciones de Xamarin.Forms, por lo que todo el código XAML debe ser escrita a mano."

Sin embargo, se mostrará el controlador de vista previa de Xamarin.Forms XAML seleccionando la **Vista > otras ventanas > controlador de vista previa de Xamarin.Forms** opción de menú.
