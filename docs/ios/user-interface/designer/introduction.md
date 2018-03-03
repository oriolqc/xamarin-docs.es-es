---
title: "conceptos básicos de diseñador de iOS"
description: "Esta guía presenta al diseñador de Xamarin para iOS. Muestra cómo utilizar el Diseñador de iOS para diseñar visualmente los controles, cómo obtener acceso a los controles en el código y cómo editar las propiedades."
ms.topic: article
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/31/2018
ms.openlocfilehash: 3046d779239076098a8b2fb74fc87e2f211074e9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="ios-designer-basics"></a>conceptos básicos de diseñador de iOS

_Esta guía presenta al diseñador de Xamarin para iOS. Muestra cómo utilizar el Diseñador de iOS para diseñar visualmente los controles, cómo obtener acceso a los controles en el código y cómo editar las propiedades._

El Diseñador de Xamarin para iOS es un diseñador de interfaz visual similar al interfaz generador del Xcode y el Diseñador de Android. Algunas de sus muchas características incluyen integración sin problemas con Visual Studio para Mac y Visual Studio 2015 y 2017, edición de tipo arrastrar y colocar, una interfaz para configurar controladores de eventos y la capacidad para representar controles personalizados.

## <a name="requirements"></a>Requisitos

El archivo iOS diseñador está disponible en Visual Studio para Mac y en Visual Studio 2015 y 2017 en Windows. En Visual Studio 2015 o 2017, el Diseñador de iOS requiere una conexión a un host de compilación de Mac configurado correctamente, aunque no necesita ejecutar Xcode.

Esta guía se da por supuesto una familiaridad con el contenido que se tratan en la [Introducción le guía](~/ios/get-started/index.md).

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>Cómo funciona el Diseñador de iOS

Esta sección describe cómo el Diseñador de iOS facilita la creación de una interfaz de usuario y conectarlo al código.

El Diseñador de iOS permite a los desarrolladores diseñar visualmente la interfaz de usuario de la aplicación. Tal como se describe en el [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md) guía, un guión gráfico describe las pantallas (controladores de la vista) que componen una aplicación, los elementos de interfaz (vistas) colocados en los controladores de la vista y el flujo general de navegación de la aplicación . 

Un controlador de vista tiene dos partes: una representación visual en el Diseñador de iOS y una clase de C# asociada:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Un controlador de vista en el Diseñador de iOS](introduction-images/1-storyboardwithviewcontroller-vsmac.png "un controlador de vista en el Diseñador de iOS")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png)

[![El código de un controlador de vista](introduction-images/2-viewcontrollercode-vsmac.png "el código de un controlador de vista")](introduction-images/2-viewcontrollercode-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Un controlador de vista en el Diseñador de iOS](introduction-images/1-storyboardwithviewcontroller-vs.png "un controlador de vista en el Diseñador de iOS")](introduction-images/1-storyboardwithviewcontroller-vs-large.png)

[![El código de un controlador de vista](introduction-images/2-viewcontrollercode-vs.png "el código de un controlador de vista")](introduction-images/2-viewcontrollercode-vs-large.png)

-----

En su estado predeterminado, un controlador de vista no proporciona ninguna funcionalidad; se debe rellenar con controles. Estos controles se colocan en la vista del controlador de vista, el área rectangular que contiene todo el contenido de la pantalla. La mayoría de los controladores de vista contengan controles comunes como botones, etiquetas y campos de texto, como se muestra en la siguiente captura de pantalla que muestra un controlador de vista que contiene un botón: 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Un controlador de vista que contiene un botón](introduction-images/3-viewcontrollerwithbutton-vsmac.png "un controlador de vista que contiene un botón")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Un controlador de vista que contiene un botón](introduction-images/3-viewcontrollerwithbutton-vs.png "un controlador de vista que contiene un botón")](introduction-images/3-viewcontrollerwithbutton-vs-large.png)

-----

Algunos controles, como las etiquetas que contiene texto estático, se pueden agregar al controlador de vista y dejarse como están. Sin embargo, más a menudo que no es así, los controles deben ser personalizados mediante programación. Por ejemplo, el botón que agregó anteriormente debería hacer algo cuando puntea, por lo que debe agregarse un controlador de eventos en el código.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para obtener acceso y manipular el botón en el código, debe tener un identificador único. Proporcionar un identificador único, seleccione el botón, abriendo el **panel de propiedades**y estableciendo su **nombre** campo en un valor como "SubmitButton":

