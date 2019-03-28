---
title: Validación en aplicaciones empresariales
description: Este capítulo explica cómo la aplicación móvil de eShopOnContainers realiza la validación de entrada del usuario. Esto incluye especificar reglas de validación, desencadenar la validación y mostrar los errores de validación.
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 22b5fe703486f0ded3a5b91241e3fe5ce41bbc98
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507102"
---
# <a name="validation-in-enterprise-apps"></a>Validación en aplicaciones empresariales

Cualquier aplicación que acepte entradas de los usuarios debe asegurarse de que la entrada sea válida. Por ejemplo, podría comprobar una aplicación para la entrada que contiene solo caracteres en un intervalo determinado, es de una determinada longitud o coincide con un formato determinado. Sin validación, un usuario puede proporcionar datos que hagan que la aplicación produzca un error. La validación aplica reglas de negocio e impide que un atacante inserte datos malintencionados.

En el contexto de Model-View-ViewModel (MVVM) de patrón, un modelo de vista o modelo a menudo se requerirá para realizar la validación de datos y señalar los errores de validación a la vista para que el usuario puede corregirlos. La aplicación móvil de eShopOnContainers realiza la validación de cliente sincrónico de las propiedades del modelo de vista y notifica al usuario los errores de validación resaltando el control que contiene los datos no válidos y muestre los mensajes de error que informan al usuario de por qué los datos no están válidos. Figura 6-1 muestra las clases implicadas en realizar la validación en la aplicación móvil de eShopOnContainers.

