---
title: Pruebas unitarias
ms.topic: article
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0842ce24139da5d963e2c528b440e6592d5910f8
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="unit-testing"></a>Pruebas unitarias

Aplicaciones móviles tengan problemas únicos que aplicaciones de escritorio y basada en web no tienen que preocuparse. Los usuarios móviles variarán los dispositivos que se utilicen, conectividad de red, por la disponibilidad de servicios y un intervalo de otros factores. Por lo tanto, deberán probarse aplicaciones móviles que se usará en el mundo real para mejorar su calidad, confiabilidad y rendimiento. Hay muchos tipos de pruebas que deben realizarse en una aplicación, incluidas las pruebas unitarias, pruebas de integración y pruebas con pruebas que se va a la forma más común de las pruebas unitarias de interfaz de usuario.

Una prueba unitaria toma una unidad pequeña de la aplicación, por lo general, un método, aísla del resto del código y comprueba si se comporta según lo esperado. Su objetivo es comprobar que cada unidad de funcionalidad lleva a cabo según lo esperado, por lo que no propagan los errores de la aplicación. Detectar un error en el que ocurre es más eficaz que observar el efecto de un error indirectamente en un punto de error secundario.

Pruebas unitarias tienen el mayor efecto en la calidad del código cuando son parte integral del flujo de trabajo de desarrollo de software. Tan pronto como se ha escrito un método, se deben escribir pruebas unitarias que comprueben el comportamiento del método en respuesta a estándar, límite e incorrectos de los casos de datos de entrada así como comprobación cualquier suposición explícita o implícita creada por el código. O bien, con el desarrollo controlado por pruebas, se escriben pruebas unitarias antes del código. En este escenario, pruebas unitarias actúan como documentación de diseño y las especificaciones funcionales.

> [!NOTE]
> Las pruebas unitarias son muy eficaces en regresión: es decir, la funcionalidad que utiliza para trabajar, pero se se ven afectada por una actualización defectuosa.

Pruebas unitarias suele usan el patrón assert para organizar act:

-   El *organizar* sección del método de prueba unitaria inicializa objetos y establece el valor de los datos que se pasan al método sometida a prueba.
-   El *actuar* sección, invoca el método en pruebas con los argumentos necesarios.
-   El *assert* sección comprueba si la acción del método en pruebas se comporta según lo esperado.

Si sigue este patrón garantiza que las pruebas unitarias son legibles y coherente.

## <a name="dependency-injection-and-unit-testing"></a>Inserción de dependencias y las pruebas unitarias

Una de las motivaciones para la adopción de una arquitectura de acoplamiento flexible es que facilita la prueba unitaria. Uno de los tipos registrados con Autofac es la `OrderService` clase. En el ejemplo de código siguiente se muestra un esquema de esta clase:

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

El `OrderDetailViewModel` clase tiene una dependencia en el `IOrderService` escriba que el contenedor resuelve cuando crea una instancia de un `OrderDetailViewModel` objeto. Sin embargo, en lugar de crear un `OrderService` objeto de la prueba unitaria el `OrderDetailViewModel` (clase), en su lugar, reemplace la `OrderService` objeto con un simulacro con el fin de las pruebas. Figura 10-1 se ilustra esta relación.

![](unit-testing-images/unittesting.png "Clases que implementan la interfaz IOrderService")

**Figura 10-1:** las clases que implementan la interfaz IOrderService

Este enfoque permite la `OrderService` objeto que se pasan a la `OrderDetailViewModel` de clase en tiempo de ejecución y en aras de la capacidad de prueba, permite el `OrderMockService` clase que se pasan a la `OrderDetailViewModel` clase en tiempo de la prueba. La principal ventaja de este enfoque es que permite pruebas unitarias que se ejecute sin necesidad de difícil de manejar recursos como servicios web o bases de datos.

## <a name="testing-mvvm-applications"></a>Probar aplicaciones de MVVM

Probar modelos y ver modelos desde aplicaciones de MVVM es idéntico a otras clases de las pruebas y las mismas herramientas y técnicas, como la prueba unitaria y de simulación, pueden utilizarse. Sin embargo, hay determinados patrones que son típicos al modelo y clases de modelo de vista, que se pueden beneficiar de técnicas de pruebas de unidad específica.

> [!TIP]
> Probar algo con cada prueba unitaria. No se sienta tentado a hacer una unidad prueba ejercicio más de uno de los aspectos del comportamiento de la unidad. Si lo hace, conduce a pruebas que son difíciles de leer y actualizar. También se puede producir confusión al interpretar un error.

