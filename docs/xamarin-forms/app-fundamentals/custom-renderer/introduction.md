---
title: Introducción a los representadores personalizados
description: En este artículo proporciona una introducción a los representadores personalizados y describe el proceso para crear a un representador personalizado.
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: 180a196e0b95854815c8a74ef1d2df63407dd04f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998009"
---
# <a name="introduction-to-custom-renderers"></a>Introducción a los representadores personalizados

_Los representadores personalizados proporcionan un método eficaz para personalizar la apariencia y comportamiento de los controles de Xamarin.Forms. Se puede usar para los cambios de estilo pequeño o diseños sofisticados de específicos de la plataforma y personalización del comportamiento. En este artículo proporciona una introducción a los representadores personalizados y describe el proceso para crear a un representador personalizado._

Xamarin.Forms [páginas, diseños y controles](~/xamarin-forms/user-interface/controls/index.md) presentar una API común para describir interfaces de usuario móviles multiplataforma. Cada página, el diseño y el control se representan de manera diferente en cada plataforma, con un `Renderer` clase que a su vez crea un control nativo (que corresponde a la representación de Xamarin.Forms), lo organiza en la pantalla y agrega el comportamiento especificado en el código compartido.

Los desarrolladores pueden implementar sus propias clases `Renderer` personalizadas para personalizar la apariencia o el comportamiento de un control. Los representadores personalizados para un tipo determinado se pueden agregar al proyecto de una aplicación para personalizar el control en un solo lugar al tiempo que permite el comportamiento predeterminado en otras plataformas; o distintos representadores personalizados se pueden agregar a cada proyecto de aplicación para crear una apariencia diferente en la plataforma Universal de Windows (UWP), iOS y Android. Sin embargo, a menudo es una respuesta exhaustiva implementar una clase de representador personalizado para llevar a cabo una personalización de controles simples. Efectos de simplifican este proceso y se suelen usar para los cambios de estilo pequeño. Para obtener más información, consulte [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Efectos).

## <a name="examining-why-custom-renderers-are-necessary"></a>Examinar representadores personalizados por qué son necesarios

Cambiar la apariencia de un control de Xamarin.Forms, sin usar a un representador personalizado, es un proceso de dos pasos que implica la creación de un control personalizado a través de la creación de subclases de y, a continuación, consumiendo el control personalizado en lugar del control original. En el ejemplo de código siguiente se muestra un ejemplo de creación de subclases el `Entry` control:

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

El `MyEntry` control es un `Entry` controlar dónde el `BackgroundColor` está establecido en gris y Xaml puede consultar declarando un espacio de nombres para su ubicación y el uso del prefijo de espacio de nombres en el elemento de control. El siguiente ejemplo de código muestra cómo el `MyEntry` control personalizado puede consumir una `ContentPage`:

```xaml
<ContentPage
    ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

El `local` prefijo de espacio de nombres puede ser cualquier cosa. Sin embargo, el `namespace` y `assembly` valores deben coincidir con los detalles del control personalizado. Una vez que se declara el espacio de nombres, el prefijo se utiliza para hacer referencia al control personalizado.

> [!NOTE]
> Definir la `xmlns` es mucho más sencillo en proyectos de biblioteca estándar de .NET que los proyectos compartidos. Una biblioteca .NET Standard se compila en un ensamblado, por lo que es fácil determinar lo que el `assembly=CustomRenderer` debe ser el valor. Cuando se usa proyectos compartidos, todos los recursos compartidos (incluido el XAML) se compilan en cada uno de los proyectos de referencia, lo que significa que si los iOS, Android y UWP proyectos tienen sus propios *nombres de ensamblado* es imposible escribir el `xmlns` declaración porque el valor debe ser diferente para cada aplicación. Controles personalizados en XAML para proyectos compartidos requerirá cada proyecto de aplicación se configura con el mismo nombre de ensamblado.

El `MyEntry` control personalizado, a continuación, se representa en cada plataforma, con un fondo gris, como se muestra en las capturas de pantalla siguiente:

![](introduction-images/screenshots.png "Control personalizado de MyEntry en cada plataforma")

Cambiar el color de fondo del control en cada plataforma ha sido exitosa puramente a través de la creación de subclases del control. Sin embargo, esta técnica está limitada en lo que puede lograr cuando no sea posible aprovechar las mejoras específicas de plataforma y las personalizaciones. Cuando se requieren, se deben implementar los representadores personalizados.

## <a name="creating-a-custom-renderer-class"></a>Creación de una clase de representador personalizado

El proceso de creación de una clase de representador personalizado es como sigue:

1. Crear una subclase de la clase de representador que representa el control nativo.
1. Reemplace el método que representa el control nativo y escribir una lógica para personalizar el control. A menudo, el `OnElementChanged` método se utiliza para representar el control nativo, que se llama cuando se crea el control correspondiente de Xamarin.Forms.
1. Agregar un `ExportRenderer` a la clase de representador personalizado para especificar que se utilizará para representar el control de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> Para la mayoría de los elementos de Xamarin.Forms, es opcional proporcionar a un representador personalizado en cada proyecto de la plataforma. Si no está registrado un representador personalizado, se usará el representador predeterminado de la clase base del control. Sin embargo, los representadores personalizados son necesarios en cada proyecto de la plataforma al representar un [vista](xref:Xamarin.Forms.View) o [ViewCell](xref:Xamarin.Forms.ViewCell) elemento.

Se proporcionará en los temas de esta serie de demostraciones y explicaciones de este proceso para distintos elementos de Xamarin.Forms.

## <a name="troubleshooting"></a>Solución de problemas

Si se encuentra un control personalizado en un proyecto de biblioteca estándar de .NET que se ha agregado a la solución (es decir, no la biblioteca .NET Standard creada por Visual Studio para la plantilla de proyecto de aplicación de Xamarin.Forms Mac o Visual Studio), una excepción puede producirse en iOS cuando al intentar tener acceso al control personalizado. Si se produce este problema se puede resolver mediante la creación de una referencia al control personalizado de la `AppDelegate` clase:

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

Esto obliga a que el compilador reconozca el `ClassInPCL` tipo por resolver el problema. Como alternativa, el `Preserve` atributo puede agregarse a la `AppDelegate` clase para conseguir el mismo resultado:

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

Esto crea una referencia a la `ClassInPCL` tipo, que indica que es necesario en tiempo de ejecución. Para obtener más información, consulte [código conservación](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Resumen

En este artículo ha proporcionado una introducción a los representadores personalizados y se describe el proceso para crear a un representador personalizado. Los representadores personalizados proporcionan un método eficaz para personalizar la apariencia y comportamiento de los controles de Xamarin.Forms. Se puede usar para los cambios de estilo pequeño o diseños sofisticados de específicos de la plataforma y personalización del comportamiento.


## <a name="related-links"></a>Vínculos relacionados

- [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md)
