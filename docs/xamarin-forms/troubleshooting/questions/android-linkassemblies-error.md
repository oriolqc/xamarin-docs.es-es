---
title: "Error de compilación en Android: tarea de LinkAssemblies el error inesperado"
ms.topic: article
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 667381e7f27f08f8b1d8b6c1979a7d0b26775177
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Error de compilación en Android: tarea de LinkAssemblies el error inesperado

Puede recibir un mensaje de error `The "LinkAssemblies" task failed unexpectedly` al generar un proyecto Xamarin.Android que utiliza formularios. Esto sucede cuando el vinculador está activo (normalmente en un *versión* compilación para reducir el tamaño del paquete de aplicación); y se produce porque no se actualizan los destinos de Android para el marco de trabajo más reciente. (Obtener más información: [Xamarin.Forms para Android requisitos](~/xamarin-forms/get-started/installation.md#android))

La resolución para este problema consiste en asegurarse de que dispone de las versiones de SDK de Android más reciente compatibles y establezca el **.NET Framework de destino** a **plataforma de uso más reciente instalado**. También se recomienda que establezca el **versión de Android de destino** a **versión de Framework de destino de uso** y **versión mínima de Android** a API 15 o posterior. Esto se considera la configuración admitida.

## <a name="setting-in-visual-studio-for-mac"></a>Configuración de Visual Studio para Mac

1.  Haga clic en el proyecto de Android.
2.  Vaya a **generar > General > Framework de destino**.
3.  Establecer el **.NET Framework de destino: plataforma de uso más reciente instalado**.
4.  En las opciones de proyecto, vaya a **generar > aplicación Android**.
5.  Establecer el **versión mínimo Android** a nivel de API 15 o posterior y en el **versión de destino Android** a **automático: versión de framework de destino de uso**.

## <a name="setting-in-visual-studio"></a>Configuración en Visual Studio

1.  Haga clic en el proyecto de Android.
2.  Vaya a **aplicación** en las opciones de proyecto.
3.  Establecer el **compilar con la versión Android** & **versión de destino Android** configuración **plataforma más reciente de uso** / **uso Compilar con la versión SDK**.
4.  Establecer el **mínimo Android al destino** establecer a API 15 o posterior.

Una vez que se ha actualizado esta configuración, por favor, limpie y recompile el proyecto para asegurarse de que se recogen los cambios.
