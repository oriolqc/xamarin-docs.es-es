---
title: Preguntas más frecuentes de Xamarin.Forms
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
---

# <a name="xamarinforms-frequently-asked-questions"></a>Preguntas más frecuentes de Xamarin.Forms

## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[¿Se puede actualizar la plantilla predeterminada de Xamarin.Forms en un paquete NuGet más reciente?](update-forms-template.md)
Esta guía usa la plantilla de biblioteca de Xamarin.Forms .NET Standard como ejemplo, pero también funcionará el mismo método general para la plantilla de proyecto compartido de Xamarin.Forms.

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[¿Por qué no funciona el diseñador XAML de Visual Studio para los archivos XAML de Xamarin.Forms?](forms-xaml-designer.md)
Xamarin.Forms no admite actualmente los diseñadores visuales para los archivos XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Error de compilación de Android: error inesperado en la tarea "LinkAssemblies"](android-linkassemblies-error.md)
Es posible que vea un mensaje de error `The "LinkAssemblies" task failed unexpectedly` al compilar un proyecto de Xamarin.Android que usa formularios. Esto sucede cuando el vinculador está activo (normalmente en un *versión* compilación para reducir el tamaño del paquete de aplicación); y se produce debido a los destinos de Android no se actualizan a la última versión de framework. 

## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["¿Por qué mi proyecto de xamarin.Forms.Maps para Android produce error compiletodalvik: ERROR NIVEL SUPERIOR INESPERADO?"](maps-compiletodalvik-error.md)
Este error puede aparecer en el panel de errores de Visual Studio para Mac o en la ventana de salida de la compilación de Visual Studio; en los proyectos mediante xamarin.Forms.Maps para Android. Normalmente se resuelve al aumentar el tamaño del montón de Java para el proyecto de Xamarin.Android.
