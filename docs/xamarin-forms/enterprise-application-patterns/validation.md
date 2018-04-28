---
title: Validación
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 80c78d359761c4383f9abf9338a995e3cc486968
ms.sourcegitcommit: a69439ad4c9fd0abe759143687d3b23582573d90
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="validation"></a>Validación

Cualquier aplicación que acepta datos proporcionados por los usuarios debe asegurarse de que la entrada es válida. Por ejemplo, podría comprobar una aplicación para la entrada que contiene solo los caracteres en un intervalo determinado, es de una determinada longitud o coincide con un formato determinado. Sin validación, un usuario puede proporcionar datos a los que hace que la aplicación genere un error. La validación aplica reglas de negocios y evita que un atacante inserte datos malintencionados.

En el contexto de los modelos ViewModel (MVVM) de patrón, un modelo de vista o modelo a menudo se requerirá a realizar la validación de datos y notificar los errores de validación a la vista para que el usuario puede corregirlos. La aplicación móvil eShopOnContainers realiza la validación del lado cliente sincrónico de propiedades del modelo de vista y notifica al usuario los errores de validación resaltando el control que contiene los datos no válidos y muestre mensajes de error que informan al usuario de por qué los datos no están válidos. Figura 6-1 se muestran las clases implicadas en realizar la validación en la aplicación móvil eShopOnContainers.

[![](validation-images/validation.png "Las clases de validación en la aplicación móvil eShopOnContainers")](validation-images/validation-large.png#lightbox "las clases de validación en la aplicación móvil eShopOnContainers")

**Figura 6-1**: las clases de validación en la aplicación móvil eShopOnContainers

Ver las propiedades de modelo que requieren validación son del tipo `ValidatableObject<T>`y cada `ValidatableObject<T>` instancia tiene reglas de validación que se agrega a su `Validations` propiedad. Se invoca la validación del modelo de vista mediante una llamada a la `Validate` método de la `ValidatableObject<T>` instancia, que recupera la validación de reglas y las ejecuta en el `ValidatableObject<T>` `Value` propiedad. Los errores de validación se colocan en la `Errors` propiedad de la `ValidatableObject<T>` instancia y el `IsValid` propiedad de la `ValidatableObject<T>` instancia se actualiza para indicar si la validación se realizó correctamente o no se pudo.

Notificación de cambio de propiedad proporcionada por el `ExtendedBindableObject` (clase) de modo que un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) puede enlazar el control a la `IsValid` propiedad de `ValidatableObject<T>` perteneciente a la clase de modelo de vista que se le notifiquen o no los datos especificados son válidos.

## <a name="specifying-validation-rules"></a>Especificar reglas de validación

Las reglas de validación se especifican mediante la creación de una clase que deriva de la `IValidationRule<T>` interfaz, que se muestra en el ejemplo de código siguiente:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Esta interfaz especifica que una clase de regla de validación debe proporcionar un `boolean` `Check` método que se usa para realizar las operaciones de validación necesarias, y un `ValidationMessage` propiedad cuyo valor es el mensaje de error de validación que se mostrará si se produce un error en la validación.

El siguiente ejemplo de código muestra la `IsNotNullOrEmptyRule<T>` regla de validación, que se utiliza para realizar la validación de nombre de usuario y una contraseña escritos por el usuario el `LoginView` al usar los servicios ficticios en la aplicación móvil eShopOnContainers:

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

El `Check` método devuelve un `boolean` que indica si el argumento de valor es `null`, está vacía o consta únicamente de caracteres de espacio en blanco.

Aunque no se usa la aplicación móvil eShopOnContainers, en el ejemplo de código siguiente se muestra una regla de validación para validar las direcciones de correo electrónico:

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

El `Check` método devuelve un `boolean` que indica si el argumento de valor es una dirección de correo electrónico válida. Esto se logra mediante la búsqueda el argumento de valor de la primera aparición de un patrón de expresión regular especificada en el `Regex` constructor. Si se ha encontrado el patrón de expresión regular en la cadena de entrada se puede determinar examinando el valor de la `Match` del objeto `Success` propiedad.

> [!NOTE]
> Validación de propiedades a veces puede implicar propiedades dependientes. Un ejemplo de las propiedades dependientes es cuando el conjunto de valores válidos para una propiedad depende del valor concreto que se ha establecido en la propiedad B. Para comprobar que el valor de propiedad A uno de los valores permitidos, implicaría la recuperación del valor de propiedad B. Además, cuando cambia el valor de propiedad B, una propiedad necesitaría necesario volver a validar.

## <a name="adding-validation-rules-to-a-property"></a>Agregar reglas de validación a una propiedad

