---
title: Trabajar con el subproceso de interfaz de usuario
ms.topic: article
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 8f348511861c21d25c3e54164bedae3baad2d4d0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-the-ui-thread"></a>Trabajar con el subproceso de interfaz de usuario

Interfaces de usuario de la aplicación siempre son un único subproceso, incluso en dispositivos multiproceso: hay sólo una representación de la pantalla y los cambios realizados en lo que se muestra deben coordinar a través de un único 'punto de acceso de '. Esto impide que varios subprocesos intentando actualizar el mismo píxel al mismo tiempo (por ejemplo).

El código debe realizar solo subproceso de cambios en los controles en la página principal de la interfaz de usuario (o interfaz de usuario). Las actualizaciones de la interfaz de usuario que se producen en un subproceso diferente (por ejemplo, un subproceso de devolución de llamada o en segundo plano) no pueden obtener representa en la pantalla, o incluso podrían provocar un bloqueo.

## <a name="ui-thread-execution"></a>Ejecución de subprocesos de interfaz de usuario

Cuando se crean controles en una vista o controle un evento iniciado por el usuario como un toque, el código ya se está ejecutando en el contexto del subproceso de interfaz de usuario.

Si se está ejecutando código en un subproceso en segundo plano, en una tarea o una devolución de llamada es probable que no se ejecute en el subproceso de interfaz de usuario principal. En este caso se debería incluir el código en una llamada a `InvokeOnMainThread` o `BeginInvokeOnMainThread` similar a la siguiente:

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

El `InvokeOnMainThread` método se define en `NSObject` por lo que puede llamarse desde dentro de los métodos definidos en cualquier objeto UIKit (por ejemplo, una vista o el controlador de vista).

Al depurar aplicaciones de Xamarin.iOS, se producirá un error si el código intenta obtener acceso a un control de interfaz de usuario desde el subproceso erróneo. Esto le ayudará a encontrar y corregir estos problemas con el método InvokeOnMainThread. Esto sólo ocurre durante la depuración y no produce un error en las compilaciones de versión. Se mostrará el mensaje de error similar al siguiente:

 ![](ui-thread-images/image10.png "Ejecución de subprocesos de interfaz de usuario")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>Ejemplo de subproceso de fondo

Este es un ejemplo que intenta obtener acceso a un control de interfaz de usuario (una `UILabel`) desde un subproceso en segundo plano mediante un subproceso simple:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Que el código iniciará el `UIKitThreadAccessException` durante la depuración. Para corregir el problema (y asegurarse de que el control de interfaz de usuario solo se obtiene acceso desde el subproceso de interfaz de usuario principal), ajustar cualquier código que hace referencia a los controles de interfaz de usuario dentro de un `InvokeOnMainThread` expresión similar al siguiente:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

No debe hacer uso para el resto de los ejemplos de este documento, pero se trata de un concepto importante recordar cuando la aplicación realiza solicitudes de red, usa el centro de notificaciones u otros métodos que requieren un controlador de finalización que se ejecutará en el otro subproceso.

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>Ejemplo Async y Await

Al utilizar las palabras clave async y await de C# 5 `InvokeOnMainThread` no es necesario porque cuando se completa una tarea en espera el método continúa en el subproceso que realiza la llamada.

Este código de ejemplo (que se espera en una llamada al método de retraso, exclusivamente con fines de demostración) muestra un método asincrónico que se llama en el subproceso de interfaz de usuario (es un controlador de TouchUpInside). Dado que se llama al método contenedor en el subproceso de interfaz de usuario, las operaciones de interfaz de usuario como el establecimiento del texto en un `UILabel` o mostrar un `UIAlertView` puede llamar de forma segura después de han completado las operaciones asincrónicas en subprocesos en segundo plano.

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

Si se llama a un método asincrónico de un subproceso en segundo plano (no el subproceso de interfaz de usuario principal), a continuación, `InvokeOnMainThread` sería necesario.


## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
- [Subprocesamiento](~/ios/app-fundamentals/threading.md)
