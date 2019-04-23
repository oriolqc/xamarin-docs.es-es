---
title: Entrada de Xamarin.Forms
description: En este artículo se explica cómo usar la clase de entrada de Xamarin.Forms para aceptar el texto de una línea o una entrada de contraseña en una aplicación.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2018
ms.openlocfilehash: c9be74bcc6e5d149856e06c98d3bef9a841d4bab
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230250"
---
# <a name="xamarinforms-entry"></a>Entrada de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_Texto de una línea o la contraseña de entrada_

Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry) se usa para la entrada de texto de una línea. El `Entry`, como el [ `Editor` ](xref:Xamarin.Forms.Editor) ver, admite varios tipos de teclado. Además, el `Entry` puede usarse como un campo de contraseña.

## <a name="display-customization"></a>Personalización de la presentación

### <a name="setting-and-reading-text"></a>Establecimiento y lectura de texto

El `Entry`, al igual que otras vistas de presentación de texto, expone el [ `Text` ](xref:Xamarin.Forms.Entry.Text) propiedad. Esta propiedad se puede utilizar para establecer y leer el texto presentado por el `Entry`. El ejemplo siguiente se muestra cómo establecer el `Text` propiedad en XAML:

```xaml
<Entry Text="I am an Entry" />
```

En C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Para leer el texto, obtener acceso a la `Text` propiedad en C#:

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>Establecer el texto de marcador de posición

