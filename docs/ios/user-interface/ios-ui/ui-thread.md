---
title: Trabajar con el subproceso de interfaz de usuario en Xamarin.iOS
description: Este documento describe cómo trabajar con el subproceso de IU en Xamarin.iOS. Describe la ejecución del subproceso de interfaz de usuario, proporciona un ejemplo de subproceso en segundo plano y examina async y await.
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: e4485c485b708bdec06f7f1dc22f0bf33e07e982
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827748"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>Trabajar con el subproceso de interfaz de usuario en Xamarin.iOS

Interfaces de usuario de la aplicación siempre son un único subproceso, incluso en dispositivos multiproceso: hay solo una representación de la pantalla y los cambios realizados en lo que se muestra tienen que coordinar a través de una sola "punto de acceso". Esto impide que varios subprocesos intentar actualizar el mismo píxel al mismo tiempo (por ejemplo).

El código solo debe hacer el subproceso de los cambios realizados en los controles en la página principal de la interfaz de usuario (o la interfaz de usuario). Las actualizaciones de la interfaz de usuario que se producen en un subproceso diferente (por ejemplo, un subproceso en segundo plano o de devolución de llamada) no pueden obtener representa en la pantalla o incluso podrían provocar un bloqueo.

## <a name="ui-thread-execution"></a>Ejecución de subprocesos de interfaz de usuario

Cuando se crean controles en una vista, o controlar un evento iniciado por el usuario como una entrada táctil, el código ya se está ejecutando en el contexto del subproceso de interfaz de usuario.

Si está ejecutando código en un subproceso en segundo plano, en una tarea o una devolución de llamada es probable que no está ejecutando en el subproceso de interfaz de usuario principal. En este caso debe ajustar el código en una llamada a `InvokeOnMainThread` o `BeginInvokeOnMainThread` similar al siguiente:

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

El `InvokeOnMainThread` método se define en `NSObject` por lo que puede llamarse desde dentro de los métodos definidos en cualquier objeto UIKit (por ejemplo, una vista o un controlador de vista).

Durante la depuración de aplicaciones de Xamarin.iOS, se producirá un error si el código intenta tener acceso a un control de IU desde el subproceso equivocado. Esto le ayudará a encontrar y corregir estos problemas con el método InvokeOnMainThread. Esto solo se produce durante la depuración y no produce un error en las compilaciones de versión. Aparecerá el mensaje de error similar al siguiente:

 ![](ui-thread-images/image10.png "Ejecución de subprocesos de interfaz de usuario")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>Ejemplo de subproceso en segundo plano

Este es un ejemplo que intenta obtener acceso a un control de interfaz de usuario (un `UILabel`) desde un subproceso en segundo plano mediante un subproceso simple:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Que el código iniciará el `UIKitThreadAccessException` durante la depuración. Para corregir el problema (y asegúrese de que el control de interfaz de usuario solo se tiene acceso desde el subproceso de interfaz de usuario principal), ajustar cualquier código que hace referencia a los controles de interfaz de usuario dentro de un `InvokeOnMainThread` expresión similar al siguiente:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

No tendrá que usar esto para el resto de los ejemplos de este documento, pero es un importante concepto que hay que recordar cuando la aplicación realiza las solicitudes de red, usa el centro de notificaciones u otros métodos que requieren un controlador de finalización que se ejecutará en el otro subproceso.

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>Ejemplo de Async y Await

Cuando se usa el C# 5 palabras clave async y await `InvokeOnMainThread` no es necesario porque cuando se completa una tarea en espera el método continúa en el subproceso que realiza la llamada.

Este código de ejemplo (que se espera en una llamada al método de retraso, exclusivamente para fines de demostración), muestra un método asincrónico que se llama en el subproceso de interfaz de usuario (es un controlador TouchUpInside). Dado que se llama al método contenedor en el subproceso de interfaz de usuario, las operaciones de la interfaz de usuario como establecer el texto en un `UILabel` o mostrar un `UIAlertView` puede llamar de forma segura después de han completado las operaciones asincrónicas en subprocesos en segundo plano.

```csharp
async partial void button2_TouchUpInside (UIButton sender)
{
    textfield1.ResignFirstResponder ();
    textfield2.ResignFirstResponder ();
    textview1.ResignFirstResponder ();
    label1.Text = "async method started";
    await Task.Delay(1000); // example purpose only
    label1.Text = "1 second passed";
    await Task.Delay(2000);
    label1.Text = "2 more seconds passed";
    await Task.Delay(1000);
    new UIAlertView("Async method complete", "This method", 
               null, "Cancel", null)
        .Show();
    label1.Text = "async method completed";
}
```

Si un método asincrónico se llama desde un subproceso en segundo plano (no el subproceso de interfaz de usuario principal), a continuación, `InvokeOnMainThread` sería necesario.


## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/monotouch/Controls/)
- [Subprocesamiento](~/ios/app-fundamentals/threading.md)
