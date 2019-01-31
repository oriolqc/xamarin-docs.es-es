---
title: 'Error de compilación de Android: tarea LinkAssemblies el error inesperado'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: b027dd23b9144a865bc16b55ebac71855bae0725
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292043"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Error de compilación de Android: tarea LinkAssemblies el error inesperado

Es posible que vea un mensaje de error `The "LinkAssemblies" task failed unexpectedly` al compilar un proyecto de Xamarin.Android que usa formularios. Esto sucede cuando el vinculador está activo (normalmente en un *versión* compilación para reducir el tamaño del paquete de aplicación); y se produce debido a los destinos de Android no se actualizan a la última versión de framework. (Obtener más información: [Xamarin.Forms para Android requisitos](~/get-started/installation.md#android))

La solución a este problema consiste en asegurarse de que dispone de las versiones de Android SDK más reciente compatibles y establezca el **.NET Framework de destino** a **usar la plataforma más reciente instalada**. También se recomienda que establezca el **versión Android de destino** a **Use la versión de Framework de destino** y **versión Android mínima** en API 15 o superior. Esto se considera la configuración admitida.

## <a name="setting-in-visual-studio-for-mac"></a>Configuración de Visual Studio para Mac

1.  Haga clic con el botón secundario en el proyecto de Android.
2.  Vaya a **compilar > General > destino Framework**.
3.  Establecer el **Framework de destino: Usar la plataforma más reciente instalada**.
4.  Todavía en las opciones del proyecto, vaya a **compilar > aplicación de Android**.
5.  Establecer el **versión Android mínima** a nivel de API 15 o superior y el **versión Android de destino** a **automático: use la versión de framework de destino**.

## <a name="setting-in-visual-studio"></a>Configuración de Visual Studio

1.  Haga clic con el botón secundario en el proyecto de Android.
2.  Vaya a **aplicación** en las opciones del proyecto.
3.  Establecer el **compilar con la versión Android** & **versión Android de destino** configuración **usar la plataforma más reciente** / **uso Compilar con la versión SDK**.
4.  Establecer el **Android mínima de destino** establecer en API 19 o superior.

Una vez que se ha actualizado esas configuraciones, por favor, limpie y recompile el proyecto para asegurarse de que se recogen los cambios.