[![Establecer el nombre del botón en el panel de propiedades](introduction-images/4-settingbuttonname-vsmac.png "establecer nombre del botón en el panel de propiedades")](introduction-images/4-settingbuttonname-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para obtener acceso y manipular el botón en el código, debe tener un identificador único. Proporcionar un identificador único, seleccione el botón, abriendo el **ventana propiedades**y estableciendo su **nombre** campo en un valor como "SubmitButton":

[![Nombre de un botón de la configuración en la ventana propiedades](introduction-images/4-settingbuttonname-vs.png "establecer nombre del botón en la ventana Propiedades")](introduction-images/4-settingbuttonname-vs-large.png)

-----

Ahora que el botón tiene un nombre, puede tener acceso en el código. Pero, ¿cómo funciona?

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

En el **solución Pad**, navegación a **ViewController.cs** y haga clic en el indicador de divulgación revela que el controlador de vista `ViewController` intervalos de definición de clase dos archivos, cada uno de ellos contiene un [clase parcial](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definición:

[![Los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

En el **el Explorador de soluciones**, navegación a **ViewController.cs** y haga clic en el indicador de divulgación revela que el controlador de vista `ViewController` definición de clase abarca dos archivos, cada uno de que contiene un [clase parcial](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) definición:

[![Los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "los dos archivos que componen la clase ViewController: ViewController.cs y ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png)

-----

- **ViewController.cs** debe rellenarse con código personalizado relacionado con la `ViewController` clase. En este archivo, la `ViewController` clase puede responder a iOS diversos métodos de ciclo de vida del controlador de vista, personalizar la interfaz de usuario y responder a la entrada como botón puntea del usuario.

- **ViewController.designer.cs** es un archivo generado, creado por el diseñador para asignar la interfaz construido visualmente a código de iOS. Puesto que se sobrescribirá los cambios realizados en este archivo, no debe modificarse. Las declaraciones de propiedad de este archivo le permite para el código en el `ViewController` por la clase de acceso, **nombre**, controla el conjunto de seguridad en el Diseñador de iOS. Abrir **ViewController.designer.cs** revela el código siguiente:

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

El `SubmitButton` declaración de propiedad conecta toda la `ViewController` clase - no simplemente el **ViewController.designer.cs** archivo – al botón definido en el guión gráfico. Puesto que **ViewController.cs** define la parte de la `ViewController` (clase), tiene acceso a `SubmitButton`.

Captura de pantalla siguiente muestra que IntelliSense ahora reconoce el `SubmitButton` referencia en **ViewController.cs**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![IntelliSense reconocido por la referencia SubmitButton](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense reconocido por la referencia SubmitButton")](introduction-images/6-submitbuttonintellisense-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![IntelliSense reconocido por la referencia SubmitButton](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense reconocido por la referencia SubmitButton")](introduction-images/6-submitbuttonintellisense-vs-large.png)

-----

En esta sección se muestra cómo crear un botón en el Diseñador de iOS y tener acceso a ese botón en el código.

El resto de este documento proporciona una descripción adicional del Diseñador de iOS.

## <a name="ios-designer-basics"></a>conceptos básicos de diseñador de iOS

Esta sección presenta las partes del Diseñador de iOS y proporciona un recorrido por los de sus características.

### <a name="launching-the-ios-designer"></a>Iniciar el Diseñador de iOS

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Proyectos de Xamarin.iOS creados con Visual Studio para Mac incluyen un guión gráfico. Para ver el contenido de un guión gráfico, haga doble clic en el archivo .storyboard en el **solución panel**:

[![Un guión gráfico abierto en el Diseñador de iOS](introduction-images/7-storyboardopen-vsmac.png "un guión gráfico abierto en el Diseñador de iOS")](introduction-images/7-storyboardopen-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

La mayoría de Xamarin.iOS de proyectos creados con Visual Studio 2015 o 2017 incluyen un guión gráfico. Para ver el contenido de un guión gráfico, haga doble clic en el archivo .storyboard en el **el Explorador de soluciones**:

[![Un guión gráfico abierto en el Diseñador de iOS](introduction-images/7-storyboardopen-vs.png "un guión gráfico abierto en el Diseñador de iOS")](introduction-images/7-storyboardopen-vs-large.png)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>características del Diseñador de iOS

El Diseñador de iOS tiene seis secciones principales:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Secciones del Diseñador de iOS](introduction-images/8-sixpartsofiosdesigner-vsmac.png "secciones del Diseñador de iOS")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png)

1. **Superficie de diseño** : área de trabajo principal del Diseñador de iOS. Se muestra en el área del documento, permite la construcción visual de las interfaces de usuario.
2. **Barra de herramientas de restricciones** : permite cambiar entre el modo de edición y modo de edición de restricción, dos maneras diferentes para colocar elementos en una interfaz de usuario de marco.
3. **Cuadro de herramientas** : enumera los controladores, objetos, controles, vistas de datos, identificadores de gestos, windows y barras que puede arrastrar hasta la superficie de diseño y se agrega a una interfaz de usuario.
4. **Panel de propiedades** : muestra las propiedades para el control seleccionado, incluida la identidad, los estilos visuales, accesibilidad, diseño y comportamiento.
5. **Esquema de documento** : muestra el árbol de controles que componen el diseño de la interfaz que se está editando. Al hacer clic en un elemento en el árbol de selecciona en el Diseñador de iOS y muestra sus propiedades en el **panel de propiedades**. Esto es útil para seleccionar un control específico en una interfaz de usuario profundamente anidado.
6. **Abajo la barra de herramientas** – contiene opciones para cambiar la forma en que el Diseñador de iOS muestra el archivo .storyboard o .xib, incluidos los dispositivos, la orientación y el zoom.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Secciones del Diseñador de iOS](introduction-images/8-sixpartsofiosdesigner-vs.png "secciones del Diseñador de iOS")](introduction-images/8-sixpartsofiosdesigner-vs-large.png)

1. **Superficie de diseño** : área de trabajo principal del Diseñador de iOS. Se muestra en el área del documento, permite la construcción visual de las interfaces de usuario.
2. **Barra de herramientas de restricciones** : permite cambiar entre el modo de edición y modo de edición de restricción, dos maneras diferentes para colocar elementos en una interfaz de usuario de marco.
3. **Cuadro de herramientas** : enumera los controladores, objetos, controles, vistas de datos, identificadores de gestos, windows y barras que puede arrastrar hasta la superficie de diseño y se agrega a una interfaz de usuario.
4. **Ventana propiedades** : muestra las propiedades para el control seleccionado, incluida la identidad, los estilos visuales, accesibilidad, diseño y comportamiento.
5. **Esquema de documento** : muestra el árbol de controles que componen el diseño de la interfaz que se está editando. Al hacer clic en un elemento en el árbol de selecciona en el Diseñador de iOS y muestra sus propiedades en el **ventana propiedades**. Esto es útil para seleccionar un control específico en una interfaz de usuario profundamente anidado.
6. **Abajo la barra de herramientas** – contiene opciones para cambiar la forma en que el Diseñador de iOS muestra el archivo .storyboard o .xib, incluidos los dispositivos, la orientación y el zoom.

-----

### <a name="design-workflow"></a>Flujo de trabajo de diseño

#### <a name="adding-a-control-to-the-interface"></a>Agregar un control a la interfaz

Para agregar un control a una interfaz, arrástrela desde el **cuadro de herramientas** y colóquela en la superficie de diseño. Al agregar o colocar un control, directrices verticales y horizontales resaltan posiciones de diseño utilizadas como centro vertical y horizontal center, los márgenes:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
 
![En la superficie de diseño, directrices resaltan posiciones de diseño utilizadas](introduction-images/9-layoutguides-vsmac.png "en la superficie de diseño, directrices resaltan posiciones de diseño usados con frecuencia")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![En la superficie de diseño, directrices resaltan posiciones de diseño utilizadas](introduction-images/9-layoutguides-vs.png "en la superficie de diseño, directrices resaltan posiciones de diseño usados con frecuencia")

-----

La línea azul de puntos en el ejemplo anterior se ofrece una guía de alineación visual centro horizontal para ayudar con la colocación del botón.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

#### <a name="context-menu-commands"></a>Comandos del menú contextual

Los menús contextuales están disponibles tanto en la superficie de diseño y en la **esquema del documento**. Este menú proporciona comandos para el control seleccionado y su elemento primario, que es útil al trabajar con vistas en una jerarquía anidada:

[![El menú contextual en la superficie de diseño](introduction-images/10-contextmenudesignsurface-vsmac.png "el menú contextual en la superficie de diseño")](introduction-images/10-contextmenudesignsurface-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

### <a name="constraints-toolbar"></a>Barra de herramientas de restricciones

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
 
[![La barra de herramientas de restricciones](introduction-images/11-constraintstoolbar-vsmac.png "la barra de herramientas de restricciones")](introduction-images/11-constraintstoolbar-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![La barra de herramientas de restricciones](introduction-images/11-constraintstoolbar-vs.png "la barra de herramientas de restricciones")](introduction-images/11-constraintstoolbar-vs-large.png)

-----

La barra de herramientas de restricciones se ha actualizado y ahora está compuesto de dos controles: el marco de modo de edición / restricción Alternar modo y las restricciones de actualización de edición o botón marcos de la actualización.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Modo de edición de marco / alternar el modo de edición de restricción

En versiones anteriores del Diseñador de iOS, haga clic en una vista ya seleccionado en la superficie de diseño alterna entre el marco de edición de modo y el modo de edición de restricción. Ahora, se activa un control de alternancia en la barra de herramientas de restricciones entre estos modos de edición.

- Modo de edición de marco:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Botón de modo de edición de marco](introduction-images/12a-frameeditingmode-vsmac.png "botón de modo de edición de marco")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Botón de modo de edición de marco](introduction-images/12a-frameeditingmode-vs.png "botón de modo de edición de marco")

-----

- Modo de edición de restricción:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Botón de modo de edición de restricción](introduction-images/12b-constrainteditingmode-vsmac.png "botón de modo de edición de restricción")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Botón de modo de edición de restricción](introduction-images/12b-constrainteditingmode-vs.png "botón de modo de edición de restricción")