El [ `Entry` ](xref:Xamarin.Forms.Entry) puede establecerse para mostrar texto de marcador de posición cuando no está almacenando proporcionados por el usuario. Esto se consigue estableciendo la [ `Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder) propiedad a un `string`y a menudo se usa para indicar el tipo de contenido que sea adecuada para el `Entry`. Además, el color del texto de marcador de posición puede controlarse estableciendo la [ `PlaceholderColor` ](xref:Xamarin.Forms.Entry.PlaceholderColor) propiedad a un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> El ancho de un `Entry` puede definirse estableciendo su `WidthRequest` propiedad. No dependen del ancho de un `Entry` que se define en función del valor de su `Text` propiedad.

### <a name="preventing-text-entry"></a>Impedir la entrada de texto

Pueden ser impide que los usuarios modificar el texto en un [ `Entry` ](xref:Xamarin.Forms.Entry) estableciendo el `IsReadOnly` propiedad, que tiene un valor predeterminado de `false`a `true`:

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> El `IsReadonly` propiedad no modifica la apariencia visual de un [ `Entry` ](xref:Xamarin.Forms.Entry), a diferencia el `IsEnabled` propiedad que también cambia la apariencia visual de la `Entry` a gris.

### <a name="limiting-input-length"></a>Limitar la longitud de entrada

El [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propiedad puede utilizarse para limitar la longitud de entrada que sea aceptable para el [ `Entry` ](xref:Xamarin.Forms.Entry). Esta propiedad debe establecerse en un entero positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Un [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) valor de propiedad de 0 indica que no se permitirá ninguna entrada y un valor de `int.MaxValue`, que es el valor predeterminado para un [ `Entry` ](xref:Xamarin.Forms.Entry), indica que no hay ninguna límite real del número de caracteres que pueden especificarse.

### <a name="password-fields"></a>Campos de contraseña

`Entry` proporciona el `IsPassword` propiedad. Cuando `IsPassword` es `true`, se mostrará el contenido del campo como círculos negros:

En XAML:

```xaml
<Entry IsPassword="true" />
```

En C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "Ejemplo de entrada de IsPassword")

Marcadores de posición se pueden usar con instancias de `Entry` que están configurados como campos de contraseña:

En XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

En C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "Ejemplo de marcador de posición y entrada IsPassword")

### <a name="setting-the-cursor-position-and-text-selection-length"></a>Establecer la posición del Cursor y la longitud de la selección de texto

El [ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition) propiedad puede usarse para devolver o establecer la posición en la que se insertará el siguiente carácter en la cadena almacenada en el [ `Text` ](xref:Xamarin.Forms.Entry.Text) propiedad:

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

El valor predeterminado de la [ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition) propiedad es 0, lo que indica que se insertará el texto al principio de la `Entry`.

Además, el [ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength) propiedad puede usarse para devolver o establecer la longitud de selección de texto dentro de la `Entry`:

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

El valor predeterminado de la [ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength) propiedad es 0, lo que indica que no hay texto seleccionado.

### <a name="customizing-the-keyboard"></a>Personalizar el teclado

El teclado que aparece cuando los usuarios interactúan con un [ `Entry` ](xref:Xamarin.Forms.Entry) se puede establecer mediante programación a través de la [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) propiedad a una de las siguientes propiedades desde el [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) clase:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) : se usa para el texto y los lugares donde emoji son útiles.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – el teclado predeterminado.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) – utilizado al especificar direcciones de correo electrónico.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) : usa al escribir números.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) : usa al escribir texto, sin ninguna [ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags) especificado.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) : usa al escribir los números de teléfono.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) : usa al escribir texto.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) : se usa para especificar las rutas de acceso de archivo & direcciones web.

Esto puede realizarse en XAML como sigue:

```xaml
<Entry Keyboard="Chat" />
```

El código de C# equivalente es:

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

Ejemplos de cada teclado pueden encontrarse en nuestra [recetas](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) repositorio.

El [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) clase también tiene un [ `Create` ](xref:Xamarin.Forms.Keyboard.Create*) método generador que puede usarse para personalizar un teclado mediante la especificación del comportamiento de mayúsculas y minúsculas, corrector ortográfico y sugerencias. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) valores de enumeración se especifican como argumentos al método, con una personalizada `Keyboard` que se devuelve. El `KeyboardFlags` enumeración contiene los siguientes valores:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) : no hay características se agregan al teclado.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) : indica que la primera letra de la primera palabra de cada oración especificado estará automáticamente en mayúscula.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) : indica que spellcheck se realizarán en el texto especificado.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) : indica que se ofrecerán las finalizaciones de word en el texto especificado.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) : indica que la primera letra de cada palabra estará automáticamente en mayúscula.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) : indica que todos los caracteres se escribirán automáticamente en mayúsculas.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) : indica que no se producirá ningún uso automático de mayúsculas.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) : indica que se producirán spellcheck, las finalizaciones de palabra y frase mayúsculas en el texto especificado.

El ejemplo de código XAML siguiente muestra cómo personalizar el valor predeterminado [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) para ofrecer las finalizaciones de word y poner en mayúsculas cada carácter escrito:

```xaml
<Entry Placeholder="Enter text here">
    <Entry.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Entry.Keyboard>
</Entry>
```

El código de C# equivalente es:

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

#### <a name="customizing-the-return-key"></a>Personalización de la tecla ENTRAR

La apariencia de la tecla ENTRAR en el teclado en pantalla, que es que se muestra cuando un [ `Entry` ](xref:Xamarin.Forms.Entry) tiene el foco, se pueden personalizar mediante el establecimiento del [ `ReturnType` ](xref:Xamarin.Forms.Entry.ReturnType) propiedad con un valor de la [ `ReturnType` ](xref:Xamarin.Forms.ReturnType) enumeración:

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) : indica que se requiere ninguna clave de valor devuelta concreta y que se usará el valor predeterminado de la plataforma.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) : indica una clave de valor devuelta "Done".
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) : indica una clave de valor devuelta "Ir".
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) : indica una clave de valor devuelta "Siguiente".
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) : indica una clave de valor devuelta de "Búsqueda".
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) : indica una tecla de retorno del "Envío".

En el siguiente ejemplo XAML se muestra cómo establecer la tecla ENTRAR:

```xaml
<Entry ReturnType="Send" />
```

El código de C# equivalente es:

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> El aspecto exacto de la clave de valor devuelto depende de la plataforma. En iOS, la clave de valor devuelta es un botón basado en texto. Sin embargo, en plataformas de Windows Universal y Android, la clave devuelta es un botón de icono.

Cuando se presiona la tecla ENTRAR, el [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) desencadena el evento y cualquier `ICommand` especificado por el [ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand) se ejecuta la propiedad. Además, cualquier `object` especificado por el [ `ReturnCommandParameter` ](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propiedad se pasará a la `ICommand` como un parámetro. Para obtener más información acerca de los comandos, consulte [la interfaz de comandos](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar y deshabilitar el corrector ortográfico

El [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad controla si corrector ortográfico está habilitado. De forma predeterminada, la propiedad se establece en `true`. Cuando el usuario escriba texto, se indican los errores de ortografía.

Sin embargo, para algunos escenarios de entrada de texto, como escribir un nombre de usuario, corrector ortográfico proporciona una experiencia negativa y debe deshabilitarse estableciendo el [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Cuando el [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad está establecida en `false`y no se usa un teclado personalizado, se deshabilitará el corrector ortográfico nativo. Sin embargo, si un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) ha sido conjunto que deshabilita el corrector comprobación, como [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), el `IsSpellCheckEnabled` se omite. Por lo tanto, no se puede usar la propiedad para habilitar el corrector ortográfico para un `Keyboard` que deshabilita de forma explícita.

### <a name="enabling-and-disabling-text-prediction"></a>Habilitación y deshabilitación de la predicción de texto

El [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propiedad controla si la predicción de texto y automática está habilitada la corrección de texto. De forma predeterminada, la propiedad se establece en `true`. Cuando el usuario escriba texto, se presentan las predicciones de word.

Sin embargo, para algunos escenarios de entrada de texto, como escribir un nombre de usuario, la predicción de texto y texto automático corrección proporciona una experiencia negativa y debe deshabilitarse estableciendo el [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propiedad `false`:

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Cuando el [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) propiedad está establecida en `false`, y un teclado personalizado no es que se va a usar, la predicción de texto y automática se deshabilita la corrección de texto. Sin embargo, si un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) se ha establecido que deshabilita la predicción de texto, el `IsTextPredictionEnabled` se omite. Por lo tanto, no se puede usar la propiedad para habilitar la predicción de texto para un `Keyboard` que deshabilita de forma explícita.

### <a name="colors"></a>Colores

Puede establecerse una entrada para usar un fondo personalizado y los colores de texto a través de las propiedades enlazables siguientes:

- **TextColor** &ndash; establece el color del texto.
- **BackgroundColor** &ndash; establece el color que aparece detrás del texto.

Atención especial es necesaria para asegurarse de que los colores se podrán usar en cada plataforma. Dado que cada plataforma tiene distintos valores predeterminados para los colores de texto y fondo, a menudo deberá establecer ambos si establece uno.

Use el código siguiente para establecer el color del texto de una entrada:

En XAML:

```xaml
<Entry TextColor="Green" />
```

En C#:

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![](entry-images/textcolor.png "Ejemplo de entrada de TextColor")

Tenga en cuenta que el marcador de posición no está afectado por el `TextColor`.

Para establecer el color de fondo en XAML:

```xaml
<Entry BackgroundColor="#2c3e50" />
```

En C#:

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![](entry-images/textbackgroundcolor.png "Ejemplo de entrada de BackgroundColor")

Tenga cuidado para asegurarse de que los colores de fondo y de texto que elija puede utilizar en cada plataforma y no interferir con cualquier texto de marcador de posición.

## <a name="events-and-interactivity"></a>Eventos e interactividad

Entrada expone dos eventos:

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) &ndash; se genera cuando cambia el texto en la entrada. Proporciona el texto antes y después del cambio.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; se genera cuando el usuario ha finalizado la entrada presionando la tecla ENTRAR del teclado.

> [!NOTE]
> El [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) (clase), desde el que [ `Entry` ](xref:Xamarin.Forms.Entry) hereda, también tiene [ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused) y [ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused)eventos.

### <a name="completed"></a>Completada

El `Completed` evento sirve para reaccionar a la finalización de una interacción con una entrada. `Completed` se produce cuando el usuario finaliza la entrada con un campo presionando la tecla ENTRAR del teclado. El controlador para el evento es un controlador de eventos genéricos, tomando el remitente y `EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

El evento completado se puede suscribir en XAML:

```xaml
<Entry Completed="Entry_Completed" />
```

y C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Después de la [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) evento se activa, cualquier `ICommand` especificado por el [ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand) se ejecuta la propiedad, con el `object` especificado por el [ `ReturnCommandParameter` ](xref:Xamarin.Forms.Entry.ReturnCommandParameter) propiedad que se pasan a la `ICommand`.

### <a name="textchanged"></a>TextChanged

El `TextChanged` evento sirve para reaccionar ante un cambio en el contenido de un campo.

`TextChanged` se produce cada vez que el `Text` de la `Entry` cambios. El controlador para el evento toma una instancia de `TextChangedEventArgs`. `TextChangedEventArgs` proporciona acceso a los valores antiguos y nuevos de la `Entry` `Text` a través de la `OldTextValue` y `NewTextValue` propiedades:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

El `TextChanged` se pueden suscribir a eventos en XAML:

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

y C#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```


## <a name="related-links"></a>Vínculos relacionados

- [Texto (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Entrada de API](xref:Xamarin.Forms.Entry)
