---
title: Restricciones de diseño mediante programación en Xamarin.iOS
description: Esta guía presenta cómo trabajar con iOS las restricciones de diseño automático en C# código en lugar de crearlos en el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 3d8e69af7f790415343abf464ea2bb22e879e025
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106966"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Restricciones de diseño mediante programación en Xamarin.iOS

_Esta guía presenta cómo trabajar con iOS las restricciones de diseño automático en C# código en lugar de crearlos en el Diseñador de iOS._

Diseño automático (también denominada "layout adaptable") es un enfoque de diseño con capacidad de respuesta. A diferencia del sistema de diseño transitorias, donde la ubicación de cada elemento es codificada a un punto en la pantalla, diseño automático consiste *relaciones* -las posiciones de los elementos con respecto a otros elementos en la superficie de diseño. En el corazón del diseño automático es la idea de las restricciones o las reglas que definen la posición de un elemento o un conjunto de elementos en el contexto de otros elementos en la pantalla. Dado que los elementos no están vinculados a una posición concreta en la pantalla, las restricciones ayudan a crear un diseño adaptable que se vea bien en diferentes tamaños de pantalla y orientaciones de dispositivos.

Normalmente, cuando se trabaja con diseño automático en iOS, usará el Diseñador de iOS para colocar gráficamente las restricciones de diseño en los elementos de interfaz de usuario. Sin embargo, puede que en ocasiones cuando necesite crear y aplicar las restricciones de C# código. Por ejemplo, cuando utiliza de manera dinámica crea elementos de interfaz de usuario que se han agregado a un `UIView`.

Esta guía le mostrará cómo crear y trabajar con restricciones mediante C# código en lugar de crearlas de forma gráfica en el Diseñador de iOS.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Creación de restricciones mediante programación

Como se indicó anteriormente, normalmente trabajará con las restricciones de diseño automático en el Diseñador de iOS. Para aquellas ocasiones en que se deben crear mediante programación las restricciones, tiene tres opciones para elegir:

