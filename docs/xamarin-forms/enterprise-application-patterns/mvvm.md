---
title: MVVM
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 32a7a7dd50edcc3eefe76429ddb1e5581447993e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="mvvm"></a>MVVM

La experiencia del desarrollador Xamarin.Forms normalmente implica crear una interfaz de usuario en XAML y, a continuación, agregarle código subyacente que opera en la interfaz de usuario. Como las aplicaciones se modifican y aumentan de tamaño y ámbito, pueden surgir problemas de mantenimiento complejo. Estos problemas incluyen el acoplamiento apretado entre los controles de interfaz de usuario y la lógica de negocios, lo que aumenta el costo de realizar modificaciones de la interfaz de usuario y la dificultad de este tipo de código de pruebas unitarias.

El modelo Model-View-ViewModel (MVVM) ayuda a separar la lógica de negocios y la presentación de una aplicación desde la interfaz de usuario (UI) de forma clara. Mantener una separación clara entre la lógica de la aplicación y la interfaz de usuario ayuda a solucionar problemas de desarrollo numerosas y puede hacer que una aplicación sea más fácil probar, mantener y desarrollar. Se pueden mejorar enormemente oportunidades de reutilización de código y permite a los desarrolladores y diseñadores de interfaz de usuario más puedan colaborar fácilmente al desarrollar sus respectivos partes de una aplicación.

## <a name="the-mvvm-pattern"></a>El patrón MVVM

Hay tres componentes principales en el patrón MVVM: el modelo, la vista y el modelo de vista. Cada uno de ellos sirve para un propósito distinto. Figura 2-1 muestra las relaciones entre los tres componentes.

![](mvvm-images/mvvm.png "El patrón MVVM")

**Figura 2-1**: patrón MVVM el

Además de comprender las responsabilidades de cada componentes, también es importante entender cómo interactúan entre sí. En un nivel alto, la vista "conoce" el modelo de vista y el modelo de vista "conoce" el modelo, pero el modelo no es consciente del modelo de vista y el modelo de vista no es consciente de la vista. Por lo tanto, el modelo de vista aísla la vista del modelo y permite que el modelo evolucionar independientemente de la vista.

Las ventajas de usar el patrón MVVM son los siguientes:

-   Si hay una implementación existente de modelo que encapsula la lógica de negocios existente, puede ser difícil o arriesgado para cambiarlo. En este escenario, el modelo de vista actúa como un adaptador para las clases del modelo y permite evitar realizar cambios importantes en el código de modelo.
-   A los desarrolladores pueden crear pruebas unitarias para el modelo de vista y el modelo, sin utilizar la vista. Las pruebas unitarias para el modelo de vista pueden ejercer exactamente la misma funcionalidad que usa la vista.
-   La interfaz de usuario de la aplicación puede volver a diseñar sin tocar el código, siempre que la vista se implementa completamente en XAML. Por lo tanto, una nueva versión de la vista debe trabajar con el modelo de vista existente.
-   Diseñadores y desarrolladores pueden trabajar independientemente y de forma simultánea en sus componentes durante el proceso de desarrollo. Diseñadores pueden centrarse en la vista, mientras que los desarrolladores pueden trabajar en el modelo de vista y los componentes del modelo.

La clave para usar de forma eficaz MVVM reside para comprender cómo separe el código de la aplicación en las clases correctas y para entender cómo interactúan las clases. Las secciones siguientes tratan las responsabilidades de cada una de las clases en el patrón MVVM.

### <a name="view"></a>Ver

La vista es responsable de definir la estructura, el diseño y la apariencia de lo que el usuario ve en pantalla. Idealmente, cada vista se define en XAML, con un limitado código subyacente que no contiene la lógica de negocios. Sin embargo, en algunos casos, el código subyacente podría contener lógica de la interfaz de usuario que implementa el comportamiento visual que es difícil de expresar en XAML, como las animaciones.

En una aplicación de Xamarin.Forms, una vista es normalmente un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-derivado o [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)-clase derivada. Sin embargo, las vistas también se puede representar mediante una plantilla de datos, que especifica los elementos de interfaz de usuario que se usará para representar visualmente un objeto cuando se muestre. Una plantilla de datos como una vista no tiene ningún código subyacente y se ha diseñado para enlazar a un tipo de modelo de vista específica.

