---
title: Diseño automático con el Diseñador de Xamarin para iOS
description: Esta guía presenta el diseño automático de iOS y describe cómo utilizar el Diseñador de Xamarin para iOS para crear y modificar los diseños con las restricciones. También describe las restricciones de modificación en el código, animar los cambios de restricción y mucho más.
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 41a9ec90b4b734dde7a982ac3d4b2e7b2082321c
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2019
ms.locfileid: "58070871"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>Diseño automático con el Diseñador de Xamarin para iOS

Diseño automático (también denominada "layout adaptable") es un enfoque de diseño con capacidad de respuesta. A diferencia del sistema de diseño transitorias, donde la ubicación de cada elemento es codificada a un punto en la pantalla, diseño automático consiste *relaciones* -las posiciones de los elementos con respecto a otros elementos en la superficie de diseño. En el corazón del diseño automático es la idea de las restricciones o las reglas que definen la posición de un elemento o un conjunto de elementos en el contexto de otros elementos en la pantalla. Dado que los elementos no están vinculados a una posición concreta en la pantalla, las restricciones ayudan a crear un diseño adaptable que se vea bien en diferentes tamaños de pantalla y orientaciones de dispositivos.

En esta guía, se presentan las restricciones y cómo trabajar con ellos en el Diseñador de iOS de Xamarin. Esta guía no trata de trabajar con restricciones mediante programación. Para obtener información sobre el uso de diseño automático mediante programación, consulte el [documentación de Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

## <a name="requirements"></a>Requisitos

El Diseñador de Xamarin para iOS está disponible en Visual Studio para Mac en Visual Studio 2017 y posterior en Windows.

Esta guía supone un conocimiento de los componentes del diseñador desde el [Introducción a iOS Designer](~/ios/user-interface/designer/introduction.md) guía.

## <a name="introduction-to-constraints"></a>Introducción a las restricciones

Una restricción es una representación matemática de la relación entre dos elementos en la pantalla. Que representa una interfaz de usuario de la posición del elemento como una relación matemática soluciona varios problemas asociados con código ubicación del elemento de una interfaz de usuario. Por ejemplo, si tuviéramos que coloque un 20px botón desde la parte inferior de la pantalla en modo vertical, posición del botón sería fuera de la pantalla en modo horizontal. Para evitar esto, se puede definir una restricción que coloca el borde inferior de la 20px botón desde la parte inferior de la vista. La posición del borde de botón, a continuación, se calcularía como *button.bottom = view.bottom - 20px*, que se colocará el 20px botón desde la parte inferior de la vista en modo vertical y horizontal. La capacidad para calcular la posición basada en una relación matemática es lo que hace restricciones tan útil en el diseño de interfaz de usuario.

Cuando se establece una restricción, creamos un `NSLayoutConstraint` objeto que se toma como argumentos de los objetos a verse limitado y las propiedades, o *atributos*, que actuará la restricción. En el Diseñador de iOS, los atributos incluyen bordes, como el *izquierdo*, *derecho*, *superior*, y *inferior* de un elemento. También incluye atributos de tamaño como *alto* y *ancho*y la ubicación, del punto central *centerX* y *centerY*. Por ejemplo, cuando se agrega una restricción en la posición del límite izquierdo de dos botones, el diseñador genera el siguiente código en segundo plano:

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

La siguiente sección se explica cómo trabajar con restricciones mediante iOS Designer, como habilitar y deshabilitar el diseño automático y utilizando la barra de herramientas de restricciones.

## <a name="enable-auto-layout"></a>Habilitar el diseño automático

La configuración predeterminada del Diseñador de iOS tiene habilitado el modo de restricción. Sin embargo, si necesita habilitar o deshabilitar manualmente, puede hacerlo en dos pasos:

1.  Haga clic en un espacio vacío en la superficie de diseño. Esto anula la selección de todos los elementos y se muestra las propiedades para el documento de guión gráfico.
1.  Active o desactive el **uso Autodiseño** casilla de verificación en el panel de propiedades:

    ![](designer-auto-layout-images/image01.png "La casilla de verificación de diseño automático de uso en el panel de propiedades")


De forma predeterminada, no hay restricciones son creada o visible en la superficie. En su lugar, automáticamente se deducen de la información del marco en tiempo de compilación. Para agregar las restricciones, es necesario seleccionar un elemento en la superficie de diseño y agregar restricciones a él. Podemos hacer que el uso del **barra de herramientas de restricción**.

## <a name="constraints-toolbar"></a>Barra de herramientas de restricciones

 [![](designer-auto-layout-images/toolbarnew.png "Comandos de menú contextual")](designer-auto-layout-images/toolbarnew.png#lightbox)

La barra de herramientas de restricciones se ha actualizado y ahora consta de dos partes principales:

- **Un control de alternancia del botón de modo de restricciones**: Anteriormente, se ha especificado el modo de restricciones haciendo clic de nuevo en una vista seleccionada en la superficie de diseño. Ahora debe usar este botón de alternancia en la barra de restricciones:

  ![Activar o desactivar los modos de restricciones](designer-auto-layout-images/constraints.png)

- **Un botón de "Restricciones de actualización":** Es importante tener en cuenta que los cambios, dependiendo de si está en modo de edición de restricciones.
  - Este botón ajusta las restricciones para que coincida con el marco del elemento en modo de edición de la restricción.
  - En modo de edición de marco este botón ajusta el marco de elemento para que coincida con la posición que se va a definir las restricciones.


## <a name="surface-based-constraint-editing"></a>Restricción basada en la superficie de edición

En la sección anterior, hemos aprendido a agregar las restricciones default y quitar las restricciones de uso de la barra de herramientas de restricciones. Para la edición de restricción ajustado más, que podamos interactuar con las restricciones directamente en la superficie de diseño. Esta sección presenta los conceptos básicos de la edición basada en la superficie de restricción, incluidos controles anclar espaciado, áreas de colocación y trabajar con diferentes tipos de restricciones.

### <a name="creating-constraints"></a>Creación de restricciones

La herramienta de diseñador de iOS ofrece dos tipos de controles para manipular elementos en la superficie de diseño. *Arrastrar controles* y *anclar espaciado controles*, como se muestra en la siguiente imagen:

![controles de vista](designer-auto-layout-images/controls.png)

Estos se pueden alternar seleccionando el botón de modo de restricciones en la barra de restricciones.

Definen los identificadores en forma de T 4 en cada lado del elemento de la *superior*, *derecho*, *inferior*, y *izquierdo* bordes del elemento para un restricción. Definen los dos identificadores con forma de I en la derecha e inferior del elemento *alto* y *ancho* restricción respectivamente. El cuadrado central controla ambos *centerX* y *centerY* restricciones.

Para crear una restricción, seleccione un identificador y arrastrarlo en alguna parte de la superficie de diseño. Cuando se inicia la operación de arrastrar, aparecerá una serie de líneas o cuadros verdes en la superficie que le indica lo que se pueden restringir. Por ejemplo, en la siguiente captura de pantalla, estamos restringiendo el lado superior del botón central:

 [![](designer-auto-layout-images/image07.png "Restringir el lado superior del botón central")](designer-auto-layout-images/image07.png#lightbox)

Tenga en cuenta las tres líneas discontinuas de verde a través de los otros dos botones. Las líneas verdes indican *áreas de colocación*, o los atributos de otros elementos a la que se puede restringir. En la captura de pantalla anterior, los otros dos botones ofrecen 3 áreas de colocación vertical ( *inferior*, *centerY*, *superior*) para restringir el botón. El controlador de vista ofrece una restricción en la parte superior de la vista y el cuadro verde sólido significa que el controlador de vista ofrece una restricción de la Guía de diseño superior, significa que la línea discontinua verde en la parte superior de la vista.

> [!IMPORTANT]
> Las guías de diseño son tipos especiales de los destinos de delimitación que nos permiten crear la parte superior y las restricciones de la parte inferior que tener en cuenta la presencia de las barras del sistema, como barras de estado o las barras de herramientas. Uno de los usos principales es que una aplicación compatible entre iOS 6 e iOS 7, ya que la versión más reciente tiene la vista del contenedor se extiende por debajo de la barra de estado. Para obtener más información sobre la Guía de diseño superior, consulte el [documentación de Apple](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2).



Las tres secciones siguientes presentan trabajar con diferentes tipos de restricciones.

### <a name="size-constraints"></a>Restricciones de tamaño

Con las restricciones de tamaño - *alto* y *ancho* -tiene dos opciones. La primera opción es arrastrar el controlador para restringir a un tamaño de elemento de vecino, como se muestra en el ejemplo anterior. La otra opción es hacer doble clic en el controlador para crear una restricción de autoservicio. Esto nos permite especificar un valor de tamaño constante, como se muestra en la captura de pantalla siguiente:

 [![](designer-auto-layout-images/sizec.png "Arrastre el controlador para restringir a un tamaño de elemento de vecino, como se muestra aquí")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Restricciones del centro

El controlador cuadrado creará un *centerX* o *centerY* restricción, dependiendo del contexto. Arrastre el manipulador de cuadrado se ilumina los demás elementos para ofrecer dos áreas de colocación horizontal y vertical, como se muestra en la captura de pantalla siguiente:

 [![](designer-auto-layout-images/centerc.png "Restricciones del centro")](designer-auto-layout-images/centerc.png#lightbox)

Si elige un área de colocación vertical, un *centerY* se creará la restricción. Si elige un área de colocación horizontal, la restricción se basará en *centerX*.

### <a name="combinational-constraints"></a>Restricciones de combinación

Para crear la alineación y restricciones de tamaño de igualdad entre dos elementos, puede seleccionar elementos de una barra de herramientas superior para especificar - en orden: la alineación horizontal, la alineación vertical e igualdades de tamaño, como se muestra en la captura de pantalla siguiente:

 [![](designer-auto-layout-images/image06.png "Restricciones de combinación")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>Visualización y edición de restricciones

Cuando se agrega una restricción, se mostrará en la superficie de diseño como una línea azul cuando se selecciona un elemento:

 [![](designer-auto-layout-images/image09.png "Visualización de las restricciones")](designer-auto-layout-images/image09.png#lightbox)

Puede seleccionar una restricción haciendo clic en una línea azul y editar los valores de restricción directamente en el panel de propiedades. Como alternativa, haga doble clic en una línea azul, aparecerá un elemento flotante que le permite editar los valores directamente en la superficie de diseño:

 [![](designer-auto-layout-images/image08.png "Limitaciones de edición")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>Problemas de restricción

Varios tipos de problemas pueden surgir al usar restricciones:

-  **Las restricciones conflictivas** , esto sucede cuando varias restricciones de forzar el elemento que tengan valores en conflicto para un atributo y el motor de restricción no puede sincronizarlos.
-  **Underconstrained elementos** : propiedades de un elemento (ubicación + tamaño) deben estar completamente cubiertas por su conjunto de restricciones e intrínsecos tamaños de las restricciones de ser válido. Si estos valores son ambiguos, se dice que el elemento se underconstrained.
-  **Enmarcar una mala colocación** , esto se produce cuando el marco de un elemento y su conjunto de restricciones definen dos rectángulos resultantes diferentes.


En esta sección se profundiza en los tres problemas mencionados anteriormente y proporciona detalles sobre cómo controlarlos.

### <a name="conflicting-constraints"></a>Restricciones en conflicto

Restricciones en conflicto se marcan en rojo y tienen un símbolo de advertencia. Mantener el mouse sobre los símbolos de advertencia, se abre un elemento flotante con información sobre el conflicto:

 [![](designer-auto-layout-images/image11.png "Las restricciones conflictivas advertencia")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Elementos underconstrained

Underconstrained elementos aparecen en color naranja y desencadenan la apariencia de un icono de marcador naranja en la barra de objeto de controlador de vista:

 [![](designer-auto-layout-images/image02.png "Underconstrained elementos aparecen en color naranja")](designer-auto-layout-images/image02.png#lightbox)

Si hace clic en ese icono de marcador, puede obtener información acerca de los elementos underconstrained en la escena y solucionar los problemas mediante cualquier totalmente restringirlos o quitando sus restricciones, como se muestra en la captura de pantalla siguiente:

 [![](designer-auto-layout-images/image10.png "Corregir Underconstrained elementos")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>Marco una mala colocación

Una mala colocación del marco utiliza el mismo código de color como elementos underconstrained. El elemento siempre se representará en la superficie de su marco nativo, pero en el caso de una ubicación incorrecta de marco un rectángulo rojo marcará donde el elemento terminará cuando se ejecuta la aplicación, como se muestra en la captura de pantalla siguiente:

 [![](designer-auto-layout-images/image05.png "Vista de una mala colocación del marco de ejemplo")](designer-auto-layout-images/image05.png#lightbox)

Para resolver errores de una mala colocación de marco, seleccione el **actualización marcos según las restricciones** botón desde la barra de herramientas de restricciones (botón más a la derecha):

 [![](designer-auto-layout-images/image03.png "Actualizar marcos basándose en el botón de barra de herramientas de restricciones")](designer-auto-layout-images/image03.png#lightbox)

Esto ajustará automáticamente el marco de elemento para que coincida con las posiciones definidas por los controles.

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>Modificación de restricciones en el código

Según los requisitos de la aplicación, podría haber cuando necesite modificar una restricción en el código. Por ejemplo, para cambiar el tamaño o cambiar la posición de la vista se adjunta una restricción, para cambiar la prioridad de una restricción o desactivar una restricción por completo.

Para obtener acceso a una restricción en el código, primero debe exponerla en el Diseñador de iOS haciendo lo siguiente:

1. Crear la restricción como normal (mediante cualquiera de los métodos mencionados anteriormente).
2. En el **Explorador de documentos de esquema**, busque la restricción que desea y selecciónela:

    [![](designer-auto-layout-images/modify01.png "El Explorador de esquema de documento")](designer-auto-layout-images/modify01.png#lightbox)
3. A continuación, asigne un **nombre** a la restricción en el **Widget** pestaña de la **Explorador de propiedades**:

    [![](designer-auto-layout-images/modify02.png "La pestaña de Widget")](designer-auto-layout-images/modify02.png#lightbox)
4. Guarde los cambios.

Con los cambios anteriores en su lugar, puede tener acceso a ella en el código y modificar sus propiedades. Por ejemplo, puede usar lo siguiente para establecer el alto de la vista adjunta en cero:

```csharp
ViewInfoHeight.Constant = 0;
```

Dada la siguiente configuración para la restricción en el Diseñador de iOS:

[![](designer-auto-layout-images/modify03.png "Edición de una restricción en el Explorador de propiedades")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>La fase de diseño aplazada

En lugar de actualizar al instante la vista adjunta en respuesta a cambios de restricción, el motor de diseño automático programa una _pase de diseño aplazado_ para el futuro próximo. Durante esta fase diferida, no solo es actualizado, de restricción de la vista dada las restricciones para cada vista en la jerarquía se vuelven a calcular y se actualizan para ajustar el diseño de nuevo.

En cualquier momento, puede programar sus propios pase de diseño aplazada llamando el `SetNeedsLayout` o `SetNeedsUpdateConstraints` métodos de la vista primaria. 

La fase de diseño aplazado consta de dos fases únicos a través de la jerarquía de vistas:

- **La fase de actualización** -en este paso, el motor de diseño automático recorre la jerarquía de vistas e invoca el `UpdateViewConstraints` método en todos los controladores de vista y el `UpdateConstraints` método en todas las vistas.
- **La fase de diseño** : nuevo, el motor de diseño automático recorre la jerarquía de vistas, pero esta vez invoca el `ViewWillLayoutSubviews` método en todos los controladores de vista y el `LayoutSubviews` método en todas las vistas. El `LayoutSubviews` método actualiza el `Frame` propiedad de cada vista secundaria con el rectángulo calculado por el motor de diseño automático.

### <a name="animating-constraint-changes"></a>Animar cambios de restricción

Además de modificar las propiedades de restricción, puede usar la animación principal animar los cambios a las restricciones de la vista. Por ejemplo:

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

La clave aquí es llamar a la `LayoutIfNeeded` método de la vista primaria dentro del bloque de animación. Esto indica a la vista para dibujar cada "marco" de la ubicación animado o cambio de tamaño. Sin esta línea, simplemente podría ajustar la vista a la versión final sin animación.

## <a name="summary"></a>Resumen

Esta guía presentan iOS automática (o "adaptive") el concepto de las restricciones como representaciones matemáticas de las relaciones entre elementos en la superficie de diseño y de diseño. Describe cómo habilitar el diseño automático en el Diseñador de iOS, trabajar con el **barra de herramientas de restricciones**y editar restricciones individualmente en la superficie de diseño. A continuación, explica cómo solucionar problemas comunes de las restricciones tres. Por último, se ha mostrado cómo modificar restricciones en el código.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [Tutorial de controles pueden diseñar de iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Información general del diseñador Android](~/android/user-interface/android-designer/index.md)
- [Restricciones mediante programación](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple - Guía de diseño automático](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
