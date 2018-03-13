---
title: Entrada
description: "Texto de una línea o una contraseña de entrada"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 88b133a803aa74324e60a4f7610a2c0f0d31a807
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="entry"></a>Entrada

_Texto de una línea o una contraseña de entrada_

Xamarin.Forms `Entry` se usa para la entrada de texto de línea. `Entry`, al igual que la vista del Editor, admite varios tipos de teclado. Además, `Entry` se puede usar como un campo de contraseña.

## <a name="display-customization"></a>Personalización de la presentación

### <a name="setting-and-reading-text"></a>Establecer y leer texto

Entrada, al igual que otras vistas de presentación de texto, expone la `Text` propiedad. `Text` puede usarse para establecer y lea el texto presentado por el `Entry`. En el ejemplo siguiente se muestra cómo establecer el texto en XAML:

```xaml
<Entry Text="I am an Entry" />
```

En C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Para leer texto, obtener acceso a la `Text` propiedad en C#:

```csharp
var text = MyEntry.Text;
```

> [!NOTE]
> El ancho de un `Entry` puede definirse estableciendo su `WidthRequest` propiedad. No dependa en el ancho de un `Entry` que se define en función del valor de su `Text` propiedad.

### <a name="keyboards"></a>Teclados

El teclado que aparece cuando los usuarios interactúan con un `Entry` puede establecerse mediante programación a través del `Keyboard` propiedad.

Las opciones para el tipo de teclado son:

- **Valor predeterminado** &ndash; el teclado predeterminado
- **Chat** &ndash; utilizado para texto & lugares donde son útiles emoji
- **Correo electrónico** &ndash; utiliza al especificar direcciones de correo electrónico
- **Numérico** &ndash; usa al escribir números
- **Teléfono** &ndash; utiliza al especificar números de teléfono
- **Dirección URL** &ndash; utilizado para especificar las rutas de acceso de archivo y las direcciones web

Hay un [ejemplo de cada teclado](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) en nuestra sección de recetas.

### <a name="placeholders"></a>Marcadores de posición

`Entry` puede establecerse para mostrar texto de marcador de posición cuando no está almacenando proporcionados por el usuario. En la práctica, se considera a menudo en los formularios para aclarar el contenido que es adecuado para un campo determinado. Color del texto de marcador de posición no se puede personalizar y será el mismo independientemente de la `TextColor` configuración. Si el diseño requiere un color de marcador de posición personalizado, debe revertir a una [representador personalizado](). El siguiente creará un `Entry` "UserName" como el marcador de posición en XAML:

```xaml
<Entry Placeholder="Username" />
```

En C#:

```csharp
var MyEntry = new Entry { Placeholder = "Username" };
```

![](entry-images/placeholder.png "Ejemplo de entrada de marcador de posición")

### <a name="password-fields"></a>Campos de contraseña

`Entry` proporciona el `IsPassword` propiedad. Cuando `IsPassword` es `true`, el contenido del campo se mostrarán como círculos negros:

En XAML:

```xaml
<Entry IsPassword="true" />
```

En C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "Ejemplo de entrada de IsPassword")

Los marcadores de posición pueden utilizarse con instancias de `Entry` que están configurados como campos de contraseña:

En XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

En C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "Ejemplo de marcador de posición y IsPassword de entrada")


### <a name="colors"></a>Colores

Puede establecerse una entrada para usar colores de texto a través de las siguientes propiedades enlazables y un fondo personalizado:

- **TextColor** &ndash; establece el color del texto.
- **BackgroundColor** &ndash; establece el color que aparece detrás del texto.

Un cuidado especial es necesario para garantizar que los colores se podrán usar en cada plataforma. Dado que cada plataforma tiene distintos valores predeterminados para los colores de fondo y de texto, a menudo necesitará establecer ambos si establece uno.

Utilice el código siguiente para establecer el color del texto de una entrada:

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

Tenga en cuenta que el marcador de posición no está afectado por especificado `TextColor`.

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

- [TextChanged](http://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) &ndash; se produce cuando cambia el texto en la entrada. Proporciona el texto antes y después del cambio.
- [Completado](http://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/) &ndash; se produce cuando el usuario ha finalizado la entrada presionando la tecla ENTRAR del teclado.

### <a name="completed"></a>Completada

El `Completed` eventos se usan para reaccionar a la finalización de una interacción con una entrada. `Completed` se produce cuando el usuario finaliza la entrada con un campo especificando la tecla ENTRAR del teclado. El controlador para el evento es un controlador de eventos genéricos, teniendo el remitente y `EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

El evento completado puede suscribir en XAML:

```xaml
<Entry Completed="Entry_Completed" />
```

y C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

### <a name="textchanged"></a>TextChanged

El `TextChanged` eventos se usan para reaccionar a un cambio en el contenido de un campo.

`TextChanged` se produce cada vez que la `Text` de la `Entry` cambios. El controlador para el evento toma una instancia de `TextChangedEventArgs`. `TextChangedEventArgs` proporciona acceso a los valores antiguos y nuevos de la `Entry` `Text` a través de la `OldTextValue` y `NewTextValue` propiedades:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

El `TextChanged` eventos pueden suscribirse en XAML:

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
- [Entrada de API](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)