-----

#### <a name="update-constraints--update-frames-button"></a>Restricciones de actualización o la actualización de botón de marcos

Las restricciones de actualización / actualización sigue de botón a la derecha del marco de modo de edición de marcos / alternar el modo de edición de restricción.

- En el marco de modo de edición, haga clic en este botón ajusta los marcos de los elementos seleccionados para que coincida con sus restricciones.
- En el modo de edición de restricción, haga clic en este botón ajusta las restricciones de los elementos seleccionados para que coincida con sus marcos.

### <a name="bottom-toolbar"></a>Barra de herramientas inferior

La barra de herramientas de la parte inferior proporciona una manera de seleccionar el dispositivo, la orientación y el zoom sirve para ver un archivo de guión gráfico o .xib en el Diseñador de iOS:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![La barra de herramientas de la parte inferior, se utiliza para seleccionar un dispositivo y la orientación de la superficie de diseño](introduction-images/13-bottomtoolbar-vsmac.png "la barra de herramientas de la parte inferior, se utiliza para seleccionar un dispositivo y la orientación de la superficie de diseño")](introduction-images/13-bottomtoolbar-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![La barra de herramientas de la parte inferior, se utiliza para seleccionar un dispositivo y la orientación de la superficie de diseño](introduction-images/13-bottomtoolbar-vs.png "la barra de herramientas de la parte inferior, se utiliza para seleccionar un dispositivo y la orientación de la superficie de diseño")](introduction-images/13-bottomtoolbar-vs-large.png)

