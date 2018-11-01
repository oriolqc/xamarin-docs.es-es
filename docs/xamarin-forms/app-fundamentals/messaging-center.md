---
title: Xamarin.Forms MessagingCenter
description: En este artículo se explica cómo usar el Xamarin.Forms MessagingCenter para enviar y recibir mensajes, para reducir el acoplamiento entre clases como modelos de vista.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 7fef4443cacba0fa8bdb8d5df070c4244730b4f5
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675185"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

_Xamarin.Forms incluye un simple servicio de mensajería para enviar y recibir mensajes._

<a name="Overview" />

## <a name="overview"></a>Información general

Xamarin.Forms `MessagingCenter` permite ver los modelos y otros componentes se comuniquen sin tener que saber nada acerca de sí además de un contrato de mensaje simple.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Cómo funciona el MessagingCenter

Hay dos partes para `MessagingCenter`:

-  **Suscribirse** : realizar escuchas de mensajes con una firma determinada y realizar alguna acción cuando se reciben. Pueden escuchar varios suscriptores para el mismo mensaje.
-  **Enviar** -publicar un mensaje para actuar sobre los agentes de escucha. Si no se ha suscrito a ningún agente de escucha se omite el mensaje.


El `MessagingService` es una clase estática con `Subscribe` y `Send` métodos que se usan en toda la solución.

Los mensajes tienen una cadena `message` parámetro que se utiliza como forma de *dirección* mensajes. El `Subscribe` y `Send` métodos usan parámetros genéricos para un mayor control sobre cómo se entregan los mensajes: dos mensajes con el mismo `message` texto pero argumentos de tipo genérico diferente no se entregará al mismo suscriptor.

La API para `MessagingCenter` es simple:

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

A continuación se explican estos métodos.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Uso de la MessagingCenter

Como resultado de la interacción del usuario (por ejemplo, haga clic en un botón), un evento del sistema (por ejemplo, los controles de cambio de estado) o algunos otros incidentes (por ejemplo, para completar una descarga asincrónica) se pueden enviar mensajes. Los suscriptores podrían estar escuchando para cambiar la apariencia de la interfaz de usuario, guardar los datos o desencadenar otra operación.

### <a name="simple-string-message"></a>Mensaje de cadena simple

El mensaje más simple contiene simplemente una cadena en el `message` parámetro. Un `Subscribe` método que *escucha* para un mensaje de cadena simple se muestra más abajo: tenga en cuenta el tipo genérico especifica que el remitente debe ser del tipo `MainPage`. Las clases en la solución pueden suscribirse al mensaje con esta sintaxis:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

En el `MainPage` el siguiente código de clase *envía* el mensaje. El `this` parámetro es una instancia de `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

No cambia la cadena - indica el *tipo de mensaje* y se usa para determinar qué suscriptores para notificar. Este tipo de mensaje se usa para indicar que se produjo un evento, como "carga completada", donde no se requiere ninguna información adicional.

### <a name="passing-an-argument"></a>Pasar un argumento

Para pasar un argumento con el mensaje, especifique el tipo de argumento en el `Subscribe` argumentos genéricos y en la firma de acción.

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

Para enviar el mensaje con el argumento, incluya el parámetro de tipo genérico y el valor del argumento en el `Send` llamada al método.

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

Este ejemplo utiliza un `string` argumento, pero los C# se puede pasar el objeto.

### <a name="unsubscribe"></a>Cancelar la suscripción

Puede cancelar la suscripción de un objeto a una firma de mensaje para que no se entregan los mensajes futuros. El `Unsubscribe` sintaxis de método debe reflejar la firma del mensaje (por lo que sea necesario incluir el parámetro de tipo genérico para el argumento de mensaje).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Resumen

El MessagingCenter es una manera sencilla de reducir el acoplamiento, especialmente entre los modelos de vista. Se puede usar para enviar y recibir mensajes sencillos o pasar un argumento entre clases. Las clases deben cancelar la suscripción a mensajes que ya no desean recibir.


## <a name="related-links"></a>Vínculos relacionados

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