> [!TIP]
> Evite habilitar y deshabilitar elementos de interfaz de usuario en el código subyacente. Asegúrese de que los modelos de vista responsables de definir los cambios de estado lógico que afectan a algunos aspectos de la presentación de la vista, por ejemplo, si hay un comando o una indicación de que una operación está pendiente. Por lo tanto, habilitar y deshabilitar elementos de la interfaz de usuario mediante el enlace para ver las propiedades del modelo, en lugar de habilitación y deshabilitación de código subyacente.

Hay varias opciones para ejecutar código en el modelo de vista en respuesta a interacciones en la vista, como un clic del botón o la selección de elementos. Si un control admite comandos, el control `Command` propiedad puede estar enlazado a datos a un `ICommand` propiedad en el modelo de vista. Cuando se invoca el comando del control, se ejecutará el código en el modelo de vista. Además de los comandos, comportamientos se pueden adjuntar a un objeto en la vista y pueden realizar escuchas de un comando que se debe invocar o del evento. En respuesta, a continuación, puede invocar el comportamiento de un `ICommand` en el modelo de vista o un método en el modelo de vista.

### <a name="viewmodel"></a>ViewModel

El modelo de vista implementa propiedades y los comandos en las que la vista puede enlazar los datos y notifica a la vista de cualquier cambio de estado a través de eventos de notificación de cambio. Las propiedades y los comandos que proporciona el modelo de vista definen la funcionalidad para ofrecer la interfaz de usuario, pero la vista determina cómo se mostrará esa funcionalidad.

> [!TIP]
> Que la interfaz de usuario siga respondiendo con operaciones asincrónicas. Aplicaciones móviles deben mantener el subproceso de interfaz de usuario desbloqueado para mejorar la percepción del usuario de rendimiento. Por lo tanto, en el modelo de vista, use métodos asincrónicos para las operaciones de E/S y generar eventos para notificar de forma asincrónica a vistas de los cambios de propiedad.

El modelo de vista también es responsable de coordinar las interacciones de la vista con las clases del modelo que son necesarias. Normalmente hay una relación de uno a varios entre el modelo de vista y las clases del modelo. El modelo de vista puede optar por exponer directamente a la vista de clases del modelo para que los controles de la vista pueden enlazar los datos directamente a las mismas. En este caso, las clases del modelo deberá estar diseñado para admitir el enlace de datos y eventos de notificación de cambio.

Cada modelo de vista proporciona los datos de un modelo en un formulario que puede utilizar fácilmente la vista. Para lograr esto, el modelo de vista a veces, realiza la conversión de datos. Colocar esta conversión de datos en el modelo de vista es una buena idea porque proporciona propiedades que se puede enlazar la vista. Por ejemplo, el modelo de vista puede combinar los valores de dos propiedades para que sea más fácil para mostrar la vista.

> [!TIP]
> Centralizar las conversiones de datos en una capa de conversión. También es posible usar convertidores como una capa de conversión de datos independiente que se encuentra entre el modelo de vista y la vista. Esto puede ser necesario, por ejemplo, cuando los datos requieren un formato especial que no proporciona el modelo de vista.

En orden para el modelo de vista participar en el enlace de datos bidireccional con la vista, deben generar sus propiedades las `PropertyChanged` eventos. Ver modelos cumplen este requisito implementando la `INotifyPropertyChanged` de interfaz y, cuando se genera el `PropertyChanged` eventos cuando se cambia una propiedad.

Para las colecciones, la vista sencillo `ObservableCollection<T>` se proporciona. Esta colección implementa la notificación de la colección cambia, lo que exime al programador de tener que implementar el `INotifyCollectionChanged` interfaz en colecciones.

### <a name="model"></a>Modelo

Clases de modelo son no visuales que encapsulan los datos de la aplicación. Por lo tanto, el modelo puede considerarse como que representa el modelo de dominio de la aplicación, que normalmente incluye un modelo de datos junto con la lógica de negocios y la validación. Objetos de transferencia de datos (dto), objetos de CLR antiguos sin formato (POCOs) y entidad generadas y objetos proxy son ejemplos de objetos del modelo.