-----

#### <a name="device-and-orientation"></a>Dispositivo y la orientación

Cuando se expande, la barra de herramientas de la parte inferior muestra todos los dispositivos, orientaciones o las adaptaciones aplicables al documento actual. Al hacer clic en ellos, se cambia la vista mostrada en la superficie de diseño. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![La barra de herramientas de la parte inferior, se expande para mostrar todos los dispositivos y orientaciones](introduction-images/14-bottomtoolbarexpanded-vsmac.png "la barra de herramientas de la parte inferior, expandida para mostrar todos los dispositivos y orientaciones")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![La barra de herramientas de la parte inferior, se expande para mostrar todos los dispositivos y orientaciones](introduction-images/14-bottomtoolbarexpanded-vs.png "la barra de herramientas de la parte inferior, expandida para mostrar todos los dispositivos y orientaciones")](introduction-images/14-bottomtoolbarexpanded-vs-large.png)

-----

Tenga en cuenta que al seleccionar un dispositivo y la orientación cambia solo cómo el Diseñador de iOS muestra una vista previa del diseño. Independientemente de la selección actual, recién agregado las restricciones se aplican en todos los dispositivos y orientaciones a menos que la **editar rasgos** botón se ha utilizado para que se especifique lo contrario.

Cuando [cambiar el tamaño de las clases](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) son [habilitado](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), el **editar rasgos** botón aparecerá en la barra de herramientas inferior expandido.  Al hacer clic en el **editar rasgos** botón muestra las opciones para crear una variación de interfaz basada en la clase de tamaño representada por el dispositivo seleccionado y la orientación. Considere los siguientes ejemplos:

- Si **iPhone SE** / **vertical**, está seleccionado, el popover proporcionará opciones para crear una variación de interfaz para el ancho de compact, clase de tamaño de altura normal. 
- Si **iPad Pro 9.7"** / **horizontal** / **pantalla completa** está seleccionada, la popover proporcionará opciones para crear una variación de la interfaz para el ancho normal, clase de tamaño normal alto.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![La barra de herramientas de la parte inferior que se utilizan para variar una interfaz por clase de tamaño](introduction-images/15-edittraitsbutton-vsmac.png "la barra de herramientas de la parte inferior que se utilizan para variar una interfaz por clase de tamaño")](introduction-images/15-edittraitsbutton-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![La barra de herramientas de la parte inferior que se utilizan para variar una interfaz por clase de tamaño](introduction-images/15-edittraitsbutton-vs.png "la barra de herramientas de la parte inferior que se utilizan para variar una interfaz por clase de tamaño")](introduction-images/15-edittraitsbutton-vs-large.png)

-----

#### <a name="zoom-controls"></a>Controles de zoom

La superficie de diseño admite Zoom a través de varios controles:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
 
![Los controles de zoom en la barra de herramientas de la parte inferior](introduction-images/16-zoomcontrols-vsmac.png "los controles de zoom en la barra de herramientas de la parte inferior")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Los controles de zoom en la barra de herramientas de la parte inferior](introduction-images/16-zoomcontrols-vs.png "los controles de zoom en la barra de herramientas de la parte inferior")

-----

Los controles incluyen lo siguiente:

1. Zoom para ajustar
2. Alejar
3. Acercar
4. Tamaño real (tamaño de píxel de 1:1)

Estos controles ajustan el zoom en la superficie de diseño. No afectan a la interfaz de usuario de la aplicación en tiempo de ejecución.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

### <a name="properties-pad"></a>Panel de propiedades

Use la **panel de propiedades** para editar la identidad, los estilos visuales, accesibilidad y el comportamiento de un control. Captura de pantalla siguiente muestra la **panel de propiedades** opciones para un botón:

[![El panel de propiedades para un botón](introduction-images/17-buttonpropertiespad-vsmac.png "el panel de propiedades para un botón")](introduction-images/17-buttonpropertiespad-vsmac-large.png)
#### <a name="properties-pad-sections"></a>Secciones del panel de propiedades

El **panel de propiedades** contiene tres secciones:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="properties-window"></a>Ventana Propiedades

Use la **ventana propiedades** para editar la identidad, los estilos visuales, accesibilidad y el comportamiento de un control. Captura de pantalla siguiente muestra la **ventana propiedades** opciones para un botón:

[![La ventana Propiedades de un botón](introduction-images/17-buttonpropertieswindow-vs.png "la ventana de propiedades para un botón")](introduction-images/17-buttonpropertieswindow-vs-large.png)

#### <a name="properties-window-sections"></a>Secciones de la ventana de propiedades

El **ventana propiedades** contiene tres secciones:

-----

