---
title: Subprocesamiento en Xamarin.iOS
description: Este documento describe cómo usar las APIs System.Threading en una aplicación de Xamarin.iOS. Describe la biblioteca TPL, creación de aplicaciones con capacidad de respuesta y recolección de elementos.
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: 7dbb0044f09d5bc00f2393eb647efba05a061c3f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399844"
---
# <a name="threading-in-xamarinios"></a>Subprocesamiento en Xamarin.iOS

El tiempo de ejecución de Xamarin.iOS ofrece a los desarrolladores acceso a .NET API, tanto explícitamente al usar subprocesos de subprocesamiento (`System.Threading.Thread, System.Threading.ThreadPool`) e implícita al usar los patrones de delegado asincrónico o los métodos BeginXXX, así como el intervalo completo de API que admiten el Biblioteca TPL.



Xamarin recomienda encarecidamente que utilice el [Task Parallel Library](https://msdn.microsoft.com/library/dd460717.aspx) (TPL) para la creación de aplicaciones por diversas razones:
-  El programador TPL predeterminado delegará la ejecución de la tarea para el grupo de subprocesos, que a su vez crecerá dinámicamente el número de subprocesos necesarios como proceso tiene lugar, al tiempo que evita un escenario donde hay demasiados subprocesos terminan compiten por el tiempo de CPU. 
-  Es más fácil pensar en las operaciones en términos de tareas de TPL. Fácilmente puede manipularlos, programarlos, serializar su ejecución o iniciar muchas en paralelo con un amplio conjunto de API. 
-  Es la base para la programación con las nuevas extensiones de lenguaje C# asincrónico. 


El grupo de subprocesos lentamente aumentará el número de subprocesos según sea necesario en función del número de núcleos de CPU disponibles en el sistema, la carga del sistema y los requisitos de su aplicación. Puede usar este grupo de subprocesos mediante la invocación de métodos en `System.Threading.ThreadPool` o mediante el valor predeterminado `System.Threading.Tasks.TaskScheduler` (parte de la *entornos paralelos*).

Normalmente, los desarrolladores usar subprocesos cuando se necesitan para crear aplicaciones con capacidad de respuesta y no desea bloquear la interfaz de usuario principal, ejecute el bucle.

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>Desarrollo de aplicaciones con capacidad de respuesta

Acceso a elementos de interfaz de usuario debe limitarse al mismo subproceso que ejecuta el bucle principal para la aplicación. Si desea realizar cambios en la interfaz de usuario principal de un subproceso, se debe poner en cola el código mediante el uso de [NSObject.InvokeOnMainThread](xref:Foundation.NSObject), similar al siguiente:

```csharp
MyThreadedRoutine ()  
{  
    var result = DoComputation ();  

    // we want to update an object that is managed by the main
    // thread; To do so, we need to ensure that we only access
    // this from the main thread:

    InvokeOnMainThread (delegate {  
        label.Text = "The result is: " + result;  
    });
}
```

Los pasos anteriores, invoca el código del delegado en el contexto del subproceso principal, sin causar las condiciones de carrera que potencialmente podrían bloquear la aplicación.

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>Subprocesamiento y recolección de elementos

En el curso de ejecución, el tiempo de ejecución Objective-C creará y liberar objetos. Si los objetos se marcan para "auto-release" el tiempo de ejecución Objective-C liberará esos objetos para el subproceso actual de la `NSAutoReleasePool`. Xamarin.iOS crea uno `NSAutoRelease` grupo para cada subproceso de la `System.Threading.ThreadPool` y para el subproceso principal. Esta extensión trata los subprocesos creados mediante el valor TaskScheduler predeterminado en System.Threading.Tasks.

Si crea sus propios subprocesos mediante `System.Threading` tendrá que proporcionar su propiedad `NSAutoRelease` grupo para evitar que se pierdan los datos. Para ello, simplemente se ajustan el subproceso en el siguiente fragmento de código:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Nota: Desde Xamarin.iOS 5.2 no tendrá que proporcionar su propio `NSAutoReleasePool` ya que se proporciona automáticamente para usted.


## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con el subproceso de la interfaz de usuario](~/ios/user-interface/ios-ui/ui-thread.md)
