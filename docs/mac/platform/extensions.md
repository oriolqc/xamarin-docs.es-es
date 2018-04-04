---
title: Compatibilidad con las extensiones de Xamarin.Mac
description: Este artículo trata la compatibilidad con las extensiones en Xamarin.Mac versión 2.10 (y versiones posteriores).
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 03936c75d31bfd01e741ad2c09096c925dc9dbfc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinmac-extension-support"></a>Compatibilidad con las extensiones de Xamarin.Mac

En la vista previa de Xamarin.Mac 2.10 se agregó compatibilidad para varios puntos de extensión de macOS:

- Método Finder
- Compartir
- Hoy

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

A continuación se muestran las limitaciones y conoce los problemas que pueden producirse al desarrollar extensiones en Xamarin.Mac:

* Actualmente no hay ninguna compatibilidad con la depuración en Visual Studio para Mac. Depuración de todos los deberá hacerse a través de **NSLog** y **consola**. Vea la sección de sugerencias a continuación para obtener más información.
* Las extensiones deben estar contenidas en una aplicación host, que, cuando ejecute una vez con registrarse con el sistema. A continuación, debe estar habilitadas en el **extensión** sección de **preferencias del sistema**. 
* Algunos bloqueos de extensión pueden llegar a desestabilizar la aplicación host y provocar un comportamiento extraño. En concreto, **buscador** y el **hoy** sección de la **centro de notificaciones** puede convertirse en "atascado" y deje de responder. Esto se ha experimentado en proyectos de extensión en Xcode así y actualmente aparece relacionado con Xamarin.Mac. A menudo esto puede verse en el registro del sistema (a través de **consola**, consulte sugerencias para obtener más información) imprimir mensajes de error repetidos. Reiniciar macOS aparece para solucionar este problema.

<a name="Tips" />

## <a name="tips"></a>Sugerencias

Las siguientes sugerencias pueden resultar útiles al trabajar con las extensiones en Xamarin.Mac:

- Como Xamarin.Mac actualmente no admite extensiones de depuración, la experiencia de depuración principalmente dependerá de ejecución y `printf` como instrucciones. Sin embargo, las extensiones de ejecutar en un proceso de espacio aislado, por lo tanto `Console.WriteLine` no actuará como ocurre en otras aplicaciones Xamarin.Mac. Invocar [ `NSLog` directamente](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554) dará como resultado mensajes de depuración en el registro del sistema.
- Cualquier excepción no detectada bloqueará el proceso de extensión, proporcionar sólo una pequeña cantidad de información útil en el **registro del sistema**. Ajuste código problemático en un `try/catch` (excepción) bloquear que `NSLog`del antes de volver a iniciar puede resultar útil.
- El **registro del sistema** puede tener acceso desde el **consola** aplicación en **aplicaciones** > **utilidades**:

    [![](extensions-images/extension02.png "El registro del sistema")](extensions-images/extension02.png#lightbox)
- Tal y como se mencionó anteriormente, ejecutar la aplicación de host de extensión se registrarlo con el sistema. Eliminación de la agrupación de aplicaciones con anular el registro. 
- Si se ha registrado "aisladas" versiones de extensiones de una aplicación, utilice el siguiente comando para localizarlos con (de manera que se puede eliminar): `plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Tutorial y aplicaciones de ejemplo

Dado que el programador se crear y trabajar con las extensiones de Xamarin.Mac de la misma manera que las extensiones de Xamarin.iOS, consulte nuestro [Introducción a las extensiones](~/ios/platform/extensions.md) documentación para obtener más detalles.

Un proyecto de Xamarin.Mac de ejemplo que contiene pequeños, pueden encontrar ejemplos funcionales de cada tipo de extensión [aquí](https://developer.xamarin.com/samples/mac/ExtensionSamples/).

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado un vistazo rápido a trabajar con las extensiones en una aplicación de versión Xamarin.Max 2.10 (y versiones posteriores).

## <a name="related-links"></a>Vínculos relacionados

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://developer.xamarin.com/samples/mac/ExtensionSamples/)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
