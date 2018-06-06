---
title: Restricciones de diseño mediante programación en Xamarin.iOS
description: Esta guía presenta las restricciones de diseño automático de trabajar con iOS en código C# en lugar de crearlas en el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: a40a3c66369902d2d6f8dbee5a6a7e9bad8a9e05
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790606"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Restricciones de diseño mediante programación en Xamarin.iOS

_Esta guía presenta las restricciones de diseño automático de trabajar con iOS en código C# en lugar de crearlas en el Diseñador de iOS._

Diseño automático (también denominada "diseño adaptable") es un enfoque de diseño dinámico. Al contrario que el sistema de diseño de transición, donde la ubicación de cada elemento es codificado de forma rígida a un punto en la pantalla, diseño automático es sobre *relaciones* -las posiciones de los elementos con respecto a otros elementos en la superficie de diseño. La esencia del diseño automático es la idea de las restricciones o las reglas que definen la posición de un elemento o un conjunto de elementos en el contexto de otros elementos en la pantalla. Dado que los elementos no están asociados a una posición determinada en la pantalla, las restricciones ayudan a crear un diseño adaptable que se ve bien en diferentes tamaños y orientaciones de dispositivo.

Normalmente cuando se trabaja con diseño automático en iOS, usará el Diseñador de iOS para colocar gráficamente las restricciones de diseño sobre los elementos de interfaz de usuario. Sin embargo, puede haber ocasiones cuando necesite crear y aplicar las restricciones en el código C#. Por ejemplo, cuando utiliza de manera dinámica crea elementos de interfaz de usuario agregados a un `UIView`.

Esta guía le mostrará cómo crear y trabajar con restricciones mediante código C# en lugar de crearlas de forma gráfica en el Diseñador de iOS.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Creación de restricciones mediante programación

Como se mencionó anteriormente, normalmente trabajará con restricciones de diseño automático en el Diseñador de iOS. En esas ocasiones en que se deben crear las restricciones mediante programación, tiene tres opciones para elegir:

