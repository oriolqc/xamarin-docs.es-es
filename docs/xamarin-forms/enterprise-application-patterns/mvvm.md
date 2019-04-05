---
title: El patrón Model-View-ViewModel
description: Este capítulo explica cómo la aplicación móvil de eShopOnContainers utiliza el patrón MVVM para separar la lógica de negocios y la presentación de la aplicación desde su interfaz de usuario.
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 87448c556c66ea086db70699848227e1f671792b
ms.sourcegitcommit: be51b459a0a148ae3adca31d7599f53f7b2c3a68
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2019
ms.locfileid: "59019391"
---
# <a name="the-model-view-viewmodel-pattern"></a>El patrón Model-View-ViewModel

La experiencia del desarrollador Xamarin.Forms normalmente implica la creación de una interfaz de usuario en XAML y, a continuación, agregar código subyacente que opera en la interfaz de usuario. Como las aplicaciones se modifican y aumentan de tamaño y ámbito, pueden surgir problemas de mantenimiento complejas. Estos problemas incluyen el estrecho acoplamiento entre los controles de interfaz de usuario y la lógica de negocios, lo que aumenta el costo de realizar las modificaciones de la interfaz de usuario y la dificultad de este tipo de código de pruebas unitarias.

El patrón Modelo-Vista-Modelo de vista (MVVM) ayuda a separar la lógica de negocios y presentación de una aplicación de su interfaz de usuario. Mantener una separación clara entre la lógica de aplicación y la interfaz de usuario ayuda a abordar numerosos problemas de desarrollo y puede hacer que probar una aplicación, mantenerla y desarrollarla sea más fácil. También puede mejorar enormemente las oportunidades de reutilización de código y permite a los desarrolladores y diseñadores de interfaz de usuario colaborar con mayor facilidad al desarrollar sus respectivos elementos de una aplicación.

## <a name="the-mvvm-pattern"></a>El patrón MVVM

Hay tres componentes principales en el patrón MVVM: el modelo, la vista y el modelo de vista. Cada una tiene una finalidad distinta. Figura 2-1 muestra las relaciones entre los tres componentes.

![](mvvm-images/mvvm.png "El patrón MVVM")

**Figura 2-1**: El patrón MVVM

Además de comprender las responsabilidades de cada componente, también es importante entender cómo interactúan entre sí. En un nivel alto, la vista "conoce" el modelo de vista y el modelo de vista "conoce" el modelo, pero el modelo no es consciente de que el modelo de vista y el modelo de vista no es consciente de la vista. Por lo tanto, el modelo de vista aísla la vista del modelo y permite que el modelo evolucionar independientemente de la vista.

Las ventajas de usar el patrón MVVM son los siguientes:

-   Si hay una implementación existente de modelo que encapsula la lógica de negocios existente, puede ser difícil o riesgo para cambiarlo. En este escenario, el modelo de vista actúa como un adaptador para las clases del modelo y le permite evitar realizar cambios importantes en el código de modelo.
-   Los desarrolladores pueden crear pruebas unitarias para el modelo de vista y el modelo, sin usar la vista. Las pruebas unitarias para el modelo de vista pueden ejercer exactamente la misma funcionalidad que se usa la vista.
-   La interfaz de usuario de la aplicación podrá modificarse sin tocar el código, siempre que la vista se implementa completamente en XAML. Por lo tanto, una nueva versión de la vista debe trabajar con el modelo de vista existente.
-   Los diseñadores y desarrolladores pueden trabajar al mismo tiempo y de forma independiente en sus componentes durante el proceso de desarrollo. Los diseñadores pueden centrarse en la vista, mientras que los desarrolladores pueden trabajar en el modelo de vista y los componentes del modelo.

La clave para uso eficaz de MVVM reside en la comprensión de cómo incluir código de aplicación en las clases correctas y comprender cómo interactúan las clases. Las siguientes secciones describen las responsabilidades de cada una de las clases en el patrón MVVM.

