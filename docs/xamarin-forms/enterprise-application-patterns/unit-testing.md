---
title: Pruebas unitarias de aplicaciones de Enterprise
description: Este capítulo explica cómo se realiza la prueba unitaria en la aplicación móvil de eShopOnContainers.
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 02aeedd5498c47950e2fbc0d218de05bc0bb3204
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998688"
---
# <a name="unit-testing-enterprise-apps"></a>Pruebas unitarias de aplicaciones de Enterprise

Aplicaciones móviles tienen problemas únicos que escritorio y aplicaciones basadas en web no tienen que preocuparse. Los usuarios móviles variarán los dispositivos que está usando, conectividad de red, la disponibilidad de servicios y un intervalo de otros factores. Por lo tanto, se deben probar aplicaciones móviles que se usará en el mundo real para mejorar su calidad, confiabilidad y rendimiento. Hay muchos tipos de pruebas que deben realizarse en una aplicación, incluidas las pruebas unitarias, pruebas de integración y pruebas, con las pruebas que se va a la forma más común de las pruebas unitarias de interfaz de usuario.

Una prueba unitaria toma una unidad pequeña de la aplicación, normalmente un método, aísla del resto del código y comprueba si se comporta según lo previsto. Su objetivo es comprobar que cada unidad de funcionalidad se ejecuta según lo previsto, así que los errores no propagan a través de la aplicación. Detectar un error en el que ocurre es más eficaz que observar el efecto de un error indirectamente en un punto de error secundario.

Las pruebas unitarias tienen el mayor efecto en la calidad del código cuando es una parte integral del flujo de trabajo de desarrollo de software. Tan pronto como se ha escrito un método, se deben escribir pruebas unitarias que comprueben el comportamiento del método en respuesta a estándar, límite e incorrectos de los casos de datos de entrada y dicha comprobación cualquier suposición explícita o implícita creada por el código. Como alternativa, con el desarrollo controlado por pruebas, pruebas unitarias se escriben antes del código. En este escenario, las pruebas unitarias actúan como documentación de diseño y las especificaciones funcionales.

> [!NOTE]
> Las pruebas unitarias son muy eficaces contra la regresión: es decir, funcionalidad que funcionan pero ha sido ve perturbada por una actualización fallida.

Pruebas unitarias suelen usan el patrón assert para organizar act:

-   El *organizar* sección del método de prueba unitaria inicializa objetos y establece el valor de los datos que se pasan al método sometido a prueba.
-   El *actuar* sección invoca el método sometido a prueba con los argumentos necesarios.
-   El *assert* sección comprueba si la acción del método sometido a prueba se comporta según lo previsto.

Si sigue este patrón garantiza que las pruebas unitarias son legibles y coherente.

## <a name="dependency-injection-and-unit-testing"></a>Inserción de dependencias y las pruebas unitarias

Una de las motivaciones para adoptar una arquitectura de acoplamiento flexible es que facilita las pruebas unitarias. Uno de los tipos registrados con Autofac es la `OrderService` clase. En el ejemplo de código siguiente se muestra un esquema de esta clase:

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

El `OrderDetailViewModel` clase tiene una dependencia en el `IOrderService` escriba que el contenedor resuelve cuando crea una instancia de un `OrderDetailViewModel` objeto. Sin embargo, en lugar de crear un `OrderService` objeto pruebas unitarias para el `OrderDetailViewModel` (clase), en su lugar, reemplace el `OrderService` objeto con una simulación con el fin de las pruebas. Figura 10-1 se ilustra esta relación.

![](unit-testing-images/unittesting.png "Clases que implementan la interfaz IOrderService")

**Figura 10-1:** las clases que implementan la interfaz IOrderService

Este enfoque permite la `OrderService` objeto que se pasan los `OrderDetailViewModel` de clase en tiempo de ejecución y en aras de la capacidad de prueba, permite el `OrderMockService` clase que se pasan la `OrderDetailViewModel` clase en tiempo de prueba. La principal ventaja de este enfoque es que permite que las pruebas unitarias que se ejecutará sin necesidad de difícil de manejar recursos como servicios web o bases de datos.

