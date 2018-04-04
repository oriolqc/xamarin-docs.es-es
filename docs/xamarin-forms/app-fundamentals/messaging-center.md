---
title: MessagingCenter
description: Xamarin.Forms incluye un simple servicio de mensajería para enviar y recibir mensajes.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: e8145847f3c91683aa7433208d70198998cda87f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="messagingcenter"></a>MessagingCenter

_Xamarin.Forms incluye un simple servicio de mensajería para enviar y recibir mensajes._

<a name="Overview" />

## <a name="overview"></a>Información general

Xamarin.Forms `MessagingCenter` permite ver los modelos y otros componentes se comuniquen sin necesidad de saber nada sobre entre sí además de un contrato de mensaje simple.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Cómo funciona la MessagingCenter

Hay dos partes para `MessagingCenter`:

-  **Suscribirse** : realizar escuchas de mensajes con una firma determinada y realizar alguna acción cuando se reciben. Pueden escuchar varios suscriptores para el mismo mensaje.
-  **Enviar** -publicar un mensaje para actuar sobre los agentes de escucha. Si no se ha suscrito a ningún agente de escucha se omite el mensaje.


El `MessagingService` es una clase estática con `Subscribe` y `Send` métodos que se usan en toda la solución.

Mensajes tienen una cadena `message` parámetro que se utiliza como forma de *dirección* mensajes. El `Subscribe` y `Send` métodos usan parámetros genéricos para controlar aún más el modo en que se entregan los mensajes: dos mensajes con el mismo `message` texto pero los argumentos de tipo genérico diferente no se entregará al mismo suscriptor.

La API de `MessagingCenter` es simple:

-  Suscribirse&lt;TSender > (suscriptor de objeto, la acción de mensaje de cadena&lt;TSender > devolución de llamada, el origen de TSender = null)
-  Suscribirse&lt;TSender, TArgs > (suscriptor de objeto, la acción de mensaje de cadena&lt;TSender, TArgs > devolución de llamada, el origen de TSender = null)
-  Enviar&lt;TSender > (TSender remitente, mensaje de cadena)
-  Enviar&lt;TSender, TArgs > (TSender remitente, el mensaje de cadena, TArgs args)
-  Cancelar la suscripción&lt;TSender, TArgs > (suscriptor de objeto, mensaje de cadena)
-  Cancelar la suscripción&lt;TSender > (suscriptor de objeto, mensaje de cadena)


A continuación se explican estos métodos.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Uso de la MessagingCenter

Como resultado de la interacción del usuario (por ejemplo, haga clic en un botón), un evento del sistema (por ejemplo, cambiar el estado de los controles) o algún otro incidente (por ejemplo, una finalización de descarga asincrónica), se pueden enviar mensajes. Los suscriptores podrían estar escuchando para cambiar la apariencia de la interfaz de usuario, guardar los datos o desencadenar otra operación.

### <a name="simple-string-message"></a>Mensaje de cadena simple

El mensaje más simple contiene una cadena en el `message` parámetro. A `Subscribe` método que *escucha* para un mensaje de cadena simple se muestra a continuación: tenga en cuenta el tipo genérico debe indicarse al remitente debe ser del tipo `MainPage`. Todas las clases de la solución pueden suscribirse a los mensajes con esta sintaxis:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

En el `MainPage` el siguiente código de la clase *envía* el mensaje. El `this` parámetro es una instancia de `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

No cambia la cadena - indica el *tipo de mensaje* y se utiliza para determinar qué suscriptores para notificar. Este tipo de mensaje se usa para indicar que se produjo un evento, como "carga completado", donde no se requiere ninguna información adicional.

### <a name="passing-an-argument"></a>Pasar un argumento

Para pasar un argumento con el mensaje, especifique el argumento de tipo en el `Subscribe` argumentos genéricos y en la firma de acción.

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

Este sencillo ejemplo se usa un `string` se puede pasar el argumento, pero cualquier objeto de C#.

### <a name="unsubscribe"></a>Cancelar la suscripción

Un objeto puede cancelar la suscripción a una firma del mensaje para que no se entrega ningún mensaje futuras. El `Unsubscribe` sintaxis de método deben reflejar la firma del mensaje (por lo que sea necesario incluir el parámetro de tipo genérico para el argumento de mensaje).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Resumen

El MessagingCenter es una manera sencilla de reducir el acoplamiento, especialmente entre modelos de vista. Se puede utilizar para enviar y recibir mensajes simples o pasar un argumento entre clases. Clases deben cancelar la suscripción a mensajes que ya no desean recibir.


## <a name="related-links"></a>Vínculos relacionados

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
