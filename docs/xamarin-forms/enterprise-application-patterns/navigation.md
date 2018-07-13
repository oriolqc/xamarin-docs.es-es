---
title: Navegación de la aplicación de empresa
description: Este capítulo explica cómo la aplicación móvil de eShopOnContainers realiza la navegación de model first de vista de modelos de vista.
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d306b0c1c0d08129671e27b96911ec771acb658e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994775"
---
# <a name="enterprise-app-navigation"></a>Navegación de la aplicación de empresa

Xamarin.Forms incluye compatibilidad con la navegación de página, que normalmente da como resultado de la interacción del usuario con la interfaz de usuario o de la aplicación como resultado de los cambios de estado controlado por la lógica interna. Sin embargo, navegación puede ser difícil de implementar en las aplicaciones que usan el patrón Model-View-ViewModel (MVVM), tal como se deben cumplir los siguientes desafíos:

-   Cómo identificar la vista para navegar, mediante un enfoque que no introduce acoplamiento estrecho y las dependencias entre las vistas.
-   Describe cómo coordinar el proceso por el que se crea una instancia e inicializa la vista para navegar. Al usar MVVM, la vista y el modelo de vista deben crear una instancia y asociados entre sí a través del contexto de enlace de la vista. Cuando una aplicación usa un contenedor de inserción de dependencia, la creación de instancias de las vistas y los modelos de vista podría requerir un mecanismo específico de construcción.
-   Si se debe realizar la navegación a primera vista, o ver navegación model first. Con la navegación de la vista en primer lugar, para navegar a la página de hace referencia al nombre del tipo de vista. Durante la navegación, se crea la vista especificada, junto con su modelo de vista correspondiente y otros servicios dependientes. Un enfoque alternativo es usar el panel de navegación view model first, donde la página para ir a hace referencia al nombre del tipo de modelo de vista.
-   ¿Cómo a claramente separar el comportamiento de navegación de la aplicación a través de las vistas y los modelos de vista. El patrón MVVM proporciona una separación entre la interfaz de usuario de la aplicación y su presentación y lógica de negocios. Sin embargo, el comportamiento de navegación de una aplicación a menudo abarcará los elementos de interfaz de usuario y presentaciones de la aplicación. El usuario a menudo iniciará la exploración de una vista, y se reemplazará la vista como resultado de la barra de navegación. Sin embargo, navegación a menudo también podría necesitar que se inició o coordinadas desde dentro del modelo de vista.
-   Cómo pasar parámetros durante la navegación por motivos de inicialización. Por ejemplo, si el usuario navega a una vista para actualizar los detalles de pedido, los datos del pedido tendrá que pasarse a la vista para que pueda mostrar los datos correctos.
-   Cómo navegación coordinarse, para asegurarse de que determinadas reglas empresariales son obedecer. Por ejemplo, es posible que se le pida a los usuarios antes de salir de una vista para que se pueden corregir los datos no válidos o se le pida que envíe o descarte los cambios de datos que se realizaron en la vista.

Este capítulo aborda estos retos presentando un `NavigationService` clase que se usa para realizar la navegación de páginas de model first de vista.

> [!NOTE]
> El `NavigationService` utilizado por la aplicación está diseñada únicamente para realizar la navegación jerárquica entre instancias de ContentPage. Uso del servicio para navegar entre otros tipos de páginas podría producir un comportamiento inesperado.

## <a name="navigating-between-pages"></a>Navegar entre páginas

Lógica de navegación puede residir en el código subyacente de una vista o en un datos enlaza el modelo de vista. Aunque colocar lógica de navegación en una vista puede ser el enfoque más sencillo, no es probar fácilmente a través de las pruebas unitarias. Colocar en la vista lógica de navegación clases de modelo significa que la lógica puede realizarse a través de las pruebas unitarias. Además, el modelo de vista, a continuación, puede implementar la lógica para controlar el desplazamiento para garantizar que se apliquen determinadas reglas empresariales. Por ejemplo, una aplicación no es posible que permite al usuario a salir de una página sin primero lo que garantiza que los datos introducidos son válidos.

