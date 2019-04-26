---
title: Barras de herramientas de Xamarin.Mac
description: En este artículo se describe cómo trabajar con las barras de herramientas en una aplicación de Xamarin.Mac. Abarca las barras de herramientas de creación y el mantenimiento en Xcode e Interface Builder, exponerlos a código y trabajar con ellos mediante programación.
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6cb17ae0f60390564a8aa6bdb64ea612aae51b55
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61295565"
---
# <a name="toolbars-in-xamarinmac"></a>Barras de herramientas de Xamarin.Mac

_En este artículo se describe cómo trabajar con las barras de herramientas en una aplicación de Xamarin.Mac. Abarca las barras de herramientas de creación y el mantenimiento en Xcode e Interface Builder, exponerlos a código y trabajar con ellos mediante programación._

Los desarrolladores de Xamarin.Mac para trabajar con Visual Studio para Mac tienen acceso a los mismos controles de interfaz de usuario disponibles para los desarrolladores de macOS para trabajar con Xcode, incluido el control de barra de herramientas. Ya que Xamarin.Mac se integra directamente con Xcode, es posible usar Interface Builder de Xcode para crear y mantener elementos de la barra de herramientas. También se pueden crear estos elementos de barra de herramientas en C#.

Barras de herramientas de macOS se agregan a la sección superior de una ventana y facilitar el acceso a los comandos relacionados con su funcionalidad. Las barras de herramientas se pueden ocultas, se muestra o personalizadas por usuarios de la aplicación y presentan los elementos de la barra de herramientas de varias maneras.

En este artículo se trata los aspectos básicos de trabajar con las barras de herramientas y elementos de barra de herramientas en una aplicación de Xamarin.Mac. 

Antes de continuar, lea el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo — específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como abarca conceptos básicos y las técnicas que se usará en esta guía.

También echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documento. Explica la `Register` y `Export` atributos que se utilizan para conectarse a las clases de C# a las clases de C de objetivo.

## <a name="introduction-to-toolbars"></a>Introducción a las barras de herramientas

Cualquier ventana en una aplicación de macOS puede incluir una barra de herramientas:

![Una ventana de ejemplo con una barra de herramientas](toolbar-images/info01.png "una ventana de ejemplo con una barra de herramientas")

Las barras de herramientas proporcionan una manera fácil para los usuarios de la aplicación para acceder rápidamente a importantes o las características comúnmente usadas. Por ejemplo, una aplicación de edición de documentos puede proporcionar elementos de la barra de herramientas para establecer el color del texto, cambiar la fuente o imprimir el documento actual.

Las barras de herramientas pueden mostrar los elementos de tres maneras:

1. **Icono y texto** 

     ![Una barra de herramientas con iconos y texto](toolbar-images/info02.png "una barra de herramientas con iconos y texto")

2. **Icon Only** 

     ![Una barra de herramientas solo icono](toolbar-images/info03.png "una barra de herramientas solo icono")

3. **Solo texto** 

     ![Una barra de herramientas de sólo texto](toolbar-images/info04.png "una barra de herramientas de sólo texto")

Cambiar entre estos modos haciendo clic en la barra de herramientas y seleccione un modo de presentación en el menú contextual:

![El menú contextual de una barra de herramientas](toolbar-images/info05.png "el menú contextual de una barra de herramientas")

Usar el mismo menú para mostrar la barra de herramientas con un tamaño menor:

![Una barra de herramientas con iconos pequeños](toolbar-images/info06.png "una barra de herramientas con iconos pequeños")

El menú también le permite personalizar la barra de herramientas:

![El cuadro de diálogo usado para personalizar una barra de herramientas](toolbar-images/info07.png "el cuadro de diálogo usado para personalizar una barra de herramientas")

Al configurar una barra de herramientas en Interface Builder de Xcode, un desarrollador puede proporcionar elementos de barra de herramientas adicionales que no forman parte de su configuración predeterminada. Los usuarios de la aplicación, a continuación, pueden personalizar la barra de herramientas, agregar y quitar estos elementos predefinidos según sea necesario. Por supuesto, se puede restablecer la barra de herramientas a su configuración predeterminada.

