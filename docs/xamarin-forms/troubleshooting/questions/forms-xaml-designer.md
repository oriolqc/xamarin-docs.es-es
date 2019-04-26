---
title: ¿Por qué no funciona el diseñador XAML de Visual Studio para los archivos XAML de Xamarin.Forms?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 43088beba6c6a86330cac164856be98d88f07fe2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247807"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>¿Por qué no funciona el diseñador XAML de Visual Studio para los archivos XAML de Xamarin.Forms?

Xamarin.Forms no admite actualmente los diseñadores visuales para los archivos XAML. Por este motivo, al intentar abrir un archivo XAML de Forms en Visual Studio *Diseñador de IU XAML* o *Diseñador de IU XAML con codificación*, se produce el siguiente mensaje de error:

> "No se puede abrir el archivo con el editor seleccionado. Elija otro editor."

Esta limitación se describe en el [Introducción](~/xamarin-forms/xaml/xaml-basics/index.md#Overview) sección de la [conceptos básicos de XAML de Xamarin.Forms](~/xamarin-forms/xaml/xaml-basics/index.md) guía:

> "No hay todavía un diseñador visual para generar XAML en las aplicaciones de Xamarin.Forms, por lo que todo XAML debe ser escrito a mano."

Sin embargo, se pueden mostrar la vista previa de XAML de Xamarin.Forms seleccionando el **View > Other Windows > controlador de vista previa de Xamarin.Forms** opción de menú.
