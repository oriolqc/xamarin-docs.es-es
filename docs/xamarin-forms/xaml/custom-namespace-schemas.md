---
title: Esquemas personalizados Namespace XAML en Xamarin.Forms
description: Un esquema del espacio de nombres personalizado de XAML puede definirse con la clase XmlnsDefinitionAttribute, que especifica una asignación entre una dirección URL personalizada y uno o varios espacios de nombres CLR. El esquema del espacio de nombres personalizado se puede usar en las declaraciones del espacio de nombres XAML.
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
ms.openlocfilehash: 2e09e89fe17956efaef910638e827b69a5795bc0
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557222"
---
# <a name="xaml-custom-namespace-schemas-in-xamarinforms"></a>Esquemas personalizados Namespace XAML en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)

Pueden consultar en una biblioteca de tipos de XAML al declarar un espacio de nombres XAML para la biblioteca, con la declaración de espacio de nombres especificando el nombre de espacio de nombres de Common Language Runtime (CLR) y un nombre de ensamblado:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly="MyCompany.Controls">
    ...
</ContentPage>
```

Sin embargo, al especificar un nombre de espacio de nombres y ensamblado CLR en un `xmlns` definición puede ser difícil y propenso a errores. Además, varias declaraciones de espacio de nombres XAML pueden ser necesarias si la biblioteca contiene tipos en varios espacios de nombres.

Un enfoque alternativo es definir un esquema del espacio de nombres personalizado, como `http://mycompany.com/schemas/controls`, que se asigna a uno o varios espacios de nombres CLR. Esto permite una sola declaración de espacio de nombres XAML hacer referencia a todos los tipos en un ensamblado, incluso si se encuentran en diferentes espacios de nombres. También permite una sola declaración de espacio de nombres XAML a tipos de referencia en varios ensamblados.

Para obtener más información acerca de los espacios de nombres XAML, vea [espacios de nombres XAML en Xamarin.Forms](namespaces.md).

## <a name="defining-a-custom-namespace-schema"></a>Definir un esquema del espacio de nombres personalizado

La aplicación de ejemplo contiene una biblioteca que expone algunos controles simples, tales como `CircleButton`:

```csharp
using Xamarin.Forms;

namespace MyCompany.Controls
{
    public class CircleButton : Button
    {
        ...
    }
}
```

Todos los controles de la biblioteca que residen en el `MyCompany.Controls` espacio de nombres. Estos controles pueden exponerse a un ensamblado que realiza la llamada a través de un esquema del espacio de nombres personalizado.

Un esquema del espacio de nombres personalizado se define con el `XmlnsDefinitionAttribute` (clase), que especifica la asignación entre un espacio de nombres XAML y uno o varios espacios de nombres CLR. El `XmlnsDefinitionAttribute` toma dos argumentos: el nombre de espacio de nombres XAML y el nombre del espacio de nombres CLR. El nombre de espacio de nombres XAML se almacena en el `XmlnsDefinitionAttribute.XmlNamespace` propiedad y el nombre del espacio de nombres CLR se almacena en el `XmlnsDefinitionAttribute.ClrNamespace` propiedad.

> [!NOTE]
> El `XmlnsDefinitionAttribute` clase también tiene una propiedad denominada `AssemblyName`, que se puede establecer opcionalmente para el nombre del ensamblado. Esto solo es necesario cuando un espacio de nombres CLR al que hace referencia desde un `XmlnsDefinitionAttribute` está en un ensamblado externo.

El `XmlnsDefinitionAttribute` debe definirse en el nivel de ensamblado en el proyecto que contiene los espacios de nombres CLR que se asignará en el esquema del espacio de nombres personalizado. El ejemplo siguiente se muestra el **AssemblyInfo.cs** archivo desde la aplicación de ejemplo:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

Este código crea un esquema del espacio de nombres personalizado que se asigna el `http://mycompany.com/schemas/controls` dirección URL a la `MyCompany.Controls` espacio de nombres CLR. Además, el `Preserve` atributo se especifica en el ensamblado, para asegurarse de que el vinculador conserva todos los tipos del ensamblado.

> [!IMPORTANT]
> El `Preserve` atributo se debe aplicar a las clases del ensamblado que se asignan mediante el esquema del espacio de nombres personalizado o aplicado a todo el ensamblado.

A continuación, el esquema del espacio de nombres personalizado puede utilizarse para la resolución de tipos en archivos XAML.

## <a name="consuming-a-custom-namespace-schema"></a>Consumo de un esquema del espacio de nombres personalizado

Para consumir tipos desde el esquema del espacio de nombres personalizado, el compilador XAML requiere que hay una referencia de código desde el ensamblado que consume los tipos, al ensamblado que define los tipos. Esto puede realizarse mediante la adición de una clase que contiene un `Init` método para el ensamblado que define los tipos que van a consumir a través de XAML:

```csharp
namespace MyCompany.Controls
{
    public static class Controls
    {
        public static void Init()
        {
        }
    }
}
```

El `Init` , a continuación, se puede llamar al método desde el ensamblado que consume los tipos del esquema del espacio de nombres personalizado:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

namespace CustomNamespaceSchemaDemo
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            Controls.Init();
            InitializeComponent();
        }
    }
}
```

> [!WARNING]
> Se producirá un error al incluir este tipo de referencia de código en el compilador XAML que no se pueda localizar el ensamblado que contiene los tipos de esquema del espacio de nombres personalizado.

Para consumir el `CircleButton` control, se declara un espacio de nombres XAML, con la declaración de espacio de nombres que especifica la dirección URL de esquema de espacio de nombres personalizado:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="http://mycompany.com/schemas/controls"
             x:Class="CustomNamespaceSchemaDemo.MainPage">
    <StackLayout Margin="20,35,20,20">
        ...
        <controls:CircleButton Text="+"
                               BackgroundColor="Fuchsia"
                               BorderColor="Black"
                               CircleDiameter="100" />
        <controls:CircleButton Text="-"
                               BackgroundColor="Teal"
                               BorderColor="Silver"
                               CircleDiameter="70" />
        ...
    </StackLayout>
</ContentPage>
```

`CircleButton` las instancias, a continuación, se pueden agregar a la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) declarando con el `controls` prefijo de espacio de nombres.

Para buscar el espacio de nombres personalizado de tipos de esquema, buscará en ensamblados de referencia para Xamarin.Forms `XmlnsDefinitionAttribute` instancias. Si el `xmlns` atributo para un elemento en un archivo XAML coincide con el `XmlNamespace` valor de propiedad en un `XmlnsDefinitionAttribute`, Xamarin.Forms intentará usar la `XmlnsDefinitionAttribute.ClrNamespace` valor de propiedad para la resolución del tipo. Si se produce un error en la resolución de tipos, Xamarin.Forms seguirá tratando de resolución de tipos en función de cualquier coincidencia adicional `XmlnsDefinitionAttribute` instancias.

El resultado es que dos `CircleButton` se muestran las instancias:

![Círculo botones](custom-namespace-schemas-images/circle-buttons.png "círculo botones")

## <a name="related-links"></a>Vínculos relacionados

- [Esquemas personalizados de Namespace (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)
- [XAML Namespace recomienda prefijos](custom-prefix.md)
- [Espacios de nombres XAML en Xamarin.Forms](namespaces.md)