Un `NavigationService` clase normalmente se invoca desde modelos de vista, para promover la capacidad de prueba. Sin embargo, navegar a vistas de los modelos de vista requeriría los modelos de vista a las vistas de referencia y especialmente las vistas que no está asociado, lo que no se recomienda el modelo de vista activa. Por lo tanto, el `NavigationService` presentado aquí se especifica el tipo de modelo de vista como destino para navegar a.

La aplicación móvil de eShopOnContainers utiliza la `NavigationService` clase para proporcionar navegación model first de vista. Esta clase implementa la `INavigationService` interfaz, que se muestra en el ejemplo de código siguiente:

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

Esta interfaz especifica que una clase de implementación debe proporcionar los métodos siguientes:

|Método|Propósito|
|--- |--- |
|`InitializeAsync`|Realiza la navegación a uno de dos páginas cuando se inicia la aplicación.|
|`NavigateToAsync`|Realiza la navegación jerárquica en una página especificada.|
|`NavigateToAsync(parameter)`|Realiza la navegación jerárquica en una página especificada, pasando un parámetro.|
|`RemoveLastFromBackStackAsync`|Quita la página anterior de la pila de navegación.|
|`RemoveBackStackAsync`|Quita todas las páginas anteriores de la pila de navegación.|

Además, el `INavigationService` interfaz especifica que una clase de implementación debe proporcionar un `PreviousPageViewModel` propiedad. Esta propiedad devuelve el tipo de modelo de vista asociado a la página anterior en la pila de navegación.

> [!NOTE]
> Un `INavigationService` interfaz sería normalmente también especificar una `GoBackAsync` método, que se usa para devolver mediante programación a la página anterior en la pila de navegación. Sin embargo, este método es que faltan desde la aplicación móvil de eShopOnContainers porque no es necesario.

### <a name="creating-the-navigationservice-instance"></a>Creación de la instancia NavigationService

El `NavigationService` clase que implementa el `INavigationService` de la interfaz, se registra como un singleton con el contenedor de inserción de dependencia de Autofac, como se muestra en el ejemplo de código siguiente:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