[![](validation-images/validation.png "Las clases de validación en la aplicación móvil de eShopOnContainers")](validation-images/validation-large.png#lightbox "clases de validación en la aplicación móvil de eShopOnContainers")

**Figura 6-1**: Clases de validación en la aplicación móvil de eShopOnContainers

Las propiedades del modelo de vista que requieren validación son del tipo `ValidatableObject<T>`y cada `ValidatableObject<T>` instancia tiene reglas de validación que se agrega a su `Validations` propiedad. Se invoca la validación del modelo de vista mediante una llamada a la `Validate` método de la `ValidatableObject<T>` instancia, que recupera la validación de reglas y los ejecuta contra la `ValidatableObject<T>` `Value` propiedad. Errores de validación se colocan en el `Errors` propiedad de la `ValidatableObject<T>` instancia y el `IsValid` propiedad de la `ValidatableObject<T>` instancia se actualiza para indicar si la validación se realizó correctamente o no.

Notificación de cambio de propiedad proporcionada por el `ExtendedBindableObject` (clase), por lo que un [ `Entry` ](xref:Xamarin.Forms.Entry) puede enlazar el control a la `IsValid` propiedad de `ValidatableObject<T>` instancia en la clase view model para recibir una notificación o no los datos introducidos son válidos.

## <a name="specifying-validation-rules"></a>Especificar reglas de validación

Las reglas de validación se especifican mediante la creación de una clase que deriva el `IValidationRule<T>` interfaz, que se muestra en el ejemplo de código siguiente:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Esta interfaz especifica que una clase de regla de validación se debe proporcionar un `boolean` `Check` método que se usa para realizar la validación necesaria, y un `ValidationMessage` propiedad cuyo valor es el mensaje de error de validación que se mostrará si se produce un error de validación.

El siguiente ejemplo de código muestra la `IsNotNullOrEmptyRule<T>` regla de validación, que se usa para realizar la validación del nombre de usuario y la contraseña escrita por el usuario el `LoginView` al usar servicios ficticios en la aplicación móvil de eShopOnContainers:

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

El `Check` método devuelve un `boolean` que indica si el argumento de valor es `null`, vacía o consta únicamente de caracteres de espacio en blanco.

Aunque no se usa por la aplicación móvil de eShopOnContainers, en el ejemplo de código siguiente se muestra una regla de validación para validar direcciones de correo electrónico:

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

El `Check` método devuelve un `boolean` que indica si el argumento de valor es una dirección de correo electrónico válida. Esto se logra mediante la búsqueda en el argumento de valor para la primera aparición del patrón de expresión regular especificada en el `Regex` constructor. Si se ha encontrado el patrón de expresión regular en la cadena de entrada se puede determinar examinando el valor de la `Match` del objeto `Success` propiedad.

> [!NOTE]
> Validación de propiedades a veces puede implicar a las propiedades dependientes. Un ejemplo de propiedades dependientes es cuando el conjunto de valores válidos para una propiedad depende del valor concreto que se ha establecido en la propiedad B. Para comprobar que el valor de propiedad A es uno de los valores permitidos implicaría recuperar el valor de propiedad B. Además, cuando cambia el valor de propiedad B, necesitaría una propiedad volver a validar.

## <a name="adding-validation-rules-to-a-property"></a>Agregar reglas de validación a una propiedad

En la aplicación móvil de eShopOnContainers, se declaran las propiedades del modelo de vista que requieren validación de tipo `ValidatableObject<T>`, donde `T` es el tipo de los datos que se va a validarse. En el ejemplo de código siguiente se muestra un ejemplo de estas dos propiedades:

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

Para que se produzca la validación, se deben agregar reglas de validación para el `Validations` colección de cada `ValidatableObject<T>` de instancia, tal como se muestra en el ejemplo de código siguiente:

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

Este método agrega el `IsNotNullOrEmptyRule<T>` regla de validación del `Validations` colección de cada `ValidatableObject<T>` instancia, especifique valores para la regla de validación `ValidationMessage` propiedad, que especifica el mensaje de error de validación que se mostrará si se produce un error de validación.

## <a name="triggering-validation"></a>Desencadenar la validación

El enfoque de validación que se usa en la aplicación móvil de eShopOnContainers puede desencadenar manualmente la validación de una propiedad y automáticamente la validación del desencadenador cuando cambia una propiedad.

### <a name="triggering-validation-manually"></a>Desencadenar manualmente la validación

Validación se puede desencadenar manualmente para una propiedad de modelo de vista. Por ejemplo, esto ocurre en la aplicación móvil de eShopOnContainers cuando el usuario pulsa el **inicio de sesión** situado en el `LoginView`, cuando se usa servicios ficticios. El comando delegado llama el `MockSignInAsync` método en el `LoginViewModel`, que invoca la validación mediante la ejecución de la `Validate` método, que se muestra en el ejemplo de código siguiente:

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

El `Validate` método realiza la validación del nombre de usuario y la contraseña escrita por el usuario el `LoginView`, invocando el método Validate en cada `ValidatableObject<T>` instancia. El ejemplo de código siguiente muestra el método Validate desde el `ValidatableObject<T>` clase:

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

Este método borra el `Errors` colección y, a continuación, recupera cualquier validación de reglas que se han agregado para el objeto `Validations` colección. El `Check` se ejecuta el método para cada regla de validación recuperada y el `ValidationMessage` valor de propiedad para cualquier regla de validación que se produce un error al validar los datos se agrega a la `Errors` colección de la `ValidatableObject<T>` instancia. Por último, el `IsValid` se establece la propiedad y su valor se devuelve al método de llamada, que indica si la validación se realizó correctamente o no.

### <a name="triggering-validation-when-properties-change"></a>Desencadenar la validación cuando cambian las propiedades

También se puede desencadenar la validación cuando cambia una propiedad enlazada. Por ejemplo, cuando un enlace bidireccional en el `LoginView` establece la `UserName` o `Password` se desencadena la propiedad de validación. El siguiente ejemplo de código muestra cómo se produce esto:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

El [ `Entry` ](xref:Xamarin.Forms.Entry) control se enlaza a la `UserName.Value` propiedad de la `ValidatableObject<T>` instancia y el control `Behaviors` colección tiene un `EventToCommandBehavior` instancia se agregan a él. Este comportamiento se ejecuta el `ValidateUserNameCommand` en respuesta a las [`TextChanged`] evento y activar el `Entry`, que se produce cuando el texto en el `Entry` cambios. A su vez, el `ValidateUserNameCommand` delegado se ejecuta el `ValidateUserName` método, que se ejecuta el `Validate` método en el `ValidatableObject<T>` instancia. Por lo tanto, cada vez el usuario escribe un carácter en el `Entry` se realiza el control para el nombre de usuario, la validación de los datos especificados.

Para obtener más información acerca de los comportamientos, consulte [implementar comportamientos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>Mostrar errores de validación

La aplicación móvil de eShopOnContainers notifica al usuario los errores de validación resaltando el control que contiene los datos no válidos con una línea roja y mostrando un mensaje de error que informa al usuario por qué no están válidos por debajo del control que contiene los datos de la datos no válidos. Cuando los datos no válidos se ha corregido, la línea cambia a negro y se quita el mensaje de error. Figura 6-2 se muestra la LoginView en la aplicación móvil de eShopOnContainers si hay errores de validación.

![](validation-images/validation-login.png "Mostrar errores de validación durante el inicio de sesión")

**Figura 6-2:** Mostrar errores de validación durante el inicio de sesión

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Al resaltar un Control que contiene datos no válidos

El `LineColorBehavior` comportamiento asociado se usa para resaltar [ `Entry` ](xref:Xamarin.Forms.Entry) controles donde se han producido errores de validación. El siguiente ejemplo de código muestra cómo el `LineColorBehavior` comportamiento asociado se adjunta a un `Entry` control:

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

El [ `Entry` ](xref:Xamarin.Forms.Entry) control consume un estilo explícito, que se muestra en el ejemplo de código siguiente:

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

Este estilo establece la `ApplyLineColor` y `LineColor` adjunta propiedades de la `LineColorBehavior` adjunta el comportamiento en el [ `Entry` ](xref:Xamarin.Forms.Entry) control. Para obtener más información sobre los estilos, vea [Estilos](~/xamarin-forms/user-interface/styles/index.md).

Cuando el valor de la `ApplyLineColor` propiedad adjunta es conjunto o los cambios, el `LineColorBehavior` comportamiento asociado se ejecuta el `OnApplyLineColorChanged` método, que se muestra en el ejemplo de código siguiente:

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

Los parámetros de este método proporcionan la instancia del control que está asociado el comportamiento a y los valores antiguos y nuevos de la `ApplyLineColor` propiedad adjunta. El `EntryLineColorEffect` se agrega al control de la clase [ `Effects` ](xref:Xamarin.Forms.Element.Effects) colección si el `ApplyLineColor` propiedad adjunta es `true`, en caso contrario, se quita el control `Effects` colección. Para obtener más información acerca de los comportamientos, consulte [implementar comportamientos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

El `EntryLineColorEffect` subclases el [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) clase y se muestra en el ejemplo de código siguiente:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

El [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) clase representa un efecto de independiente de la plataforma que ajusta un efecto interno que es específico de la plataforma. Esto simplifica el proceso de eliminación del efecto, ya que no hay ningún acceso en tiempo de compilación a la información de tipo para un efecto específico de la plataforma. El `EntryLineColorEffect` llama al constructor de clase base, pasando un parámetro que consta de una concatenación del nombre del grupo de resolución y el identificador único que se especifica en cada clase efecto específico de la plataforma.

El siguiente ejemplo de código muestra la `eShopOnContainers.EntryLineColorEffect` implementación para iOS:

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

El `OnAttached` método recupera el control nativo para Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry) controlar y actualiza el color de línea mediante una llamada a la `UpdateLineColor` método. El `OnElementPropertyChanged` invalidación responde a los cambios de propiedad enlazable en la `Entry` control actualizando el color de línea si el archivo adjunto `LineColor` los cambios de propiedad, o la [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) propiedad de la `Entry`cambios. Para obtener más información sobre los efectos, vea [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md).

Cuando se especificaron datos válidos en el [ `Entry` ](xref:Xamarin.Forms.Entry) control, se aplicará una línea negra en la parte inferior del control, para indicar que no hay ningún error de validación. Figura 6-3 se muestra un ejemplo de esto.

![](validation-images/validation-blackline.png "Línea negra que se indica ningún error de validación")

**Figura 6-3**: Línea negra que se indica ningún error de validación

El [ `Entry` ](xref:Xamarin.Forms.Entry) control también tiene un [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) agregado a su [ `Triggers` ](xref:Xamarin.Forms.VisualElement.Triggers) colección. El siguiente ejemplo de código muestra la `DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

Esto [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) monitores el `UserName.IsValid` propiedad y si es el valor se convierte en `false`, se ejecuta el [ `Setter` ](xref:Xamarin.Forms.Setter), los cambios que el `LineColor` adjunta propiedad de la `LineColorBehavior` adjunta el comportamiento a rojo. Figura 6-4 se muestra un ejemplo de esto.

![](validation-images/validation-redline.png "Línea roja que indica el error de validación")

**Figura 6-4**: Línea roja que indica el error de validación

La línea en el [ `Entry` ](xref:Xamarin.Forms.Entry) control permanecerá rojo, mientras que los datos especificados no están válidos, en caso contrario, cambiará a negro para indicar que los datos introducidos son válidos.

Para obtener más información acerca de los desencadenadores, consulte [desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Mostrar mensajes de Error

La interfaz de usuario muestra mensajes de error de validación en los controles de etiqueta debajo de cada control cuyos datos no pudo validar. El siguiente ejemplo de código muestra la [ `Label` ](xref:Xamarin.Forms.Label) que muestra un mensaje de error de validación si el usuario no ha especificado un nombre de usuario válido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Cada [ `Label` ](xref:Xamarin.Forms.Label) enlaza a la `Errors` propiedad del objeto de modelo de vista que se va a validar. El `Errors` propiedad proporcionado por el `ValidatableObject<T>` clase y es de tipo `List<string>`. Dado que el `Errors` propiedad puede contener varios errores de validación, el `FirstValidationErrorConverter` instancia se usa para recuperar el primer error de la colección para su presentación.

## <a name="summary"></a>Resumen

La aplicación móvil de eShopOnContainers realiza la validación de cliente sincrónico de las propiedades del modelo de vista y notifica al usuario los errores de validación resaltando el control que contiene los datos no válidos y muestre los mensajes de error que informan al usuario ¿Por qué los datos no están válidos.

Las propiedades del modelo de vista que requieren validación son del tipo `ValidatableObject<T>`y cada `ValidatableObject<T>` instancia tiene reglas de validación que se agrega a su `Validations` propiedad. Se invoca la validación del modelo de vista mediante una llamada a la `Validate` método de la `ValidatableObject<T>` instancia, que recupera la validación de reglas y los ejecuta contra la `ValidatableObject<T>` `Value` propiedad. Errores de validación se colocan en el `Errors` propiedad de la `ValidatableObject<T>`instancia y el `IsValid` propiedad de la `ValidatableObject<T>` instancia se actualiza para indicar si la validación se realizó correctamente o no.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
