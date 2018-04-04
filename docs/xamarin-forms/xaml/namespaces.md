---
title: Espacios de nombres XAML
description: XAML usa el atributo XML de xmlns para las declaraciones de espacio de nombres. Este artículo presenta la sintaxis de espacio de nombres XAML y muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo.
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/10/2017
ms.openlocfilehash: ecf255d2fc6937e93258eaa021f79152cb7b1c7f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="xaml-namespaces"></a>Espacios de nombres XAML

_XAML usa el atributo XML de xmlns para las declaraciones de espacio de nombres. Este artículo presenta la sintaxis de espacio de nombres XAML y muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo._

## <a name="overview"></a>Información general

Hay dos declaraciones de espacio de nombres XAML que siempre están dentro del elemento raíz de un archivo XAML. El primero define el espacio de nombres predeterminado, tal como se muestra en el siguiente ejemplo de código XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
```

El espacio de nombres predeterminado especifica que los elementos definidos en el archivo XAML no tiene ningún prefijo hacen referencia a las clases de Xamarin.Forms, como [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/).

La segunda declaración de espacio de nombres se utiliza el `x` de prefijo, como se muestra en el siguiente ejemplo de código XAML:

```csharp
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML usa los prefijos para declarar espacios de nombres no predeterminado, con el prefijo que se va a utilizar al hacer referencia a tipos en el espacio de nombres. El `x` declaración de espacio de nombres especifica que los elementos definen en el código XAML con un prefijo de `x` se utilizan para los elementos y atributos que son intrínsecos de XAML (específicamente la especificación de XAML 2009).

La siguiente tabla se describen los `x` atributos de espacio de nombres admitidos por Xamarin.Forms:

|Construcción|Descripción|
|--- |--- |
|`x:Arguments`|Especifica los argumentos de constructor de un constructor no predeterminado o de una declaración de objeto del método de fábrica.|
|`x:Class`|Especifica el espacio de nombres y el nombre de una clase definida en XAML. El nombre de clase debe coincidir con el nombre de clase del archivo de código subyacente. Tenga en cuenta que esta construcción solo puede aparecer en el elemento raíz de un archivo XAML.|
|`x:FactoryMethod`|Especifica un método de fábrica que puede usarse para inicializar un objeto.|
|`x:Key`|Especifica una clave única definida por el usuario para cada recurso en un `ResourceDictionary`. Valor de la clave se usa para recuperar el recurso XAML y se utiliza normalmente como argumento para el `StaticResource` extensión de marcado.|
|`x:Name`|Especifica un nombre de objeto en tiempo de ejecución para el elemento XAML. Establecer `x:Name` es similar a declarar una variable en el código.|
|`x:TypeArguments`|Especifica los argumentos de tipo genérico para el constructor de un tipo genérico.|

Para obtener más información sobre la `x:Arguments`, `x:FactoryMethod`, y `x:TypeArguments` atributos, vea [pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md).

En XAML, las declaraciones de espacio de nombres heredan del elemento primario a elemento secundario. Por lo tanto, al definir un espacio de nombres en el elemento raíz de un archivo XAML, todos los elementos dentro de ese archivo heredan la declaración de espacio de nombres.

## <a name="declaring-namespaces-for-types"></a>Declarar espacios de nombres de tipos

Pueden hacer referencia a tipos en XAML mediante la declaración de un espacio de nombres XAML con un prefijo con la declaración de espacio de nombres especificando el nombre de espacio de nombres de Common Language Runtime (CLR) y, opcionalmente, un nombre de ensamblado. Esto se consigue mediante la definición de valores para las siguientes palabras clave dentro de la declaración de espacio de nombres:

- **espacio de nombres CLR:** o **con:** : el espacio de nombres CLR declarado dentro del ensamblado que contiene los tipos que se va a exponer como elementos XAML. Esta palabra clave es necesaria.
- **ensamblado =** : el ensamblado que contiene el espacio de nombres CLR que se hace referencia. Este valor es el nombre del ensamblado sin la extensión de archivo. La ruta de acceso al ensamblado que se debe establecer como una referencia en el archivo de proyecto que contiene el archivo XAML que hará referencia el ensamblado. Esta palabra clave se puede omitir si el **clr-namespace** valor está comprendido en el mismo ensamblado que el código de aplicación que hace referencia a los tipos.

Tenga en cuenta que el carácter que separa el `clr-namespace` o `using` token respecto de su valor es un signo de dos puntos, mientras que el carácter que separa el `assembly` token respecto de su valor es un signo igual. El carácter que se va a usar entre los dos tokens es un punto y coma.

En el ejemplo de código siguiente se muestra una declaración de espacio de nombres XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

O bien, esto puede escribirse como:

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

El `local` prefijo es una convención usada para indicar que los tipos en el espacio de nombres son locales de la aplicación. O bien, si los tipos están en otro ensamblado, el nombre del ensamblado debe también definirse en la declaración de espacio de nombres, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

El prefijo de espacio de nombres, a continuación, se especifica al declarar una instancia de un tipo de un espacio de nombres importado, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

## <a name="summary"></a>Resumen

Este artículo introdujo la sintaxis de espacio de nombres XAML y muestra cómo declarar un espacio de nombres XAML para tener acceso a un tipo. XAML usa el `xmlns` pueden hacer referencia a atributos XML para las declaraciones de espacio de nombres y tipos en XAML mediante la declaración de un espacio de nombres XAML con un prefijo.


## <a name="related-links"></a>Vínculos relacionados

- [Pasar argumentos en XAML](~/xamarin-forms/xaml/passing-arguments.md)
