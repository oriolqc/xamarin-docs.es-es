---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: Frente a WPF Ciclo de vida de aplicaciones de Xamarin.Forms
description: Este documento compara las similitudes y diferencias entre el ciclo de vida de aplicación para aplicaciones de Xamarin.Forms y WPF. Además, observa el árbol visual, gráficos, recursos y estilos.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 5f157f2bbf36076e542a5f96b912cb1788a99052
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269346"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>Frente a WPF Ciclo de vida de aplicaciones de Xamarin.Forms

Xamarin.Forms tiene muchas instrucciones de diseño de los marcos de XAML que se incluían antes, especialmente en WPF. Sin embargo, de otras maneras se desvían mucho que puede ser un punto rápido para intentar migrar a través de las personas. Este documento intenta identificar algunos de esos problemas y proporcionar orientación siempre que sea posible al puente de conocimiento WPF a Xamarin.Forms.

## <a name="app-lifecycle"></a>Ciclo de vida de la aplicación

El ciclo de vida de la aplicación entre WPF y Xamarin.Forms es similar. Ambos iniciar en el código externo (plataforma) e inicie la interfaz de usuario a través de una llamada al método. La diferencia es que Xamarin.Forms siempre se inicia en un ensamblado específico de la plataforma que, a continuación, inicializa y crea la interfaz de usuario de la aplicación.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> El `Main` método es, de forma predeterminada, auto generado y no están visible en el código.

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>Clase de aplicación

WPF y Xamarin.Forms tienen una `Application` clase que se crea como un singleton. En la mayoría de los casos, las aplicaciones se derivarán de esta clase para proporcionar una aplicación personalizada, aunque esto no es estrictamente necesario en WPF. Ambas exponer un `Application.Current` propiedad para buscar el singleton creado.

### <a name="global-properties--persistence"></a>Propiedades globales y persistencia

WPF y Xamarin.Forms tienen una `Application.Properties` diccionario disponible donde puede almacenar objetos de nivel de aplicación globales que son accesibles en cualquier parte de la aplicación. La principal diferencia es que le Xamarin.Forms _conservar_ cualquier tipos primitivos que se almacenan en la colección cuando se suspende la aplicación y volver a cargarlos al que se reinician. WPF no admiten automáticamente ese comportamiento; en su lugar, la mayoría de los desarrolladores dependían de almacenamiento aislado, o utilizan integrado `Settings` admite.

## <a name="defining-pages-and-the-visual-tree"></a>Definición de las páginas y el árbol Visual

WPF usa el `Window` como el elemento raíz de cualquier elemento visual de nivel superior. Esto define un HWND en el mundo de Windows para mostrar información. Puede crear y mostrar todas las ventanas simultáneamente como sea necesario en WPF.

En Xamarin.Forms, el objeto visual de nivel superior siempre se define por la plataforma: por ejemplo, en iOS, es un `UIWindow`. Representaciones de Xamarin.Forms está contenido en estas representaciones de plataforma nativa mediante un `Page` clase. Cada `Page` en Xamarin.Forms representa una única "página" en la aplicación, donde solo uno es visible a la vez.

Ambos WPFs `Window` y Xamarin.Forms `Page` incluyen un `Title` propiedad para influir en el título mostrado y ambos tienen un `Icon` propiedad para mostrar un icono específico de la página (**Nota** que el título e icono no siempre están visibles en Xamarin.Forms). Además, puede cambiar propiedades comunes de visuales tanto como el color de fondo o imagen.

Es técnicamente posible representar a dos vistas independientes de plataforma (por ejemplo, defina dos `UIWindow` objetos y tiene el procesamiento en segundo lugar, una a una pantalla externa o AirPlay), que requiere código específico de plataforma para hacerlo y no es una característica compatible directamente con Xamarin.Forms propio.

### <a name="views"></a>Vistas

La jerarquía visual para ambos marcos es similar. WPF es un poco más debido a su compatibilidad con documentos WYSIWYG.

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

