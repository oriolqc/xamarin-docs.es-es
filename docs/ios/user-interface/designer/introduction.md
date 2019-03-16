---
title: conceptos básicos del Diseñador de iOS
description: Esta guía presenta al diseñador de Xamarin para iOS. Muestra cómo usar el Diseñador de iOS para diseñar controles visualmente, cómo obtener acceso a esos controles en el código y cómo editar las propiedades.
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/31/2018
ms.openlocfilehash: 2c6409410ecba7df8559c07c3231f5e493c98897
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2019
ms.locfileid: "58071091"
---
# <a name="ios-designer-basics"></a>conceptos básicos del Diseñador de iOS

_Esta guía presenta al diseñador de Xamarin para iOS. Muestra cómo usar el Diseñador de iOS para diseñar controles visualmente, cómo obtener acceso a esos controles en el código y cómo editar las propiedades._

El Diseñador de Xamarin para iOS es un diseñador visual interfaz similar a Interface Builder de Xcode y el Diseñador de Android. Algunas de sus numerosas características incluyen la perfecta integración con Visual Studio para Windows y Mac, edición de arrastrar y colocar, una interfaz para configurar los controladores de eventos y la capacidad de representar los controles personalizados.

## <a name="requirements"></a>Requisitos

IOS Designer está disponible en Visual Studio para Mac y Visual Studio 2017 y posterior en Windows. En Visual Studio para Windows, iOS Designer requiere una conexión a un host de compilación de Mac configurado correctamente, aunque no necesita ejecutar Xcode.

Esta guía asume un conocimiento con el contenido que se tratan en el [guías de introducción a](~/ios/get-started/index.md).

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>Cómo funciona el Diseñador de iOS

En esta sección se describe cómo el Diseñador de iOS facilita la creación de una interfaz de usuario y conectarlo al código.

El Diseñador de iOS permite a los desarrolladores diseñar visualmente la interfaz de usuario de la aplicación. Como se describe en el [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md) guía, un guión gráfico describe las pantallas (controladores de vista) que componen una aplicación, los elementos de interfaz (vistas) colocados en los controladores de vista y el flujo general de navegación de la aplicación . 

Un controlador de vista tiene dos partes: una representación visual en el Diseñador de iOS y una clase de C# asociada:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Un controlador de vista en el Diseñador de iOS](introduction-images/1-storyboardwithviewcontroller-vsmac.png "un controlador de vista en el Diseñador de iOS")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![El código para un controlador de vista](introduction-images/2-viewcontrollercode-vsmac.png "el código para un controlador de vista")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Un controlador de vista en el Diseñador de iOS](introduction-images/1-storyboardwithviewcontroller-vs.png "un controlador de vista en el Diseñador de iOS")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![El código para un controlador de vista](introduction-images/2-viewcontrollercode-vs.png "el código para un controlador de vista")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

En su estado predeterminado, un controlador de vista no proporciona ninguna funcionalidad; se debe rellenar con los controles. Estos controles se colocan en la vista del controlador de vista, el área rectangular que contiene todo el contenido de la pantalla. La mayoría de los controladores de vista contienen controles comunes como botones, etiquetas y campos de texto, como se muestra en la siguiente captura de pantalla que muestra un controlador de vista que contiene un botón: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Un controlador de vista que contiene un botón](introduction-images/3-viewcontrollerwithbutton-vsmac.png "un controlador de vista que contiene un botón")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Un controlador de vista que contiene un botón](introduction-images/3-viewcontrollerwithbutton-vs.png "un controlador de vista que contiene un botón")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Algunos controles, como etiquetas que contengan texto estático, se pueden agregar al controlador de vista y dejados de lado. Sin embargo, más a menudo que no, los controles deben personalizarse mediante programación. Por ejemplo, el botón agregado anteriormente debe hacer algo al tocarlo, por lo que debe agregarse un controlador de eventos en el código.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Con el fin de obtener acceso y manipular el botón en el código, debe tener un identificador único. Proporcionar un identificador único seleccionando el botón, abriendo el **panel de propiedades**y estableciendo su **nombre** campo a un valor como "SubmitButton":