En la aplicación móvil eShopOnContainers, ver las propiedades de modelo que requieren validación se declaran como de tipo `ValidatableObject<T>`, donde `T` es el tipo de los datos que se va a validar. En el ejemplo de código siguiente se muestra un ejemplo de estas dos propiedades:

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

Para que se produzca la validación, se deben agregar reglas de validación para el `Validations` colección de cada `ValidatableObject<T>` de instancia, como se muestra en el ejemplo de código siguiente:

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

Este método agrega el `IsNotNullOrEmptyRule<T>` regla de validación para el `Validations` colección de cada `ValidatableObject<T>` instancia especificando valores para la regla de validación `ValidationMessage` propiedad, que especifica el mensaje de error de validación que se mostrará si se produce un error en la validación.

## <a name="triggering-validation"></a>Desencadene la validación

El enfoque de validación usado en la aplicación móvil eShopOnContainers puede desencadenar manualmente la validación de una propiedad y automáticamente la validación de desencadenador cuando cambia una propiedad.

### <a name="triggering-validation-manually"></a>Desencadene la validación manualmente

Validación se puede activar para una propiedad de modelo de vista. Por ejemplo, esto ocurre en la aplicación móvil eShopOnContainers cuando el usuario puntea el **inicio de sesión** situado en el `LoginView`, cuando se usa servicios ficticios. Las llamadas de delegado de comando el `MockSignInAsync` método en el `LoginViewModel`, que invoca la validación mediante la ejecución de la `Validate` método, que se muestra en el ejemplo de código siguiente:

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

El `Validate` método realiza la validación de nombre de usuario y una contraseña escritos por el usuario el `LoginView`, invocando el método Validate en cada `ValidatableObject<T>` instancia. En el ejemplo de código siguiente se muestra el método Validate de la `ValidatableObject<T>` clase:

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

Este método borra la `Errors` colección y, a continuación, recupera ninguna validación de reglas que se han agregado al objeto `Validations` colección. El `Check` se ejecuta el método para cada regla de validación recuperado y el `ValidationMessage` valor de propiedad para cualquier regla de validación que se produce un error al validar los datos se agrega a la `Errors` colección de la `ValidatableObject<T>` instancia. Por último, el `IsValid` se establece la propiedad y su valor se devuelve al método de llamada, que indica si la validación se realizó correctamente o no.

### <a name="triggering-validation-when-properties-change"></a>Desencadenar la validación cuando cambian las propiedades

Validación también se desencadena cada vez que cambia una propiedad enlazada. Por ejemplo, cuando un enlace bidireccional en el `LoginView` establece la `UserName` o `Password` se desencadena la propiedad de validación. En el ejemplo de código siguiente se muestra cómo se produce esto:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