### <a name="view-lifecycle"></a>Ciclo de vida de las vistas

Xamarin.Forms se centra principalmente en escenarios móviles. Por lo tanto, las aplicaciones son _activado_, _suspendido_, y _reactivado_ cuando el usuario interactúa con ellos. Esto es similar a hacer clic fuera de la `Window` en una aplicación WPF y hay un conjunto de métodos y eventos correspondientes, puede invalidar o engancharse para supervisar este comportamiento.

| Finalidad | Método WPF | Método de Xamarin.Forms |
|--- |--- |--- |
|Activación inicial|ctor + Window.OnLoaded|ctor + Page.OnStart|
|Se muestra|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disappearing|
|Suspender/pierde el foco|Window.OnDeactivated|Page.OnSleep|
|Activa o se ha obtenido foco|Window.OnActivated|Page.OnResume|
|Cerrado|Window.OnClosing + Window.OnClosed|N/D|


Ambos soporte ocultar o mostrar también los controles secundarios, en WPF es una propiedad tri-state `IsVisible` (visible, oculto y contraído). En Xamarin.Forms, es sólo visible u oculto a través de la `IsVisible` propiedad.

### <a name="layout"></a>Diseño

Diseño de página se produce en la misma 2-fase (medida y organizar) que se produce en WPF. Puede colocar en el diseño de página invalidando los métodos siguientes en Xamarin.Forms `Page` clase:

| Método | Finalidad |
|--- |--- |
|OnChildMeasureInvalidated|Ha cambiado el tamaño preferido de un elemento secundario.|
|OnSizeAllocated|Página se ha asignado un ancho y alto.|
|Evento LayoutChanged|Diseño y el tamaño de la página ha cambiado.|

No existe ningún evento de diseño global que se denomina hoy en día, así como tampoco hay global `CompositionTarget.Rendering` evento como se encuentra en WPF.

#### <a name="common-layout-properties"></a>Propiedades de diseño comunes

WPF y Xamarin.Forms admiten `Margin` control espaciado alrededor de un elemento, y `Padding` control espaciado _dentro de_ un elemento. Además, la mayoría de las vistas de diseño de Xamarin.Forms tiene propiedades para controlar el espaciado (p. ej., fila o columna).

Además, la mayoría de los elementos tiene propiedades para influir en cómo se coloca en el contenedor primario:

| WPF | Xamarin.Forms | Finalidad |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Opciones de la izquierda/centro o derecha/Stretch|
|VerticalAlignment|VerticalOptions|Opciones de Stretch/Top/centro/inferior|

> [!NOTE]
> La interpretación de estas propiedades real depende del contenedor primario.

#### <a name="layout-views"></a>Vistas de diseño

WPF y Xamarin.Forms usan los controles de diseño para colocar los elementos secundarios. En la mayoría de los casos, estos son muy cercanos entre sí en términos de funcionalidad.

| WPF | Xamarin.Forms | Estilo de diseño |
|--- |--- |--- |
|StackPanel|StackLayout|Apilamiento infinito de izquierda a derecha o arriba a abajo|
|Cuadrícula|Cuadrícula|Formato tabular (filas y columnas)|
|DockPanel|N/D|Acoplar a los bordes de ventana|
|Canvas|AbsoluteLayout|Posición o coordenada de píxel|
|WrapPanel|N/D|Ajuste de pila|
|N/D|RelativeLayout|Ubicación relativa basada en reglas|

> [!NOTE]
> Xamarin.Forms no admite un `GridSplitter`.

Ambas plataformas usan _propiedades adjuntas_ para ajustar los elementos secundarios.

### <a name="rendering"></a>Representación