El `INavigationService` interfaz se resuelve en el `ViewModelBase` constructor de clase, como se muestra en el ejemplo de código siguiente:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Esto devuelve una referencia a la `NavigationService` objeto que se almacena en el contenedor de inserción de dependencia de Autofac, que es creado por el `InitNavigation` método en el `App` clase. Para obtener más información, consulte [navegar cuando la aplicación se inicia](#navigating_when_the_app_is_launched).

El `ViewModelBase` clase almacena el `NavigationService` de instancia en un `NavigationService` propiedad de tipo `INavigationService`. Por lo tanto, todos ver las clases de modelo, que se derivan de la `ViewModelBase` de clases, puede usar el `NavigationService` propiedad para tener acceso a los métodos especificados por el `INavigationService` interfaz. Esto evita la sobrecarga de insertar el `NavigationService` objeto desde el contenedor de inserción de dependencias de Autofac en cada clase de modelo de vista.

### <a name="handling-navigation-requests"></a>Controlar las solicitudes de navegación

Xamarin.Forms proporciona el [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) (clase), que implementa una experiencia de navegación jerárquica en el que el usuario es capaz de navegar a través de páginas hacia delante y hacia atrás, según sea necesario. Para obtener más información sobre la navegación jerárquica, consulte [Hierarchical Navigation](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) (Navegación jerárquica).

En lugar de utilizar el [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) directamente, la clase el ajusta de la aplicación eShopOnContainers el `NavigationPage` clase en el `CustomNavigationView` clase, como se muestra en el ejemplo de código siguiente:

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

El propósito de este ajuste es para facilitar la aplicación de estilos del [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instancia dentro del archivo XAML para la clase.

Navegación se realiza dentro de las clases de modelo de vista mediante la invocación de uno de los `NavigateToAsync` métodos, que especifica el tipo de modelo de vista de la página que se navega a, como se muestra en el ejemplo de código siguiente:

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

El siguiente ejemplo de código muestra la `NavigateToAsync` métodos proporcionados por el `NavigationService` clase:

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

Cada método permite que cualquier clase de modelo de vista que se deriva el `ViewModelBase` clase para realizar la navegación jerárquica invocando el `InternalNavigateToAsync` método. Además, el segundo `NavigateToAsync` método permite que los datos de navegación que se especifican como un argumento que se pasa al modelo de vista que se navega, donde normalmente se usa para realizar la inicialización. Para obtener más información, consulte [pasar parámetros durante la navegación](#passing_parameters_during_navigation).

El `InternalNavigateToAsync` método se ejecuta la solicitud de navegación y se muestra en el ejemplo de código siguiente:

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

El `InternalNavigateToAsync` método realiza la navegación a un modelo de vista por llamar primero a la `CreatePage` método. Este método localiza la vista que corresponde al tipo de modelo de vista especificada y crea y devuelve una instancia de este tipo de vista. Buscar la vista que se corresponde con el tipo de modelo de vista usa un enfoque basado en convenciones, que se da por supuesto que:

-   Las vistas son del mismo ensamblado como tipos de modelo de vista.
-   Las vistas están en una. Espacio de nombres de las vistas secundarias.
-   Los modelos de vista están en una. Espacio de nombres secundario ViewModels.
-   Se corresponden los nombres de vista para ver los nombres de modelo, con "Modelo" quitado.

Cuando se crea una instancia de una vista, esta se asocia con su modelo de vista correspondiente. Para obtener más información acerca de cómo ocurre esto, consulte [creación automática de un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Si la vista que se va a crear es un `LoginView`, se ajusta dentro de una nueva instancia de la `CustomNavigationView` clase y se asigna a la [ `Application.Current.MainPage` ](xref:Xamarin.Forms.Application.MainPage) propiedad. En caso contrario, el `CustomNavigationView` instancia es recuperar así que no es null, el [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage) se invoca el método para insertar la vista se crea en la pila de navegación. Sin embargo, si el objeto recuperado `CustomNavigationView` instancia es `null`, la vista se crea se ajusta dentro de una nueva instancia de la `CustomNavigationView` clase y se asigna a la `Application.Current.MainPage` propiedad. Este mecanismo garantiza que durante la navegación, las páginas se agregan correctamente a la pila de navegación cuando está vacía y cuando no contiene datos.

> [!TIP]
> Considere la posibilidad de almacenar en caché páginas. Página de almacenamiento en caché de resultados en el consumo de memoria para las vistas que no se muestran actualmente. Sin embargo, sin almacenamiento en caché de página significa que el análisis de XAML y la construcción de la página y su modelo de vista se producirán cada vez que se navega una página nueva, que puede afectar al rendimiento de una página compleja. Para una página bien diseñada que no use un número excesivo de controles, el rendimiento debería ser suficiente. Sin embargo, el almacenamiento en caché de página podría resultar útil si se producen tiempos de carga de páginas lentas.

Después de la vista se crea y se navega a, el `InitializeAsync` se ejecuta el método asociada de la vista modelo de vista. Para obtener más información, consulte [pasar parámetros durante la navegación](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Navegar por la aplicación cuando se inicia

Cuando se inicia la aplicación, el `InitNavigation` método en el `App` se invoca la clase. El ejemplo de código siguiente muestra este método:

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

El método crea un nuevo `NavigationService` objeto en el contenedor de inserción de dependencia de Autofac y devuelve una referencia a él, antes de invocar su `InitializeAsync` método.

> [!NOTE]
> Cuando el `INavigationService` interfaz se resuelve mediante la `ViewModelBase` (clase), el contenedor devuelve una referencia a la `NavigationService` objeto creado cuando se invoca el método InitNavigation.

El siguiente ejemplo de código muestra la `NavigationService` `InitializeAsync` método:

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

El `MainView` se navega a si la aplicación tiene un token de acceso almacenada en caché que se usa para la autenticación. En caso contrario, el `LoginView` se navega.

Para obtener más información sobre el contenedor de inserción de dependencia de Autofac, consulte [Introducción a la inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Pasar parámetros durante la navegación

Uno de los `NavigateToAsync` métodos, especificados por el `INavigationService` interfaz, los datos de exploración permite que se especifican como un argumento que se pasa al modelo de vista que se navega, donde normalmente se usa para realizar la inicialización.

Por ejemplo, el `ProfileViewModel` clase contiene un `OrderDetailCommand` que se ejecuta cuando el usuario selecciona un pedido en la `ProfileView` página. A su vez, esto se ejecuta el `OrderDetailAsync` método, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Este método invoca la navegación a la `OrderDetailViewModel`, pasando un `Order` instancia que representa el orden en que el usuario ha seleccionado en el `ProfileView` página. Cuando el `NavigationService` clase crea el `OrderDetailView`, `OrderDetailViewModel` crea y asigna a la vista de la clase [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Después de navegar a la `OrderDetailView`, `InternalNavigateToAsync` método se ejecuta el `InitializeAsync` método de la vista asociada al modelo de vista.

El `InitializeAsync` método se define en el `ViewModelBase` clase como un método que se puede invalidar. Este método especifica un `object` argumento que representa los datos que se pasará a un modelo de vista durante una operación de navegación. Por lo tanto, las clases de modelo de vista que desea recibir datos de una operación de navegación que proporcionen su propia implementación de la `InitializeAsync` método para realizar la inicialización necesaria. El siguiente ejemplo de código muestra la `InitializeAsync` método desde el `OrderDetailViewModel` clase:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

Este método recupera el `Order` detalles de instancia que se pasó en el modelo de vista durante la operación de navegación y lo usa para recuperar el orden completo desde el `OrderService` instancia.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Uso de comportamientos de navegación invocación

Navegación desde una vista se desencadena normalmente por una interacción del usuario. Por ejemplo, el `LoginView` realiza la navegación sigue una autenticación correcta. El ejemplo de código siguiente muestra cómo se invoca la navegación por un comportamiento:

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

En tiempo de ejecución, el `EventToCommandBehavior` responderá a la interacción con el [ `WebView` ](xref:Xamarin.Forms.WebView). Cuando el `WebView` navega a una página web, el [ `Navigating` ](xref:Xamarin.Forms.WebView.Navigating) se desencadenará el evento, que se ejecutará el `NavigateCommand` en el `LoginViewModel`. De forma predeterminada, los argumentos de evento para el evento se pasan al comando. Estos datos se convierten como se pasa entre el origen y destino por el convertidor especificado en el `EventArgsConverter` propiedad, que devuelve el [ `Url` ](xref:Xamarin.Forms.WebNavigationEventArgs.Url) desde el [ `WebNavigatingEventArgs` ](xref:Xamarin.Forms.WebNavigatingEventArgs). Por lo tanto, cuando el `NavigationCommand` es ejecuta, se pasa la dirección Url de la página web como un parámetro al registrado `Action`.

A su vez, el `NavigationCommand` ejecuta el `NavigateAsync` método, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

Este método invoca la navegación a la `MainViewModel`, y después en panel de navegación, quita el `LoginView` página de la pila de navegación.

### <a name="confirming-or-cancelling-navigation"></a>Confirmar o cancelar la navegación

Una aplicación podría necesitar interactuar con el usuario durante una operación de navegación, por lo que el usuario puede confirmar o cancelar la navegación. Esto podría ser necesario, por ejemplo, cuando el usuario intenta navegar antes por completo una vez completada una página de entrada de datos. En esta situación, una aplicación debe proporcionar una notificación que permite al usuario a salir de la página, o para cancelar la operación de navegación antes de que ocurra. Esto puede lograrse en una clase de modelo de vista mediante el uso de la respuesta de una notificación para controlar si se invoca la navegación.

## <a name="summary"></a>Resumen

Xamarin.Forms incluye compatibilidad con la navegación de página, que normalmente da como resultado de la interacción del usuario con la interfaz de usuario o de la aplicación, como resultado de los cambios de estado controlado por la lógica interna. Sin embargo, navegación puede ser difícil de implementar en las aplicaciones que usan el patrón MVVM.

Este capítulo presentada un `NavigationService` (clase), que se usa para realizar la navegación de model first de vista de modelos de vista. Colocar en la vista lógica de navegación clases de modelo significa que la lógica puede realizarse a través de pruebas automatizadas. Además, el modelo de vista, a continuación, puede implementar la lógica para controlar el desplazamiento para garantizar que se apliquen determinadas reglas empresariales.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
