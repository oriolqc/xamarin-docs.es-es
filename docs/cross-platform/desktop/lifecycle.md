---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: Frente a WPF Ciclo de vida de aplicación de Xamarin.Forms
description: Conocer el proceso de inicio de la aplicación y trabajar con los Estados de fondo
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: b4f9aebbbcab48290d37c5732c69267897238272
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>Frente a WPF Ciclo de vida de aplicación de Xamarin.Forms

Xamarin.Forms toma un lote de instrucciones de diseño de los marcos de XAML que se ejecutaron anteriormente, especialmente en WPF. Sin embargo, en otras formas se desvían significativamente que puede ser un punto rápido para intentar migrar a través de personas. Este documento intenta identificar algunos de esos problemas y proporcionar orientación siempre que sea posible al puente de conocimiento WPF a Xamarin.Forms.

## <a name="app-lifecycle"></a>Ciclo de vida de aplicación

El ciclo de vida de la aplicación entre WPF y Xamarin.Forms es similar. Ambos iniciar en el código externo (plataforma) e inicie la interfaz de usuario a través de una llamada al método. La diferencia es que Xamarin.Forms siempre se inicia en un ensamblado específico de la plataforma que, a continuación, inicializa y crea la interfaz de usuario de la aplicación.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> El `Main` método es, de forma predeterminada, auto generado y no es visible en el código.

**Xamarin.Forms**

- **IOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>Clase de aplicación

WPF y Xamarin.Forms no tienen un `Application` clase que se crea como un singleton. En la mayoría de los casos, las aplicaciones se derivarán de esta clase para proporcionar una aplicación personalizada, aunque esto no es estrictamente necesario en WPF. Ambas exponer un `Application.Current` propiedad que se va a buscar el singleton creado.

### <a name="global-properties--persistence"></a>Propiedades globales + persistencia

WPF y Xamarin.Forms no tienen un `Application.Properties` diccionario disponible donde puede almacenar los objetos globales de nivel de aplicación que son accesibles en cualquier parte de la aplicación. La principal diferencia es que le Xamarin.Forms _conservar_ cualquier almacenados en la colección cuando la aplicación se suspende y volverlos a cargar cuando lo está relanzado los tipos primitivos. WPF no admite ese comportamiento automáticamente; en su lugar, la mayoría de los desarrolladores se basa en el almacenamiento aislado, o use integrado `Settings` admite.

## <a name="defining-pages-and-the-visual-tree"></a>Definición de páginas y el árbol Visual

WPF utiliza el `Window` como el elemento raíz de cualquier elemento visual de nivel superior. Esto define un HWND en el mundo de Windows para mostrar información. Puede crear y mostrar todas las ventanas al mismo tiempo como sea necesario en WPF.

De Xamarin.Forms, el objeto visual de nivel superior siempre se define por la plataforma: por ejemplo, en iOS, es un `UIWindow`. Representa Xamarin.Forms está contenido en estas representaciones de plataforma nativa mediante un `Page` clase. Cada `Page` de Xamarin.Forms representa una "página" única en la aplicación, donde sólo uno está visible a la vez.

Ambos WPFs `Window` y Xamarin.Forms `Page` incluyen un `Title` propiedad para influir en el título que se muestra y ambos tienen un `Icon` propiedad para mostrar un icono específico para la página (**Nota** que el título y el icono no están siempre visibles en Xamarin.Forms). Además, puede cambiar propiedades comunes de visuales tanto como el color de fondo o imagen.

Es técnicamente posible representar a dos vistas de plataforma independiente (por ejemplo, definir dos `UIWindow` objetos y tener el procesamiento en segundo lugar, una a una pantalla externa o AirPlay), no necesita un código específico de la plataforma para hacerlo y no es una característica compatible directamente Xamarin.Forms propio.

### <a name="views"></a>Vistas

La jerarquía visual para ambos marcos de trabajo es similar. WPF es un poco más profundo debido a su compatibilidad con documentos WYSIWYG.

**WPF**

```
DependencyObject - base class for all bindable things
   Visual - rendering mechanics
      UIElement - common events + interactions
         FrameworkElement - adds layout
            Shape - 2D graphics
            Control - interactive controls
```

**Xamarin.Forms**

