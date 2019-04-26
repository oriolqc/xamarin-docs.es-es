---
title: Espacios de nombres XAML en Xamarin.Forms
description: XAML usa el atributo xmlns XML para las declaraciones de espacio de nombres. Este artículo presenta la sintaxis del espacio de nombres XAML y muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo.
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
ms.openlocfilehash: d307c128826775e6d4f7129c79e17522e7e05d6a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61176526"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>Espacios de nombres XAML en Xamarin.Forms

_XAML usa el atributo xmlns XML para las declaraciones de espacio de nombres. Este artículo presenta la sintaxis del espacio de nombres XAML y muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo._

## <a name="overview"></a>Información general

Hay dos declaraciones de espacio de nombres XAML que siempre están dentro del elemento raíz de un archivo XAML. La primera define el espacio de nombres predeterminado, tal como se muestra en el ejemplo de código XAML siguiente:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
```

El espacio de nombres predeterminado especifica que los elementos definidos dentro del archivo XAML con ningún prefijo hace referencia a clases de Xamarin.Forms, como [ `ContentPage` ](xref:Xamarin.Forms.ContentPage).

La segunda declaración de espacio de nombres usa el `x` prefijo, como se muestra en el ejemplo de código XAML siguiente:

```csharp
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML usa los prefijos para declarar los espacios de nombres no predeterminados, con el prefijo que se va a usar cuando se hace referencia a tipos del espacio de nombres. El `x` declaración de espacio de nombres especifica que los elementos definen en el XAML con el prefijo `x` se usan para los elementos y atributos que son intrínsecos de XAML (específicamente la especificación de XAML 2009).

La siguiente tabla se describen los `x` atributos de espacio de nombres compatibles con Xamarin.Forms:

|Construcción|Descripción|
|--- |--- |
|`x:Arguments`|Especifica los argumentos de constructor para un constructor no predeterminado, o para una declaración de objeto del método de fábrica.|
|`x:Class`|Especifica el nombre de clase y espacio de nombres para una clase definida en XAML. El nombre de clase debe coincidir con el nombre de clase del archivo de código subyacente. Tenga en cuenta que esta construcción sólo puede aparecer en el elemento raíz de un archivo XAML.|
|`x:DataType`|Especifica el tipo del objeto que se enlazan el elemento XAML y sus elementos secundarios, a.|
|`x:FactoryMethod`|Especifica un método de fábrica que puede usarse para inicializar un objeto.|
|`x:FieldModifier`|Especifica el nivel de acceso para los campos generados para los elementos XAML con nombre.|
|`x:Key`|Especifica una clave única definida por el usuario para cada recurso en un `ResourceDictionary`. El valor de clave se usa para recuperar el recurso XAML y se utiliza normalmente como argumento para el `StaticResource` extensión de marcado.|
|`x:Name`|Especifica un nombre de objeto en tiempo de ejecución para el elemento XAML. Establecer `x:Name` es similar a declarar una variable en el código.|
|`x:TypeArguments`|Especifica los argumentos de tipo genérico para el constructor de un tipo genérico.|

Para obtener más información sobre la `x:DataType` atributo, vea [compilado enlaces](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md). Para obtener más información sobre la `x:FieldModifier` atributo, vea [modificadores de campo](~/xamarin-forms/xaml/field-modifiers.md). Para obtener más información sobre la `x:Arguments`, `x:FactoryMethod`, y `x:TypeArguments` atributos, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

> [!NOTE]
> Además de los atributos de espacio de nombres enumerados anteriormente, Xamarin.Forms también incluye las extensiones de marcado que se pueden consumir mediante el `x` prefijo de espacio de nombres. Para obtener más información, consulte [las extensiones de marcado XAML de consumo](~/xamarin-forms/xaml/markup-extensions/consuming.md).

En XAML, las declaraciones de espacio de nombres se heredan de elemento primario al elemento secundario. Por lo tanto, al definir un espacio de nombres en el elemento raíz de un archivo XAML, todos los elementos dentro de ese archivo heredan la declaración de espacio de nombres.

## <a name="declaring-namespaces-for-types"></a>Declarar espacios de nombres para tipos

Pueden hacer referencia a tipos en XAML mediante la declaración de un espacio de nombres XAML con un prefijo, con la declaración de espacio de nombres que especifica el nombre del espacio de nombres de Common Language Runtime (CLR) y, opcionalmente, un nombre de ensamblado. Esto se logra mediante la definición de los valores de las siguientes palabras clave dentro de la declaración de espacio de nombres:

- **clr-namespace:** o **mediante:** : el espacio de nombres CLR declarado dentro del ensamblado que contiene los tipos que se exponen como elementos XAML. Esta palabra clave es necesaria.
- **ensamblado =** : el ensamblado que contiene el espacio de nombres CLR que se hace referencia. Este valor es el nombre del ensamblado, sin la extensión de archivo. La ruta de acceso al ensamblado se debe establecer como referencia en el archivo de proyecto que contiene el archivo XAML que hará referencia el ensamblado. Esta palabra clave se puede omitir si el **clr-namespace** valor está dentro del mismo ensamblado que el código de aplicación que hace referencia a los tipos.

Tenga en cuenta que el carácter que separa el `clr-namespace` o `using` símbolo (token) de su valor es un signo de dos puntos, mientras que el carácter que separa el `assembly` símbolo (token) de su valor es un signo igual. El carácter que se va a usar entre los dos tokens es un punto y coma.

El ejemplo de código siguiente muestra una declaración de espacio de nombres XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

Como alternativa, esto puede escribirse como:

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

El `local` prefijo es una convención que se utiliza para indicar que los tipos del espacio de nombres son locales de la aplicación. Como alternativa, si los tipos están en un ensamblado diferente, el nombre del ensamblado debe también se define en la declaración de espacio de nombres, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

El prefijo de espacio de nombres, a continuación, se especifica al declarar una instancia de un tipo a partir de un espacio de nombres importado, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

Para obtener información acerca de cómo definir un esquema del espacio de nombres personalizado, vea [esquemas de XAML personalizados Namespace](custom-namespace-schemas.md).

## <a name="summary"></a>Resumen

En este artículo introdujo la sintaxis del espacio de nombres XAML y se muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo. XAML usa la `xmlns` puede hacer referencia a las declaraciones de espacio de nombres y tipos de atributo XML en XAML mediante la declaración de un espacio de nombres XAML con un prefijo.

## <a name="related-links"></a>Vínculos relacionados

- [Pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
