---
title: Diseño automático con el Diseñador de Xamarin para iOS
description: Esta guía presenta iOS diseño automático y describe cómo utilizar el Diseñador de Xamarin para iOS para crear y modificar los diseños con las restricciones. También se explica cómo modificar restricciones en el código, animar cambios de restricción y mucho más.
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 876bf3de19d2bcce7d951facc92d5b05a928cd38
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790206"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>Diseño automático con el Diseñador de Xamarin para iOS

Diseño automático (también denominada "diseño adaptable") es un enfoque de diseño dinámico. Al contrario que el sistema de diseño de transición, donde la ubicación de cada elemento es codificado de forma rígida a un punto en la pantalla, diseño automático es sobre *relaciones* -las posiciones de los elementos con respecto a otros elementos en la superficie de diseño. La esencia del diseño automático es la idea de las restricciones o las reglas que definen la posición de un elemento o un conjunto de elementos en el contexto de otros elementos en la pantalla. Dado que los elementos no están asociados a una posición determinada en la pantalla, las restricciones ayudan a crear un diseño adaptable que se ve bien en diferentes tamaños y orientaciones de dispositivo.

En esta guía, se introducen restricciones y cómo trabajar con ellos en el Diseñador de iOS de Xamarin. Esta guía no cubre trabajar con restricciones mediante programación. Para obtener información sobre cómo usar Autodiseño mediante programación, consulte la la [documentación de Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

## <a name="requirements"></a>Requisitos

El Diseñador de Xamarin para iOS está disponible en Visual Studio para Mac en Visual Studio 2015 y 2017 en Windows.

Esta guía supone un conocimiento de los componentes del Diseñador de la [introducción en el Diseñador de iOS](~/ios/user-interface/designer/introduction.md) guía.

## <a name="introduction-to-constraints"></a>Introducción a las restricciones

Una restricción es una representación matemática de la relación entre dos elementos en la pantalla. Que representa una interfaz de usuario la posición del elemento como una relación matemática soluciona varios problemas asociados a codificar de forma rígida ubicación del elemento de interfaz de usuario. Por ejemplo, si se llamase a colocar un 20px de botón de la parte inferior de la pantalla en modo vertical, posición del botón sería fuera de la pantalla en modo horizontal. Para evitar esto, se puede definir una restricción que coloca el borde inferior de la 20px de botón de la parte inferior de la vista. La posición del borde de botón, a continuación, se calcularía como *button.bottom = view.bottom - 20px*, que habría insertado el 20px de botón de la parte inferior de la vista en modo vertical y horizontal. La capacidad para calcular la posición basada en una relación matemática es lo que hace que las restricciones es muy útil en el diseño de la interfaz de usuario.

Cuando se establece una restricción, creamos un `NSLayoutConstraint` objeto que toma como argumentos de los objetos que se restringirá y las propiedades, o *atributos*, que actúe en la restricción. En el Diseñador de iOS, atributos incluyen bordes como el *izquierdo*, *derecho*, *arriba*, y *inferior* de un elemento. También incluyen atributos de tamaño como *alto* y *ancho*y el centro de punto de ubicación, *centerX* y *centerY*. Por ejemplo, cuando se agrega una restricción en la posición del límite izquierdo de dos botones, el diseñador genera el siguiente código en segundo plano:

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

La siguiente sección explica cómo trabajar con restricciones mediante el diseñador, como habilitar y deshabilitar el diseño automático y utilizando la barra de herramientas de las restricciones de iOS.

## <a name="enable-auto-layout"></a>Habilitar diseño automático

La configuración predeterminada del Diseñador de iOS tiene habilitado el modo de restricción. Sin embargo, si necesita habilitar o deshabilitar de forma manual, puede hacerlo en dos pasos:

1.  Haga clic en un espacio vacío en la superficie de diseño. Esto anula la selección de todos los elementos y las propiedades para el documento de guión gráfico, se abrirá.
1.  Active o desactive el **uso Autodiseño** casilla de verificación en el panel de propiedades:

    ![](designer-auto-layout-images/image01.png "La casilla de verificación Autodiseño de uso en el panel de propiedades")


De forma predeterminada, no hay ninguna restricción es creado o visible en la superficie. En su lugar, automáticamente se deducen de la información del marco en tiempo de compilación. Para agregar las restricciones, es necesario seleccionar un elemento en la superficie de diseño y agregar restricciones a él. Podemos hacer que el uso de la **barra de herramientas de restricción**.

## <a name="constraints-toolbar"></a>Barra de herramientas de restricciones

 [![](designer-auto-layout-images/toolbarnew.png "Los comandos del menú contextual")](designer-auto-layout-images/toolbarnew.png#lightbox)

La barra de herramientas de restricciones se ha actualizado y ahora está formada por dos partes principales:

- **Un control de alternancia del botón de modo de restricciones**: anteriormente, entró en modo de restricciones haciendo clic de nuevo en una vista seleccionada en la superficie de diseño. Ahora se debe usar este botón de alternancia en la barra de restricciones:

  ![Alternar modos de restricciones](designer-auto-layout-images/constraints.png)

- **Un botón "Las restricciones de actualización":** es importante tener en cuenta que los cambios dependiendo de si está en modo de edición de restricciones.
  - En modo de edición de restricción este botón ajusta las restricciones para que coincida con el marco de elemento.
  - En modo de edición de marco este botón ajusta el marco de elemento para que coincida con la posición que se va a definir las restricciones.


## <a name="surface-based-constraint-editing"></a>Restricción basada en la superficie de edición

En la sección anterior, hemos visto agregar las restricciones default y quitar restricciones mediante la barra de herramientas de restricciones. Para la edición de restricción ajustarse más, podemos interactúan restricciones directamente en la superficie de diseño. Esta sección presenta los conceptos básicos de la restricción basada en la superficie de edición, incluidos los controles de espaciado de pin, áreas de colocación y trabajar con diferentes tipos de restricciones.

### <a name="creating-constraints"></a>Creación de restricciones

La herramienta de diseñador de iOS ofrece dos tipos de controles para manipular los elementos en la superficie de diseño. *Arrastre controles* y *pin espaciado controles*, tal y como se muestra en la siguiente imagen:

![controles de vista](designer-auto-layout-images/controls.png)

Estos se alternan seleccionando el botón de modo de restricciones en la barra de restricciones.

Definen los identificadores en forma de T 4 en cada lado del elemento la *arriba*, *derecho*, *inferior*, y *izquierdo* bordes del elemento de una restricción. Definen los dos identificadores de la derecha e inferior del elemento en forma de *alto* y *ancho* restricción respectivamente. El cuadrado central controla ambas *centerX* y *centerY* restricciones.

Para crear una restricción, seleccione un identificador y arrástrelo en alguna parte en la superficie de diseño. Cuando se inicia la operación de arrastre, aparecerá una serie de líneas y cuadros verdes en la superficie que le indica lo que se puede restringir. Por ejemplo, en la siguiente captura de pantalla, estamos restringiendo el lado superior del botón central:

 [![](designer-auto-layout-images/image07.png "Restringir el lado superior del botón central")](designer-auto-layout-images/image07.png#lightbox)

Tenga en cuenta las tres líneas discontinuas de verde a través de los otros dos botones. Las líneas verdes indican *áreas de colocación*, o los atributos de otros elementos a la que se puede restringir. En la captura de pantalla anterior, los otros dos botones ofrecen 3 áreas de colocación vertical ( *inferior*, *centerY*, *arriba*) para restringir el botón. La línea discontinua verde en la parte superior de la vista significa que el controlador de vista ofrece una restricción en la parte superior de la vista y el cuadro verde sólido significa que el controlador de vista ofrece una restricción por debajo de la Guía de diseño superior.

> [!IMPORTANT]
> Guías de diseño son tipos especiales de destinos de restricción que nos permiten crear la parte superior y las restricciones de la parte inferior que tener en cuenta la presencia de barras del sistema, como barras de estado o las barras de herramientas. Uno de los usos principales es que una aplicación compatible entre iOS 6 e iOS 7, puesto que la versión más reciente tiene la vista del contenedor se extiende por debajo de la barra de estado. Para obtener más información sobre la Guía de diseño principales, consulte el [documentación de Apple](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2).



Las tres secciones siguientes presentan trabajar con diferentes tipos de restricciones.

### <a name="size-constraints"></a>Restricciones de tamaño

Con las restricciones de tamaño - *alto* y *ancho* -tiene dos opciones. La primera opción es arrastrar el identificador para limitar a un tamaño de elemento de vecino, como se muestra en el ejemplo anterior. La otra opción es hacer doble clic en el identificador para crear una restricción de autoservicio. Esto nos permite especificar un valor de tamaño constante, como se muestra en la captura de pantalla siguiente:

 [![](designer-auto-layout-images/sizec.png "Arrastre el controlador para limitar a un tamaño de elemento de vecino, tal y como se ilustra a continuación")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Restricciones de Center

El asa cuadrada, se creará un *centerX* o *centerY* restricción, dependiendo del contexto. El manipulador de cuadrado se ilumina los otros elementos para ofrecer ambas áreas de colocación horizontal y vertical, como se muestra en la captura de pantalla siguiente:

 [![](designer-auto-layout-images/centerc.png "Restricciones de Center")](designer-auto-layout-images/centerc.png#lightbox)

Si elige un área de colocación vertical, un *centerY* se creará la restricción. Si elige un área de colocación horizontal, la restricción se basará en *centerX*.

### <a name="combinational-constraints"></a>Restricciones de combinación

Para crear la alineación y restricciones de tamaño de igualdad entre dos elementos, puede seleccionar elementos de una barra de herramientas superior para especificar - en orden: alineación horizontal, la alineación vertical e igualdades de tamaño, como se muestra en la captura de pantalla siguiente:

 [![](designer-auto-layout-images/image06.png "Restricciones de combinación")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>Visualizar y editar las restricciones

Cuando se agrega una restricción, se mostrará en la superficie de diseño como una línea azul cuando se selecciona un elemento:

 [![](designer-auto-layout-images/image09.png "La visualización de restricciones")](designer-auto-layout-images/image09.png#lightbox)

Puede seleccionar una restricción haciendo clic en una línea azul y editar los valores de restricción directamente en el panel de propiedades. Como alternativa, haga doble clic en una línea azul se abrirá una popover que le permite modificar los valores directamente en la superficie de diseño:

 [![](designer-auto-layout-images/image08.png "Modificar restricciones")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>Problemas de restricción

Varios tipos de problemas pueden surgir al usar restricciones:

-  **Las restricciones conflictivas** , esto sucede cuando varias restricciones de forzar el elemento que tengan valores en conflicto para un atributo y el motor de restricción no puede sincronizarlos.
-  **Underconstrained elementos** : propiedades de un elemento (ubicación + tamaño) deben ser abarcados totalmente por su conjunto de restricciones intrínsecos tamaños y de las restricciones de ser válido. Si estos valores son ambiguos, se dice que el elemento se underconstrained.
-  **Ubicación incorrecta de marco** : Esto se produce cuando el marco de un elemento y su conjunto de restricciones definen dos rectángulos resultantes diferentes.


En esta sección se elaboran los tres problemas mencionados anteriormente y proporciona detalles sobre cómo controlarlos.

### <a name="conflicting-constraints"></a>Restricciones en conflicto

Las restricciones conflictivas se marcan en rojo y tienen un símbolo de advertencia. Mantiene el mouse sobre los símbolos de advertencia aparecerá una popover con información sobre el conflicto:

 [![](designer-auto-layout-images/image11.png "Las restricciones conflictivas advertencia")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Elementos underconstrained

Underconstrained elementos aparecen en color naranja y desencadenan la apariencia de un icono de marcador naranja en la barra de objeto de controlador de vista:

 [![](designer-auto-layout-images/image02.png "Underconstrained elementos aparecen en color naranja")](designer-auto-layout-images/image02.png#lightbox)

Si hace clic en ese icono de marcador, puede obtener información acerca de los elementos underconstrained de la escena y resolver los problemas por cualquier totalmente restringirlos o mediante la eliminación de sus restricciones, como se muestra en la captura de pantalla siguiente:

 [![](designer-auto-layout-images/image10.png "Corrección de elementos Underconstrained")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>Ubicación incorrecta de marco

Ubicación incorrecta de marco, usa el mismo código de color como elementos underconstrained. El elemento siempre se representará en la superficie utilizando su marco nativo, pero en el caso de una ubicación incorrecta de marco un rectángulo rojo marcará donde el elemento terminará cuando se ejecuta la aplicación, como se muestra en la captura de pantalla siguiente:

 [![](designer-auto-layout-images/image05.png "Vista de la ubicación incorrecta del marco de ejemplo")](designer-auto-layout-images/image05.png#lightbox)

Para resolver errores de trama ubicación incorrecta, seleccione la **actualización fotogramas basadas en las restricciones** botón desde la barra de herramientas de restricciones (botón más a la derecha):

 [![](designer-auto-layout-images/image03.png "Actualizar los fotogramas en función de botón de barra de herramientas de restricciones")](designer-auto-layout-images/image03.png#lightbox)

Esto ajustará automáticamente el marco de elemento para que coincida con las posiciones definidas por los controles.

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>Modificar restricciones en el código

En función de los requisitos de la aplicación, puede haber ocasiones cuando necesite modificar una restricción en el código. Por ejemplo, para cambiar el tamaño o la posición de la vista se adjunta una restricción, para cambiar la prioridad de una restricción o desactivar una restricción por completo.

Para obtener acceso a una restricción en el código, primero tiene que exponer en el Diseñador de iOS haciendo lo siguiente:

1. Crear la restricción como normal (con cualquiera de los métodos mencionados anteriormente).
2. En el **Document Explorer de esquema**, busque la restricción que desea y selecciónela:

    [![](designer-auto-layout-images/modify01.png "El Explorador de esquema de documento")](designer-auto-layout-images/modify01.png#lightbox)
3. A continuación, asignar un **nombre** a la restricción en la **Widget** pestaña de la **el Explorador de propiedades**:

    [![](designer-auto-layout-images/modify02.png "La pestaña de Widget")](designer-auto-layout-images/modify02.png#lightbox)
4. Guarde los cambios.

Con los cambios mencionados en su lugar, puede tener acceso a ella en el código y modificar sus propiedades. Por ejemplo, puede utilizar lo siguiente para establecer el alto de la vista adjunta en cero:

```csharp
ViewInfoHeight.Constant = 0;
```

Dada la siguiente configuración para la restricción en el Diseñador de iOS:

[![](designer-auto-layout-images/modify03.png "Edición de una restricción en el Explorador de propiedades")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>La fase de diseño diferida

En lugar de actualizar al instante la vista adjunta en respuesta a los cambios de restricción, el motor de diseño automático programa un _diferida del paso de diseño_ en un futuro próximo. Durante esta fase diferida, no solo es restricción de la vista dada actualizado, las restricciones para cada vista de la jerarquía se vuelven a calcular y se actualizan para ajustar el diseño nuevo.

En cualquier momento, puede programar su propia diferida del paso de diseño mediante una llamada a la `SetNeedsLayout` o `SetNeedsUpdateConstraints` métodos de la vista de elemento primario. 

La fase de diseño diferida consta de dos fases únicos a través de la jerarquía de vista:

- **La fase de actualización** -en este paso, el motor de diseño automático recorre la jerarquía de vista e invoca el `UpdateViewConstraints` método en todos los controladores de vista y el `UpdateConstraints` método en todas las vistas.
- **La fase de diseño** : nuevo, el motor de diseño automático recorre la jerarquía de vista, pero esta vez, se invoca el `ViewWillLayoutSubviews` método en todos los controladores de vista y el `LayoutSubviews` método en todas las vistas. El `LayoutSubviews` método actualiza el `Frame` propiedad de cada vista secundaria con el rectángulo calculado por el motor de diseño automático.

### <a name="animating-constraint-changes"></a>Animar cambios de restricción

Además de modificar las propiedades de la restricción, puede usar Core animación para animar los cambios a las restricciones de la vista. Por ejemplo:

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

La clave aquí es llamar a la `LayoutIfNeeded` método de la vista primaria dentro del bloque de animación. Esto indica a la vista para dibujar cada "frame" de la ubicación animado o cambio de tamaño. Sin esta línea, simplemente podría ajustar la vista a la versión final sin animación.

## <a name="summary"></a>Resumen

Esta guía introdujo iOS Auto (o "adaptive") el concepto de restricciones como representaciones matemáticas de las relaciones entre elementos en la superficie de diseño y de diseño. Describe cómo habilitar el diseño automático en el Diseñador de iOS, trabajar con el **barra de herramientas de restricciones**y modificar restricciones individualmente en la superficie de diseño. A continuación, explica cómo solucionar problemas comunes de las restricciones tres. Por último, ha explicado cómo modificar restricciones en el código.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [iOS pueden diseñar tutorial de controles](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Información general del diseñador Android](~/android/user-interface/android-designer/index.md)
- [Restricciones mediante programación](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple - Guía de diseño automático](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