1.  **El widget** : las propiedades principales del control, como el nombre, clase, propiedades de estilo, etcetera. Propiedades para administrar el contenido del control normalmente se colocan aquí.
2.  **Diseño** – propiedades que realizar un seguimiento de la posición y el tamaño del control, incluidas las restricciones y marcos, se muestran aquí.
3.  **Eventos** : eventos y controladores de eventos se especifican aquí. Resulta útil para controlar eventos como táctil, puntee, arrastrar, etcetera. También se pueden controlar eventos directamente en el código.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

#### <a name="editing-properties-in-the-properties-pad"></a>Edición de propiedades en el panel de propiedades

Además de la edición visual en la superficie de diseño, el Diseñador de iOS admite la edición de propiedades en el **panel de propiedades**. El cambio de propiedades disponibles basándose en el control seleccionado, como se muestra en las capturas de pantalla siguiente:

[![Botón propiedades](introduction-images/18a-buttonpropertiespad-vsmac.png "botón Propiedades")](introduction-images/18a-buttonpropertiespad-vsmac-large.png)

[![Ver las propiedades del controlador](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "ver las propiedades del controlador")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="editing-properties-in-the-properties-window"></a>Edición de propiedades en la ventana Propiedades

Además de la edición visual en la superficie de diseño, el Diseñador de iOS admite la edición de propiedades en el **ventana propiedades**. El cambio de propiedades disponibles basándose en el control seleccionado, como se muestra en las capturas de pantalla siguiente:

[![Botón propiedades](introduction-images/18a-buttonpropertieswindow-vs.png "botón Propiedades")](introduction-images/18a-buttonpropertieswindow-vs-large.png)

[![Ver las propiedades del controlador](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "ver las propiedades del controlador")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png)

-----

> [!IMPORTANT]
> La sección de identidad del panel de propiedades ahora muestra un **módulo** campo. Es necesario rellenar esta sección solo cuando se interopera con las clases de Swift. Usar para escribir un nombre de módulo para Swift clases, que son espacios de nombres.

#### <a name="default-values"></a>Valores predeterminados

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Muchas propiedades en el **panel de propiedades** mostrar ningún valor o un valor predeterminado. Sin embargo, el código de la aplicación puede modificar estos valores. El **panel de propiedades** no muestra los valores establecidos en el código.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Muchas propiedades en el **ventana propiedades** mostrar ningún valor o un valor predeterminado. Sin embargo, el código de la aplicación puede modificar estos valores. El **ventana propiedades** no muestra los valores establecidos en el código.

-----

#### <a name="event-handlers"></a>Controladores de eventos

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para especificar controladores de eventos personalizados para varios eventos, use la **eventos** pestaña de la **panel de propiedades**. Por ejemplo, en la captura de pantalla siguiente, un `HandleClick` método controla el botón **Touch seguridad dentro de** eventos:

[![El panel de propiedades, con un controlador de eventos establecido para un botón](introduction-images/19-buttonpropertiespadevents-vsmac.png "el panel de propiedades, con un controlador de eventos establecido para un botón")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para especificar controladores de eventos personalizados para varios eventos, use la **eventos** pestaña de la **ventana propiedades**. Por ejemplo, en la captura de pantalla siguiente, un `HandleClick` método controla el botón **Touch seguridad dentro de** eventos:

[![La ventana Propiedades, con un controlador de eventos establecido para un botón](introduction-images/19-buttonpropertieswindowevents-vs.png "la ventana de propiedades, con un controlador de eventos establecido para un botón")](introduction-images/19-buttonpropertieswindowevents-vs-large.png)

-----

Una vez que se ha especificado un controlador de eventos, un método del mismo nombre debe agregarse a la clase de controlador de vista correspondiente. En caso contrario, un `unrecognized selector` excepción se producirá cuando se puntea el botón:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Una excepción no reconocida selector](introduction-images/20-unrecognizedselector-vsmac.png "una excepción de selector no reconocido")](introduction-images/20-unrecognizedselector-vsmac-large.png)

