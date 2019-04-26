---
title: 'Error de compilación de Android: tarea LinkAssemblies el error inesperado'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: f517aaa770fa7b2f1463954638f0afc95168bf65
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250754"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Error de compilación de Android: tarea LinkAssemblies el error inesperado

Es posible que vea un mensaje de error `The "LinkAssemblies" task failed unexpectedly` al compilar un proyecto de Xamarin.Android que usa formularios. Esto sucede cuando el vinculador está activo (normalmente en un *versión* compilación para reducir el tamaño del paquete de aplicación); y se produce debido a los destinos de Android no se actualizan a la última versión de framework. (Obtener más información: [Xamarin.Forms para Android requisitos](~/get-started/requirements.md#android))

La solución a este problema consiste en asegurarse de que dispone de las versiones de Android SDK más reciente compatibles y establezca el **.NET Framework de destino** a la última plataforma instalada. También se recomienda que establezca el **versión Android de destino** a la última plataforma instalada y el **versión Android mínima** en API 19 o superior. Esto se considera la configuración admitida.

## <a name="setting-in-visual-studio-for-mac"></a>Configuración de Visual Studio para Mac

1.  Haga clic con el botón derecho en el proyecto de Android y seleccione **opciones** en el menú.
2.  En el **opciones de proyecto** cuadro de diálogo, vaya a **compilar > General**.
3.  Establecer el **compilar con la versión de Android: (.NET Framework de destino)**  a la última plataforma instalada.
4.  En el **opciones de proyecto** cuadro de diálogo, vaya a **compilar > aplicación de Android**.
5.  Establecer el **versión Android mínima** a nivel de API 19 o superior y el **versión Android de destino** a la última plataforma instalada que eligió en (3).

## <a name="setting-in-visual-studio"></a>Configuración de Visual Studio

1.  Haga clic con el botón derecho en el proyecto de Android y seleccione **dimensiones** en el menú.
2.  En las propiedades del proyecto, vaya a **aplicación**.
3.  Establecer el **compilar con la versión de Android: (.NET Framework de destino)**  a la última plataforma instalada.
4.  En las propiedades del proyecto, vaya a **manifiesto de Android**.
5.  Establecer el **versión Android mínima** a nivel de API 19 o superior y el **versión Android de destino** a la última plataforma instalada que eligió en (3).

Una vez que se ha actualizado esas configuraciones, por favor, limpie y recompile el proyecto para asegurarse de que se recogen los cambios.