* [Delimitadores de diseño](#Layout-Anchors) -esta API proporciona acceso a las propiedades de delimitador (como `TopAnchor`, `BottomAnchor` o `HeightAnchor`) de los elementos de interfaz de usuario que se restringe.
* [Las restricciones de diseño](#Layout-Constraints) -puede crear restricciones directamente mediante la `NSLayoutConstraint` clase.
* [Idioma de formato Visual](#Visual-Format-Language) -proporciona un arte ASCII como método para definir las restricciones.

En las siguientes secciones tratará cada opción detalladamente.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Delimitadores de diseño

Mediante el uso de la `NSLayoutAnchor` (clase), tiene una interfaz fluida para crear restricciones basadas en las propiedades de anclaje de los elementos de interfaz de usuario que se restringe. Por ejemplo, expone las guías de diseño de parte superior e inferior de un controlador Vista la `TopAnchor`, `BottomAnchor` y `HeightAnchor` propiedades de anclaje mientras una vista expone las propiedades de borde, el centro, el tamaño y línea de base.

> [!IMPORTANT]
> Además del conjunto estándar de propiedades del delimitador, vistas de iOS también incluyen la `LayoutMarginsGuides` y `ReadableContentGuide` propiedades. Estas propiedades exponen `UILayoutGuide` objetos para trabajar con los márgenes de la vista y legibles contenido guías respectivamente.

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

Una restricción de diseño habitual se puede expresar simplemente como una expresión lineal. Considere el ejemplo siguiente:

[![](programmatic-layout-constraints-images/graph01.png "Una restricción de diseño expresado como una expresión lineal")](programmatic-layout-constraints-images/graph01.png#lightbox)

Que se convierte en la siguiente línea de código C# con delimitadores de diseño:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Donde las partes del código C# se corresponden a las partes de la ecuación determinadas como sigue:

|Ecuación|Código|
|---|---|
|Elemento 1|PurpleView|
|Atributo 1|LeadingAnchor|
|Relación|ConstraintEqualTo|
|Multiplicador|El valor predeterminado es 1.0 por lo que no se especifica|
|Elemento 2|OrangeView|
|Atributo 2|TrailingAnchor|
|Constante|10.0|

Además de proporcionar solo los parámetros necesarios para resolver una ecuación de restricción de esquema dado, cada uno de los métodos de diseño delimitador exigir la seguridad de tipos de los parámetros pasados a ellos. Por lo tanto horizontal restricción fija como `LeadingAnchor` o `TrailingAnchor` sólo puede utilizarse con otro delimitador horizontal tipos y multiplicadores solo se proporcionan a las restricciones de tamaño.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Restricciones de diseño

Puede agregar restricciones de diseño automático manualmente mediante la creación de directamente un `NSLayoutConstraint` en código C#. A diferencia de los anclajes de diseño, debe especificar un valor para cada parámetro, aunque no tendrá ningún efecto en la restricción que se está definida. Como resultado, terminará generar una cantidad considerable de difíciles de leer, código reutilizable. Por ejemplo:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Donde la `NSLayoutAttribute` enum define el valor de los márgenes de la vista y se corresponde con el `LayoutMarginsGuide` propiedades como `Left`, `Right`, `Top` y `Bottom` y `NSLayoutRelation` enum define la relación que se crearán entre los atributos dados como `Equal`, `LessThanOrEqual` o `GreaterThanOrEqual`.

A diferencia de con la API de anclaje de diseño, el `NSLayoutConstraint` métodos de creación no resaltan los aspectos importantes de una restricción concreta y no hay ninguna compilación comprobaciones realizadas en la restricción de tiempo. Como resultado, es fácil crear una restricción no válida que se iniciará una excepción en tiempo de ejecución.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Idioma de formato Visual

El idioma de formato Visual le permite definir restricciones mediante arte ASCII como cadenas que proporcionan una representación visual de la restricción que se va a crear. Esto tiene las siguientes ventajas y desventajas:

- El idioma de formato Visual exige la creación de restricciones válidas solo.
 - Diseño automático da como resultado las restricciones en la consola mediante el lenguaje de formato Visual para que los mensajes de depuración asemejará el código utilizado para crear la restricción.
 - El idioma de formato Visual permite crear varias restricciones al mismo tiempo con una expresión muy compacto.
 - Puesto que no hay ninguna validación del lado de la compilación de las cadenas de idioma de formato Visual, solo se detecten problemas en tiempo de ejecución.
 - Puesto que el idioma de formato Visual resalta visualización integridad que algunos tipos de restricción no se puede crear con él (por ejemplo, proporciones).

Siga estos pasos cuando se utiliza el idioma de formato Visual para crear una restricción:

1. Crear un `NSDictionary` que contiene los objetos de vista y guías de diseño y una clave de cadena que se usarán al definir los formatos.
2. La opción de crear un `NSDictionary` que define un conjunto de claves y valores (`NSNumber`) utiliza como el valor constante para la restricción.
3. Crear la cadena de formato al diseño de una única columna o fila de elementos.
4. Llame a la `FromVisualFormat` método de la `NSLayoutConstraint` clase para generar las restricciones.
5. Llame a la `ActivateConstraints` método de la `NSLayoutConstraint` clase activar y aplicar las restricciones.

Por ejemplo, para crear un inicial y una restricción final en el idioma de formato Visual, podría utilizar lo siguiente:

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

Puesto que el idioma de formato Visual crea siempre cero restricciones de punto asociadas a los márgenes de la vista del primario cuando se usa el espaciado predeterminado, este código genera resultados idénticos a los ejemplos presentados por encima.

Para los diseños de interfaz de usuario más complejos, como varias vistas secundarias en una sola línea, el idioma de formato Visual especifica el espaciado horizontal y la alineación vertical. Como se muestra en el ejemplo anterior, donde especifica la `AlignAllTop` `NSLayoutFormatOptions` Alinea todas las vistas en una fila o columna a sus partes superiores.

Vea de Apple [apéndice de idioma de formato Visual](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) algunos ejemplos de especificar la gramática de cadena de formato Visual y restricciones comunes.

<a name="Summary" />

## <a name="summary"></a>Resumen

Esta guía presenta cómo crear y trabajar con las restricciones de diseño automático en C# en lugar de crearlas de forma gráfica en el Diseñador de iOS. En primer lugar, examinó el uso de delimitadores de diseño (`NSLayoutAnchor`) para controlar el diseño automático. A continuación, ha explicado cómo trabajar con las restricciones de diseño (`NSLayoutConstraint`). Por último, presenta mediante el lenguaje de formato Visual para el diseño automático.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [iOS pueden diseñar tutorial de controles](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Diseño automático con el Diseñador de Xamarin para iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple - creación de restricciones mediante programación](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple - apéndice de idioma de formato Visual](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
