---
title: Navegación de la aplicación de empresa
description: Este capítulo explica cómo la aplicación móvil eShopOnContainers realiza la navegación por modelo basado en la vista de modelos de vista.
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9ac9f3200440001752c07ad45fdaaf2b1d9ba6a5
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243686"
---
# <a name="enterprise-app-navigation"></a>Navegación de la aplicación de empresa

Xamarin.Forms incluye compatibilidad para la navegación en páginas, que normalmente tiene como resultado de la interacción del usuario con la interfaz de usuario o de la aplicación como resultado de cambios de estado interno controlado por la lógica. Sin embargo, la navegación puede ser bastante complicada implementar en aplicaciones que utilizan el modelo Model-View-ViewModel (MVVM), que se deben cumplir los siguientes desafíos:

-   Cómo identificar la vista para navegar, mediante un enfoque que no presentan el acoplamiento apretado y las dependencias entre las vistas.
-   Describe cómo coordinar el proceso por el que se crea una instancia e inicializa la vista para navegar. Cuando se usa MVVM, la vista y el modelo de vista deben crear instancias y asociados entre sí a través del contexto de enlace de la vista. Cuando una aplicación usa un contenedor de inyección de dependencia, la creación de instancias de vistas y ver modelos puede requerir un mecanismo de construcción específico.
-   Si desea realizar la primera vista de navegación o ver el primer modelo de navegación. Con la navegación de la vista en primer lugar, la página para ir a hace referencia al nombre del tipo de vista. Durante la navegación, se crea la vista especificada, junto con su modelo de vista correspondiente y otros servicios dependientes. Un enfoque alternativo es utilizar la navegación del primer modelo de vista, donde la página para navegar a hacer referencia al nombre del tipo de modelo de vista.
-   ¿Cómo a correctamente separar el comportamiento de navegación de la aplicación a través de las vistas y los modelos de vista. El patrón MVVM proporciona una separación entre la interfaz de usuario de la aplicación y su presentación y lógica de negocios. Sin embargo, el comportamiento de navegación de una aplicación a menudo abarcarán las partes de la interfaz de usuario y presentaciones de la aplicación. El usuario a menudo iniciará la navegación de una vista y la vista se sustituirán como resultado de la navegación. Sin embargo, navegación a menudo también deba iniciar o coordinada desde dentro del modelo de vista.
-   Cómo pasar parámetros durante la navegación por motivos de inicialización. Por ejemplo, si el usuario navega a una vista para actualizar los detalles del pedido, los datos del pedido debe pasarse a la vista para que pueden mostrar los datos correctos.
-   Cómo navegación coordinar, para asegurarse de que se obedecer determinadas reglas de negocios. Por ejemplo, podrían pedirse a los usuarios antes de salir de una vista para que pueden corregir los datos no válidos o que deba enviar o descartar los cambios de datos que se realizaron en la vista.

En este capítulo se aborda estos retos presentando un `NavigationService` clase que se utiliza para realizar la navegación de página basado en modelo de vista.

> [!NOTE]
> El `NavigationService` utilizado por la aplicación sólo está diseñada para realizar la navegación jerárquica entre instancias de contenidoPage. Usar el servicio para navegar entre otros tipos de páginas podría producir un comportamiento inesperado.

## <a name="navigating-between-pages"></a>Navegar entre páginas

Lógica de navegación puede residir en el código subyacente de una vista o en datos enlaza el modelo de vista. Al colocar la lógica de navegación en una vista podría ser el método más sencillo, no es probar fácilmente a través de pruebas unitarias. Colocar en la vista lógica de navegación clases del modelo significa que la lógica puede realizarse a través de pruebas unitarias. Además, el modelo de vista, a continuación, puede implementar la lógica para controlar el desplazamiento para asegurarse de que se aplican ciertas reglas de negocios. Por ejemplo, una aplicación no puede permitir al usuario que sale de una página sin primero asegurarse de que los datos que escribió están válidos.