El [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control se enlaza a la `UserName.Value` propiedad de la `ValidatableObject<T>` instancia y el control `Behaviors` colección tiene un `EventToCommandBehavior` instancia se agregan a él. Este comportamiento se ejecuta el `ValidateUserNameCommand` en respuesta a las [`TextChanged`] en el que se desencadenó el evento el `Entry`, que se produce cuando el texto en el `Entry` cambios. A su vez, el `ValidateUserNameCommand` delegado se ejecuta el `ValidateUserName` método, que se ejecuta el `Validate` método en el `ValidatableObject<T>` instancia. Por lo tanto, cada vez que el usuario escribe un carácter en el `Entry` control para el nombre de usuario, la validación de los datos introducidos se realiza.

Para obtener más información acerca de los comportamientos, consulte [implementar comportamientos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>Mostrar errores de validación

La aplicación móvil eShopOnContainers notifica al usuario los errores de validación resaltando el control que contiene los datos no válidos con una línea roja y mostrando un mensaje de error que informa al usuario de por qué los datos no están válidos por debajo el control que contiene el datos no válidos. Cuando se corrige los datos no válidos, la línea cambia a negro y se quita el mensaje de error. Figura 6-2 muestra la LoginView en la aplicación móvil eShopOnContainers cuando hay errores de validación.

![](validation-images/validation-login.png "Mostrar errores de validación durante el inicio de sesión")

**Figura 6-2:** mostrar errores de validación durante el inicio de sesión

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Resaltar un Control que contiene datos no válidos

El `LineColorBehavior` comportamiento adjunto se utiliza para resaltar [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controles donde se hayan producido errores de validación. El siguiente ejemplo de código muestra cómo el `LineColorBehavior` comportamiento adjunto se adjunta a un `Entry` control:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

El [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control consume un estilo explícito, que se muestra en el ejemplo de código siguiente:

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

Este estilo establece la `ApplyLineColor` y `LineColor` adjunta propiedades de la `LineColorBehavior` adjunta el comportamiento en el [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control. Para obtener más información sobre los estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

Cuando el valor de la `ApplyLineColor` propiedad adjunta es conjunto o cambios, el `LineColorBehavior` comportamiento asociado se ejecuta el `OnApplyLineColorChanged` método, que se muestra en el ejemplo de código siguiente:

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

Los parámetros de este método proporcionan la instancia del control que se adjunta el comportamiento a y los valores antiguos y nuevos de la `ApplyLineColor` propiedad adjunta. El `EntryLineColorEffect` se agrega al control de la clase [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) colección si el `ApplyLineColor` propiedad adjunta es `true`, en caso contrario, se quitará del control `Effects` colección. Para obtener más información acerca de los comportamientos, consulte [implementar comportamientos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

El `EntryLineColorEffect` subclases el [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) clase y se muestra en el ejemplo de código siguiente:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

El [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) clase representa un efecto de independiente de la plataforma que contiene un efecto interno que es específica de la plataforma. Esto simplifica el proceso de eliminación del efecto, porque no hay ningún acceso en tiempo de compilación a la información de tipo para un efecto específico de la plataforma. El `EntryLineColorEffect` llama al constructor de clase base, pasando un parámetro que consta de una concatenación del nombre del grupo de resolución y el identificador único que se especifica en cada clase de efecto específico de la plataforma.

El siguiente ejemplo de código muestra la `eShopOnContainers.EntryLineColorEffect` implementación para iOS:

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

El `OnAttached` método recupera el control nativo para la Xamarin.Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar y actualiza el color de línea mediante una llamada a la `UpdateLineColor` método. El `OnElementPropertyChanged` invalidación responde a los cambios de propiedad enlazable en la `Entry` control actualizando el color de línea, si el archivo adjunto `LineColor` cambios de propiedades, o la [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) propiedad de la `Entry`cambios. Para obtener más información acerca de los efectos, consulte [efectos](~/xamarin-forms/app-fundamentals/effects/index.md).

Cuando los datos válidos se registran en el [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) (control), aplicará una línea negra en la parte inferior del control, para indicar que no hay ningún error de validación. Figura 6-3 muestra un ejemplo de esto.

![](validation-images/validation-blackline.png "Línea negra que indica ningún error de validación")

**Figura 6-3**: línea negra que indica ningún error de validación

El [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control también tiene un [ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) agregado a su [ `Triggers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Triggers/) colección. El siguiente ejemplo de código muestra la `DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

Esto [ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) monitores el `UserName.IsValid` propiedad y si es el valor se convierte en `false`, se ejecuta el [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/), los cambios que el `LineColor` adjunta propiedad de la `LineColorBehavior` adjunta el comportamiento a rojo. Figura 6-4 muestra un ejemplo de esto.

![](validation-images/validation-redline.png "Línea roja que indica el error de validación")

**Figura 6-4**: línea roja que indica el error de validación

La línea en la [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control permanecerá rojo, mientras que los datos especificados son válidos, en caso contrario, cambiará a color negro para indicar que los datos especificados son válidos.

Para obtener más información acerca de los desencadenadores, vea [desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Mostrar mensajes de Error

La interfaz de usuario muestra mensajes de error de validación en los controles de etiqueta por debajo de cada control cuyos datos no pudo validar. El siguiente ejemplo de código muestra la [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) que muestra un mensaje de error de validación si el usuario no ha especificado un nombre de usuario válido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Cada [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) se enlaza a la `Errors` propiedad del objeto de modelo de vista que se está validando. El `Errors` propiedad se proporciona por la `ValidatableObject<T>` clase y es de tipo `List<string>`. Dado que la `Errors` propiedad puede contener varios errores de validación, el `FirstValidationErrorConverter` instancia se utiliza para recuperar el primer error de la colección para su presentación.

## <a name="summary"></a>Resumen

La aplicación móvil eShopOnContainers realiza la validación del lado cliente sincrónico de propiedades del modelo de vista y notifica al usuario los errores de validación resaltando el control que contiene los datos no válidos y muestre mensajes de error que informan al usuario ¿Por qué los datos no están válidos.

Ver las propiedades de modelo que requieren validación son del tipo `ValidatableObject<T>`y cada `ValidatableObject<T>` instancia tiene reglas de validación que se agrega a su `Validations` propiedad. Se invoca la validación del modelo de vista mediante una llamada a la `Validate` método de la `ValidatableObject<T>` instancia, que recupera la validación de reglas y las ejecuta en el `ValidatableObject<T>` `Value` propiedad. Los errores de validación se colocan en la `Errors` propiedad de la `ValidatableObject<T>`instancia y el `IsValid` propiedad de la `ValidatableObject<T>` instancia se actualiza para indicar si la validación se realizó correctamente o no se pudo.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
