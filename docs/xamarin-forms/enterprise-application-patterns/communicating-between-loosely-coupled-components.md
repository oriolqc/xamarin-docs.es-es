---
title: "Comunicación entre débilmente acoplados componentes"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: e05cd0ec7d03a033e24dcbfb8124cfc2ccfa438e
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="communicating-between-loosely-coupled-components"></a>Comunicación entre débilmente acoplados componentes

La publicación-suscribirse patrón es un patrón de mensajería en la que publicadores envían mensajes sin tener conocimiento de los destinatarios, conocido como suscriptores. De forma similar, los suscriptores escuchan mensajes específicos, sin necesidad de conocimiento de todos los publicadores.

Los eventos de .NET implementan la publicación-suscribirse patrón y son más sencilla y directa enfoque para una capa de comunicación entre los componentes si un acoplamiento no es necesario, como un control y la página que lo contiene. Sin embargo, las duraciones de publicador y el suscriptor están acopladas por referencias a objetos entre sí, y el tipo de suscriptor debe tener una referencia al tipo de publicador. Esto puede crear la forma en memoria problemas de administración, especialmente cuando hay objetos de corta duración que se suscriben a un evento de un objeto estático o de larga duración. Si no se quita el controlador de eventos, el suscriptor se mantendrá activo por la referencia a ella en el publicador, y esto se impedir o retrasar la colección de elementos no utilizados del suscriptor.

## <a name="introduction-to-messagingcenter"></a>Introducción a MessagingCenter

El Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) clase implementa la publicación-suscribirse patrón, que permita la comunicación basada en mensajes entre los componentes que son poco práctico vincular por referencias de objeto y el tipo. Este mecanismo permite publicadores y suscriptores de comunicarse sin necesidad de una referencia a entre sí, lo que ayuda a reducir las dependencias entre los componentes, mientras que permite también componentes desarrollado y probado por separado.

El [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) clase proporciona multidifusión funcionalidad de publicación y suscripción. Esto significa que puede haber varios publicadores que publican un único mensaje y puede haber varios suscriptores que realice escuchas para el mismo mensaje. Figura 4-1 se ilustra esta relación:

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Multidifusión funcionalidad de publicación y suscripción")

**Figura 4-1:** multidifusión funcionalidad de publicación y suscripción

Editores de envían mensajes mediante el [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender%7D/p/TSender/System.String/) método, mientras que los suscriptores realizar escuchas de mensajes mediante la [ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) método. Además, los suscriptores también cancelar la suscripción de suscripciones de mensajes, si es necesario, con el [ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender%7D/p/System.Object/System.String/) método.

Internamente, la [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) clase usa referencias débiles. Esto significa que no mantendrá objetos activo y le permitan haya sido recolectado. Por lo tanto, sólo será necesario cancelar la suscripción de un mensaje cuando una clase ya no desea recibir el mensaje.

Los usos de la aplicación móvil eShopOnContainers el [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) componentes de acoplamiento de clase para la comunicación entre flexible. La aplicación define tres mensajes:

-   El `AddProduct` mensaje se publica mediante el `CatalogViewModel` clase cuando se agrega un elemento a la cesta de compra. En cambio, la `BasketViewModel` clase se suscribe al mensaje e incrementa el número de elementos de la cesta de compra en la respuesta. Además, la `BasketViewModel` clase también elimina la suscripción de este mensaje.
-   El `Filter` mensaje se publica mediante el `CatalogViewModel` clase cuando el usuario aplica un filtro de la marca o el tipo a los elementos mostrados en el catálogo. En cambio, la `CatalogView` clase se suscribe al mensaje y actualiza la interfaz de usuario para que se muestren solo los elementos que coinciden con los criterios de filtro.
-   El `ChangeTab` mensaje se publica mediante el `MainViewModel` clase cuando la `CheckoutViewModel` navega hasta la `MainViewModel` después de la creación correcta y el envío de un nuevo pedido. En cambio, el `MainView` clase suscribe al mensaje y las actualizaciones de la interfaz de usuario hasta que la **mi perfil** pestaña está activa, para mostrar los pedidos del usuario.

> [!NOTE]
> Mientras el [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) clase permite la comunicación entre las clases de acoplamiento flexible, no ofrece la arquitectura solo para solucionar este problema. Por ejemplo, la comunicación entre un modelo de vista y una vista también se consigue mediante el motor de enlace y a través de las notificaciones de cambio de propiedad. Además, la comunicación entre los dos modelos de vista también se puede lograr pasando datos durante la navegación.

