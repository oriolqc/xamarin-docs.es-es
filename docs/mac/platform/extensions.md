---
title: Compatibilidad con las extensiones de Xamarin.Mac
description: Este documento describe el soporte técnico de Xamarin.Mac para las extensiones de Finder, recurso compartido y hoy en día. Limitaciones y problemas conocidos, vínculos a una aplicación de ejemplo y tutorial, se examinan y proporciona sugerencias para trabajar con las extensiones.
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 60b981a764a2656210730ae0602ff32dc580cd0a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117569"
---
# <a name="xamarinmac-extension-support"></a>Compatibilidad con las extensiones de Xamarin.Mac

En la versión preliminar de Xamarin.Mac 2.10 se agregó compatibilidad para varios puntos de extensión de macOS:

- Finder
- Compartir
- Hoy

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

A continuación se muestran las limitaciones y conoce los problemas que pueden producirse al desarrollar extensiones de Xamarin.Mac:

* Actualmente no hay ninguna compatibilidad con la depuración en Visual Studio para Mac. Depuración de todos los deberá realizarse a través de **NSLog** y **consola**. Consulte la sección de sugerencias a continuación para obtener más información.
* Las extensiones deben estar contenidas en una aplicación host, que, cuando ejecute una vez con el registro con el sistema. A continuación, debe estar habilitadas en el **extensión** sección de **preferencias del sistema**. 
* Algunos bloqueos de extensión pueden llegar a desestabilizar la aplicación host y provocar un comportamiento extraño. En concreto, **buscador** y **hoy en día** sección de la **centro de notificaciones** puede convertirse en "atascada" y deje de responder. Esto se ha experimentado en proyectos de extensión en Xcode también y aparece actualmente no relacionada a Xamarin.Mac. A menudo esto puede verse en el registro del sistema (a través de **consola**, consulte sugerencias para obtener más información) imprimir mensajes de error repetidas. Reiniciar macOS aparece para solucionar este problema.

<a name="Tips" />

## <a name="tips"></a>Sugerencias

Las sugerencias siguientes pueden ser útiles al trabajar con las extensiones de Xamarin.Mac:

- Ya que Xamarin.Mac no admite actualmente las extensiones de depuración, la experiencia de depuración principalmente dependerá de la ejecución y `printf` como las instrucciones. Sin embargo, las extensiones de ejecutar en un proceso de espacio aislado, por lo tanto `Console.WriteLine` no actuará como lo hace en otras aplicaciones de Xamarin.Mac. Invocar [ `NSLog` directamente](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554) dará como resultado mensajes de depuración para el registro del sistema.
- Las excepciones no detectadas bloquearán el proceso de extensión, que proporciona solo una pequeña cantidad de información útil en el **registro del sistema**. Ajuste el código problemático en un `try/catch` (excepción) que bloquear `NSLog`del antes de volver a producir puede resultar útil.
- El **registro del sistema** se puede acceder desde el **consola** aplicación bajo **aplicaciones** > **utilidades**:

    [![](extensions-images/extension02.png "El registro del sistema")](extensions-images/extension02.png#lightbox)
- Como se mencionó anteriormente, ejecuta la aplicación de host de la extensión se registrarlo con el sistema. Al eliminar el lote de aplicación con anular el registro. 
- Si se ha registrado "aisladas" versiones de extensiones de una aplicación, use el siguiente comando para localizarlos con (por lo que se puede eliminar): `plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Tutorial y aplicación de ejemplo

Dado que el desarrollador creará y trabajar con las extensiones de Xamarin.Mac en la misma manera que las extensiones de Xamarin.iOS, consulte nuestra [Introducción a las extensiones](~/ios/platform/extensions.md) documentación para obtener más detalles.

Un proyecto de Xamarin.Mac de ejemplo que contiene pequeños, pueden encontrar algunos ejemplos funcionales de cada tipo de extensión [aquí](https://developer.xamarin.com/samples/mac/ExtensionSamples/).

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado un vistazo rápido a trabajar con las extensiones en una aplicación de versión Xamarin.Max 2.10 (y versiones posteriores).

## <a name="related-links"></a>Vínculos relacionados

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://developer.xamarin.com/samples/mac/ExtensionSamples/)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
