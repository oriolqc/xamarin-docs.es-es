---
title: Preguntas más frecuentes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 5a36c6ab14fdc7bfc5916456670be9c8fe4476ff
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049888"
---
# <a name="frequently-asked-questions"></a>Preguntas más frecuentes


## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[¿Se puede actualizar la plantilla predeterminada de Xamarin.Forms en un paquete NuGet más reciente?](update-forms-template.md)
Esta guía usa la plantilla de biblioteca estándar de .NET de Xamarin.Forms como ejemplo, pero también funcionará el mismo método general para la plantilla de proyecto compartido de Xamarin.Forms. 

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[¿Por qué no funciona el diseñador XAML de Visual Studio para los archivos XAML de Xamarin.Forms?](forms-xaml-designer.md)
Xamarin.Forms no admite actualmente diseñadores visuales para archivos XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Error de compilación de Android: error inesperado en la tarea "LinkAssemblies"](android-linkassemblies-error.md)
Puede recibir un mensaje de error `The "LinkAssemblies" task failed unexpectedly` al generar un proyecto Xamarin.Android que utiliza formularios. Esto sucede cuando el vinculador está activo (normalmente en un *versión* compilación para reducir el tamaño del paquete de aplicación); y se produce porque no se actualizan los destinos de Android para el marco de trabajo más reciente. 


## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["¿Por qué se realiza mi proyecto de Xamarin.Forms.Maps Android por error con COMPILETODALVIK: ERROR inesperado de nivel superior?"](maps-compiletodalvik-error.md)
Este error puede aparecer en el panel de errores de Visual Studio para Mac o en la ventana de salida de compilación de Visual Studio; en proyectos de Android mediante Xamarin.Forms.Maps. Normalmente se resuelve si aumenta el tamaño del montón de Java para el proyecto Xamarin.Android.

