---
title: Navegación mediante teclado
description: En lugar de usar la secuencia de tabulación de forma predeterminada, a veces es necesario ajustar la interfaz de usuario mediante la especificación de la secuencia de tabulación con una combinación de las propiedades TabIndex y IsTapStop.
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: f703dff56d2947c35a9bc76e0eb909bfe9023bac
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131955"
---
# <a name="keyboard-navigation-in-xamarinforms"></a>Navegación mediante el teclado en Xamarin.Forms

Algunos usuarios pueden tener dificultades para usar las aplicaciones que no proporcionan acceso de teclado correspondiente. Especificar un orden de tabulación de controles permite la navegación por teclado y prepara las páginas de aplicación para recibir entradas en un orden concreto.

De forma predeterminada, el orden de tabulación de controles es el mismo orden en que se están enumerados en XAML o agregar mediante programación a una colección secundaria. Este orden es el orden en que los controles se abrirá a través de con un teclado y, a menudo este orden predeterminado es el mejor orden posible. Sin embargo, el orden predeterminado no es siempre el mismo que el orden esperado, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname" />
</Grid>
```

Captura de pantalla siguiente muestra el orden de tabulación predeterminada para este ejemplo de código:

![](keyboard-images/default-tab-order.png "Orden de tabulación en función de fila predeterminado")

El orden de tabulación aquí está basado en la fila y es el orden de que los controles aparecen en el XAML. Por lo tanto, al presionar la tecla Tab se desplaza por apellidos [ `Entry` ](xref:Xamarin.Forms.Entry) instancias, seguidas por el apellido `Entry` instancias. Sin embargo, una experiencia más intuitiva sería usar una navegación por tabulación de la columna en primer lugar, por lo que al presionar la tecla Tab se desplaza por los pares de nombre apellido. Esto puede lograrse mediante la especificación del orden de tabulación de los controles de entrada.

> [!NOTE]
> En la plataforma Universal de Windows, se pueden definir métodos abreviados de teclado que proporcionan una manera intuitiva para los usuarios a navegar rápidamente e interactuar con de interfaz de usuario visible la aplicación a través de un teclado en lugar de a través de funciones táctiles o un mouse. Para obtener más información, consulte [claves de acceso de configuración VisualElement](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#visualelement-accesskeys).

## <a name="setting-the-tab-order"></a>Establecer el orden de tabulación

El `VisualElement.TabIndex` propiedad se utiliza para indicar el orden en que [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) instancias reciben el foco cuando el usuario navega por los controles presionando la tecla Tab. El valor predeterminado de la propiedad es 0, y se puede establecer en cualquier `int` valor.

Las reglas siguientes aplican cuando usa el orden de tabulación predeterminado, o cuando el `TabIndex` propiedad:

 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) las instancias con un `TabIndex` es igual a 0 se agregan a la orden de tabulación basándose en su orden de declaración en colecciones de XAML o secundarios.
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) las instancias con un `TabIndex` mayor que 0 se agregan en el orden de tabulación en función de sus `TabIndex` valor.
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) las instancias con un `TabIndex` menores que 0 se agregan en el orden de tabulación y aparecen antes que cualquier valor cero.
 - Entra en conflicto en un `TabIndex` se resuelven por orden de declaración.

Después de definir un orden de tabulación, al presionar la tecla Tab se recorrerá en ciclo el foco a través de controles en orden ascendente `TabIndex` orden, alrededor de ajuste al principio una vez alcanzado el final de los controles.

El siguiente ejemplo XAML muestra el `TabIndex` propiedad establecida en los controles de entrada para habilitar la navegación por tabulación de la columna en primer lugar:

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="1" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="3" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="2" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="4" />
</Grid>
```

Captura de pantalla siguiente muestra el orden de tabulación para este ejemplo de código:

![](keyboard-images/correct-tab-order.png "Orden de tabulación basado en columnas")

El orden de tabulación aquí está basado en la columna. Por lo tanto, al presionar la tecla Tab se desplaza por apellidos surname [ `Entry` ](xref:Xamarin.Forms.Entry) pares.

## <a name="excluding-controls-from-the-tab-order"></a>Excluidos los controles del orden de tabulación

Además de establecer el orden de tabulación de controles, puede ser necesario excluir los controles de la orden de tabulación. Una manera de conseguirlo es estableciendo el [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement) propiedad de los controles a `false`, ya que se excluyen los controles deshabilitados en el orden de tabulación.

Sin embargo, puede ser necesario excluir los controles del orden de tabulación, aunque no están deshabilitados. Esto puede lograrse con la `VisualElement.IsTapStop` propiedad, que indica si un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) se incluye en la navegación por tabulación. Su valor predeterminado es `true`, y cuando su valor es `false` se omite el control por la infraestructura de navegación de la ficha, independientemente de si un `TabIndex` está establecido.

## <a name="supported-controls"></a>Controles admitidos

El `TabIndex` y `IsTapStop` se admiten las propiedades en los siguientes controles, que aceptan entradas de teclado en una o varias plataformas:

- [`Button`](xref:Xamarin.Forms.Button)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`Switch`](xref:Xamarin.Forms.Switch)
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

> [!NOTE]
> Cada uno de estos controles no puede recibir el foco en todas las plataformas de ficha.

## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