Tenga en cuenta que después de un controlador de eventos se ha especificado en el **panel de propiedades**, el diseñador se abra el archivo de código correspondiente inmediatamente y se ofrecen insertar la declaración del método de iOS. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Una excepción no reconocida selector](introduction-images/20-unrecognizedselector-vs.png "una excepción de selector no reconocido")](introduction-images/20-unrecognizedselector-vs-large.png)

-----

Para obtener un ejemplo que utiliza los controladores de eventos personalizados, consulte la [Hello, iOS Guía de introducción de](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>Vista Esquema

El Diseñador de iOS también puede mostrar la jerarquía de la interfaz de controles como un contorno. El esquema está disponible si selecciona el **esquema del documento** ficha, tal y como se muestra a continuación:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![El esquema del documento](introduction-images/21-buttonoutlineview-vsmac.png "el esquema del documento")](introduction-images/21-buttonoutlineview-vsmac-large.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![El esquema del documento](introduction-images/21-buttonoutlineview-vs.png "el esquema del documento")](introduction-images/21-buttonoutlineview-vs-large.png)

-----

El control seleccionado en la vista de esquema está sincronizado con el control seleccionado en la superficie de diseño.  Esta característica es útil para seleccionar un elemento de una jerarquía de interfaz profundamente anidada.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="revert-to-xcode"></a>Volver a Xcode

Es posible utilizar el diseñador y el generador de interfaz de Xcode de iOS indistintamente. Para abrir un guión gráfico o un archivo .xib en el generador de interfaz de Xcode, haga doble clic en el archivo y seleccione **abrir con > Generador de interfaz de Xcode**, tal y como se muestra en la captura de pantalla siguiente:

[![Abrir un guión gráfico en el generador de interfaz de Xcode](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "abrir un guión gráfico en el generador de interfaz de Xcode")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png)

Después de realizar modificaciones en el generador de interfaz de Xcode, guarde el archivo y vuelva a Visual Studio para Mac. Los cambios se sincronizarán con el proyecto de Xamarin.iOS.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

## <a name="xib-support"></a>compatibilidad con .xib

El Diseñador de iOS es compatible con la creación, edición y administración de archivos de .xib. Estos son archivos XML que representar único personalizado vistas que se pueden agregar a jerarquía de vista de la aplicación. Un archivo .xib generalmente representa la interfaz para una vista única o en pantalla en una aplicación, mientras que un guión gráfico representa muchas pantallas y las transiciones entre ellos.

Hay muchas opiniones sobre qué solución-.xib archivos, guiones gráficos o bien código-funciona mejor para crear y mantener una interfaz de usuario. En realidad, no hay ninguna solución perfecta y siempre es vale la pena considerar la mejor herramienta para el trabajo en cuestión. Es decir, los archivos .xib son generalmente más eficaces cuando se usa para representar una vista personalizada que se necesita en varios lugares de una aplicación, como una celda de la vista de tabla personalizada. 

Puede encontrar más documentación sobre el uso de archivos de .xib en las recetas siguientes:

- [Mediante la plantilla de .xib de vista](https://developer.xamarin.com/recipes/ios/general/templates/using_the_ios_view_xib_template/)
- [Crear un TableViewCell personalizado mediante un .xib](https://developer.xamarin.com/recipes/ios/content_controls/tables/custom-tableviewcell/)
- [Crear una pantalla de inicio con un .xib](https://developer.xamarin.com/recipes/ios/general/templates/launchscreen-xib/)

Para obtener más información sobre el uso de guiones gráficos, consulte la [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md).

Esta y otras guías relacionadas con el Diseñador de iOS hacen referencia al uso de guiones gráficos como el método estándar para la creación de interfaces de usuario, como la mayoría Xamarin.iOS nuevas plantillas de proyecto proporcionan un guión gráfico de forma predeterminada.

## <a name="summary"></a>Resumen

Esta guía proporciona una introducción al diseñador, que describe sus características y las herramientas que ofrece para diseñar interfaces de usuario atractivas la esquematización de iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [iOS pueden diseñar tutorial de controles](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Información general del diseñador Android](~/android/user-interface/android-designer/index.md)
- [Clases y métodos parciales](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [ENTRAR en el Diseñador de Xamarin para iOS - 2014 evolucionar (vídeo)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [Usar el Diseñador de iOS para crear una pantalla de inicio (vídeo)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