Un `NavigationService` clase normalmente se invoca desde modelos de vista, para promover la capacidad de prueba. Sin embargo, navegar a vistas de ver modelos requeriría los modelos de vista para que hacen referencia a vistas y especialmente las vistas que no está asociado, lo que no se recomienda el modelo de vista activa. Por lo tanto, la `NavigationService` presentan aquí se especifica el tipo de modelo de vista como destino para navegar a.

Los usos de la aplicación móvil eShopOnContainers la `NavigationService` clase para proporcionar navegación por modelo basado en la vista. Esta clase implementa la `INavigationService` interfaz, que se muestra en el ejemplo de código siguiente:

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
|`InitializeAsync`|Lleva a cabo la navegación a uno de dos páginas cuando se inicia la aplicación.|
|`NavigateToAsync`|Lleva a cabo la navegación jerárquica en una página especificada.|
|`NavigateToAsync(parameter)`|Lleva a cabo la navegación jerárquica en una página especificada, se pasa un parámetro.|
|`RemoveLastFromBackStackAsync`|Quita la página anterior de la pila de navegación.|
|`RemoveBackStackAsync`|Quita todas las páginas anteriores de la pila de navegación.|

Además, el `INavigationService` interfaz especifica que una clase de implementación debe proporcionar un `PreviousPageViewModel` propiedad. Esta propiedad devuelve el tipo de modelo de vista asociado a la página anterior en la pila de navegación.

> [!NOTE]
> Un `INavigationService` interfaz normalmente iniciaría también especificar un `GoBackAsync` método, que se usa para devolver mediante programación a la página anterior en la pila de navegación. Sin embargo, este método no está en la aplicación móvil eShopOnContainers porque no es necesario.

### <a name="creating-the-navigationservice-instance"></a>Crear la instancia NavigationService

El `NavigationService` de la clase, que implementa el `INavigationService` de la interfaz, se registra como un singleton con el contenedor de inyección de dependencia de Autofac, como se muestra en el ejemplo de código siguiente:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