Clases de modelo se utilizan normalmente junto con los servicios o repositorios que encapsulan el acceso a datos y almacenamiento en caché.

## <a name="connecting-view-models-to-views"></a>Conectarse a modelos de vista a las vistas

Ver modelos pueden estar conectados a vistas utilizando las capacidades de enlace de datos de Xamarin.Forms. Existen muchos enfoques que pueden usarse para generar vistas y ver los modelos y asociarlos en tiempo de ejecución. Estos métodos se dividen en dos categorías, conocidas como la composición de la primera vista y vista de modelo primera composición. Elija entre vista Composición primer modelo es un problema de preferencias y la complejidad y composición de la primera vista. No obstante, todos los enfoques comparten el mismo objetivo, que es para que la vista de tener un modelo de vista asignado a su propiedad BindingContext.

Con la vista de composición primero la aplicación conceptualmente se compone de vistas que se conectan a los modelos de vista que dependen. La principal ventaja de este enfoque es que facilita la construcción de acoplamiento flexible, las aplicaciones pueden someterse a prueba unitaria porque los modelos de vista no tienen ninguna dependencia en las vistas por sí mismos. También es fácil de entender la estructura de la aplicación después de su estructura visual, en lugar de tener que realizar un seguimiento de la ejecución del código para entender cómo se crea y se asocian clases. Además, construcción primera vista se alinea con el sistema de navegación de Xamarin.Forms que es responsable de la creación de páginas cuando se produce la navegación, lo que resulta una composición de la primera vista modelo complejo y mal alineados con la plataforma.

Con la vista de la aplicación de primera composición de modelo conceptualmente consta de modelos de vista, con un servicio responsable de localizar la vista de un modelo de vista. Vista modelo primera composición se siente más natural para algunos desarrolladores, puesto que la creación de vistas se puede abstraer inmediatamente, lo que les permite centrarse en la estructura lógica no son de interfaz de usuario de la aplicación. Además, permite ver modelos que se creará en otros modelos de vista. Sin embargo, este enfoque a menudo es compleja y puede resultar difícil entender cómo se crean y se asocian las distintas partes de la aplicación.

> [!TIP]
> Mantener los modelos de vista y vistas independientes. El enlace de vistas a una propiedad en un origen de datos debe tener principal depende la vista de su modelo de vista correspondiente. En concreto, no hacen referencia a tipos de vistas, como [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) y [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), de modelos de vista. Siguiendo los principios descritos a continuación, ver modelos se pueden probar de forma aislada, lo que se reduce la probabilidad de defectos de software mediante la limitación de ámbito.

Las secciones siguientes tratan los enfoques principales para efectuar la conexión de modelos de vista a las vistas.

### <a name="creating-a-view-model-declaratively"></a>Crear un modelo de vista de forma declarativa

Es el enfoque más sencillo para que la vista para crear una instancia de su modelo de vista correspondiente en XAML mediante declaración. Cuando se crea la vista, también se creará el objeto de modelo de vista correspondiente. Este enfoque se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Cuando el [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) se crea una instancia de la `LoginViewModel` se crea automáticamente y se establece como la vista [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/).

Esta construcción declarativa y asignación del modelo de vista en la vista tiene la ventaja de que es sencillo, pero tiene la desventaja que requiere un constructor de (sin parámetros) predeterminado en el modelo de vista.

### <a name="creating-a-view-model-programmatically"></a>Crear un modelo de vista mediante programación

Una vista puede tener el código en el archivo de código subyacente que da como resultado el modelo de vista que se asigna a su [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) propiedad. A menudo, esto se logra en el constructor de la vista, tal como se muestra en el ejemplo de código siguiente:

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