La barra de herramientas se conecta automáticamente a la **vista** menú, que permite a los usuarios ocultarlo, mostrarlo y personalizarla:

![Elementos relacionados con la barra de herramientas en el menú Ver](toolbar-images/info08.png "elementos relacionados con la barra de herramientas en el menú Ver")

Consulte la [funcionalidad integrada de menú](~/mac/user-interface/menu.md) documentación para obtener más detalles.

Además, si la barra de herramientas está configurado correctamente en el generador de interfaz, la aplicación conservará automáticamente las personalizaciones de la barra de herramientas a través de varios lanzamientos de la aplicación.

Las secciones siguientes de esta guía describen cómo crear y mantener las barras de herramientas con Interface Builder de Xcode y cómo trabajar con ellos en el código.

## <a name="setting-a-custom-main-window-controller"></a>Configuración de un controlador de ventana principal personalizado

Para exponer elementos de interfaz de usuario al código de C# a través de las salidas y acciones, la aplicación de Xamarin.Mac, debe usar un controlador de ventana personalizados:

1. Abra el guión gráfico de la aplicación en Interface Builder de Xcode.
2. Seleccione el controlador de ventana en la superficie de diseño.
3. Cambie a la **Inspector de identidad** y escriba "WindowController" como el **nombre de la clase**: 

    [![Configuración de un nombre de clase personalizada para el controlador de ventana](toolbar-images/windowcontroller01.png "establecer un nombre de clase personalizada para el controlador de ventana")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar.
5. Un **WindowController.cs** archivo se agregará al proyecto en el **panel de solución** en Visual Studio para Mac: 

    ![Seleccionar WindowController.cs en el panel de solución](toolbar-images/windowcontroller02.png "seleccionar WindowController.cs en el panel de solución")

6. Vuelva a abrir el guión gráfico de Interface Builder de Xcode.
7. El **WindowController.h** archivo estará disponible para su uso: 

    [![El archivo WindowController.h](toolbar-images/windowcontroller03.png "WindowController.h el archivo")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>Crear y mantener las barras de herramientas de Xcode

Las barras de herramientas se crean y mantienen con Interface Builder de Xcode. Para agregar una barra de herramientas a una aplicación, edite el guión gráfico principal de la aplicación (en este caso **Main.storyboard**) haga doble clic en el **panel de solución**:

![Abrir Main.storyboard en el panel de solución](toolbar-images/edit01.png "abrir Main.storyboard en el panel de solución")

En el **Inspector de biblioteca**, escriba "herramientas" en el **cuadro de búsqueda** que resulte más fácil de ver todos los elementos de la barra de herramientas disponibles:

![El Inspector de biblioteca, se filtra para mostrar los elementos de la barra de herramientas](toolbar-images/edit02.png "el Inspector de biblioteca, filtran para mostrar los elementos de la barra de herramientas")

Arrastre una barra de herramientas a la ventana en la **Editor de la interfaz**. Con la barra de herramientas seleccionado, configure su comportamiento estableciendo las propiedades el **Inspector de atributos**:

![El Inspector de atributos de una barra de herramientas](toolbar-images/edit04.png "el Inspector de atributos para una barra de herramientas")

Están disponibles las propiedades siguientes:

1. **Mostrar** : controla si la barra de herramientas muestra los iconos, texto o ambos
2. **Visible en el lanzamiento** -si se selecciona, la barra de herramientas está visible de forma predeterminada.
3. **Puede personalizar** -si se selecciona, los usuarios pueden editar y personalizar la barra de herramientas.
4. **Separador** -si se selecciona, una línea horizontal delgada separa la barra de herramientas de contenido de la ventana.
5. **Tamaño** -establece el tamaño de la barra de herramientas
6. **Autoguardar** -si se selecciona, la aplicación conservará los cambios de configuración de barra de herramientas de un usuario a través de inicios de la aplicación.

Seleccione el **Autosave** opción y dejar todas las demás propiedades en sus valores predeterminados. 

Después de abrir la barra de herramientas en el **jerarquía de la interfaz**, aparezca el cuadro de diálogo de personalización seleccionando un elemento de barra de herramientas:

![Personalizar la barra de herramientas](toolbar-images/edit05.png "personalizando la barra de herramientas")

Utilice este cuadro de diálogo para establecer las propiedades de elementos que ya forman parte de la barra de herramientas al diseño de la barra de herramientas predeterminado para la aplicación, y para proporcionar elementos de la barra de herramientas adicionales para un usuario seleccionar la hora de personalizar la barra de herramientas. Para agregar elementos a la barra de herramientas, arrástrelos desde el **Inspector de biblioteca**:

![El Inspector de biblioteca](toolbar-images/edit06.png "el Inspector de biblioteca")

Se pueden agregar los siguientes elementos de la barra de herramientas:

- **Elemento de barra de herramientas de la imagen** -un elemento de barra de herramientas con una imagen personalizada como un icono.
- **Elemento de barra de herramientas flexible de espacio** -Flexible espacio utilizado para justificar los elementos de la barra de herramientas posteriores. Por ejemplo, un elemento de barra de herramientas flexible espacio seguido de uno o varios elementos de la barra de herramientas y otro elemento de barra de herramientas anclaría el último elemento en el lado derecho de la barra de herramientas.
- **Elemento de barra de herramientas del espacio** -se ha corregido el espacio entre los elementos en la barra de herramientas
- **Elemento de barra de herramientas de separador** -un separador entre dos o más elementos de barra de herramientas, para la agrupación visible
- **Personalizar la barra de herramientas del elemento** -permite a los usuarios personalizar la barra de herramientas
- **Imprimir un elemento de barra de herramientas** -permite a los usuarios imprimir el documento abierto
- **Mostrar el elemento de barra de herramientas de colores** -muestra el selector de colores estándar del sistema: 

     ![El selector de colores del sistema](toolbar-images/edit07.png "el selector de colores del sistema")

- **Mostrar el elemento de barra de herramientas de fuentes** -muestra el cuadro de diálogo de fuentes estándar del sistema: 

     ![El selector de fuente](toolbar-images/edit08.png "el selector de fuente")

> [!IMPORTANT]
> Como se verá más adelante, muchos controles de interfaz de usuario de cacao estándar como campos de búsqueda, controles segmentados y controles deslizantes horizontales también pueden agregarse a una barra de herramientas.

### <a name="adding-an-item-to-a-toolbar"></a>Agregar un elemento a una barra de herramientas

Para agregar un elemento a una barra de herramientas, seleccione la barra de herramientas en el **jerarquía de la interfaz** y haga clic en uno de sus elementos, el cuadro de diálogo de personalización que aparezca la causa. A continuación, arrastre un nuevo elemento de la **Inspector de biblioteca** a la **elementos de barra de herramientas permite** área:

![Los elementos de barra de herramientas permite en el cuadro de diálogo de personalización de barra de herramientas](toolbar-images/add01.png "los elementos de barra de herramientas permite en el cuadro de diálogo de personalización de barra de herramientas")

Para asegurarse de que un nuevo elemento forma parte de la barra de herramientas de forma predeterminada, arrástrelo hasta el **elementos de barra de herramientas predeterminada** área: 

![Reordenación de un elemento de barra de herramientas arrastrando](toolbar-images/add02.png "reordenación de un elemento de barra de herramientas arrastrando")

Para reordenar los elementos de barra de herramientas de forma predeterminada, arrástrelos izquierdo o derecho.

A continuación, use el **Inspector de atributos** para establecer las propiedades predeterminadas para el elemento:

![Personalización de un elemento de barra de herramientas con el Inspector de atributos](toolbar-images/add03.png "personalizar un elemento de barra de herramientas con el Inspector de atributos")

Están disponibles las propiedades siguientes:

- **Nombre de la imagen** -imagen que se usará como icono para el elemento
- **Etiqueta** : texto que se va a mostrar para el elemento en la barra de herramientas
- **Etiqueta de paleta** -texto que se va a mostrar para el elemento en el **elementos de barra de herramientas permite** área
- **Etiqueta** : un identificador único opcional que ayuda a identificar el elemento en el código.
- **Identificador** -define el tipo de elemento de barra de herramientas. Un valor personalizado se puede usar para seleccionar un elemento de barra de herramientas en el código.
- **Puede seleccionar** -si está activada, el elemento actuará como un botón de encendido y apagado.

> [!IMPORTANT]
> Agregar un elemento a la **elementos de barra de herramientas permite** área, pero no la barra de herramientas predeterminada para proporcionar opciones de personalización de los usuarios. 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>Agregar otros controles de interfaz de usuario a una barra de herramientas

Buscar en varios elementos de interfaz de usuario de cacao como campos y controles segmentados también pueden agregarse a una barra de herramientas.

Para probar esto, abra la barra de herramientas en el **jerarquía de la interfaz** y seleccione un elemento de barra de herramientas para abrir el cuadro de diálogo de personalización. Arrastre un **campo de búsqueda** desde el **Inspector de biblioteca** a la **elementos de barra de herramientas permite** área:

![Mediante el cuadro de diálogo de personalización de barra de herramientas](toolbar-images/add05.png "mediante el cuadro de diálogo de personalización de barra de herramientas")

Desde aquí, usar Interface Builder para configurar el campo de búsqueda y exponerlo a código a través de una acción o toma de corriente.

## <a name="built-in-toolbar-item-support"></a>Compatibilidad para elementos de barra de herramientas integrada

Varios elementos de interfaz de usuario de cacao interactúan con los elementos de barra de herramientas estándar de forma predeterminada. Por ejemplo, arrastre un **vista de texto** en la ventana de aplicación y colóquela para rellenar el área de contenido:

[![Agregar una vista de texto a la aplicación](toolbar-images/edit09.png "agregar una vista de texto a la aplicación")](toolbar-images/edit09-large.png#lightbox)

Guardar el documento, vuelva a Visual Studio para Mac sincronizar con Xcode, ejecute la aplicación, escriba algún texto, selecciónelo y haga clic en el **colores** elemento de barra de herramientas. Tenga en cuenta que la vista de texto funciona automáticamente con el selector de colores:

![Funcionalidad de la barra de herramientas integrada con un selector de vista y el color de texto](toolbar-images/edit10.png "funcionalidad de la barra de herramientas integrada con un selector de vista y el color del texto")

## <a name="using-images-with-toolbar-items"></a>Uso de imágenes con elementos de la barra de herramientas

Mediante un **elemento de barra de herramientas de imagen**, cualquier imagen de mapa de bits que se agrega a la **recursos** carpeta (y tiene una acción de compilación **agrupación de recursos**) se pueden mostrar en la barra de herramientas como un icono:

1. En Visual Studio para Mac, en el **panel de solución**, haga clic en el **recursos** carpeta y seleccione **agregar** > **agregar archivos** .
2. Desde el **agregar archivos** diálogo cuadro, vaya a las imágenes que desee, selecciónelos y haga clic en el **abierto** botón: 

    [![Selección de imágenes para agregar](toolbar-images/edit11.png "selección de imágenes para agregar")](toolbar-images/edit11-large.png#lightbox)

3. Seleccione **copia**, comprobar **usar la misma acción para todos los archivos seleccionados**y haga clic en **Aceptar**:

    ![Seleccione la acción de copia para las imágenes se ha agregado](toolbar-images/edit12.png "seleccionando la acción de copia de las imágenes agregadas.")

4. En el **panel de solución**, haga doble clic en **MainWindow.xib** para abrirlo en Xcode.

5. Seleccione la barra de herramientas en el **jerarquía de la interfaz** y haga clic en uno de sus elementos para abrir el cuadro de diálogo de personalización.

6. Arrastre un **elemento de barra de herramientas de imagen** desde el **Inspector de biblioteca** a la barra de herramientas **elementos de barra de herramientas permite** área: 

    ![Agrega un elemento de barra de herramientas de imagen al área de elementos de barra de herramientas permite](toolbar-images/edit14.png "un elemento de barra de herramientas de imagen se agregan al área de elementos permitidos de barra de herramientas")

7. En el **Inspector de atributos**, seleccione la imagen que se acaba de agregar en Visual Studio para Mac: 

    ![Configuración de una imagen personalizada para un elemento de barra de herramientas](toolbar-images/edit15.png "establecer una imagen personalizada para un elemento de barra de herramientas")

8. Establecer el **etiqueta** a "La Papelera" y el **paleta etiqueta** para "Borrar documento": 

    ![Configuración de la barra de herramientas de elemento etiqueta y la etiqueta de la paleta](toolbar-images/edit16.png "etiqueta y la etiqueta de la paleta de elemento de configuración de la barra de herramientas")

9. Arrastre un **separador de barra de herramientas del elemento** desde el **Inspector de biblioteca** a la barra de herramientas **elementos de barra de herramientas permite** área: 

    [![Un elemento de barra de herramientas de separador que se agregan al área de elementos de barra de herramientas permite](toolbar-images/edit17.png "un separador de barra de herramientas del elemento agregado al área de elementos permitidos de barra de herramientas")](toolbar-images/edit17-large.png#lightbox)

10. Arrastre el elemento separador y el elemento "Papelera" a la **elementos de barra de herramientas predeterminado** área y establezca el orden de la barra de herramientas elementos de izquierda a derecha como sigue (colores, fuentes, separador, la Papelera, espacio Flexible, Print): 

    ![Los elementos de la barra de herramientas predeterminada](toolbar-images/edit18.png "los elementos de la barra de herramientas predeterminado")

11. Guardar los cambios y volver a Visual Studio para Mac sincronizar con Xcode.

Ejecute la aplicación para comprobar que la nueva barra de herramientas se muestra de forma predeterminada:

![Una barra de herramientas con los elementos predeterminados personalizados](toolbar-images/edit19.png "una barra de herramientas con los elementos predeterminados personalizados")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>Exponer los elementos de la barra de herramientas con las salidas y acciones

Para obtener acceso a una barra de herramientas o un elemento de barra de herramientas en el código, debe asociarse a un enchufe o una acción:

1. En el **panel de solución**, haga doble clic en **Main.storyboard** para abrirlo en Xcode.
2. Asegúrese de que la clase personalizada "WindowController" se ha asignado al controlador de ventana principal de la **Inspector de identidad**:

    [![Usar el Inspector de identidad para establecer una clase personalizada para el controlador de ventana](toolbar-images/edit20a.png "utilizando el Inspector de identidad para establecer una clase personalizada para el controlador de ventana")](toolbar-images/edit20a-large.png#lightbox)

3. A continuación, seleccione el elemento de barra de herramientas en el **jerarquía de la interfaz**: 

    ![Seleccionar el elemento de barra de herramientas en la jerarquía de interfaz](toolbar-images/edit20.png "seleccionando el elemento de barra de herramientas en la jerarquía de interfaz")  

4. Abra el **Ayudante para la vista**, seleccione el **WindowController.h** archivo y la tecla control mientras arrastra desde el elemento de barra de herramientas para el **WindowController.h** archivo.
5. Establecer el **conexión** escriba a **acción**, escriba "trashDocument" para el **nombre**y haga clic en el **Connect** botón: 

    [![Configurar una acción para un elemento de barra de herramientas](toolbar-images/edit23.png "configurar una acción para un elemento de barra de herramientas")](toolbar-images/edit23-large.png#lightbox)

6. Exponer el **vista de texto** como una salida denominada "documentEditor" en el **ViewController.h** archivo: 

    [![Configurar una salida para la vista de texto](toolbar-images/edit24.png "configurar una salida para la vista de texto")](toolbar-images/edit24-large.png#lightbox)

7. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

En Visual Studio para Mac, edite el **ViewController.cs** archivo y agregue el código siguiente:

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

A continuación, edite el **WindowController.cs** archivo y agregue el código siguiente a la parte inferior de la `WindowController` clase:

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

Al ejecutar la aplicación, el **Papelera** estará activo el elemento de barra de herramientas:

![Una barra de herramientas con un elemento de la Papelera active](toolbar-images/edit25.png "una barra de herramientas con un elemento de la Papelera activo")

Tenga en cuenta que el **Papelera** elemento de barra de herramientas puede usarse ahora para eliminar texto.

## <a name="disabling-toolbar-items"></a>Deshabilitar los elementos de la barra de herramientas

Para deshabilitar un elemento en una barra de herramientas, crear una personalizada `NSToolbarItem` clase e invalidar el `Validate` método. A continuación, en el generador de interfaz, asignar al tipo personalizado para el elemento que desea habilitar o deshabilitar.

Para crear un personalizado `NSToolbarItem` clase, haga doble clic en el proyecto y seleccione **agregar** > **nuevo archivo...** . Seleccione **General** > **clase vacía**, escriba "ActivatableItem" para el **nombre**y haga clic en el **New** botón: 

![Adición de una clase vacía en Visual Studio para Mac](toolbar-images/custom01.png "adición de una clase vacía en Visual Studio para Mac")

A continuación, edite el **ActivatableItem.cs** archivo para que quede como sigue:

```csharp
using System;

using Foundation;
using AppKit;

namespace MacToolbar
{
    [Register("ActivatableItem")]
    public class ActivatableItem : NSToolbarItem
    {
        public bool Active { get; set;} = true;

        public ActivatableItem ()
        {
        }

        public ActivatableItem (IntPtr handle) : base (handle)
        {
        }

        public ActivatableItem (NSObjectFlag  t) : base (t)
        {
        }

        public ActivatableItem (string title) : base (title)
        {
        }

        public override void Validate ()
        {
            base.Validate ();
            Enabled = Active;
        }
    }
}
```

Haga doble clic en **Main.storyboard** para abrirlo en Xcode. Seleccione el **Papelera** elemento de barra de herramientas creada anteriormente y cambie su clase "ActivatableItem" en el **Inspector de identidad**:

![Configuración de una clase personalizada para un elemento de barra de herramientas](toolbar-images/custom02.png "configuración una clase personalizada para un elemento de barra de herramientas")

Crear una salida denominada `trashItem` para el **Papelera** elemento de barra de herramientas. Guardar los cambios y volver a Visual Studio para Mac sincronizar con Xcode. Por último, abra **MainWindow.cs** y actualizar la `AwakeFromNib` método para que quede como sigue:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

Ejecute la aplicación y tenga en cuenta que el **Papelera** elemento ahora está deshabilitado en la barra de herramientas:

![Una barra de herramientas con un elemento de la Papelera inactiva](toolbar-images/custom03.png "una barra de herramientas con un elemento de la Papelera inactiva")

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con las barras de herramientas y elementos de barra de herramientas en una aplicación de Xamarin.Mac. Describe cómo crear y mantener las barras de herramientas en Interface Builder de Xcode, cómo funcionan automáticamente algunos controles de interfaz de usuario con los elementos de la barra de herramientas, cómo trabajar con las barras de herramientas en código C# y cómo habilitar y deshabilitar los elementos de barra de herramientas.


## <a name="related-links"></a>Vínculos relacionados

- [MacToolbar (ejemplo)](https://developer.xamarin.com/samples/mac/MacToolbar/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Directrices de interfaz humana de las barras de herramientas](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [Introducción a las barras de herramientas](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