### <a name="view"></a>Ver

La vista es responsable de definir la estructura, el diseño y la apariencia de lo que ve el usuario en la pantalla. Idealmente, cada vista se define en XAML, con un limitado código subyacente que no contienen lógica de negocios. Sin embargo, en algunos casos, el código subyacente podría contener lógica de la interfaz de usuario que implementa el comportamiento visual que es difícil de expresar en XAML, como animaciones.

En una aplicación de Xamarin.Forms, una vista es normalmente un [ `Page` ](xref:Xamarin.Forms.Page)-derivados o [ `ContentView` ](xref:Xamarin.Forms.ContentView)-clase derivada. Sin embargo, las vistas también pueden representarse mediante una plantilla de datos, que especifica los elementos de interfaz de usuario que se usará para representar visualmente un objeto cuando se muestre. Una plantilla de datos como una vista no tiene ningún código subyacente y está diseñada para enlazar a un tipo de modelo de vista concreta.

> [!TIP]
> Evite habilitar y deshabilitar los elementos de interfaz de usuario en el código subyacente. Asegúrese de que los modelos de vista son responsables de definir los cambios de estado lógico que afectan a algunos aspectos de presentación de la vista, por ejemplo, si un comando está disponible, o una indicación de que una operación está pendiente. Por lo tanto, habilitar y deshabilitar los elementos de interfaz de usuario mediante un enlace a ver las propiedades del modelo, en lugar de habilitación y deshabilitación de código subyacente.

Hay varias opciones para ejecutar código en el modelo de vista en respuesta a interacciones en la vista, como un clic del botón o la selección de elementos. Si un control es compatible con los comandos, el control `Command` propiedad puede ser datos enlazados a un `ICommand` propiedad en el modelo de vista. Cuando se invoca el comando del control, se ejecutará el código en el modelo de vista. Además de los comandos, comportamientos pueden asociarse a un objeto en la vista y pueden escuchar para que un comando que se debe invocar o el evento. En respuesta, el comportamiento, a continuación, puede invocar un `ICommand` en el modelo de vista o un método en el modelo de vista.

### <a name="viewmodel"></a>ViewModel

El modelo de vista implementa propiedades y comandos a la que la vista puede enlazar datos a y notifica a la vista de cualquier cambio de estado a través de los eventos de notificación de cambio. Las propiedades y los comandos que proporciona el modelo de vista definen la funcionalidad que se les ofrezca la interfaz de usuario, pero la vista determina cómo se mostrará esa funcionalidad.

> [!TIP]
> Que la interfaz de usuario siga respondiendo con operaciones asincrónicas. Aplicaciones móviles deben mantener el subproceso de interfaz de usuario desbloqueado para mejorar la percepción de rendimiento. Por lo tanto, en el modelo de vista, use métodos asincrónicos para operaciones de E/S y generar eventos para notificar de forma asincrónica las vistas de los cambios de propiedad.

El modelo de vista también es responsable de coordinar las interacciones de la vista con las clases de modelo que son necesarias. Normalmente, hay una relación de uno a varios entre el modelo de vista y las clases del modelo. El modelo de vista puede optar por exponer directamente a la vista de clases del modelo para que los controles en la vista pueden enlazar los datos directamente a ellos. En este caso, las clases del modelo debe estar diseñado para admitir el enlace de datos y los eventos de notificación de cambio.

Cada modelo de vista proporciona los datos de un modelo en un formulario que puede consumir fácilmente la vista. Para lograr esto, el modelo de vista a veces realiza la conversión de datos. Colocar esta conversión de datos en el modelo de vista es una buena idea, ya que proporciona propiedades que se puede enlazar la vista. Por ejemplo, el modelo de vista podría combinar los valores de dos propiedades que resulte más fácil para mostrar la vista.