```
BindableObject - base class for all bindable things
   Element - basic parent/child support + resources + effects
      VisualElement - adds visual rendering properties (color, fonts, transforms, etc.)
         View - layout + gesture support
```

### <a name="view-lifcycle"></a>Vista Lifcycle

Xamarin.Forms se orienta principalmente alrededor de los escenarios móviles. Por lo tanto, las aplicaciones son _activado_, _suspendido_, y _reactivar_ cuando el usuario interactúa con ellos. Esto es similar a hacer clic fuera de la `Window` en una aplicación WPF y hay un conjunto de métodos y eventos correspondientes puede invalidar o enlazar al supervisar este comportamiento.

| Propósito | Método WPF | Xamarin.Forms (método) |
|--- |--- |--- |
|Activación inicial|ctor + Window.OnLoaded|ctor + Page.OnStart|
|Se muestra|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disapearing|
|Enfoque de suspender/perdidos|Window.OnDeactivated|Page.OnSleep|
|Activa o se ha denegado foco|Window.OnActivated|Page.OnResume|
|Cerrado|Window.OnClosing + Window.OnClosed|N/D|


Ambos compatibilidad ocultar/mostrar así los controles secundarios, en WPF es una propiedad de tres estados `IsVisible` (visible, oculto y contraído). En Xamarin.Forms, es simplemente visible u oculto a través de la `IsVisible` propiedad.

### <a name="layout"></a>Diseño

Diseño de página se produce en la misma 2-fase (medida/organizar) que se produce en WPF. Puede enlazar en el diseño de página invalidando los métodos siguientes en el Xamarin.Forms `Page` clase:

| Método | Propósito |
|--- |--- |
|OnChildMeasureInvalidated|Ha cambiado el tamaño preferido de un elemento secundario.|
|OnSizeAllocated|Página se ha asignado un valor de ancho/alto.|
|Evento LayoutChanged|Diseño y el tamaño de la página ha cambiado.|

No existe ningún evento de diseño global que se denomina hoy en día, así como tampoco hay global `CompositionTarget.Rendering` evento como se encuentra en WPF.

#### <a name="common-layout-properties"></a>Propiedades comunes de diseño

WPF y Xamarin.Forms admiten `Margin` control espaciado alrededor de un elemento, y `Padding` control espaciado _en_ un elemento. Además, la mayoría de las vistas de diseño de Xamarin.Forms tiene propiedades que permiten controlar el espaciado (p. ej., fila o columna).

Además, la mayoría de los elementos tiene propiedades para influir en cómo se colocan en el contenedor primario:

| WPF | Xamarin.Forms | Propósito |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Opciones de la izquierda/Centro/derecha/Stretch|
|VerticalAlignment|VerticalOptions|Opciones de la parte superior/centro/final/Stretch|

> [!NOTE]
> La interpretación de estas propiedades real depende del contenedor primario.

#### <a name="layout-views"></a>Vistas de diseño

WPF y Xamarin.Forms usa controles de diseño para colocar los elementos secundarios. En la mayoría de los casos, estos son muy cercanos entre sí en términos de funcionalidad.

| WPF | Xamarin.Forms | Estilo de diseño |
|--- |--- |--- |
|StackPanel|StackLayout|Apilamiento infinita de izquierda a derecha y de arriba a abajo|
|Cuadrícula|Cuadrícula|Formato tabular (filas y columnas)|
|DockPanel|N/D|Acoplar en bordes de ventana|
|Canvas|AbsoluteLayout|Posición de píxel/coordenadas|
|WrapPanel|N/D|Ajuste de pila|
|N/D|RelativeLayout|Posición relativa basadas en reglas|

> [!NOTE]
> Xamarin.Forms no admite un `GridSplitter`.

Ambas plataformas usan _propiedades adjuntas_ para ajustar los elementos secundarios.

### <a name="rendering"></a>Representación