En la aplicación móvil eShopOnContainers,[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) se utiliza para actualizar en la interfaz de usuario en respuesta a una acción que se produce en otra clase. Por lo tanto, los mensajes se publican en el subproceso de interfaz de usuario, con los suscriptores que reciben el mensaje en el mismo subproceso.

> [!TIP]
> Calcular las referencias al subproceso de interfaz de usuario al realizar la interfaz de usuario de actualizaciones. Si un mensaje que se envía desde un subproceso en segundo plano es necesario para actualizar la interfaz de usuario, procesar el mensaje en el subproceso de interfaz de usuario en el suscriptor mediante la invocación de la [ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/) método.

Para obtener más información acerca de [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/), consulte [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Definir un mensaje

[`MessagingCenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) los mensajes son cadenas que se usan para identificar los mensajes. En el ejemplo de código siguiente se muestra los mensajes definidos dentro de la aplicación móvil eShopOnContainers:

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

En este ejemplo, los mensajes se definen mediante constantes. La ventaja de este enfoque es que proporciona seguridad de tipos de tiempo de compilación y la compatibilidad con la refactorización.

## <a name="publishing-a-message"></a>Publicar un mensaje

Editores de notificar a los suscriptores de un mensaje con uno de los [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) sobrecargas. En el ejemplo de código siguiente se muestra la publicación el `AddProduct` mensaje:

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

En este ejemplo, el [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) método especifica tres argumentos:

-   El primer argumento especifica la clase del remitente. La clase del remitente debe especificarse ningún suscriptor que deseen recibir el mensaje.
-   El segundo argumento especifica el mensaje.
-   El tercer argumento especifica los datos de carga que se enviarán al suscriptor. En este caso los datos de carga están un `CatalogItem` instancia.

El [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) método publicará el mensaje y sus datos de carga mediante un enfoque de enviar y olvidarse. Por lo tanto, se envía el mensaje incluso si no hay ningún suscriptor registrado para recibir el mensaje. En esta situación, se omite el mensaje enviado.

> [!NOTE]
> El [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) método puede utilizar parámetros genéricos para controlar cómo se entregan los mensajes. Por lo tanto, se pueden recibir varios mensajes que comparten una identidad de mensaje y tipos de datos de carga útil diferentes se envían a distintos suscriptores.

## <a name="subscribing-to-a-message"></a>Suscribirse a un mensaje

Los suscriptores pueden registrarse para recibir un mensaje mediante uno de los [ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) sobrecargas. En el ejemplo de código siguiente se muestra cómo se suscribe a la aplicación móvil eShopOnContainers y procesa, el `AddProduct` mensaje:

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

En este ejemplo, el [ `Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) método se suscribe a la `AddProduct` el mensaje y ejecuta un delegado de devolución de llamada en respuesta a recibir el mensaje. Este delegado de devolución de llamada, que se especifica como una expresión lambda, ejecuta código que actualice la interfaz de usuario.

> [!TIP]
> Considere el uso de datos de carga inmutable. No intente modificar los datos de carga de un delegado de devolución de llamada porque hay varios subprocesos, pudieron acceder a los datos recibidos al mismo tiempo. En este escenario, los datos de carga deben ser inmutables para evitar errores de simultaneidad.

Un suscriptor que no precise controlar todas las instancias de un mensaje publicado, y esto puede controlarse mediante los argumentos de tipo genérico que se especifican en el [ `Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/) método. En este ejemplo, el suscriptor sólo recibirá `AddProduct` mensajes que se envían desde el `CatalogViewModel` (clase), cuyos datos de carga están un `CatalogItem` instancia.

## <a name="unsubscribing-from-a-message"></a>Cancelación de un mensaje de la suscripción

Los suscriptores pueden cancelar la suscripción a mensajes que ya no desean recibir. Esto se logra con uno de los [ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/) sobrecargas, como se muestra en el ejemplo de código siguiente:

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

En este ejemplo, el [ `Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/) sintaxis de método refleja los argumentos de tipo especificados al suscribirse para recibir el `AddProduct` mensaje.

## <a name="summary"></a>Resumen

El Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) clase implementa la publicación-suscribirse patrón, que permita la comunicación basada en mensajes entre los componentes que son poco práctico vincular por referencias de objeto y el tipo. Este mecanismo permite publicadores y suscriptores de comunicarse sin necesidad de una referencia a entre sí, lo que ayuda a reducir las dependencias entre los componentes, mientras que permite también componentes desarrollado y probado por separado.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
