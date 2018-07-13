---
title: Entrada de Xamarin.Forms
description: En este artículo se explica cómo usar la clase de entrada de Xamarin.Forms para aceptar el texto de una línea o una entrada de contraseña en una aplicación.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 95afdfde878759d4a598e200d16fe6fb1fa2005e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998253"
---
# <a name="xamarinforms-entry"></a>Entrada de Xamarin.Forms

_Texto de una línea o la contraseña de entrada_

Xamarin.Forms `Entry` se usa para la entrada de texto de una línea. El `Entry`, como el `Editor` ver, admite varios tipos de teclado. Además, el `Entry` puede usarse como un campo de contraseña.

## <a name="display-customization"></a>Personalización de la presentación

### <a name="setting-and-reading-text"></a>Establecimiento y lectura de texto

El `Entry`, al igual que otras vistas de presentación de texto, expone el `Text` propiedad. Esta propiedad se puede utilizar para establecer y leer el texto presentado por el `Entry`. El ejemplo siguiente se muestra cómo establecer el `Text` propiedad en XAML:

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

> [!NOTE]
> El ancho de un `Entry` puede definirse estableciendo su `WidthRequest` propiedad. No dependen del ancho de un `Entry` que se define en función del valor de su `Text` propiedad.

### <a name="limiting-input-length"></a>Limitar la longitud de entrada

El [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) propiedad puede utilizarse para limitar la longitud de entrada que sea aceptable para el [ `Entry` ](xref:Xamarin.Forms.Entry). Esta propiedad debe establecerse en un entero positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Un [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) valor de propiedad de 0 indica que no se permitirá ninguna entrada y un valor de `int.MaxValue`, que es el valor predeterminado para un [ `Entry` ](xref:Xamarin.Forms.Entry), indica que no hay ninguna límite real del número de caracteres que pueden especificarse.

### <a name="keyboards"></a>Teclados

El teclado que aparece cuando los usuarios interactúan con un `Entry` se puede establecer mediante programación a través de la `Keyboard` propiedad.

Las opciones para el tipo de teclado son:

- **Valor predeterminado** &ndash; el teclado predeterminado
- **Chat** &ndash; usado para texto y los lugares donde son útiles emoji
- **Correo electrónico** &ndash; utilizado al especificar direcciones de correo electrónico
- **Numérico** &ndash; usa al escribir números
- **Teléfono** &ndash; utilizado al especificar números de teléfono
- **Dirección URL** &ndash; utilizado para escribir las rutas de acceso de archivo y las direcciones web

Hay un [ejemplo de cada teclado](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) en nuestra sección de recetas.

### <a name="enabling-and-disabling-spell-checking"></a>Habilitar y deshabilitar el corrector ortográfico

El [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad controla si corrector ortográfico está habilitado. De forma predeterminada, la propiedad se establece en `true`. Cuando el usuario escriba texto, se indican los errores de ortografía.

Sin embargo, algunos escenarios de entrada de texto, como escribir un nombre de usuario, corrector ortográfico ofrece una experiencia negativa de modo que deben deshabilitarse estableciendo el [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Cuando el [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) propiedad está establecida en `false`y no se usa un teclado personalizado, se deshabilitará el corrector ortográfico nativo. Sin embargo, si un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) ha sido conjunto que deshabilita el corrector comprobación, como [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), el `IsSpellCheckEnabled` se omite. Por lo tanto, no se puede usar la propiedad para habilitar el corrector ortográfico para un `Keyboard` que deshabilita de forma explícita.

### <a name="placeholders"></a>Marcadores de posición

`Entry` se puede establecer para mostrar texto de marcador de posición cuando no está almacenando proporcionados por el usuario. En la práctica, esto suele aparecer en los formularios para aclarar el contenido que sea adecuado para un campo determinado. Color del texto de marcador de posición no se pueden personalizar y será el mismo independientemente de la `TextColor` configuración. Si el diseño requiere un color de marcador de posición personalizado, deberá revertir a una [representador personalizado](). La siguiente creará un `Entry` con "Username" como marcador de posición en XAML:

```xaml
<Entry Placeholder="Username" />
```

En C#:

```csharp
var MyEntry = new Entry { Placeholder = "Username" };
```

![](entry-images/placeholder.png "Ejemplo de entrada de marcador de posición")

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

- [TextChanged](xref:Xamarin.Forms.Entry.TextChanged) &ndash; se genera cuando cambia el texto en la entrada. Proporciona el texto antes y después del cambio.
- [Completado](xref:Xamarin.Forms.Entry.Completed) &ndash; se genera cuando el usuario ha finalizado la entrada presionando la tecla ENTRAR del teclado.

### <a name="completed"></a>Completada

El `Completed` evento sirve para reaccionar a la finalización de una interacción con una entrada. `Completed` se produce cuando el usuario finaliza la entrada con un campo especificando la tecla ENTRAR del teclado. El controlador para el evento es un controlador de eventos genéricos, tomando el remitente y `EventArgs`:

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