[![Establecer el nombre de un botón en el panel de propiedades](introduction-images/4-settingbuttonname-vsmac.png "establecer nombre de un botón en el panel de propiedades")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Con el fin de obtener acceso y manipular el botón en el código, debe tener un identificador único. Proporcionar un identificador único seleccionando el botón, abriendo el **ventana propiedades**y estableciendo su **nombre** campo a un valor como "SubmitButton":

[![Establecer el nombre de un botón en la ventana propiedades](introduction-images/4-settingbuttonname-vs.png "establecer nombre de un botón en la ventana Propiedades")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Ahora que el botón tiene un nombre, se puede acceder en el código. Pero, ¿cómo funciona?

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En el **panel de solución**, al ir a **ViewController.cs** y haga clic en el indicador de divulgación revela que el controlador de vista `ViewController` intervalos de definición de clase dos archivos, cada uno de ellos contiene un [clase parcial](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definición:

[![Los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En el **el Explorador de soluciones**, al ir a **ViewController.cs** y haga clic en el indicador de divulgación revela que el controlador de vista `ViewController` definición de clase abarca dos archivos, cada uno de que contiene un [clase parcial](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definición:

[![Los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** debe rellenarse con código personalizado relacionado con la `ViewController` clase. En este archivo, el `ViewController` clase puede responder a iOS diversos métodos de ciclo de vida del controlador de vista, personalizar la interfaz de usuario y responder a entradas, como pulsa el botón de usuario.

- **ViewController.designer.cs** es un archivo generado, creado por el diseñador para asignar la interfaz construidos visualmente al código de iOS. Puesto que se sobrescribirán los cambios realizados en este archivo, no debe modificarse. Las declaraciones de propiedad de este archivo hacen posible para el código en el `ViewController` por la clase de acceso, **nombre**, controla el conjunto de copia en el Diseñador de iOS. Abrir **ViewController.designer.cs** revela el código siguiente:

```csharp
namespace Designer
{
    [Register ("ViewController")]
    partial class ViewController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        UIKit.UIButton SubmitButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (SubmitButton != null) {
                SubmitButton.Dispose ();
                SubmitButton = null;
            }
        }
    }
}
```

El `SubmitButton` declaración de propiedad conecta toda la `ViewController` clase - no solo el **ViewController.designer.cs** archivo – al botón definido en el guión gráfico. Puesto que **ViewController.cs** define la parte de la `ViewController` (clase), no tiene acceso a `SubmitButton`.

Captura de pantalla siguiente se muestra que IntelliSense ahora reconoce la `SubmitButton` hace referencia en **ViewController.cs**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![IntelliSense reconoce la referencia SubmitButton](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense reconoce la referencia SubmitButton")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![IntelliSense reconoce la referencia SubmitButton](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense reconoce la referencia SubmitButton")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

En esta sección se muestra cómo crear un botón en el Diseñador de iOS y tener acceso a ese botón en el código.

El resto de este documento proporciona una introducción más iOS Designer.

## <a name="ios-designer-basics"></a>conceptos básicos del Diseñador de iOS

Esta sección presenta las partes del Diseñador de iOS y proporciona un paseo por sus características.

### <a name="launching-the-ios-designer"></a>Iniciar el Diseñador de iOS

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Los proyectos de Xamarin.iOS creados con Visual Studio para Mac incluyen un guión gráfico. Para ver el contenido de un guión gráfico, haga doble clic en el archivo .storyboard en el **panel de solución**:

[![Un guión gráfico abierto en el Diseñador de iOS](introduction-images/7-storyboardopen-vsmac.png "un guion gráfico abierto en el Diseñador de iOS")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

La mayoría de los proyectos de Xamarin.iOS creados con Visual Studio incluyen un guión gráfico. Para ver el contenido de un guión gráfico, haga doble clic en el archivo .storyboard en el **el Explorador de soluciones**:

[![Un guión gráfico abierto en el Diseñador de iOS](introduction-images/7-storyboardopen-vs.png "un guion gráfico abierto en el Diseñador de iOS")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>las características del Diseñador de iOS

El Diseñador de iOS tiene seis secciones principales:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Las secciones de iOS Designer](introduction-images/8-sixpartsofiosdesigner-vsmac.png "secciones de iOS Designer")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Superficie de diseño** : área de trabajo principal del Diseñador de iOS. Se muestra en el área del documento, permite la construcción visual de las interfaces de usuario.
2. **Barra de herramientas de restricciones** : permite cambiar entre el modo de edición y modo de edición de restricción, dos formas diferentes para colocar elementos en una interfaz de usuario del marco.
3. **Cuadro de herramientas** : se enumeran los controladores, objetos, controles, las vistas de datos, los reconocedores de gestos, windows y las barras que se pueden arrastrar a la superficie de diseño y agregar a una interfaz de usuario.
4. **Panel de propiedades** : muestra las propiedades del control seleccionado, incluida la identidad, los estilos visuales, accesibilidad, diseño y comportamiento.
5. **Esquema de documento** : muestra el árbol de controles que componen el diseño de la interfaz que se está editando. Al hacer clic en un elemento en el árbol se selecciona en el Diseñador de iOS y muestra sus propiedades en el **panel de propiedades**. Esto resulta útil para seleccionar un control específico en una interfaz de usuario profundamente anidada.
6. **Inferior de la barra de herramientas** – contiene opciones para cambiar la forma en que el Diseñador de iOS muestra el archivo .storyboard o .xib, incluido el dispositivo, orientación y el zoom.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Las secciones de iOS Designer](introduction-images/8-sixpartsofiosdesigner-vs.png "secciones de iOS Designer")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Superficie de diseño** : área de trabajo principal del Diseñador de iOS. Se muestra en el área del documento, permite la construcción visual de las interfaces de usuario.
2. **Barra de herramientas de restricciones** : permite cambiar entre el modo de edición y modo de edición de restricción, dos formas diferentes para colocar elementos en una interfaz de usuario del marco.
3. **Cuadro de herramientas** : se enumeran los controladores, objetos, controles, las vistas de datos, los reconocedores de gestos, windows y las barras que se pueden arrastrar a la superficie de diseño y agregar a una interfaz de usuario.
4. **Ventana propiedades** : muestra las propiedades del control seleccionado, incluida la identidad, los estilos visuales, accesibilidad, diseño y comportamiento.
5. **Esquema de documento** : muestra el árbol de controles que componen el diseño de la interfaz que se está editando. Al hacer clic en un elemento en el árbol se selecciona en el Diseñador de iOS y muestra sus propiedades en el **ventana propiedades**. Esto resulta útil para seleccionar un control específico en una interfaz de usuario profundamente anidada.
6. **Inferior de la barra de herramientas** – contiene opciones para cambiar la forma en que el Diseñador de iOS muestra el archivo .storyboard o .xib, incluido el dispositivo, orientación y el zoom.

-----

### <a name="design-workflow"></a>Flujo de trabajo de diseño

#### <a name="adding-a-control-to-the-interface"></a>Agregar un control a la interfaz

Para agregar un control a una interfaz, arrástrelo desde el **cuadro de herramientas** y colóquela en la superficie de diseño. Al agregar o colocar un control, pautas verticales y horizontales abordan las posiciones de diseño usadas como centro vertical, horizontal center y los márgenes:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
 
![En la superficie de diseño, instrucciones resaltan las posiciones de diseño usadas con frecuencia](introduction-images/9-layoutguides-vsmac.png "en la superficie de diseño, instrucciones resaltan las posiciones de diseño usadas con frecuencia")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![En la superficie de diseño, instrucciones resaltan las posiciones de diseño usadas con frecuencia](introduction-images/9-layoutguides-vs.png "en la superficie de diseño, instrucciones resaltan las posiciones de diseño usadas con frecuencia")

-----

La línea de puntos azul en el ejemplo anterior ofrece una guía de alineación visual de centrado horizontal para ayudar con la colocación del botón.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="context-menu-commands"></a>Comandos de menú contextual

Un menú contextual está disponible en la superficie de diseño y en el **esquema del documento**. Este menú proporciona comandos para el control seleccionado y su elemento primario, lo cual resulta útil cuando se trabaja con vistas en una jerarquía anidada:

[![El menú contextual en la superficie de diseño](introduction-images/10-contextmenudesignsurface-vsmac.png "el menú contextual en la superficie de diseño")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>Barra de herramientas de restricciones

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
 
[![La barra de herramientas de restricciones](introduction-images/11-constraintstoolbar-vsmac.png "la barra de herramientas de restricciones")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![La barra de herramientas de restricciones](introduction-images/11-constraintstoolbar-vs.png "la barra de herramientas de restricciones")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

La barra de herramientas de restricciones se ha actualizado y ahora se compone de dos controles: el marco de modo de edición o restricción Alternar modo de edición de las restricciones de actualización / marcos botón Actualizar.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Modo de edición del marco / alternar el modo de edición de restricción

En versiones anteriores de iOS Designer, haga clic en una vista ya seleccionado en la superficie de diseño alternar entre modo de edición y modo de edición de restricción de marco. Ahora, se activa un control de alternancia en la barra de herramientas de restricciones entre estos modos de edición.

- Modo de edición de marco:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Botón de modo de edición de marco](introduction-images/12a-frameeditingmode-vsmac.png "botón de modo de edición de fotogramas")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Botón de modo de edición de marco](introduction-images/12a-frameeditingmode-vs.png "botón de modo de edición de fotogramas")

-----

- Modo de edición de restricción:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Botón de modo de edición de restricción](introduction-images/12b-constrainteditingmode-vsmac.png "botón de modo de edición de restricción")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Botón de modo de edición de restricción](introduction-images/12b-constrainteditingmode-vs.png "botón de modo de edición de restricción")

-----

#### <a name="update-constraints--update-frames-button"></a>Actualizar restricciones o marcos botón Actualizar

Las restricciones de actualización y se encuentra de botón a la derecha del marco de modo de edición de marcos de actualización / alternar el modo de edición de restricción.

- En modo de edición de marco, al hacer clic en este botón se ajusta los marcos de los elementos seleccionados para que coincida con sus restricciones.
- En modo de edición de restricción, al hacer clic en este botón se ajusta las restricciones de los elementos seleccionados para que coincida con sus marcos.

### <a name="bottom-toolbar"></a>Barra de herramientas inferior

La barra de herramientas inferior proporciona una manera de seleccionar el dispositivo, orientación y zoom utilizado para ver un archivo de guión gráfico o .xib en el Diseñador de iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![La barra de herramientas de la parte inferior, se utiliza para seleccionar un dispositivo y la orientación de la superficie de diseño](introduction-images/13-bottomtoolbar-vsmac.png "la barra de herramientas de la parte inferior, se utiliza para seleccionar un dispositivo y la orientación de la superficie de diseño")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![La barra de herramientas de la parte inferior, se utiliza para seleccionar un dispositivo y la orientación de la superficie de diseño](introduction-images/13-bottomtoolbar-vs.png "la barra de herramientas de la parte inferior, se utiliza para seleccionar un dispositivo y la orientación de la superficie de diseño")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Dispositivo y la orientación

Cuando se expande, la barra de herramientas de la parte inferior muestra todos los dispositivos, orientaciones o adaptaciones aplicables al documento actual. Al hacer clic en ellos, se cambia la vista que se muestra en la superficie de diseño. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![La barra de herramientas de la parte inferior, se expande para mostrar los dispositivos y las orientaciones](introduction-images/14-bottomtoolbarexpanded-vsmac.png "la barra de herramientas de la parte inferior, expandida para mostrar los dispositivos y las orientaciones")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![La barra de herramientas de la parte inferior, se expande para mostrar los dispositivos y las orientaciones](introduction-images/14-bottomtoolbarexpanded-vs.png "la barra de herramientas de la parte inferior, expandida para mostrar los dispositivos y las orientaciones")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Tenga en cuenta que seleccionar un dispositivo y la orientación cambia solo cómo el Diseñador de iOS muestra una vista previa del diseño. Independientemente de la selección actual, recién agregado las restricciones se aplican en todos los dispositivos y orientaciones, a menos que el **editar rasgos** botón se ha usado para que se especifique lo contrario.

Cuando [clases de tamaño](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) son [habilitado](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), **editar rasgos** botón aparecerá en la barra de herramientas inferior expandida.  Al hacer clic en el **editar rasgos** botón muestra opciones para crear una variación de la interfaz basada en la clase de tamaño representada por el dispositivo seleccionado y la orientación. Considere los siguientes ejemplos:

- Si **iPhone SE** / **vertical**, está seleccionada, el elemento flotante proporcionará opciones para crear una variación de la interfaz para el ancho de compact, clase de tamaño normal alto. 
- Si **iPad Pro 9.7"** / **horizontal** / **pantalla completa** está seleccionada, el elemento flotante proporcionará opciones para crear una variación de la interfaz para el ancho normal, clase de tamaño normal alto.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![La barra de herramientas inferior que se utilizan para modificar una interfaz mediante la clase de tamaño](introduction-images/15-edittraitsbutton-vsmac.png "la barra de herramientas inferior que se utilizan para modificar una interfaz mediante la clase de tamaño")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![La barra de herramientas inferior que se utilizan para modificar una interfaz mediante la clase de tamaño](introduction-images/15-edittraitsbutton-vs.png "la barra de herramientas inferior que se utilizan para modificar una interfaz mediante la clase de tamaño")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Controles de zoom

La superficie de diseño admite a través de varios controles de zoom:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
 
![Los controles de zoom en la barra de herramientas inferior](introduction-images/16-zoomcontrols-vsmac.png "los controles de zoom en la barra de herramientas inferior")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Los controles de zoom en la barra de herramientas inferior](introduction-images/16-zoomcontrols-vs.png "los controles de zoom en la barra de herramientas inferior")

-----

Los controles incluyen lo siguiente:

1. Zoom para ajustar
2. Alejar
3. Acercar
4. Tamaño real (el tamaño de píxel de 1:1)

Estos controles ajustan el zoom en la superficie de diseño. No afectan a la interfaz de usuario de la aplicación en tiempo de ejecución.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

### <a name="properties-pad"></a>Panel de propiedades

Use la **panel de propiedades** para editar la identidad, los estilos visuales, accesibilidad y comportamiento de un control. Captura de pantalla siguiente se ilustra la **panel de propiedades** opciones para un botón:

[![El panel de propiedades para un botón](introduction-images/17-buttonpropertiespad-vsmac.png "el panel de propiedades para un botón")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Secciones del panel de propiedades

El **panel de propiedades** contiene tres secciones:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>Ventana Propiedades

Use la **ventana propiedades** para editar la identidad, los estilos visuales, accesibilidad y comportamiento de un control. Captura de pantalla siguiente se ilustra la **ventana propiedades** opciones para un botón:

[![La ventana Propiedades de un botón](introduction-images/17-buttonpropertieswindow-vs.png "la ventana Propiedades de un botón")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Secciones de la ventana de propiedades

El **ventana propiedades** contiene tres secciones:

-----

1.  **Widget** : las propiedades principales del control, como el nombre de clase, propiedades de estilo, etcetera. Normalmente se colocan aquí las propiedades para administrar el contenido del control.
2.  **Diseño** : se enumeran las propiedades que realizar un seguimiento de la posición y el tamaño del control, incluidas las restricciones y marcos, aquí.
3.  **Eventos** : eventos y controladores de eventos se especifican aquí. Resulta útil para controlar eventos de toque, tap, arrastre, etcetera. También se pueden controlar eventos directamente en el código.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>Edición de propiedades en el panel de propiedades

Además de la edición visual en la superficie de diseño, el Diseñador de iOS admite la edición de propiedades en el **panel de propiedades**. El cambio de las propiedades disponibles según el control seleccionado, como se muestra en las capturas de pantalla siguiente:

[![Propiedades del botón](introduction-images/18a-buttonpropertiespad-vsmac.png "propiedades del botón")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Ver las propiedades de controlador](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "ver las propiedades de controlador")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>Edición de propiedades en la ventana Propiedades

Además de la edición visual en la superficie de diseño, el Diseñador de iOS admite la edición de propiedades en el **ventana propiedades**. El cambio de las propiedades disponibles según el control seleccionado, como se muestra en las capturas de pantalla siguiente:

[![Propiedades del botón](introduction-images/18a-buttonpropertieswindow-vs.png "propiedades del botón")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Ver las propiedades de controlador](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "ver las propiedades de controlador")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> La sección identidad del panel de propiedades ahora muestra un **módulo** campo. Es necesario rellenar en esta sección solo cuando se interopera con las clases de Swift. Usar para escribir un nombre de módulo para Swift clases, que tiene espacio de nombres.

#### <a name="default-values"></a>Valores predeterminados

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Muchas propiedades en el **panel de propiedades** mostrar ningún valor o un valor predeterminado. Sin embargo, el código de la aplicación todavía puede modificar estos valores. El **panel de propiedades** no muestra los valores establecidos en el código.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Muchas propiedades en el **ventana propiedades** mostrar ningún valor o un valor predeterminado. Sin embargo, el código de la aplicación todavía puede modificar estos valores. El **ventana propiedades** no muestra los valores establecidos en el código.

-----

#### <a name="event-handlers"></a>Controladores de eventos

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para especificar controladores de eventos personalizados para varios eventos, use el **eventos** pestaña de la **panel de propiedades**. Por ejemplo, en la captura de pantalla siguiente, un `HandleClick` método controla el botón **Touch copia dentro de** eventos:

[![El panel de propiedades con un controlador de eventos definido para un botón](introduction-images/19-buttonpropertiespadevents-vsmac.png "el panel de propiedades, con un controlador de eventos definido para un botón")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para especificar controladores de eventos personalizados para varios eventos, use el **eventos** pestaña de la **ventana propiedades**. Por ejemplo, en la captura de pantalla siguiente, un `HandleClick` método controla el botón **Touch copia dentro de** eventos:

[![La ventana Propiedades, con un controlador de eventos definido para un botón](introduction-images/19-buttonpropertieswindowevents-vs.png "la ventana Propiedades, con un controlador de eventos definido para un botón")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

Una vez que se ha especificado un controlador de eventos, un método del mismo nombre debe agregarse a la clase de controlador de vista correspondiente. En caso contrario, un `unrecognized selector` excepción se producirá cuando se pulsa el botón:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Una excepción no reconocido selector](introduction-images/20-unrecognizedselector-vsmac.png "una excepción de selector no reconocido")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Tenga en cuenta que después de un controlador de eventos se ha especificado en el **panel de propiedades**, el diseñador se abra el archivo de código correspondiente inmediatamente y se ofrecen insertar la declaración del método de iOS. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Una excepción no reconocido selector](introduction-images/20-unrecognizedselector-vs.png "una excepción de selector no reconocido")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Para obtener un ejemplo que usa controladores de eventos personalizados, consulte el [Hello, iOS Guía de introducción de](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>Vista de esquema

IOS Designer también puede mostrar la jerarquía de la interfaz de controles como un esquema. El esquema está disponible si selecciona el **esquema del documento** pestaña, como se muestra a continuación:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![El esquema del documento](introduction-images/21-buttonoutlineview-vsmac.png "el esquema del documento")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![El esquema del documento](introduction-images/21-buttonoutlineview-vs.png "el esquema del documento")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

El control seleccionado en la vista de esquema está sincronizado con el control seleccionado en la superficie de diseño.  Esta característica es útil para seleccionar un elemento de una jerarquía de interfaz profundamente anidado.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="revert-to-xcode"></a>Volver a Xcode

Es posible utilizar iOS Designer y en Interface Builder de Xcode indistintamente. Para abrir un guión gráfico o un archivo .xib en Interface Builder de Xcode, haga doble clic en el archivo y seleccione **abrir con > Interface Builder de Xcode**, tal y como se muestra en la captura de pantalla siguiente:

[![Abrir un guion gráfico en Interface Builder de Xcode](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "abrir un guion gráfico en Interface Builder de Xcode")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Después de realizar modificaciones en Interface Builder de Xcode, guarde el archivo y vuelva a Visual Studio para Mac. Los cambios se sincronizarán con el proyecto de Xamarin.iOS.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="revert-to-xcode"></a>Volver a Xcode

Es posible utilizar iOS Designer y en Interface Builder de Xcode de forma intercambiable, sin embargo, Interface Builder de Xcode solo está disponible en Mac. Para abrir un archivo de guión gráfico o .xib en Interface Builder de Xcode en un equipo Mac, abra la solución que contiene el proyecto de Xamarin.iOS en [Visual Studio para Mac](/visualstudio/mac/), haga doble clic en el archivo y seleccione **abrir con > interfaz de Xcode El generador de**, tal y como se muestra en la captura de pantalla siguiente:

[![Abrir un guion gráfico en Interface Builder de Xcode](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "abrir un guion gráfico en Interface Builder de Xcode")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

Después de realizar modificaciones en Interface Builder de Xcode, guarde el archivo y vuelva a Visual Studio para Mac. Los cambios se sincronizarán con el proyecto de Xamarin.iOS.

-----

## <a name="xib-support"></a>compatibilidad con .xib

El Diseñador de iOS es compatible con la creación, edición y administración de archivos .xib. Estos son archivos XML que representar personalizado, solo las vistas que pueden agregarse a la jerarquía de vistas de la aplicación. Un archivo .xib generalmente representa la interfaz de una vista única o una pantalla en una aplicación, mientras que un guión gráfico representa varias pantallas y las transiciones entre ellos.

Hay muchas opiniones acerca de qué solución – archivos .xib, los guiones gráficos o código – funciona mejor para crear y mantener una interfaz de usuario. En realidad, no hay ninguna solución perfecta, y siempre es merece la pena considerar la mejor herramienta para el trabajo en cuestión. Es decir, archivos .xib son generalmente más eficaces cuando se usa para representar una vista personalizada que sea necesaria en varios lugares de una aplicación, como una celda de vista de tabla personalizada. 

Puede encontrar más documentación sobre el uso de archivos .xib en las recetas siguientes:

- [Mediante la plantilla de vista .xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [Creación de un TableViewCell personalizado mediante un .xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [Crear una pantalla de inicio mediante un .xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

Para obtener más información acerca del uso de los guiones gráficos, consulte el [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md).

Consulte el uso de guiones gráficos como el enfoque estándar para la creación de interfaces de usuario, dado que la mayoría Xamarin.iOS nuevas plantillas de proyecto proporcionan un guión gráfico de forma predeterminada esta y otras guías relacionadas con el Diseñador de iOS.

## <a name="summary"></a>Resumen

Esta guía proporciona una introducción a iOS Designer, que describe sus características y las herramientas que ofrece para diseñar interfaces de usuario atractivas de esquematización.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [Tutorial de controles pueden diseñar de iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Información general del diseñador Android](~/android/user-interface/android-designer/index.md)
- [Clases y métodos parciales](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Profundizar en el Diseñador de Xamarin para iOS - 2014 evolucionar (vídeo)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [Usar el Diseñador de iOS para crear una pantalla de inicio (vídeo)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