> [!TIP]
> Centralizar las conversiones de datos en una capa de conversión. También es posible usar convertidores de tipos como una capa de conversión de datos independiente que se encuentra entre el modelo de vista y la vista. Esto puede ser necesario, por ejemplo, cuando los datos requieren un formato especial que no proporciona el modelo de vista.

En orden para el modelo de vista participar en el enlace de datos bidireccional con la vista, sus propiedades deben generar el `PropertyChanged` eventos. Ver modelos cumplen este requisito mediante la implementación de la `INotifyPropertyChanged` interfaz y generar el `PropertyChanged` evento cuando se cambia una propiedad.

Para las colecciones, la vista fáciles `ObservableCollection<T>` se proporciona. Esta colección implementa la notificación de colección cambia, lo cual elimina el desarrollador tenga que implementar el `INotifyCollectionChanged` interfaz en colecciones.

### <a name="model"></a>Modelo

Clases de modelo son no visuales que encapsulan los datos de la aplicación. Por lo tanto, el modelo puede considerarse que representa el modelo de dominio de la aplicación, que normalmente incluye un modelo de datos junto con la lógica empresarial y la validación. Objetos de transferencia de datos (dto), objetos CLR estándar (poco) y entidad generada y objetos proxy son ejemplos de los objetos del modelo.

Clases de modelo se utilizan normalmente junto con los servicios o los repositorios que encapsulan el acceso a datos y almacenamiento en caché.

## <a name="connecting-view-models-to-views"></a>Conectar los modelos de vista a las vistas

Los modelos de vista se pueden conectar a las vistas mediante el uso de las capacidades de enlace de datos de Xamarin.Forms. Existen varios enfoques que pueden usarse para construir las vistas y ver modelos y las asocia en tiempo de ejecución. Estos enfoques se dividen en dos categorías, conocidas como composición de la primera vista y la primera composición del modelo de vista. Elección entre la composición de la primera vista y la composición del primer modelo es una cuestión de preferencias y la complejidad de la vista. No obstante, todos los enfoques comparten el mismo objetivo, que es para que la vista tiene un modelo de vista asignado a su propiedad BindingContext.

Con la vista de composición primero la aplicación conceptualmente se compone de las vistas que se conectan a los modelos de vista que dependen. La principal ventaja de este enfoque es que resulta fácil de construir el acoplamiento flexible, aplicaciones apta para las pruebas unitarias porque los modelos de vista no tienen ninguna dependencia en las vistas a sí mismos. También es fácil de entender la estructura de la aplicación siguiendo su estructura visual, en lugar de tener que realizar un seguimiento de ejecución del código para comprender cómo las clases se crean y asociadas. Además, construcción primera vista se alinea con el sistema de navegación de Xamarin.Forms que es responsable de construir páginas cuando se produce la navegación, lo que hace que una composición primer modelo de vista complejo y mal alineados con la plataforma.

Con la vista de composición del modelo primero la aplicación conceptualmente se compone de modelos de vista, con un servicio que se va a responsable de localizar la vista de un modelo de vista. Primera composición del modelo de vista se siente más natural para algunos desarrolladores, ya que la creación de vistas se puede resumir inmediatamente, lo que les permite centrarse en la estructura lógica que no son de interfaz de usuario de la aplicación. Además, permite modelos de vista que va a crear otros modelos de vista. Sin embargo, este enfoque suele ser complejo y puede ser difícil de entender cómo se crea y se asocian las distintas partes de la aplicación.

> [!TIP]
> Mantener los modelos de vista y vistas independientes. El enlace de vistas para una propiedad en un origen de datos debe tener dependencias de entidad de seguridad de la vista en su modelo de vista correspondiente. En concreto, no hacen referencia a tipos de vistas, como [ `Button` ](xref:Xamarin.Forms.Button) y [ `ListView` ](xref:Xamarin.Forms.ListView), desde modelos de vista. Siguiendo los principios descritos aquí, los modelos de vista se pueden probar de forma aislada, lo que reduce la probabilidad de que los defectos de software limitando el ámbito.