## <a name="testing-mvvm-applications"></a>Probar las aplicaciones MVVM

Probar los modelos y los modelos de vista de las aplicaciones MVVM es idéntica a las pruebas de otras clases y las mismas herramientas y técnicas, como la prueba unitaria y simulación, se pueden usar. Sin embargo, hay algunos patrones típicos de modelo y las clases de modelo de vista, que pueden beneficiarse de las técnicas de pruebas de unidad específica.

> [!TIP]
> Probar una cosa con cada prueba unitaria. No tener la tentación de hacer que una unidad de prueba más de uno de los aspectos del comportamiento de la unidad de ejercicio. Esto puede dar lugar a las pruebas que son difíciles de leer y actualizar. También puede provocar confusión al interpretar un error.

Los usos de la aplicación móvil de eShopOnContainers [xUnit](https://xunit.github.io/) para realizar las pruebas unitarias, que admite dos tipos diferentes de las pruebas unitarias:

-   Los hechos son las pruebas que están siempre es true, que prueba las condiciones invariantes.
-   Teorías son pruebas que sólo son true para un determinado conjunto de datos.

Las pruebas unitarias incluidas con la aplicación móvil de eShopOnContainers son pruebas de hechos y, por lo que cada método de prueba unitaria está decorada con el `[Fact]` atributo.

> [!NOTE]
> un ejecutor de pruebas ejecuta las pruebas de xUnit. Para ejecutar el ejecutor de pruebas, ejecute el proyecto eShopOnContainers.TestRunner para la plataforma requerida.

### <a name="testing-asynchronous-functionality"></a>Probar la funcionalidad asincrónica

Al implementar el patrón MVVM, los modelos de vista normalmente invocan operaciones en servicios, a menudo forma asincrónica. Pruebas para el código que invoca normalmente estas operaciones utilizan objetos ficticios como reemplazos para los servicios reales. El siguiente ejemplo de código muestra cómo probar la funcionalidad asincrónica al pasar de un servicio ficticio a un modelo de vista:

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

Esta prueba unitaria que comprueba la `Order` propiedad de la `OrderDetailViewModel` instancia tendrá un valor después de la `InitializeAsync` ha invocado al método. El `InitializeAsync` método se invoca cuando se navega vista correspondiente del modelo de vista. Para obtener más información sobre la navegación, consulte [navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md).

Cuando el `OrderDetailViewModel` se crea una instancia, espera un `OrderService` instancia que se especifique como argumento. Sin embargo, el `OrderService` recupera datos de un servicio web. Por lo tanto, un `OrderMockService` instancia, que es una versión ficticia de la `OrderService` de clase, se especifica como argumento para el `OrderDetailViewModel` constructor. Después, cuando el modelo de vista `InitializeAsync` se invoca el método, que invoca `IOrderService` operaciones, datos simulados están recuperado en lugar de establecer la comunicación con un servicio web.

### <a name="testing-inotifypropertychanged-implementations"></a>Probar las implementaciones de INotifyPropertyChanged

Implementar el `INotifyPropertyChanged` interfaz permite que las vistas para reaccionar ante los cambios que se originan en la vista de modelos y los modelos. Estos cambios no se limitan a los datos que se muestran en los controles, también se usan para controlar la vista, como los Estados de modelo de vista que hacen que se puede iniciar las animaciones o controles va a deshabilitar.

Se pueden probar las propiedades que se pueden actualizar directamente por la prueba unitaria adjuntando un controlador de eventos para el `PropertyChanged` eventos y comprobar si el evento se genera después de establecer un nuevo valor para la propiedad. El ejemplo de código siguiente muestra una prueba de este tipo:

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

Esta prueba unitaria invoca el `InitializeAsync` método de la `OrderViewModel` clase, lo que hace que su `Order` propiedad que se va a actualizarse. Pasará la prueba unitaria, siempre que el `PropertyChanged` evento se desencadena para el `Order` propiedad.

### <a name="testing-message-based-communication"></a>Probar la comunicación basada en mensajes

Vista de modelos que usan el [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) clase para la comunicación entre el acoplamiento de clases puede unidad probarse suscribiéndose a los mensajes enviados por el código sometido a prueba, como se muestra en el ejemplo de código siguiente:

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

Esta prueba unitaria que comprueba la `CatalogViewModel` publica el `AddProduct` mensaje de respuesta a su `AddCatalogItemCommand` que se está ejecutando. Dado que el [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) clase es compatible con suscripciones de mensajes de multidifusión, la prueba unitaria puede suscribirse a la `AddProduct` del mensaje y ejecutar un delegado de devolución de llamada en respuesta a recibirlo. Este delegado de devolución de llamada, especificado como una expresión lambda, Establece una `boolean` campo utilizado por el `Assert` instrucción para comprobar el comportamiento de la prueba.

### <a name="testing-exception-handling"></a>Las pruebas de control de excepciones

Pruebas unitarias también se pueden escribir esa comprobación que se producen excepciones específicas de las acciones no válidas o entradas, como se muestra en el ejemplo de código siguiente:

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

Esta prueba unitaria iniciará una excepción, porque el [ `ListView` ](xref:Xamarin.Forms.ListView) control no tiene un evento denominado `OnItemTapped`. El `Assert.Throws<T>` método es un método genérico donde `T` es el tipo de la excepción esperada. El argumento pasado a la `Assert.Throws<T>` método es una expresión lambda que producirá la excepción. Por lo tanto, pasará la prueba unitaria siempre que la expresión lambda produce una `ArgumentException`.

>💡 **Sugerencia**: evitar escribir pruebas unitarias que examine las cadenas de mensaje de excepción. Las cadenas de mensaje de excepción pueden cambiar con el tiempo y, por lo que se consideran las pruebas unitarias que se basan en su presencia frágiles.

### <a name="testing-validation"></a>Las pruebas de validación

Hay dos aspectos a la implementación de la validación de pruebas: pruebas que se haya implementado correctamente las reglas de validación y las pruebas que el `ValidatableObject<T>` clase se realiza según lo previsto.

Lógica de validación es normalmente más simple probar, porque normalmente es un proceso independiente, donde el resultado depende de la entrada. Debe haber pruebas en los resultados de invocar el `Validate` método en cada propiedad que tiene al menos una regla de validación asociado, como se muestra en el ejemplo de código siguiente:

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

Esta prueba unitaria comprueba que la validación es correcta cuando los dos `ValidatableObject<T>` propiedades en el `MockViewModel` instancia ambos tienen datos.

Así como la comprobación de que la validación se realiza correctamente, las pruebas unitarias de validación también deberían comprobar los valores de la `Value`, `IsValid`, y `Errors` propiedad de cada uno `ValidatableObject<T>` instancia, para comprobar que la clase se realiza según lo previsto. El ejemplo de código siguiente muestra una prueba unitaria que se hace esto:

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

Esta prueba unitaria comprueba que falla la validación cuando el `Surname` propiedad de la `MockViewModel` no tiene ningún dato y el `Value`, `IsValid`, y `Errors` propiedad de cada uno `ValidatableObject<T>` instancia se han definido correctamente.

## <a name="summary"></a>Resumen

Una prueba unitaria toma una unidad pequeña de la aplicación, normalmente un método, aísla del resto del código y comprueba si se comporta según lo previsto. Su objetivo es comprobar que cada unidad de funcionalidad se ejecuta según lo previsto, así que los errores no propagan a través de la aplicación.

El comportamiento de un objeto sometido a prueba se puede aislar mediante la sustitución de los objetos dependientes con objetos ficticios que simulan el comportamiento de los objetos dependientes. Esto permite que las pruebas unitarias que se ejecutará sin necesidad de difícil de manejar recursos como servicios web o bases de datos.

Probar los modelos y los modelos de vista de las aplicaciones MVVM es idéntica a las pruebas de otras clases, y se pueden usar las mismas herramientas y técnicas.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