Los mecanismos de representación de WPF y Xamarin.Forms son totalmente diferentes. En WPF, los controles que cree directamente representan el contenido con los píxeles en la pantalla. WPF mantiene dos gráficos de objetos (_árboles_) para representar esto - la _árbol lógico_ representa los controles, como se define en código o XAML y el _árbol visual_ representa el representación real que tiene lugar en la pantalla que realiza directamente mediante el elemento visual (a través de un método virtual draw), o a través de un objeto definido en el XAML `ControlTemplate` que puede reemplazar o personalizar. Normalmente, el árbol visual es más complejo ya que esto incluye como bordes alrededor de los controles, las etiquetas de contenido implícita, etcetera. WPF incluye un conjunto de API (`LogicalTreeHelper` y `VisualTreeHelper`) para examinar estos dos gráficos de objetos.

En Xamarin.Forms, los controles se definen en un `Page` son objetos de datos simplemente simple. Son similares a la representación de árbol lógico, pero nunca representa el contenido en sus propios. En su lugar, son el _modelo de datos_ que influye en la representación de elementos. El procesamiento real se realiza mediante un [separar el conjunto de _representadores visuales_ que se asignan a cada tipo de control](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Estos representadores se registran en cada uno de los proyectos específicos de la plataforma mediante ensamblados de Xamarin.Forms específico de la plataforma. Puede ver una lista [aquí](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md). Además de reemplazar o extender el representador, Xamarin.Forms también es compatible con [efectos](~/xamarin-forms/app-fundamentals/effects/index.md) que puede utilizarse para influir en la representación nativa de forma por plataformas.

#### <a name="the-logicalvisual-tree"></a>El árbol lógico/Visual

No hay ninguna API expuesta para recorrer el árbol lógico de Xamarin.Forms - pero puede usar la reflexión para obtener la misma información. Por ejemplo, [éste es un método que puede enumerar los elementos secundarios lógicos](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) mediante la reflexión.

## <a name="graphics"></a>Gráficos

Xamarin.Forms no incluye un sistema de gráficos para elementos primitivos más allá de un simple rectángulo (`BoxView`). Puede incluir como bibliotecas de fabricantes 3rd [SkiaSharp](~/graphics-games/skiasharp/index.md) para obtener soluciones multiplataforma dibujo 2D, o [UrhoSharp](~/graphics-games/urhosharp/index.md) para 3D.

## <a name="resources"></a>Recursos

WPF y Xamarin.Forms tienen el concepto de recursos y los diccionarios de recursos. Puede colocar cualquier tipo de objeto en un `ResourceDictionary` con una clave y, a continuación, búsquelo con `{StaticResource}` para realizar acciones que no va a cambiar, o `{DynamicResource}` para realizar acciones que pueden cambiar en el diccionario en tiempo de ejecución. El uso y los mecanismos son los mismos con una diferencia: Xamarin.Forms requiere que se defina el `ResourceDictionary` para asignar a la `Resources` propiedad mientras que WPF previamente crea uno y lo asigna automáticamente.

Por ejemplo, vea la siguiente definición:

**WPF**

```xaml
<Window.Resources>
   <Color x:Key="redColor">#ff0000</Color>
   ...
</Window.Resources>
```

**Xamarin.Forms**

```xaml
<ContentPage.Resources>
   <ResourceDictionary>
      <Color x:Key="redColor">#ff0000</Color>
      ...
   </ResourceDictionary>
</ContentPage.Resources>
```

Si no se define la `ResourceDictionary`, se genera un error en tiempo de ejecución.

## <a name="styles"></a>Estilos

Estilos también son totalmente compatibles en Xamarin.Forms y se puede usar al tema de los elementos de Xamarin.Forms que constituyen la interfaz de usuario. Admite la herencia de (propiedad, evento y datos), de desencadenadores a través de `BasedOn`y las búsquedas de recursos para los valores. Se aplican los estilos a los elementos ya sea explícitamente mediante el `Style` propiedad o implícitamente por sin proporcionar una clave de recurso - igual que WPF.

### <a name="device-styles"></a>Estilos de dispositivo

WPF tiene un conjunto de propiedades predefinidas (almacenados como valores estáticos en un conjunto de clases estáticas como `SystemColors`) que dictan sistema colores, fuentes y métricas en forma de valores y las claves de recursos. Xamarin.Forms es similar, pero define un conjunto de [estilos de dispositivo](~/xamarin-forms/user-interface/styles/device.md) para representar las mismas tareas. Estos estilos son proporcionados por el frameowrk y establecen en valores según el entorno en tiempo de ejecución (por ejemplo, accesibilidad).

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