La construcción de programación y la asignación del modelo de vista de código subyacente de la vista tiene la ventaja de que es sencillo. Sin embargo, el principal inconveniente de este enfoque es que la vista debe proporcionar el modelo de vista con las dependencias necesarias. Utilización de un contenedor de inyección de dependencia puede ayudar a mantener un acoplamiento entre la vista y el modelo de vista flexible. Para obtener más información, consulte [inyección de dependencia](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Crear una vista que se define como una plantilla de datos

Una vista puede definirse como una plantilla de datos y asociada a un tipo de modelo de vista. Plantillas de datos se pueden definir como recursos, o pueden definir en línea dentro del control que se mostrará el modelo de vista. El contenido del control es la instancia de modelo de vista y la plantilla de datos se usa para representar visualmente. Esta técnica es un ejemplo de una situación en la que el modelo de vista se crea una instancia en primer lugar, seguida de la creación de la vista.

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Creación automática de un modelo de vista con un localizador de modelo de vista

Un localizador de modelo de vista es una clase personalizada que administra la creación de instancias de los modelos de vista y su asociación a vistas. En la aplicación móvil eShopOnContainers, la `ViewModelLocator` clase tiene una propiedad adjunta, `AutoWireViewModel`, que se utiliza para asociar ver modelos con las vistas. En XAML la vista, esta propiedad adjunta se establece en true para indicar que el modelo de vista debe estar conectado automáticamente a la vista, tal como se muestra en el ejemplo de código siguiente:

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

El `AutoWireViewModel` propiedad es una propiedad enlazable que se inicializa en false y, cuando se cambia su valor la `OnAutoWireViewModelChanged` se llama al controlador de eventos. Este método resuelve el modelo de vista para la vista. En el ejemplo de código siguiente se muestra cómo se consigue esto:

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

El `OnAutoWireViewModelChanged` método intenta resolver el modelo de vista con un enfoque basado en la convención. Esta convención supone:

-   Ver modelos están en el mismo ensamblado que los tipos de vista.
-   Las vistas están en una. Espacio de nombres de vistas secundarias.
-   Ver modelos están en una. Espacio de nombres de ViewModels secundario.
-   Ver nombres de modelo se corresponden con los nombres de las vistas y terminan con "ViewModel".

Por último, el `OnAutoWireViewModelChanged` método establece la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del tipo de vista para el tipo de modelo de vista resuelto. Para obtener más información acerca de cómo resolver el tipo de modelo de vista, vea [resolución](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Este enfoque tiene la ventaja de que una aplicación tiene una única clase que es responsable de la creación de instancias de los modelos de vista y su conexión a vistas.

> [!TIP]
> Utilice un localizador de modelo de vista para facilitar la sustitución. Un localizador de modelo de vista también sirve como punto de sustitución para las implementaciones alternativas de dependencias, como para los datos en tiempo de diseño o pruebas unitarias.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Actualizables vistas en respuesta a los cambios en subyacente ver modelo o el modelo

Todos los modelos de vista y clases del modelo que se puede acceder a una vista deben implementar la `INotifyPropertyChanged` interfaz. Implementa esta interfaz en un modelo de vista o una clase de modelo permite a la clase proporcionar notificaciones de cambio a los controles enlazados a datos en la vista cuando cambia el valor de propiedad subyacente.

La aplicación debe llevar a cabo para el uso correcto de notificación de cambio de propiedad, si se cumplen los requisitos siguientes:

-   Siempre cuando se genera un `PropertyChanged` evento si cambia el valor de una propiedad pública. No se da por supuesto que cuando se genera el `PropertyChanged` puede hacer caso omiso de evento debido a la información de cómo se produce el enlace XAML.
-   Siempre cuando se genera un `PropertyChanged` eventos para cualquier calculan propiedades cuyos valores se utilizan en otras propiedades en la vista de modelo o el modelo.
-   Siempre cuando se genera el `PropertyChanged` eventos al final del método que constituye una propiedad cambiar, o cuando se conoce el objeto esté en un estado seguro. Cuando se genera el evento, interrumpe la operación mediante la invocación de forma sincrónica los controladores del evento. Si esto ocurre en el medio de una operación, que podría exponer el objeto a las funciones de devolución de llamada cuando se encuentra en un estado parcialmente actualizado, no seguro. Además, es posible que los cambios en cascada que va a desencadenar `PropertyChanged` eventos. Cambios en cascada suelen requieran actualizaciones para completarse antes de que el cambio en cascada es seguro ejecutar.
-   Nunca cuando se genera un `PropertyChanged` evento si la propiedad no cambia. Esto significa que se deben comparar los valores antiguos y nuevos antes de generar el `PropertyChanged` eventos.
-   Nunca cuando se genera el `PropertyChanged` eventos durante el constructor de un modelo vista si inicializa una propiedad. Controles enlazados a datos en la vista no tendrá suscribirse para recibir notificaciones de cambios en este momento.
-   Nunca cuando se genera más de un `PropertyChanged` evento con el mismo argumento de nombre de propiedad en una única invocación sincrónica de un método público de una clase. Por ejemplo, dada una `NumberOfItems` propiedad cuya memoria auxiliar es la `_numberOfItems` campo, si un método aumenta `_numberOfItems` cincuenta veces durante la ejecución de un bucle, debería solo desencadenar la notificación de cambio de propiedad en el `NumberOfItems` una vez, propiedad una vez completado la todo el trabajo. Para los métodos asincrónicos, generar el `PropertyChanged` eventos para un nombre de propiedad especificado en cada segmento sincrónica de una cadena de continuación asincrónica.

Los usos de la aplicación móvil eShopOnContainers la `ExtendedBindableObject` clase para proporcionar notificaciones de cambio, que se muestra en el ejemplo de código siguiente:

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Del Xamarin.Form [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) la clase implementa la `INotifyPropertyChanged` de interfaz y proporciona un [ `OnPropertyChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.OnPropertyChanged/p/System.String/) método. El `ExtendedBindableObject` clase proporciona el `RaisePropertyChanged` método que se invoca la propiedad notificación de cambios y, si lo hace, usa la funcionalidad proporcionada por el `BindableObject` clase.

Cada clase de modelo de vista en la aplicación móvil eShopOnContainers se deriva de la `ViewModelBase` (clase), que a su vez se deriva de la `ExtendedBindableObject` clase. Por lo tanto, cada clase de vista de modelo utiliza el `RaisePropertyChanged` método en la `ExtendedBindableObject` clase para proporcionar una notificación de cambio de propiedad. En el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers invoca la notificación de cambio de propiedad mediante el uso de una expresión lambda:

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

Tenga en cuenta que usa una expresión lambda de esta manera implica un pequeño costo porque la expresión lambda tiene va a evaluar para cada llamada de rendimiento. Aunque el costo de rendimiento es pequeño y normalmente no afecte a una aplicación, pueden que los costos se acumulan cuando hay que muchas las notificaciones de cambio. Sin embargo, la ventaja de este enfoque es que proporciona seguridad de tipos de tiempo de compilación y la compatibilidad con la refactorización al cambiar el nombre de propiedades.

## <a name="ui-interaction-using-commands-and-behaviors"></a>Usar comandos y comportamientos de interacción de interfaz de usuario

En las aplicaciones móviles, las acciones se invocan habitualmente en respuesta a una acción del usuario, como un clic del botón, que se pueden implementar mediante la creación de un controlador de eventos en el archivo de código subyacente. Sin embargo, en el patrón MVVM, la responsabilidad de implementar la acción está relacionado con el modelo de vista, y se debe evitar colocar código en el código subyacente.

Comandos proporcionan una manera cómoda para representar las acciones que se pueden enlazar a controles en la interfaz de usuario. Se encapsular el código que implementa la acción y ayudan a mantenerlo separa de su representación visual en la vista. Xamarin.Forms se incluyen los controles que se pueden conectar mediante declaración a un comando, y estos controles invocará el comando cuando el usuario interactúa con el control.

Comportamientos también permiten que los controles se conecten mediante declaración a un comando. Sin embargo, se pueden utilizar comportamientos para invocar una acción que esté asociada con un intervalo de los eventos generados por un control. Por lo tanto, comportamientos solucionar muchos de los mismos escenarios como controles de comando habilitado, al proporcionar un mayor grado de flexibilidad y control. Además, también se pueden utilizar comportamientos para asociar objetos de comando o métodos con controles que no se han diseñado específicamente para interactuar con los comandos.

### <a name="implementing-commands"></a>Implementación de comandos

Ver modelos suelen exponen propiedades de comando, para el enlace de la vista, que son instancias de objetos que implementan la `ICommand` interfaz. Proporcione un número de controles de Xamarin.Forms una `Command` propiedad, que puede ser datos enlazados a un `ICommand` objeto proporcionado por el modelo de vista. El `ICommand` interfaz define un `Execute` método, que encapsula la operación en Sí, un `CanExecute` método, que indica si se puede invocar el comando y un `CanExecuteChanged` evento que tiene lugar cuando producen cambios que afectan a si debe ejecutar el comando. El [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) y [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) implementan clases, proporcionado por Xamarin.Forms, la `ICommand` interfaz, donde `T` es el tipo de los argumentos `Execute`y `CanExecute`.

Dentro de un modelo de vista, debe ser un objeto de tipo [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) o [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) para cada propiedad pública en el modelo de vista de tipo `ICommand`. El `Command` o `Command<T>` constructor requiere un `Action` objeto de devolución de llamada que se llama cuando el `ICommand.Execute` se invoca el método. El `CanExecute` método es un parámetro de constructor opcional y es un `Func` que devuelve un `bool`.

El siguiente código muestra cómo un [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) instancia, que representa un comando de registro, se construye especificando un delegado para que la `Register` ver el método de modelo:

```csharp
public ICommand RegisterCommand => new Command(Register);
```

El comando se expone a la vista a través de una propiedad que devuelve una referencia a un `ICommand`. Cuando el `Execute` método se llama en el [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) objeto, simplemente reenvía la llamada al método en el modelo de vista a través del delegado que se especificó en el `Command` constructor.

Un método asincrónico puede invocarse mediante un comando mediante el uso de la `async` y `await` palabras clave al especificar el comando `Execute` delegar. Esto indica que la devolución de llamada es un `Task` y debe esperar. Por ejemplo, el siguiente código muestra cómo un [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) instancia, que representa un comando de inicio de sesión, se construye especificando un delegado para que la `SignInAsync` ver el método de modelo:

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

Se pueden pasar parámetros a la `Execute` y `CanExecute` acciones mediante la [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) clase para crear instancias del comando. Por ejemplo, el siguiente código muestra cómo un `Command<T>` instancia se utiliza para indicar que la `NavigateAsync` método requerirá un argumento de tipo `string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

Tanto en el [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) y [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) clases, el delegado a la `CanExecute` método en cada constructor es opcional. Si no se especifica un delegado, el `Command` devolverá `true` para `CanExecute`. Sin embargo, el modelo de vista puede indicar un cambio en el comando `CanExecute` estado mediante una llamada a la `ChangeCanExecute` método en la `Command` objeto. Esto hace que el `CanExecuteChanged` evento. Todos los controles en la interfaz de usuario que están enlazados al comando, a continuación, actualizará su estado habilitado para reflejar la disponibilidad de los comandos enlazados a datos.

#### <a name="invoking-commands-from-a-view"></a>Invocar comandos desde una vista

El siguiente ejemplo de código muestra cómo un [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) en el `LoginView` se enlaza a la `RegisterCommand` en el `LoginViewModel` clase mediante el uso de un [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) instancia:

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

Un parámetro de comando también pueden opcionalmente definirse mediante el [ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.CommandParameter/) propiedad. El tipo de argumento esperado se especifica en el `Execute` y `CanExecute` métodos de destino. El [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) invocará automáticamente el comando de destino cuando el usuario interactúa con el control asociado. El parámetro de comando, si se proporciona, se pasarán como el argumento para el comando `Execute` delegar.

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>Implementación de comportamientos

Comportamientos de permiten la funcionalidad de agregarse a los controles de interfaz de usuario sin tener que subclase ellos. En su lugar, la funcionalidad se implementa en una clase de comportamiento y conectada al control como si fuera parte del propio control. Comportamientos le permiten implementar el código que normalmente tendría que escribir como código subyacente, porque interactúa directamente con la API del control, de tal manera que puede ser concisa adjunta al control y empaqueta para su reutilización en más de una vista o aplicación. En el contexto de MVVM, los comportamientos son un enfoque útil para conectar los controles a los comandos.

Un comportamiento que se adjunta a un control a través de las propiedades adjuntas se conoce como un *adjunta comportamiento*. El comportamiento, a continuación, puede usar la API expuesta del elemento al que está asociado para agregar funcionalidad a ese control u otros controles, en el árbol visual de la vista. Contiene la aplicación móvil eShopOnContainers el `LineColorBehavior` (clase), que es un comportamiento asociado. Para obtener más información acerca de este comportamiento, consulte [mostrar errores de validación](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors).

Un comportamiento de Xamarin.Forms es una clase que deriva de la [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) (clase), donde `T `es el tipo del control al que debe aplicarse el comportamiento. Estas clases proporcionan `OnAttachedTo` y `OnDetachingFrom` métodos, y se deben reemplazar para proporcionar la lógica que se ejecutará cuando el comportamiento se asocian y desasocian de controles.

En la aplicación móvil eShopOnContainers, la `BindableBehavior<T>` clase se deriva de la [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) clase. El propósito de la `BindableBehavior<T>` clase es proporcionar una clase base para los comportamientos de Xamarin.Forms que requieren la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del comportamiento se establezca en el control asociado.

El `BindableBehavior<T>` clase proporciona un reemplazable `OnAttachedTo` método que establece la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del comportamiento y un reemplazable `OnDetachingFrom` método que limpie el `BindingContext`. Además, la clase almacena una referencia para el control asociado en la `AssociatedObject` propiedad.

La aplicación móvil de eShopOnContainers incluye una `EventToCommandBehavior` (clase), que se ejecuta un comando como respuesta a la que se produzca un evento. Esta clase se deriva de la `BindableBehavior<T>` clase para que el comportamiento se puede enlazar a y ejecutar un `ICommand` especificado por un `Command` propiedad cuando se consume el comportamiento. En el ejemplo de código siguiente se muestra la clase `EventToCommandBehavior`:

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

El `OnAttachedTo` y `OnDetachingFrom` métodos se usan para registrar y anular el registro de un controlador de eventos para el evento definido en el `EventName` propiedad. A continuación, cuando se desencadene el evento, el `OnFired` se invoca el método, que ejecuta el comando.

La ventaja de usar el `EventToCommandBehavior` ejecutar un comando cuando se activa un evento, es que se pueden asociados con controles que no se han diseñado para interactuar con los comandos de comandos. Además, mueve el código de control de eventos para ver modelos, donde puede ser pasado las pruebas unitarias.

#### <a name="invoking-behaviors-from-a-view"></a>Invocar comportamientos de una vista

El `EventToCommandBehavior` es especialmente útil para asociar un comando a un control que no es compatible con comandos. Por ejemplo, el `ProfileView` utiliza la `EventToCommandBehavior` para ejecutar el `OrderDetailCommand` cuando el [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/) evento se desencadena en el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) que muestra los pedidos del usuario, como se muestra en el código siguiente:

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

En tiempo de ejecución, el `EventToCommandBehavior` responderá a la interacción con el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Cuando se selecciona un elemento en el `ListView`, [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/) se desencadenará el evento, que se ejecutará el `OrderDetailCommand` en el `ProfileViewModel`. De forma predeterminada, los argumentos de evento para el evento se pasan al comando. Estos datos se convierten como se pasa entre el origen y de destino por el convertidor especificado en la `EventArgsConverter` propiedad, que devuelve el [ `Item` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemTappedEventArgs.Item/) de la `ListView` desde el [ `ItemTappedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemTappedEventArgs/). Por lo tanto, cuando la `OrderDetailCommand` se ejecuta, seleccionado `Order` se pasa como un parámetro a la acción registrada.

Para obtener más información acerca de los comportamientos, consulte [comportamientos](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Resumen

El modelo Model-View-ViewModel (MVVM) ayuda a separar la lógica de negocios y la presentación de una aplicación desde la interfaz de usuario (UI) de forma clara. Mantener una separación clara entre la lógica de la aplicación y la interfaz de usuario ayuda a solucionar problemas de desarrollo numerosas y puede hacer que una aplicación sea más fácil probar, mantener y desarrollar. Se pueden mejorar enormemente oportunidades de reutilización de código y permite a los desarrolladores y diseñadores de interfaz de usuario más puedan colaborar fácilmente al desarrollar sus respectivos partes de una aplicación.

Usar el MVVM de patrón, la interfaz de usuario de la aplicación y la lógica de presentación y empresarial subyacente se divide en tres clases independientes: la vista, que encapsula la interfaz de usuario y la interfaz de usuario lógica; el modelo de vista, que encapsula la lógica de presentación y el estado; y el modelo, que encapsula la lógica de negocios y los datos de la aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