Los usos de la aplicación móvil eShopOnContainers [xUnit](https://xunit.github.io/) para realizar pruebas unitarias, que admite dos tipos diferentes de pruebas unitarias:

-   Los hechos son pruebas que están siempre es true, que prueban condiciones invariables.
-   Teorías son pruebas que solo son verdaderos para un determinado conjunto de datos.

Las pruebas unitarias incluidas con la aplicación móvil eShopOnContainers son hechos pruebas y, por lo que cada método de prueba unitaria se decora con el `[Fact]` atributo.

> [!NOTE]
> un ejecutor de pruebas se ejecutan pruebas de xUnit. Para ejecutar el ejecutor de pruebas, ejecute el proyecto eShopOnContainers.TestRunner para la plataforma requerida.

### <a name="testing-asynchronous-functionality"></a>Probando la funcionalidad asincrónica

Al implementar el patrón MVVM, ver modelos normalmente invocación operaciones en servicios, a menudo asincrónicamente. Pruebas para el código que invoca normalmente estas operaciones utilizan simulacros como reemplazos para los servicios reales. En el ejemplo de código siguiente se muestra una comprobación funcionalidad asincrónica pasando un servicio ficticio en un modelo de vista:

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

Esta prueba unitaria comprueba que la `Order` propiedad de la `OrderDetailViewModel` instancia tendrá un valor después de la `InitializeAsync` se ha invocado el método. El `InitializeAsync` método se invoca cuando se abrirá la vista correspondiente del modelo de vista. Para obtener más información sobre la navegación, consulte [navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md).

Cuando el `OrderDetailViewModel` se crea la instancia, espera un `OrderService` instancia que se especifique como argumento. Sin embargo, la `OrderService` recupera datos de un servicio web. Por lo tanto, un `OrderMockService` instancia, que es una versión ficticia de la `OrderService` clase, se especifica como argumento para el `OrderDetailViewModel` constructor. A continuación, cuando el modelo de vista `InitializeAsync` se invoca el método, que invoca `IOrderService` operaciones, datos simulados están recuperado en lugar de establecer la comunicación con un servicio web.

### <a name="testing-inotifypropertychanged-implementations"></a>Implementaciones de prueba INotifyPropertyChanged

Implementar el `INotifyPropertyChanged` interfaz permite vistas reaccione ante los cambios que se originan en la vista de modelos y los modelos. Estos cambios no se limitan a los datos que se muestran en los controles, también se usan para controlar la vista, como los Estados del modelo de vista que hacen que las animaciones se inicie o controles que se deshabilite.

Propiedades que se pueden actualizar directamente por la prueba unitaria se pueden probar si se adjunta un controlador de eventos para el `PropertyChanged` eventos y comprobar si el evento se desencadena después de establecer un nuevo valor para la propiedad. En el ejemplo de código siguiente se muestra una prueba de este tipo:

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

Esta prueba unitaria, se invoca el `InitializeAsync` método de la `OrderViewModel` de la clase, lo que hace que su `Order` propiedad que se va a actualizar. La prueba unitaria pasará, siempre que el `PropertyChanged` evento se desencadena para el `Order` propiedad.

### <a name="testing-message-based-communication"></a>Comunicación basada en mensajes de prueba

Vista de modelos que usan la [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) clase para comunicarse entre clases de acoplamiento unidad para probar, suscripción al mensaje que se va a enviar el código sometido a prueba, como se muestra en el ejemplo de código siguiente:

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

Esta prueba unitaria comprueba que la `CatalogViewModel` publica el `AddProduct` mensaje en respuesta a su `AddCatalogItemCommand` que se está ejecutando. Dado que la [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) clase es compatible con suscripciones de mensajes de multidifusión, la prueba unitaria puede suscribirse a la `AddProduct` el mensaje y un delegado de devolución de llamada se ejecutan como respuesta a los receptores. Este delegado de devolución de llamada, que se especifica como una expresión lambda, Establece una `boolean` campo utilizado por el `Assert` instrucción para comprobar el comportamiento de la prueba.

### <a name="testing-exception-handling"></a>Probar el control de excepciones

Pruebas unitarias también pueden escribirse comprobación que se producen excepciones específicas para acciones no válidas o entradas, como se muestra en el ejemplo de código siguiente:

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

Esta prueba unitaria iniciará una excepción, porque la [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) control no tiene un evento denominado `OnItemTapped`. El `Assert.Throws<T>` método es un método genérico donde `T` es el tipo de la excepción esperada. El argumento pasado a la `Assert.Throws<T>` método es una expresión lambda que produce la excepción. Por lo tanto, la prueba unitaria pasará siempre que la expresión lambda produce una `ArgumentException`.

>💡 **Sugerencia**: evitar escribir pruebas unitarias que examine las cadenas de mensaje de excepción. Las cadenas de mensajes de excepción pueden cambiar con el tiempo y, por lo que se consideran pruebas unitarias que se basan en su presencia frágiles.

### <a name="testing-validation"></a>Probar la validación

Hay dos aspectos para probar la implementación de la validación: prueba que se haya implementado correctamente las reglas de validación y las pruebas que el `ValidatableObject<T>` clase realiza según lo previsto.

Lógica de validación es normalmente simple probar, porque normalmente es un proceso independiente que el resultado depende de la entrada. Debe haber pruebas en los resultados de invocar el `Validate` método en cada propiedad que tiene al menos una regla de validación asociados, como se muestra en el ejemplo de código siguiente:

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

Pruebas unitarias de validación, así como la comprobación de que la validación se realiza correctamente, deben comprobar los valores de la `Value`, `IsValid`, y `Errors` propiedad de cada `ValidatableObject<T>` instancia, para comprobar que la clase se realiza según lo previsto. En el ejemplo de código siguiente se muestra una prueba unitaria que lo haga:

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

Esta prueba unitaria comprueba que falla la validación cuando el `Surname` propiedad de la `MockViewModel` no tiene ningún dato y el `Value`, `IsValid`, y `Errors` propiedad de cada `ValidatableObject<T>` instancia se han definido correctamente.

## <a name="summary"></a>Resumen

Una prueba unitaria toma una unidad pequeña de la aplicación, por lo general, un método, aísla del resto del código y comprueba si se comporta según lo esperado. Su objetivo es comprobar que cada unidad de funcionalidad lleva a cabo según lo esperado, por lo que no propagan los errores de la aplicación.

El comportamiento de un objeto sometido a prueba puede aislarse reemplazando los objetos dependientes con objetos ficticios que simulan el comportamiento de los objetos dependientes. Esto permite pruebas unitarias que se ejecute sin necesidad de difícil de manejar recursos como servicios web o bases de datos.

Probar modelos y ver modelos desde aplicaciones de MVVM es idéntico a otras clases de las pruebas, y pueden utilizarse las mismas herramientas y técnicas.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