El `INavigationService` interfaz se resuelve en el `ViewModelBase` constructor de clase, como se muestra en el ejemplo de código siguiente:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Esto devuelve una referencia a la `NavigationService` objeto que se almacena en el contenedor de inyección de dependencia de Autofac, que se crea mediante la `InitNavigation` método en la `App` clase. Para obtener más información, consulte [navegar cuando la aplicación se inicia](#navigating_when_the_app_is_launched).

El `ViewModelBase` clase almacena la `NavigationService` de instancia de un `NavigationService` propiedad de tipo `INavigationService`. Por lo tanto, todas las clases de vista modelo, que derivan de la `ViewModelBase` de clases, puede usar el `NavigationService` propiedad para tener acceso a los métodos especificados por el `INavigationService` interfaz. Esto evita la sobrecarga de insertar el `NavigationService` objeto desde el contenedor de inyección de dependencia Autofac en cada clase de vista de modelo.

### <a name="handling-navigation-requests"></a>Controlar las solicitudes de navegación

Xamarin.Forms ofrece la [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) (clase), que implementa una experiencia de navegación jerárquica en el que el usuario tiene permiso navegar a través de páginas hacia delante y hacia atrás, según sea necesario. Para obtener más información sobre la navegación jerárquica, consulte [Hierarchical Navigation](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) (Navegación jerárquica).

En lugar de usar el [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) clase directamente, los ajustes de aplicación eShopOnContainers el `NavigationPage` clase en el `CustomNavigationView` de la clase, como se muestra en el ejemplo de código siguiente:

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

El propósito de este ajuste es para facilitar la aplicación de estilos la [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) instancia dentro del archivo XAML para la clase.

Exploración se realiza dentro de clases del modelo de vista mediante la invocación de una de las `NavigateToAsync` métodos, especificar el tipo de modelo de vista para la página que se navega a, como se muestra en el ejemplo de código siguiente:

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

Cada método permite que cualquier clase de modelo de vista que se deriva de la `ViewModelBase` clase para realizar la navegación jerárquica mediante la invocación de la `InternalNavigateToAsync` método. Además, el segundo `NavigateToAsync` método permite que los datos de navegación que se especifique como argumento que se pasa al modelo de vista que se navega, donde normalmente se utiliza para realizar la inicialización. Para obtener más información, consulte [pasar parámetros durante la navegación](#passing_parameters_during_navigation).

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

El `InternalNavigateToAsync` método realiza la navegación a un modelo de vista llamando primero la `CreatePage` método. Este método busca la vista que corresponde al tipo de modelo de vista especificada y crea y devuelve una instancia de este tipo de vista. Buscar la vista que se corresponde con el tipo de modelo de vista, usa un enfoque basado en convenciones, que se da por supuesto que:

-   Las vistas son del mismo ensamblado como tipos de modelo de vista.
-   Las vistas están en una. Espacio de nombres de vistas secundarias.
-   Ver modelos están en una. Espacio de nombres de ViewModels secundario.
-   Puede ver nombres corresponden para ver los nombres de modelo, con el "modelo" quitado.

Cuando se crea una instancia de una vista, está asociada a su modelo de vista correspondiente. Para obtener más información acerca de cómo se produce esto, consulte [creación automática de un modelo de vista con un localizador de modelo de vista](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Si la vista que se va a crear es un `LoginView`, se ajusta dentro de una nueva instancia de la `CustomNavigationView` clase y asigna a la [ `Application.Current.MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) propiedad. En caso contrario, el `CustomNavigationView` instancia se recuperan y proporcionada que no es null, el [ `PushAsync` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) se invoca el método para insertar la vista se crea en la pila de navegación. Sin embargo, si el objeto recuperado `CustomNavigationView` instancia es `null`, la vista se crea se ajusta dentro de una nueva instancia de la `CustomNavigationView` clase y asigna a la `Application.Current.MainPage` propiedad. Este mecanismo garantiza que durante la navegación, páginas se agregan correctamente a la pila de navegación cuando está vacío y si contiene datos.

> [!TIP]
> Considere la posibilidad de almacenar en caché páginas. Página de almacenamiento en caché de resultados en el consumo de memoria para las vistas que no se muestran actualmente. Sin embargo, sin almacenamiento en caché de página significa que el análisis de XAML y la construcción de la página y su modelo de vista se producirán cada vez que se navega una nueva página, lo que puede tener un impacto en el rendimiento de una página compleja. Para una página bien diseñada que no use un número excesivo de controles, el rendimiento debería ser suficiente. Sin embargo, el almacenamiento en caché de página podría ayudar si se producen tiempos de carga de página lenta.

Después de la vista se crea y se abrirá, el `InitializeAsync` se ejecuta el método de modelo de vista asociada de la vista. Para obtener más información, consulte [pasar parámetros durante la navegación](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Navegar por la aplicación el cuando se inicia

Cuando se inicia la aplicación, el `InitNavigation` método en el `App` se invoca la clase. En el ejemplo de código siguiente se muestra este método:

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

El método crea un nuevo `NavigationService` objeto en el contenedor de inyección de dependencia de Autofac y devuelve una referencia a él, antes de invocar su `InitializeAsync` método.

> [!NOTE]
> Cuando el `INavigationService` interfaz se resuelve mediante la `ViewModelBase` (clase), el contenedor devuelve una referencia a la `NavigationService` objeto que se creó cuando se invoca el método InitNavigation.

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

El `MainView` se navega a si la aplicación tiene un token de acceso almacenada en caché, que se utiliza para la autenticación. En caso contrario, el `LoginView` se navega.

Para obtener más información sobre el contenedor de inyección de dependencia de Autofac, consulte [Introducción a la inyección de dependencia](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Pasar parámetros durante la navegación

Uno de los `NavigateToAsync` métodos, especificados por el `INavigationService` de la interfaz, datos de navegación permite que se especifique como argumento que se pasa al modelo de vista que se navega, donde normalmente se utiliza para realizar la inicialización.

Por ejemplo, el `ProfileViewModel` clase contiene una `OrderDetailCommand` que se ejecuta cuando el usuario selecciona un pedido en la `ProfileView` página. A su vez, esto se ejecuta el `OrderDetailAsync` método, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Este método invoca la navegación a la `OrderDetailViewModel`, pasando un `Order` instancia que representa el orden en que el usuario seleccionado en el `ProfileView` página. Cuando el `NavigationService` clase crea el `OrderDetailView`, `OrderDetailViewModel` se crea y se asigna a la vista de la clase [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). Después de navegar a la `OrderDetailView`, `InternalNavigateToAsync` el método se ejecuta el `InitializeAsync` asociado de método de la vista modelo de vista.

El `InitializeAsync` método está definido en el `ViewModelBase` clase como un método que se puede invalidar. Este método especifica un `object` argumento que representa los datos que se pasan a un modelo de vista durante una operación de navegación. Por lo tanto, las clases de modelo de vista que van a recibir datos de una operación de navegación proporcionar su propia implementación de la `InitializeAsync` método para realizar la inicialización necesaria. El siguiente ejemplo de código muestra la `InitializeAsync` método desde el `OrderDetailViewModel` clase:

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

Este método recupera el `Order` detalles de instancia que se pasó en el modelo de vista durante la operación de navegación y lo usa para recuperar el pedido completo desde el `OrderService` instancia.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Navegación invocar con comportamientos

Navegación se desencadena normalmente desde una vista de una interacción del usuario. Por ejemplo, el `LoginView` realiza navegación tras la autenticación correcta. En el ejemplo de código siguiente se muestra cómo se invoca la navegación por un comportamiento:

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

En tiempo de ejecución, el `EventToCommandBehavior` responderá a la interacción con el [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/). Cuando el `WebView` navega a una página web, el [ `Navigating` ](https://developer.xamarin.com/api/event/Xamarin.Forms.WebView.Navigating/) se desencadenará el evento, que se ejecutará el `NavigateCommand` en el `LoginViewModel`. De forma predeterminada, los argumentos de evento para el evento se pasan al comando. Estos datos se convierten como se pasa entre el origen y de destino por el convertidor especificado en la `EventArgsConverter` propiedad, que devuelve el [ `Url` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebNavigationEventArgs.Url/) desde el [ `WebNavigatingEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebNavigatingEventArgs/). Por lo tanto, cuando la `NavigationCommand` es ejecuta, se pasa la dirección Url de la página web como un parámetro a registrado `Action`.

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

Este método invoca la navegación a la `MainViewModel`, y después de navegación, quita la `LoginView` página de la pila de navegación.

### <a name="confirming-or-cancelling-navigation"></a>Confirmar o cancelar la navegación

Una aplicación que tenga que interactuar con el usuario durante una operación de navegación, por lo que el usuario puede confirmar o cancelar la navegación. Esto podría ser necesario, por ejemplo, cuando el usuario intenta desplazarse antes totalmente una vez completada una página de entrada de datos. En esta situación, una aplicación debe proporcionar una notificación que permite al usuario abandone la página, o cancelar la operación de navegación antes de que ocurra. Esto se puede lograr de una clase de modelo de vista utilizando la respuesta desde una notificación para controlar si se invoca la navegación.

## <a name="summary"></a>Resumen

Xamarin.Forms incluye compatibilidad para la navegación en páginas, que normalmente tiene como resultado de la interacción del usuario con la interfaz de usuario o de la propia aplicación como resultado de cambios de estado interno controlado por la lógica. Sin embargo, la navegación puede ser bastante complicada implementar en aplicaciones que usan el patrón MVVM.

Este capítulo presentada un `NavigationService` (clase), que se usa para realizar la navegación por modelo basado en la vista de modelos de vista. Colocar en la vista lógica de navegación clases del modelo significa que la lógica puede realizarse a través de las pruebas automatizadas. Además, el modelo de vista, a continuación, puede implementar la lógica para controlar el desplazamiento para asegurarse de que se aplican ciertas reglas de negocios.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