La mecánica de la representación de WPF y Xamarin.Forms es radicalmente distinto. En WPF, los controles que cree directamente representan el contenido a los píxeles en la pantalla. WPF mantiene dos gráficos de objetos (_árboles_) para representar esto - la _árbol lógico_ representa los controles, tal como se define en el código o XAML y el _árbol visual_ representa el representación real que se produce en la pantalla que realiza directamente mediante el elemento visual (a través de un método virtual draw), o a través de definida en XAML `ControlTemplate` que puede reemplazar o personalizar. Normalmente, el árbol visual es más complejo, ya que incluye cosas como bordes alrededor de los controles, las etiquetas de contenido implícita, etcetera. WPF incluye un conjunto de API (`LogicalTreeHelper` y `VisualTreeHelper`) para examinar estos dos gráficos de objetos.

En Xamarin.Forms, los controles se define en un `Page` son objetos de datos muy simples. Son similares a la representación de árbol lógico, pero nunca representar el contenido por sí solos. En su lugar, son el _modelo de datos_ que influye en la representación de elementos. El procesamiento real se realiza mediante un [separar el conjunto de _representadores visuales_ que se asignan a cada tipo de control](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Estos representadores se registran en cada uno de los proyectos específicos de la plataforma por los ensamblados específicos de la plataforma de Xamarin.Forms. Puede ver una lista [aquí](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md). Además de reemplazar o ampliar el representador, Xamarin.Forms también tiene compatibilidad para [efectos](~/xamarin-forms/app-fundamentals/effects/index.md) que se puede usar para influir en la representación en forma de acuerdo con la plataforma nativa.

#### <a name="the-logicalvisual-tree"></a>El árbol lógico o Visual

No hay ninguna API expuesta para recorrer el árbol lógico en Xamarin.Forms - pero puede usar la reflexión para obtener la misma información. Por ejemplo, [aquí es un método que puede enumerar los elementos secundarios lógicos](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) con reflexión.

## <a name="graphics"></a>Gráficos

Xamarin.Forms no incluye un sistema de gráficos para primitivas más allá de un rectángulo simple (`BoxView`). Puede incluir las bibliotecas de terceros 3rd como [SkiaSharp](~/graphics-games/skiasharp/index.md) para obtener el dibujo en 2D multiplataforma, o [UrhoSharp](~/graphics-games/urhosharp/index.md) para 3D.

## <a name="resources"></a>Recursos

WPF y Xamarin.Forms, ambos tienen el concepto de recursos y los diccionarios de recursos. Puede colocar cualquier tipo de objeto en un `ResourceDictionary` con una clave y, a continuación, búsquelo con `{StaticResource}` para realizar acciones que no cambiará, o `{DynamicResource}` para realizar acciones que pueden cambiar en el diccionario en tiempo de ejecución. El uso y la mecánica es los mismos con una diferencia: Xamarin.Forms requiere que se defina el `ResourceDictionary` para asignar a la `Resources` propiedad mientras que WPF crea previamente uno y lo asigna automáticamente.

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

Estilos también son compatibles en Xamarin.Forms y se pueden usar al tema de los elementos de Xamarin.Forms que componen la interfaz de usuario. Que admiten la herencia de los desencadenadores (propiedad, eventos y datos) a través de `BasedOn`y las búsquedas de recursos para los valores. Los estilos se aplican a los elementos ya sea explícitamente mediante el `Style` propiedad, o implícitamente por sin proporcionar una clave de recurso: al igual que WPF.

### <a name="device-styles"></a>Estilos de dispositivo

WPF tiene un conjunto de propiedades predefinidas (almacenados como valores estáticos en un conjunto de clases estáticas como `SystemColors`) que determinan los colores del sistema, las fuentes y las métricas en forma de valores y claves de recurso. Xamarin.Forms es similar, pero define un conjunto de [estilos de dispositivo](~/xamarin-forms/user-interface/styles/device.md) para representar las mismas cosas. Estos estilos son proporcionados por el marco de trabajo y establecer en valores en función del entorno en tiempo de ejecución (por ejemplo, accesibilidad).

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