* [Delimitadores de diseño](#Layout-Anchors) -esta API proporciona acceso a las propiedades delimitador (como `TopAnchor`, `BottomAnchor` o `HeightAnchor`) de los elementos de interfaz de usuario que se restringe.
* [Las restricciones de diseño](#Layout-Constraints) -puede crear restricciones directamente mediante el `NSLayoutConstraint` clase.
* [Lenguaje Visual formato](#Visual-Format-Language) -proporciona un arte ASCII como método para definir las restricciones.

Las siguientes secciones se pasará a través de cada opción en detalle.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Delimitadores de diseño

Mediante el uso de la `NSLayoutAnchor` (clase), tiene una interfaz fluida para la creación de restricciones basadas en las propiedades de delimitador de los elementos de interfaz de usuario que se restringe. Por ejemplo, expone las guías de diseño de arriba y abajo de un controlador de vista el `TopAnchor`, `BottomAnchor` y `HeightAnchor` delimitar propiedades, mientras que una vista expone las propiedades de borde, centro, tamaño y línea base.

> [!IMPORTANT]
> Además del conjunto estándar de propiedades del delimitador, vistas de iOS también incluyen la `LayoutMarginsGuides` y `ReadableContentGuide` propiedades. Estas propiedades se exponen `UILayoutGuide` objetos para trabajar con los márgenes de la vista y legibles guías de contenido, respectivamente.

Delimitadores de diseño proporcionan varios métodos para crear restricciones en un formato fácil de leer y compacto:

- **ConstraintEqualTo** -define una relación donde `first attribute = second attribute + [constant]` con un proporciona de manera opcional `constant` valor de desplazamiento.
- **ConstraintGreaterThanOrEqualTo** -define una relación donde `first attribute >= second attribute + [constant]` con un proporciona de manera opcional `constant` valor de desplazamiento.
- **ConstraintLessThanOrEqualTo** -define una relación donde `first attribute <= second attribute + [constant]` con un proporciona de manera opcional `constant` valor de desplazamiento.

Por ejemplo:

```csharp
// Get the parent view's layout
var margins = View.LayoutMarginsGuide;

// Pin the leading edge of the view to the margin
OrangeView.LeadingAnchor.ConstraintEqualTo (margins.LeadingAnchor).Active = true;

// Pin the trailing edge of the view to the margin
OrangeView.TrailingAnchor.ConstraintEqualTo (margins.TrailingAnchor).Active = true;

// Give the view a 1:2 aspect ratio
OrangeView.HeightAnchor.ConstraintEqualTo (OrangeView.WidthAnchor, 2.0f);
```

Simplemente como una expresión lineal, se puede expresar una restricción de diseño habitual. Considere el ejemplo siguiente:

[![](programmatic-layout-constraints-images/graph01.png "Una restricción de diseño, expresado como una expresión lineal")](programmatic-layout-constraints-images/graph01.png#lightbox)

Que se convierte en la siguiente línea de C# delimitadores de diseño de código:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Donde las partes de la C# código corresponden a las partes de la ecuación determinadas como sigue:

|Ecuación|Código|
|---|---|
|Elemento 1|PurpleView|
|Atributo 1|LeadingAnchor|
|Relación|ConstraintEqualTo|
|Multiplicador|El valor predeterminado es 1.0 por lo que no se especifica|
|Elemento 2|OrangeView|
|Atributo 2|TrailingAnchor|
|Constante|10.0|

Además de proporcionar solo los parámetros necesarios para resolver una ecuación de restricción de diseño especificado, cada uno de los métodos de diseño delimitador exigir la seguridad de tipos de los parámetros pasados a ellos. Restricción por lo tanto horizontal como ancla `LeadingAnchor` o `TrailingAnchor` solo se puede usar con otro punto de anclaje horizontal multiplicadores y tipos solo se proporcionan a las restricciones de tamaño.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Restricciones de diseño

Puede agregar manualmente las restricciones de diseño automático construyendo directamente un `NSLayoutConstraint` en C# código. A diferencia de los anclajes de diseño, debe especificar un valor para cada parámetro, aunque no tendrá ningún efecto en la restricción que se está definida. Como resultado, acabará producir una cantidad considerable de difícil de leer, código reutilizable. Por ejemplo:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Donde el `NSLayoutAttribute` enum define el valor de los márgenes de la vista y se corresponden con el `LayoutMarginsGuide` propiedades como `Left`, `Right`, `Top` y `Bottom` y `NSLayoutRelation` enumeración define la relación que se crearán entre los atributos especificados como `Equal`, `LessThanOrEqual` o `GreaterThanOrEqual`.

A diferencia de con la API de anclaje de diseño, el `NSLayoutConstraint` métodos de creación no resaltan los aspectos importantes de una restricción determinada y no hay ninguna compilación comprobaciones realizadas en la restricción de tiempo. Como resultado, es fácil crear una restricción no válida que se iniciará una excepción en tiempo de ejecución.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Idioma de formato Visual

El idioma de formato Visual permite definir restricciones de uso de arte ASCII como cadenas que proporcionan una representación visual de la restricción que se va a crear. Esto tiene las siguientes ventajas y desventajas:

- El idioma de formato Visual exige la creación de restricciones válidas solo.
 - Diseño automático da como resultado las restricciones en la consola mediante el lenguaje de formato Visual para los mensajes de depuración se parecerá al código usado para crear la restricción.
 - El idioma de formato Visual permite crear varias restricciones al mismo tiempo con una expresión muy compacta.
 - Puesto que no hay ninguna validación del lado de compilación de las cadenas de idioma de formato Visual, solo se pueden detectar problemas en tiempo de ejecución.
 - Puesto que el idioma de formato Visual enfatiza la visualización a través de la integridad de que algunos tipos de restricción no se puede crear con él (por ejemplo, relaciones).

Realice los pasos siguientes cuando se usa el idioma de formato Visual para crear una restricción:

1. Crear un `NSDictionary` que contiene los objetos de vista y las guías de diseño y una clave de cadena que se usará al definir los formatos.
2. Opcionalmente, crear un `NSDictionary` que define un conjunto de claves y valores (`NSNumber`) utiliza como el valor constante para la restricción.
3. Crear la cadena de formato al diseño de una sola columna o fila de elementos.
4. Llame a la `FromVisualFormat` método de la `NSLayoutConstraint` clase para generar las restricciones.
5. Llame a la `ActivateConstraints` método de la `NSLayoutConstraint` clase para activar y se aplican las restricciones.

Por ejemplo, para crear uno de los principales y una restricción final en el idioma de formato Visual, podría utilizar lo siguiente:

```csharp
// Get views being constrained
var views = new NSMutableDictionary (); 
views.Add (new NSString ("orangeView"), OrangeView);

// Define format and assemble constraints
var format = "|-[orangeView]-|";
var constraints = NSLayoutConstraint.FromVisualFormat (format, NSLayoutFormatOptions.AlignAllTop, null, views);

// Apply constraints
NSLayoutConstraint.ActivateConstraints (constraints);
```

Debido a que el idioma de formato Visual crea siempre cero restricciones de punto asociadas a los márgenes de la vista del primario cuando se usa el espaciado predeterminado, este código genera resultados idénticos a los ejemplos presentados anteriormente.

Para los diseños de interfaz de usuario más complejos, como varias vistas secundarias en una sola línea, el idioma de formato Visual especifica el espaciado horizontal y la alineación vertical. Como se muestra en el ejemplo anterior, donde especifica la `AlignAllTop` `NSLayoutFormatOptions` Alinea todas las vistas en una fila o columna a sus máximos.

Consulte Apple [apéndice de idioma de formato Visual](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) para ver algunos ejemplos de la especificación de restricciones comunes y la gramática de cadena de formato Visual.

<a name="Summary" />

## <a name="summary"></a>Resumen

Esta guía presenta crear y trabajar con las restricciones de diseño automático en C# en lugar de crearlas de forma gráfica en el Diseñador de iOS. En primer lugar, examinó el uso de delimitadores de diseño (`NSLayoutAnchor`) para controlar el diseño automático. A continuación, se ha mostrado cómo trabajar con restricciones de diseño (`NSLayoutConstraint`). Por último, presenta mediante el lenguaje de formato Visual para el diseño automático.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [Tutorial de controles pueden diseñar de iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Diseño automático con el Diseñador de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple - creación de restricciones mediante programación](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple - apéndice de idioma de formato Visual](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
