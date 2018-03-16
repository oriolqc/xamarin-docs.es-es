---
title: Subprocesos
ms.topic: article
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 693ada611dc24d3bb22de7c51efe378939a732ad
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="threading"></a>Subprocesos

El tiempo de ejecución de Xamarin.iOS proporciona a los desarrolladores acceso a .NET API, ambos explícitamente al utilizar subprocesos de subprocesamiento (`System.Threading.Thread, System.Threading.ThreadPool`) e implícitamente cuando se utiliza los patrones de delegado asincrónico o los métodos BeginXXX, así como el intervalo completo de API que admiten el Biblioteca TPL.



Xamarin recomienda encarecidamente que utilice la [Task Parallel Library](http://msdn.microsoft.com/en-us/library/dd460717.aspx) (TPL) para la creación de aplicaciones por diversas razones:
-  El programador TPL predeterminado suplantará la ejecución de la tarea para el grupo de subprocesos, que a su vez crecerá dinámicamente el número de subprocesos es necesario porque el proceso realiza, evitando un escenario donde hay demasiados subprocesos terminan compiten por el tiempo de CPU. 
-  Es más fácil pensar en las operaciones en cuanto a las tareas de TPL. Fácilmente puede manipularlos, programarlas, serializar su ejecución o iniciar muchas en paralelo con un amplio conjunto de API. 
-  Es la base para la programación con las nuevas extensiones de lenguaje C# asincrónico. 


El grupo de subprocesos lenta crece el número de subprocesos según sea necesario según el número de núcleos de CPU disponibles en el sistema, la carga del sistema y los requisitos de su aplicación. Puede usar este grupo de subprocesos mediante la invocación de métodos en `System.Threading.ThreadPool` o mediante el valor predeterminado `System.Threading.Tasks.TaskScheduler` (parte de la *entornos paralelos*).

Normalmente los desarrolladores usar subprocesos cuando se necesitan para crear aplicaciones con capacidad de respuesta y no desea bloquear la interfaz de usuario principal ejecuta el bucle.

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>Desarrollar aplicaciones con capacidad de respuesta

Acceso a elementos de interfaz de usuario debe estar limitado al mismo subproceso que se ejecuta el bucle principal para la aplicación. Si desea realizar cambios en la interfaz de usuario principal de un subproceso, se debe poner en cola el código mediante el uso de [NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/type/Foundation.NSObject/), similar a la siguiente:

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

Los pasos anteriores, invoca el código en el delegado en el contexto del subproceso principal, sin causar que las condiciones de carrera que podrían bloquearse la aplicación.

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>Subprocesamiento y recolección de elementos

En el curso de ejecución, el tiempo de ejecución de C de objetivo se crear y liberar objetos. Si los objetos estén marcados para su "auto-release" el tiempo de ejecución de C objetivo liberará esos objetos para el subproceso actual de la `NSAutoReleasePool`. Xamarin.iOS crea una `NSAutoRelease` grupo para cada subproceso de la `System.Threading.ThreadPool` y para el subproceso principal. Esta extensión trata los subprocesos creados en el valor predeterminado TaskScheduler System.Threading.Tasks.

Si crea sus propios subprocesos mediante `System.Threading` tendrá que proporcionar su propiedad `NSAutoRelease` grupo para evitar que se pierdan los datos. Para ello, simplemente encapsulan el subproceso en el siguiente fragmento de código:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Nota: Desde Xamarin.iOS 5.2 no tendrá que proporcionar su propio `NSAutoReleasePool` ya tal y como se proporciona automáticamente para usted.


## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con el subproceso de la interfaz de usuario](~/ios/user-interface/ios-ui/ui-thread.md)
