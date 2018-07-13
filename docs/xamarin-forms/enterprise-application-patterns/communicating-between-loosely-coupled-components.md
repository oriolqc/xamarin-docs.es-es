---
title: Comunicarse libremente entre los componentes acoplados
description: 'Este capítulo explica cómo la aplicación móvil de eShopOnContainers implementa publish-patrón, que permite la comunicación basada en mensajes entre los componentes que no son convenientes para vincular mediante referencias de objeto y el tipo de suscripción '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: ddc33d28aad4e00c9259893c0f8e7a1ab40ee429
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998549"
---
# <a name="communicating-between-loosely-coupled-components"></a>Comunicarse libremente entre los componentes acoplados

Publish-suscribirse patrón es un patrón de mensajería en la que los editores envían mensajes sin tener conocimiento de los destinatarios, conocido como suscriptores. De forma similar, los suscriptores escuchan mensajes concretos, sin tener conocimiento de los publicadores.

Los eventos de .NET implementan la publicación-patrón de suscripción y es más simple y sencillo enfoque para un nivel de comunicación entre componentes si un acoplamiento no es necesario, como un control y la página que lo contiene. Sin embargo, las duraciones de publicador y suscriptor están acopladas por referencias a objetos entre sí y el tipo de suscriptor debe tener una referencia al tipo de publicador. Esto puede crear la forma en memoria problemas de administración, especialmente cuando hay objetos de corta duración que se suscriben a un evento de un objeto estático o de larga duración. Si no se elimina el controlador de eventos, el suscriptor se mantendrá conectado por la referencia a él en el publicador, y esto se impedir o retrasar la recolección de elementos del suscriptor.

## <a name="introduction-to-messagingcenter"></a>Introducción a MessagingCenter

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) clase implementa la publicación-patrón, que permite la comunicación basada en mensajes entre los componentes que no son convenientes para vincular mediante referencias de objeto y el tipo de suscripción. Este mecanismo permite a los publicadores y suscriptores transmitir sin tener una referencia entre sí, lo que ayuda a reducir las dependencias entre componentes, mientras que permite que los componentes que se desarrollan y prueban de forma independiente.

El [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) clase proporciona multidifusión funcionalidad de publicación y suscripción. Esto significa que puede haber varios publicadores que publican un único mensaje y puede haber varios suscriptores a la escucha para el mismo mensaje. Figura 4-1 se ilustra esta relación:

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Multidifusión funcionalidad de publicación y suscripción")

**Figura 4-1:** multidifusión funcionalidad de publicación y suscripción

