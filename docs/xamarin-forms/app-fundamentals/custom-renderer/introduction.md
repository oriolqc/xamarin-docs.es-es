---
title: "Introducción a los representadores de personalizado"
description: "Representadores personalizados proporcionan un método eficaz para personalizar la apariencia y el comportamiento de los controles de Xamarin.Forms. Pueden usarse para cambios de estilo pequeño o sofisticado diseño específico de la plataforma y personalización del comportamiento. Este artículo proporciona una introducción a los representadores personalizados y describe el proceso para crear a un representador personalizado."
ms.topic: article
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: a9908429994f4575a9e41936d500bfd8906a843b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-custom-renderers"></a>Introducción a los representadores de personalizado

_Representadores personalizados proporcionan un método eficaz para personalizar la apariencia y el comportamiento de los controles de Xamarin.Forms. Pueden usarse para cambios de estilo pequeño o sofisticado diseño específico de la plataforma y personalización del comportamiento. Este artículo proporciona una introducción a los representadores personalizados y describe el proceso para crear a un representador personalizado._

Xamarin.Forms [páginas, diseños y controles](~/xamarin-forms/user-interface/controls/index.md) presentar una API común para describir las interfaces de usuario móviles entre plataformas. Cada página, el diseño y el control se representan de manera diferente en cada plataforma, con un `Renderer` clase que a su vez crea un control nativo (que corresponde a la representación de Xamarin.Forms), lo organiza en la pantalla y agrega el comportamiento especificado en el código compartido.

Los desarrolladores pueden implementar sus propias clases `Renderer` personalizadas para personalizar la apariencia o el comportamiento de un control. Los representadores personalizados para un tipo determinado se pueden agregar al proyecto de una aplicación para personalizar el control en su una lugar vez que permite que el comportamiento predeterminado en otras plataformas; o bien, se pueden agregar distintos representadores personalizados a cada proyecto de aplicación para crear una apariencia diferente en Windows Phone, iOS y Android. Sin embargo, a menudo es una respuesta exhaustiva implementar una clase de representador personalizado para llevar a cabo una personalización de control simple. Efectos simplifican este proceso y se suelen usar para cambios de estilo pequeño. Para obtener más información, consulte [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Efectos).

## <a name="examining-why-custom-renderers-are-necessary"></a>Examinar representadores personalizados por qué son necesarios

Cambiar la apariencia de un control de Xamarin.Forms, sin usar a un representador personalizado, es un proceso de dos pasos que implica la creación de un control personalizado a través de la creación de subclases y, a continuación, utilizando el control personalizado en lugar del control original. En el ejemplo de código siguiente se muestra un ejemplo de creación de subclases el `Entry` control:

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

El `MyEntry` control es un `Entry` controla dónde el `BackgroundColor` se establece en gris y puede hacer referencia en Xaml mediante la declaración de un espacio de nombres para su ubicación y con el prefijo de espacio de nombres en el elemento de control. El siguiente ejemplo de código muestra cómo el `MyEntry` control personalizado puede ser utilizado por un `ContentPage`:

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
> **Tenga en cuenta**: definir la `xmlns` es mucho más sencillo en PCLs que los proyectos compartidos. Una PCL se compila en un ensamblado, por lo que es fácil determinar lo que el `assembly=CustomRenderer` valor debe ser. Cuando se usa proyectos compartidos, todos los recursos compartidos (incluido el código XAML) se compilan en cada uno de los proyectos de referencia, lo que significa que si los iOS, Android y Windows Phone proyectos tienen sus propios *nombres de ensamblado* es imposible para escribir el `xmlns` declaración porque el valor debe ser diferente para cada aplicación. Controles personalizados en XAML para los proyectos compartidos requerirá cada proyecto de aplicación esté configurado con el mismo nombre de ensamblado.

El `MyEntry` control personalizado se representa en cada plataforma, con un fondo gris, como se muestra en las capturas de pantalla siguiente:

![](introduction-images/screenshots.png "Control personalizado de MyEntry en cada plataforma")

Cambiar el color de fondo del control en cada plataforma ha se ha realizado exclusivamente a través de la creación de subclases del control. Sin embargo, esta técnica está limitada en lo que puede lograr ya que no es posible aprovecharse de las mejoras de específico de la plataforma y las personalizaciones. Cuando se necesitan, se deben implementar representadores personalizados.

## <a name="creating-a-custom-renderer-class"></a>Crear una clase de representador personalizado

El proceso para crear una clase de representador personalizado es como sigue:

1. Crear una subclase de la clase de representador que representa el control nativo.
1. Reemplace el método que representa el control nativo y escribir una lógica para personalizar el control. A menudo, el `OnElementChanged` método se usa para representar el control nativo, que se llama cuando se crea el control de Xamarin.Forms correspondiente.
1. Agregar un `ExportRenderer` atributo a la clase de representador personalizado para especificar que se utilizará para representar el control de Xamarin.Forms. Este atributo se usa para registrar al representador personalizado con Xamarin.Forms.

> [!NOTE]
> **Tenga en cuenta**: para la mayoría de los elementos de Xamarin.Forms, es opcional proporcionar un representador personalizado en cada proyecto de la plataforma. Si no se ha registrado un representador personalizado, se usará el representador predeterminado para la clase base del control. Sin embargo, los representadores personalizados necesarios en cada proyecto de la plataforma al representar un [vista](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) o [ViewCell](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) elemento.

Los temas de esta serie proporcionará demostraciones y explicaciones de este proceso para los diferentes elementos de Xamarin.Forms.

## <a name="troubleshooting"></a>Solución de problemas

Si un control personalizado se encuentra en un proyecto PCL que se han agregado a la solución (es decir, no la PCL creada por Visual Studio para la plantilla de proyecto aplicación de Mac/Visual Studio Xamarin.Forms), puede producirse una excepción en iOS al intentar obtener acceso al control personalizado. Si se produce este problema puede resolverse mediante la creación de una referencia al control personalizado de la `AppDelegate` clase:

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

Esto hace que el compilador reconozca el `ClassInPCL` tipo por cómo resolver el problema. Como alternativa, el `Preserve` atributo puede agregarse a la `AppDelegate` clase para lograr el mismo resultado:

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

Esto crea una referencia a la `ClassInPCL` tipo, que indica que se necesita en tiempo de ejecución. Para obtener más información, consulte [código preservar](~/ios/deploy-test/linker.md).

## <a name="summary"></a>Resumen

En este artículo se proporciona una introducción a los representadores personalizados y se ha descrito el proceso de creación de un representador personalizado. Representadores personalizados proporcionan un método eficaz para personalizar la apariencia y el comportamiento de los controles de Xamarin.Forms. Pueden usarse para cambios de estilo pequeño o sofisticado diseño específico de la plataforma y personalización del comportamiento.


## <a name="related-links"></a>Vínculos relacionados

- [Efectos](~/xamarin-forms/app-fundamentals/effects/index.md)