Las secciones siguientes tratan los enfoques principales para conectar los modelos de vista a las vistas.

### <a name="creating-a-view-model-declaratively"></a>Crear un modelo de vista de forma declarativa

Es el enfoque más sencillo para que la vista crear instancias mediante declaración a su modelo de vista correspondiente en XAML. Cuando se crea la vista, también se construirá el objeto de modelo de vista correspondiente. Este método se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Cuando el [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) se crea una instancia de la `LoginViewModel` se crea automáticamente y se establece como la vista [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext).

Esta construcción declarativa y la asignación de la vista del modelo de vista tiene la ventaja de que es sencillo, pero tiene la desventaja que requiere un constructor de (sin parámetros) predeterminado del modelo de vista.

### <a name="creating-a-view-model-programmatically"></a>Crear un modelo de vista mediante programación

Una vista puede tener código en el archivo de código subyacente que da como resultado el modelo de vista que se asigna a su [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propiedad. A menudo, esto se logra en el constructor de la vista, tal como se muestra en el ejemplo de código siguiente:

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

La construcción de programación y la asignación del modelo de vista de código subyacente de la vista tiene la ventaja que es sencillo. Sin embargo, la principal desventaja de este enfoque es que se debe proporcionar el modelo de vista con las dependencias necesarias. Uso de un contenedor de inserción de dependencia puede ayudar a mantener un acoplamiento flexible entre la vista y el modelo de vista. Para obtener más información, consulte [inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Creación de una vista que se define como una plantilla de datos

Una vista puede definirse como una plantilla de datos y asociada con un tipo de modelo de vista. Plantillas de datos se pueden definir como recursos o se pueden definir en línea dentro del control que se mostrará el modelo de vista. El contenido del control es la instancia de modelo de vista y la plantilla de datos se usa para representar visualmente en él. Esta técnica es un ejemplo de una situación en la que el modelo de vista se crea una instancia en primer lugar, seguida de la creación de la vista.

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Creación automática de un modelo de vista con un localizador de modelo de vista

Un localizador de modelo de vista es una clase personalizada que administra la creación de instancias de los modelos de vista y su asociación a vistas. En la aplicación móvil de eShopOnContainers, el `ViewModelLocator` clase tiene una propiedad adjunta, `AutoWireViewModel`, que se utiliza para asociar los modelos de vista con las vistas. En XAML la vista, esta propiedad adjunta se establece en true para indicar que el modelo de vista se debe conectar automáticamente a la vista, tal como se muestra en el ejemplo de código siguiente:

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

El `AutoWireViewModel` es una propiedad enlazable que se inicializa en false y, cuando se cambia su valor el `OnAutoWireViewModelChanged` se llama al controlador de eventos. Este método resuelve el modelo de vista para la vista. En el ejemplo de código siguiente se muestra la forma de conseguirlo:

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

El `OnAutoWireViewModelChanged` método intenta resolver el modelo de vista con un enfoque basado en convenciones. Esta convención supone que:

-   Modelos de vista están en el mismo ensamblado como tipos de vista.
-   Las vistas están en una. Espacio de nombres de las vistas secundarias.
-   Los modelos de vista están en una. Espacio de nombres secundario ViewModels.
-   Los nombres de modelo de vista se corresponden con los nombres de vista y terminan con "ViewModel".

Por último, el `OnAutoWireViewModelChanged` método establece el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del tipo de vista para el tipo de modelo de vista resuelto. Para obtener más información acerca de cómo resolver el tipo de modelo de vista, consulte [resolución](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Este enfoque tiene la ventaja de que una aplicación tiene una única clase que es responsable de la creación de instancias de los modelos de vista y su conexión a las vistas.

> [!TIP]
> Utilice un localizador de modelo de vista para facilitar la sustitución. Un localizador de modelo de vista también sirve como punto de sustitución para implementaciones alternativas de dependencias, como para los datos en tiempo de diseño o las pruebas unitarias.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Actualizables vistas en respuesta a cambios en la base de ver el modelo o el modelo

Todos los modelo de vista y las clases de modelo que se puede acceder a una vista deben implementar la `INotifyPropertyChanged` interfaz. Implementa esta interfaz en un modelo de vista o una clase de modelo permite a la clase proporcionar notificaciones de cambio a los controles enlazados a datos en la vista cuando cambia el valor de propiedad subyacente.

Las aplicaciones deben llevar a cabo para el uso correcto de notificación de cambio de propiedad, cumplir los requisitos siguientes:

-   Generar siempre un `PropertyChanged` eventos si cambia el valor de una propiedad pública. No suponga que genera el `PropertyChanged` eventos pueden omitirse debido a conocimiento de cómo se realiza el enlace XAML.
-   Generar siempre un `PropertyChanged` eventos para cualquier calculan las propiedades cuyos valores se utilizan por otras propiedades en la vista de modelo o el modelo.
-   Generar siempre la `PropertyChanged` eventos al final del método que hace que una propiedad cambia, o cuando se conoce el objeto esté en un estado seguro. Provoca el evento interrumpe la operación mediante la invocación de forma sincrónica los controladores del evento. Si esto ocurre en el medio de una operación, que podría exponer el objeto a funciones de devolución de llamada cuando se encuentra en un estado parcialmente actualizado no seguro. Además, es posible que los cambios en cascada que va a desencadenar `PropertyChanged` eventos. Cambios en cascada normalmente necesitan actualizaciones que se complete antes de que es seguro ejecutar el cambio en cascada.
-   No generar nunca una `PropertyChanged` evento si la propiedad no cambia. Esto significa que deben comparar los valores antiguos y nuevos antes de generar el `PropertyChanged` eventos.
-   No elevar nunca el `PropertyChanged` eventos durante el constructor de un modelo vista si se está inicializando una propiedad. Los controles enlazados a datos en la vista no habrá suscrito para recibir notificaciones de cambio en este momento.
-   No elevar nunca más de uno `PropertyChanged` eventos con el mismo argumento de nombre de propiedad dentro de una sola invocación sincrónica de un método público de una clase. Por ejemplo, dada una `NumberOfItems` propiedad cuya memoria auxiliar es la `_numberOfItems` campo, si un método incrementa `_numberOfItems` cincuenta veces durante la ejecución de un bucle, debería solo desencadenar la notificación de cambio de propiedad en el `NumberOfItems` una vez, propiedad una vez completado todo el trabajo. Para los métodos asincrónicos, elevar el `PropertyChanged` eventos para un nombre de la propiedad especificada en cada segmento sincrónica de una cadena de continuación asincrónica.

La aplicación móvil de eShopOnContainers utiliza la `ExtendedBindableObject` clase para proporcionar notificaciones de cambio, que se muestra en el ejemplo de código siguiente:

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Del Xamarin.Form [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) la clase implementa la `INotifyPropertyChanged` interfaz y proporciona un [ `OnPropertyChanged` ](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) método. El `ExtendedBindableObject` clase proporciona el `RaisePropertyChanged` método que se invoca la propiedad notificación de cambios y al hacerlo, usa la funcionalidad proporcionada por el `BindableObject` clase.

Cada clase de modelo de vista en la aplicación móvil de eShopOnContainers se deriva de la `ViewModelBase` (clase), que a su vez se deriva de la `ExtendedBindableObject` clase. Por lo tanto, cada clase de modelo de vista usa la `RaisePropertyChanged` método en el `ExtendedBindableObject` clase para proporcionar una notificación de cambio de propiedad. En el ejemplo de código siguiente se muestra cómo la aplicación móvil de eShopOnContainers invoca la notificación de cambio de propiedad mediante el uso de una expresión lambda:

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

Tenga en cuenta que el uso de una expresión lambda de esta manera implica un pequeño costo porque la expresión lambda debe evaluarse para cada llamada de rendimiento. Aunque el costo de rendimiento es pequeño y normalmente no afectaría a una aplicación, pueden acumular los costos cuando hay que muchas notificaciones de cambio. Sin embargo, la ventaja de este enfoque es que proporciona seguridad de tipos de tiempo de compilación y la compatibilidad de refactorización al cambiar el nombre de propiedades.

## <a name="ui-interaction-using-commands-and-behaviors"></a>Interacción de la interfaz de usuario mediante los comandos y comportamientos

En aplicaciones móviles, las acciones se invocan normalmente en respuesta a una acción del usuario, como un clic de botón que se puede implementar mediante la creación de un controlador de eventos en el archivo de código subyacente. Sin embargo, en el patrón MVVM, responsable de aplicar la acción está relacionado con el modelo de vista, y se debe evitar colocar código en el código subyacente.

Comandos proporcionan una manera cómoda para representar las acciones que se pueden enlazar a controles en la interfaz de usuario. Se encapsula el código que implementa la acción y ayudar a mantenerlo separa de su representación visual en la vista. Xamarin.Forms incluye controles que se pueden conectar mediante declaración a un comando, y estos controles invocan el comando cuando el usuario interactúa con el control.

Los comportamientos también permiten controles mediante declaración se conecten a un comando. Sin embargo, los comportamientos pueden usarse para invocar una acción que está asociado con un intervalo de los eventos generados por un control. Por lo tanto, los comportamientos abordan muchos de los mismos escenarios que los controles de comando habilitado, al tiempo que proporciona un mayor grado de flexibilidad y control. Además, los comportamientos también pueden utilizarse para asociar los objetos de comando o métodos a los controles que no se diseñaron específicamente para interactuar con los comandos.

### <a name="implementing-commands"></a>Implementación de comandos

Los modelos de vista normalmente exponen propiedades de comando, para el enlace de la vista, que son instancias de objetos que implementan la `ICommand` interfaz. Proporcione un número de controles de Xamarin.Forms un `Command` propiedad, que puede ser datos enlazados a un `ICommand` objeto proporcionado por el modelo de vista. El `ICommand` interfaz define un `Execute` método, que encapsula la operación en Sí, un `CanExecute` método, que indica si se puede invocar el comando y un `CanExecuteChanged` evento que tiene lugar cuando producen cambios que afectan a si debe ejecutar el comando. El [ `Command` ](xref:Xamarin.Forms.Command) y [ `Command<T>` ](xref:Xamarin.Forms.Command) implementan clases, que proporciona Xamarin.Forms, el `ICommand` interfaz, donde `T` es el tipo de los argumentos `Execute`y `CanExecute`.

Dentro de un modelo de vista, debe ser un objeto de tipo [ `Command` ](xref:Xamarin.Forms.Command) o [ `Command<T>` ](xref:Xamarin.Forms.Command) para cada propiedad pública en el modelo de vista del tipo `ICommand`. El `Command` o `Command<T>` constructor requiere un `Action` objeto de devolución de llamada que se llama cuando el `ICommand.Execute` se invoca el método. El `CanExecute` método es un parámetro de constructor opcional y es un `Func` que devuelve un `bool`.

El siguiente código muestra cómo un [ `Command` ](xref:Xamarin.Forms.Command) se construye la instancia, que representa un comando register, especificando un delegado para el `Register` ver el método de modelo:

```csharp
public ICommand RegisterCommand => new Command(Register);
```

El comando se expone a la vista a través de una propiedad que devuelve una referencia a un `ICommand`. Cuando el `Execute` se llama al método en el [ `Command` ](xref:Xamarin.Forms.Command) objeto, simplemente reenvía la llamada al método en el modelo de vista a través del delegado que se especificó en el `Command` constructor.

Un método asincrónico se puede invocar un comando mediante el uso de la `async` y `await` palabras clave al especificar el comando `Execute` delegar. Esto indica que la devolución de llamada es un `Task` y se debe esperar. Por ejemplo, el siguiente código muestra cómo un [ `Command` ](xref:Xamarin.Forms.Command) instancia, que representa un comando de inicio de sesión, se construye especificando un delegado para el `SignInAsync` ver el método de modelo:

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

Se pueden pasar parámetros a la `Execute` y `CanExecute` acciones mediante la [ `Command<T>` ](xref:Xamarin.Forms.Command) clase para crear instancias del comando. Por ejemplo, el siguiente código muestra cómo un `Command<T>` instancia se utiliza para indicar que el `NavigateAsync` método requerirá un argumento de tipo `string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

Tanto en el [ `Command` ](xref:Xamarin.Forms.Command) y [ `Command<T>` ](xref:Xamarin.Forms.Command) clases, el delegado para el `CanExecute` método en cada constructor es opcional. Si no se especifica un delegado, el `Command` devolverá `true` para `CanExecute`. Sin embargo, el modelo de vista puede indicar un cambio en el comando `CanExecute` estado mediante una llamada a la `ChangeCanExecute` método en el `Command` objeto. Esto hace que el `CanExecuteChanged` evento. Los controles en la interfaz de usuario que están enlazados al comando, a continuación, actualizará su estado habilitado para reflejar la disponibilidad de los comandos enlazados a datos.

#### <a name="invoking-commands-from-a-view"></a>Invocar comandos desde una vista

El siguiente ejemplo de código muestra cómo un [ `Grid` ](xref:Xamarin.Forms.Grid) en el `LoginView` enlaza a la `RegisterCommand` en el `LoginViewModel` clase mediante el uso de un [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) instancia:

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

Un parámetro de comando también se puede, opcionalmente, definir mediante el [ `CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propiedad. El tipo del argumento esperado se especifica en el `Execute` y `CanExecute` métodos de destino. El [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) invocará automáticamente el comando de destino cuando el usuario interactúa con el control adjunto. El parámetro de comando, si se proporciona, se pasarán como argumento para el comando `Execute` delegar.

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>Implementación de comportamientos

Comportamientos permiten la funcionalidad que se agregará a los controles de interfaz de usuario sin tener que subclase ellos. En su lugar, la función se implementa en una clase de comportamiento y se asocia al control como si fuera parte de este. Los comportamientos le permiten implementar el código que normalmente tendría que escribir como código subyacente, ya que interactúa directamente con la API de control, de tal manera que puede ser concisa adjunta al control y empaqueta para su reutilización en más de una vista o la aplicación. En el contexto de MVVM, los comportamientos son un enfoque útil para conectar los controles a los comandos.

Un comportamiento que se adjunta a un control a través de las propiedades adjuntas se conoce como un *adjunta comportamiento*. El comportamiento, a continuación, puede usar la API expuesta del elemento al que se adjunta para agregar funcionalidad a ese control u otros controles, en el árbol visual de la vista. La aplicación móvil de eShopOnContainers contiene el `LineColorBehavior` (clase), que es un comportamiento asociado. Para obtener más información acerca de este comportamiento, consulte [mostrar errores de validación](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors).

Un comportamiento de Xamarin.Forms es una clase que deriva el [ `Behavior` ](xref:Xamarin.Forms.Behavior) o [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) (clase), donde `T `es el tipo del control al que debe aplicarse el comportamiento. Estas clases proporcionan `OnAttachedTo` y `OnDetachingFrom` métodos, que se deben invalidar para proporcionar la lógica que se ejecutará cuando el comportamiento se asocian y desasocian de controles.

En la aplicación móvil de eShopOnContainers, el `BindableBehavior<T>` clase se deriva de la [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) clase. El propósito de la `BindableBehavior<T>` clase es proporcionar una clase base para los comportamientos de Xamarin.Forms que requieren la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamiento para establecerse en el control adjunto.

El `BindableBehavior<T>` clase proporciona un reemplazable `OnAttachedTo` método que establece el [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamiento y un reemplazable `OnDetachingFrom` método que limpia la `BindingContext`. Además, la clase almacena una referencia al control adjunto en la propiedad `AssociatedObject`.

La aplicación móvil de eShopOnContainers incluye un `EventToCommandBehavior` (clase), que se ejecuta un comando como respuesta a la que se produzca un evento. Esta clase se deriva de la `BindableBehavior<T>` clase para que el comportamiento se puede enlazar a y ejecutar un `ICommand` especificado por un `Command` propiedad cuando se consume el comportamiento. En el ejemplo de código siguiente se muestra la clase `EventToCommandBehavior`:

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

El `OnAttachedTo` y `OnDetachingFrom` métodos se usan para registrar y anular el registro de un controlador de eventos para el evento definido en el `EventName` propiedad. A continuación, cuando se activa el evento, el `OnFired` se invoca el método, que ejecuta el comando.

La ventaja de usar el `EventToCommandBehavior` ejecutar un comando cuando se activa un evento, es que se pueden asociados con controles que no se han diseñado para interactuar con los comandos de comandos. Además, esto mueve el código de control de eventos para los modelos de vista, donde se puede realizar pruebas unitarias.

#### <a name="invoking-behaviors-from-a-view"></a>Invocar comportamientos de una vista

El `EventToCommandBehavior` es especialmente útil para asociar un comando a un control que no es compatible con los comandos. Por ejemplo, el `ProfileView` usa el `EventToCommandBehavior` para ejecutar el `OrderDetailCommand` cuando el [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) evento se desencadena en el [ `ListView` ](xref:Xamarin.Forms.ListView) que muestra los pedidos del usuario, como se muestra en el código siguiente:

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

En tiempo de ejecución, el `EventToCommandBehavior` responderá a la interacción con el [ `ListView` ](xref:Xamarin.Forms.ListView). Cuando se selecciona un elemento en el `ListView`, [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) se desencadenará el evento, que se ejecutará el `OrderDetailCommand` en el `ProfileViewModel`. De forma predeterminada, los argumentos de evento para el evento se pasan al comando. Estos datos se convierten como se pasa entre el origen y destino por el convertidor especificado en el `EventArgsConverter` propiedad, que devuelve el [ `Item` ](xref:Xamarin.Forms.ItemTappedEventArgs.Item) de la `ListView` desde el [ `ItemTappedEventArgs` ](xref:Xamarin.Forms.ItemTappedEventArgs). Por lo tanto, cuando el `OrderDetailCommand` se ejecuta, seleccionado `Order` se pasa como parámetro a la acción registrada.

Para obtener más información acerca de los comportamientos, consulte [comportamientos](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Resumen

El patrón Modelo-Vista-Modelo de vista (MVVM) ayuda a separar la lógica de negocios y presentación de una aplicación de su interfaz de usuario. Mantener una separación clara entre la lógica de aplicación y la interfaz de usuario ayuda a abordar numerosos problemas de desarrollo y puede hacer que probar una aplicación, mantenerla y desarrollarla sea más fácil. También puede mejorar enormemente las oportunidades de reutilización de código y permite a los desarrolladores y diseñadores de interfaz de usuario colaborar con mayor facilidad al desarrollar sus respectivos elementos de una aplicación.

Mediante el MVVM de patrón, la interfaz de usuario de la aplicación y la lógica de presentación y empresarial subyacente se divide en tres clases distintas: la vista, que encapsula la interfaz de usuario y la interfaz de usuario lógica; el modelo de vista, que encapsula la lógica de presentación y el estado; y el modelo, que encapsula la lógica de negocios y datos de la aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