Los editores envían mensajes mediante el [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) método, mientras que los suscriptores realizar escuchas de mensajes mediante el [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método. Además, los suscriptores pueden también cancelar la suscripción a suscripciones de mensajes, si es necesario, con el [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) método.

Internamente, el [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) clase usa referencias débiles. Esto significa que no mantendrá objetos activa y les permitirá sea recolectado. Por lo tanto, sólo debería ser necesario cancelar la suscripción a un mensaje cuando una clase ya no desea recibir el mensaje.

La aplicación móvil de eShopOnContainers utiliza la [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) componentes de clase para la comunicación entre débilmente acoplados. La aplicación define tres mensajes:

-   El `AddProduct` mensaje publicado por el `CatalogViewModel` clase cuando se agrega un elemento a la cesta de compra. En cambio, el `BasketViewModel` clase se suscribe al mensaje e incrementa el número de elementos de la cesta de compra en la respuesta. Además, el `BasketViewModel` clase también cancela la suscripción de este mensaje.
-   El `Filter` mensaje publicado por el `CatalogViewModel` clase cuando el usuario aplica un filtro de marca o el tipo a los elementos mostrados en el catálogo. En cambio, el `CatalogView` clase se suscribe al mensaje y actualiza la interfaz de usuario para que se muestren solo los elementos que coinciden con los criterios de filtro.
-   El `ChangeTab` mensaje publicado por el `MainViewModel` clase cuando la `CheckoutViewModel` navega a la `MainViewModel` siguiendo la creación correcta y el envío de un nuevo pedido. En cambio, el `MainView` clase suscribe al mensaje y las actualizaciones de la interfaz de usuario hasta que el **mi perfil** pestaña está activa, para mostrar los pedidos del usuario.

> [!NOTE]
> Mientras el [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) clase permite la comunicación entre las clases de acoplamiento flexible, no ofrece la solución de arquitectura solo a este problema. Por ejemplo, la comunicación entre un modelo de vista y una vista también puede lograrse mediante el motor de enlace y a través de las notificaciones de cambio de propiedad. Además, también puede lograrse la comunicación entre los dos modelos de vista pasando los datos durante la navegación.

En la aplicación móvil de eShopOnContainers,[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) se usa para actualizar en la interfaz de usuario en respuesta a una acción que se producen en otra clase. Por lo tanto, los mensajes se publican en el subproceso de interfaz de usuario, con los suscriptores que reciben el mensaje en el mismo subproceso.

> [!TIP]
> Calcular las referencias al subproceso de interfaz de usuario cuando la interfaz de usuario de realizar actualizaciones. Si es necesario actualizar la interfaz de usuario un mensaje que se envía desde un subproceso en segundo plano, procesar el mensaje en el subproceso de interfaz de usuario en el suscriptor mediante la invocación del [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método.

Para obtener más información acerca de [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter), consulte [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Definir un mensaje

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) los mensajes son cadenas que se usan para identificar los mensajes. El ejemplo de código siguiente muestra los mensajes definidos dentro de la aplicación móvil de eShopOnContainers:

```csharp
public class MessengerKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

En este ejemplo, los mensajes se definen mediante las constantes. La ventaja de este enfoque es que proporciona seguridad de tipos de tiempo de compilación y la compatibilidad de refactorización.

## <a name="publishing-a-message"></a>Publicar un mensaje

Editores de notificar a los suscriptores de un mensaje con uno de los [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) sobrecargas. En el ejemplo de código siguiente se muestra cómo publicar el `AddProduct` mensaje:

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

En este ejemplo, el [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) método especifica tres argumentos:

-   El primer argumento especifica la clase del remitente. La clase del remitente debe especificarse ningún suscriptor que deseen recibir el mensaje.
-   El segundo argumento especifica el mensaje.
-   El tercer argumento especifica los datos de carga para enviarse al suscriptor. En este caso los datos de carga están un `CatalogItem` instancia.

El [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) método va a publicar el mensaje y sus datos de carga, con un enfoque de fire and forget. Por lo tanto, se envía el mensaje incluso si no hay ningún suscriptor registrado para recibir el mensaje. En esta situación, se omite el mensaje enviado.

> [!NOTE]
> El [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) método puede usar los parámetros genéricos para controlar cómo se entregan los mensajes. Por lo tanto, diferentes suscriptores pueden recibir varios mensajes que comparten una identidad de mensaje pero enviar carga útil diferentes tipos de datos.

## <a name="subscribing-to-a-message"></a>Suscribirse a un mensaje

Los suscriptores pueden registrar para recibir un mensaje mediante uno de los [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) sobrecargas. En el ejemplo de código siguiente se muestra cómo se suscribe a la aplicación móvil de eShopOnContainers y procesa, el `AddProduct` mensaje:

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

En este ejemplo, el [ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método se suscribe a la `AddProduct` del mensaje y un delegado de devolución de llamada se ejecuta en respuesta a recibir el mensaje. Este delegado de devolución de llamada, especificado como una expresión lambda, ejecuta el código que actualiza la interfaz de usuario.

> [!TIP]
> Considere el uso de datos de carga inmutable. No intente modificar los datos de carga desde dentro de un delegado de devolución de llamada porque varios subprocesos podrían tener acceso a los datos recibidos al mismo tiempo. En este escenario, los datos de carga deben ser inmutables para evitar errores de simultaneidad.

Un suscriptor no es posible que deba controlar todas las instancias de un mensaje publicado, y esto puede controlarse mediante los argumentos de tipo genérico que se especifican en el [ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método. En este ejemplo, el suscriptor sólo recibirá `AddProduct` los mensajes que se envían desde el `CatalogViewModel` (clase), cuyos datos de carga están un `CatalogItem` instancia.

## <a name="unsubscribing-from-a-message"></a>Cancelación de un mensaje de la suscripción

Los suscriptores pueden cancelar la suscripción a mensajes que ya no desean recibir. Esto se logra con uno de los [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sobrecargas, como se muestra en el ejemplo de código siguiente:

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

En este ejemplo, el [ `Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sintaxis de método refleja los argumentos de tipo especificados al suscribirse para recibir el `AddProduct` mensaje.

## <a name="summary"></a>Resumen

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) clase implementa la publicación-patrón, que permite la comunicación basada en mensajes entre los componentes que no son convenientes para vincular mediante referencias de objeto y el tipo de suscripción. Este mecanismo permite a los publicadores y suscriptores transmitir sin tener una referencia entre sí, lo que ayuda a reducir las dependencias entre componentes, mientras que permite que los componentes que se desarrollan y prueban de forma independiente.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
