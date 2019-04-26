---
title: Guiones gráficos unificados en Xamarin.iOS
description: Este documento describen los guiones gráficos unificados en Xamarin.iOS. Guiones gráficos unificados permiten a los desarrolladores admitir varios tamaños de pantalla con una definición de interfaz único.
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 26aeaa3d230a5c104014edd899b8d9231ced31e9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61430506"
---
# <a name="unified-storyboards-in-xamarinios"></a>Guiones gráficos unificados en Xamarin.iOS

iOS 8 incluye un mecanismo nuevo y más fácil de usar para crear la interfaz de usuario, el guión gráfico unificado. Con un solo guión gráfico para cubrir todos los tamaños de pantalla de hardware diferente, se pueden crear vistas rápidas y con capacidad de respuesta en un "diseño-una vez, use varios" estilo.

Como el desarrollador ya no necesita crear un guion gráfico independiente y específico para los dispositivos iPhone y iPad, tienen la flexibilidad para diseñar aplicaciones con una interfaz común y, a continuación, personalizar esa interfaz para las clases de tamaño diferentes. De este modo, se puede adaptar una aplicación a los puntos fuertes de cada factor de forma y cada interfaz de usuario se puede optimizar para ofrecer la mejor experiencia.

<a name="size-classes" />

## <a name="size-classes"></a>Clases de tamaño

Antes de iOS 8, usado por el programador `UIInterfaceOrientation` y `UIInterfaceIdiom` para diferenciar entre los modos vertical y horizontal y entre los dispositivos iPhone y iPad. En iOS8, orientación y el dispositivo se determina mediante *las clases de tamaño*.

Los dispositivos se definen mediante las clases de tamaño, en los ejes horizontales y verticales, y hay dos tipos de clases de tamaño en iOS 8:

-  **Regular** : se trata de un tamaño de pantalla de gran tamaño (por ejemplo, un iPad) o un gadget que da la impresión de un tamaño grande (como un `UIScrollView`
-  **Compact** : se trata de los dispositivos más pequeños (por ejemplo, un iPhone). Este tamaño tiene en cuenta la orientación del dispositivo.


Si los dos conceptos se usan juntos, el resultado es una cuadrícula de 2 x 2 que define los posibles tamaños diferentes que se pueden usar en las orientaciones diferentes, tal como se muestra en el diagrama siguiente:

 [![](unified-storyboards-images/sizeclassgrid.png "Una cuadrícula de 2 x 2 que define los posibles tamaños diferentes que pueden usarse en las orientaciones normal y compacto")](unified-storyboards-images/sizeclassgrid.png#lightbox)

El desarrollador puede crear un controlador de vista que usa cualquiera de las cuatro posibilidades que daría lugar a diseños diferentes (como se muestra en la ilustración anterior).

### <a name="ipad-size-classes"></a>las clases de tamaño de iPad

El iPad, debido al tamaño, tiene un **regular** tamaño para ambas orientaciones de la clase.

 [![](unified-storyboards-images/image1.png "las clases de tamaño de iPad")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>las clases de tamaño de iPhone

El iPhone tiene clases de tamaño diferente según la orientación del dispositivo:

 [![](unified-storyboards-images/iphonesizeclasses.png "las clases de tamaño de iPhone")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  Cuando el dispositivo está en modo vertical, la pantalla tiene un **compact** clase horizontalmente y **regular** verticalmente
-  Cuando el dispositivo está en modo horizontal, se invierten las clases de pantalla de modo vertical.

### <a name="iphone-6-plus-size-classes"></a>Clases de tamaño de iPhone 6 Plus

Los tamaños son el mismo que el anterior iPhone en orientación vertical, pero es diferente en horizontal:

[![](unified-storyboards-images/iphone6sizeclasses.png "Clases de tamaño de iPhone 6 Plus")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Dado que el iPhone 6 Plus tiene una pantalla lo suficientemente grande, es capaz de tener una clase de tamaño ancho normal en el modo horizontal.

### <a name="support-for-a-new-screen-scale"></a>Compatibilidad con una nueva escala de la pantalla

El iPhone 6 Plus usa una nueva pantalla Retina HD con un factor de escala de la pantalla de 3.0 (tres veces el original iPhone resolución de pantalla). Para proporcionar la mejor experiencia posible en estos dispositivos, incluir ilustración nueva diseñada para esta escala de la pantalla. En Xcode 6 y posteriores, asset catálogos pueden incluir imágenes en 1 x, x 2 y 3 tamaños x; basta con agregar el nuevo activos de imagen y el iOS elegirá los recursos correctos cuando se ejecuta en un iPhone 6 Plus.

La imagen que se carga el comportamiento de iOS también reconoce un `@3x` sufijo en los archivos de imagen. Por ejemplo, si el desarrollador incluye un recurso de imagen (con distintas resoluciones) en el paquete de la aplicación con los siguientes nombres de archivo: `MonkeyIcon.png`, `MonkeyIcon@2x.png`, y `MonkeyIcon@3x.png`. En el iPhone 6 Plus la `MonkeyIcon@3x.png` imagen se usará automáticamente si el desarrollador carga una imagen mediante el código siguiente:

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
O si asigna la imagen a un elemento de interfaz de usuario con iOS diseñador como `MonkeyIcon.png`, el `MonkeyIcon@3x.png` se usará, nuevo de forma automática, en el iPhone 6 Plus.

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>Pantallas de inicio dinámica

El archivo de la pantalla de inicio se muestra como pantalla de presentación mientras se está iniciando una aplicación de iOS para proporcionar comentarios al usuario que la aplicación es realmente puesta en marcha. Antes de iOS 8, el desarrollador tendría que incluir varios `Default.png` activos para cada dispositivo tipo, orientación y resolución de pantalla que se estarían ejecutando la aplicación en la imagen.

Nuevo IOS 8, el desarrollador puede crear un único, de forma atómico `.xib` archivo en Xcode que usa el diseño automático y las clases de tamaño para crear un *dinámica de pantalla de inicio* que funcionará para cada dispositivo, la resolución y la orientación. Esto no sólo reduce la cantidad de trabajo necesaria del desarrollador para crear y mantener todos los activos de imagen necesarios, pero reduce el tamaño de lote de la aplicación instalada.

## <a name="traits"></a>Rasgos

Rasgos son propiedades que pueden usarse para determinar cómo se cambia un diseño como los cambios en su entorno. Están formados por un conjunto de propiedades (el `HorizontalSizeClass` y `VerticalSizeClass` según `UIUserInterfaceSizeClass`), así como la expresión de la interfaz ( `UIUserInterfaceIdiom`) y la escala de visualización.

Todos los estados anteriores se envuelve en un contenedor que Apple hace referencia como una colección de rasgo ( `UITraitCollection`), que contiene no solo las propiedades sino también sus valores.

## <a name="trait-environment"></a>Entorno de rasgo

Rasgo entornos son una nueva interfaz en iOS 8 y pueden devolver una colección de rasgos para los objetos siguientes:

-  Las pantallas ( `UIScreens` ).
-  Windows ( `UIWindows` ).
-  Ver los controladores ( `UIViewController` ).
-  Vistas ( `UIView` ).
-  Controlador de presentación ( `UIPresentationController` ).


El desarrollador utiliza la colección de rasgo devuelta por un entorno de rasgo para determinar cómo se debe disponer una interfaz de usuario.

Todos los entornos de rasgo de realizar una jerarquía, tal como se muestra en el diagrama siguiente:

 [![](unified-storyboards-images/viewhierarchy.png "El diagrama de jerarquía de entornos de rasgo")](unified-storyboards-images/viewhierarchy.png#lightbox)

La colección de rasgo que tienen cada uno de los entornos de rasgo anterior fluirá de forma predeterminada, desde el elemento primario en el entorno secundario.

Además de obtener la colección de rasgo actual, el entorno de rasgo tiene un `TraitCollectionDidChange` método, que se puede invalidar en las subclases de la vista o controlador de vista. El programador puede utilizar este método para modificar cualquiera de los elementos de interfaz de usuario que dependen de rasgos cuando han cambiado los rasgos.

## <a name="typical-trait-collections"></a>Colecciones de rasgo típico

Esta sección explica los tipos típicos de las colecciones de rasgo que experimentará el usuario al trabajar con iOS 8.

El siguiente es una colección de rasgo habitual que los desarrolladores pueden ver en un iPhone:

|Propiedad|Valor|
|--- |--- |
|`HorizontalSizeClass`|Compacto|
|`VerticalSizeClass`|Estándar|
|`UserInterfaceIdom`|Teléfono|
|`DisplayScale`|2.0|

El conjunto anterior representaría una colección de rasgo totalmente calificados, puesto que tiene los valores de todas sus propiedades de rasgo.

También es posible tener una colección de rasgo que carece de algunas de sus valores (lo que Apple denomina *Unspecified*):

|Propiedad|Valor|
|--- |--- |
|`HorizontalSizeClass`|Compacto|
|`VerticalSizeClass`|Sin especificar|
|`UserInterfaceIdom`|Sin especificar|
|`DisplayScale`|Sin especificar|

Por lo general, sin embargo, cuando el programador solicite el entorno de rasgo de su colección de rasgo, devolverá una colección completa tal como se muestra en el ejemplo anterior.

Si no es un entorno de rasgo (por ejemplo, una vista o un controlador de vista) dentro de la jerarquía de vista actual, el desarrollador podría volver sin especificar valores para una o varias de las propiedades de rasgo.

El desarrollador también puede obtener una colección de rasgo parcialmente completo si usan uno de los métodos de creación proporcionados por Apple, como `UITraitCollection.FromHorizontalSizeClass`, para crear una nueva colección.

Una operación que se puede realizar en varias colecciones de rasgo es comparándolos entre sí, lo que implica que pide una colección de rasgo si contiene otro. ¿Qué se entiende por *contención* es que, para cualquier rasgo especificado en la segunda colección, el valor debe coincidir exactamente con el valor de la primera colección.

Para probar dos rasgos utilizar el `Contains` método de la `UITraitCollection` pasando el valor del rasgo que va a probarse.

El desarrollador puede realizar las comparaciones manualmente en el código para determinar cómo las vistas de diseño o los controladores de vista. Sin embargo, `UIKit` utiliza internamente este método para proporcionar parte de su funcionalidad, como se muestra en el Proxy de apariencia, por ejemplo.

## <a name="appearance-proxy"></a>Proxy de apariencia

El Proxy de apariencia se introdujo en versiones anteriores de iOS para permitir a los desarrolladores personalizar las propiedades de sus vistas. Se amplió en iOS 8 para admitir colecciones de rasgo.

Servidores proxy de apariencia incluyen ahora un nuevo método `AppearanceForTraitCollection`, que devuelve un nuevo Proxy de la apariencia de la colección especificada de rasgo que se ha pasado. Las personalizaciones que lleva a cabo en el que el desarrollador apariencia Proxy solo tendrá efecto en las vistas que se ajustan a la colección especificada de rasgo.

Normalmente pasará el desarrollador de una colección de rasgo parcialmente especificado en el `AppearanceForTraitCollection` método, como el que acaba de especificar un Horizontal tamaño clase de compactar, para que puede personalizar cualquier vista de la aplicación que sea conciso horizontalmente.

## <a name="uiimage"></a>UIImage

Otra clase que Apple ha agregado la colección de rasgo para es `UIImage`. En el pasado, el desarrollador tenía que especificar un @1X y @2x versión de cualquier recurso de gráficos de mapa de bits que se van a incluir en la aplicación (por ejemplo, un icono).

iOS 8 se ha ampliado para permitir que el desarrollador debe incluir varias versiones de una imagen en un catálogo de imagen basados en una colección de rasgo. Por ejemplo, el desarrollador podría incluir una imagen más pequeña para trabajar con una clase de rasgo Compact y una imagen en tamaño completo para cualquier otra colección.

Cuando se utiliza una de las imágenes dentro de un `UIImageView` (clase), la vista de la imagen mostrará automáticamente la versión correcta de la imagen para su colección de rasgo. Si cambia el entorno de rasgo (por ejemplo, el usuario cambiar el dispositivo de la posición vertical a horizontal), la vista de imagen seleccionará automáticamente el nuevo tamaño de imagen que coincida con la nueva colección de rasgo y cambiar su tamaño para que coincida con la versión actual de la imagen que se va a muestra.

## <a name="uiimageasset"></a>UIImageAsset

Apple ha agregado una nueva clase a iOS 8 denominada `UIImageAsset` para dar al programador tener un mayor control sobre la selección de imagen.

Un recurso de imagen resume todas las versiones diferentes de una imagen y permite al desarrollador pedir una imagen específica que coincida con una colección de rasgo que se ha pasado. Las imágenes se pueden agregar o quitar de un recurso de imagen, sobre la marcha.

Para obtener más información sobre activos de imagen, consulte Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) documentación.

## <a name="combining-trait-collections"></a>Combinar colecciones de rasgo

Otra función que un desarrollador puede realizar en las colecciones de rasgo es sumar dos que tendrá como resultado de la colección combinada, donde los valores de una colección no especificados se reemplazan por los valores especificados en un segundo. Esto se realiza mediante el `FromTraitsFromCollections` método de la `UITraitCollection` clase.

Como se indicó anteriormente, si cualquiera de las cualidades no está especificado en una de las colecciones de rasgo y se especifica en el otro, el valor se establecerá en la versión especificada. Sin embargo, si hay varias versiones de un determinado valor especificado, el valor de la última colección de rasgo será el valor que se usa.

## <a name="adaptive-view-controllers"></a>Controladores de vista adaptable

En esta sección se tratará los detalles de cómo la vista y los controladores de vista de iOS han adoptado los conceptos de rasgos y las clases de tamaño automáticamente se adapten mejor en las aplicaciones del desarrollador.

### <a name="split-view-controller"></a>Controlador de vista dividida

Una de las clases de controlador de vista que ha cambiado más en iOS 8 es la `UISplitViewController` clase. En el pasado, el desarrollador usa a menudo un controlador de vista dividida en la versión de iPad de la aplicación y, a continuación, tendría que proporcionar una versión completamente diferente de su jerarquía de vistas de la versión de iPhone de la aplicación.

En iOS 8, el `UISplitViewController` clase está disponible en ambas plataformas (iPad y iPhone), que permite al desarrollador crear una jerarquía de controlador de vista que funcionará para iPhone e iPad.

Cuando se encuentra un iPhone en horizontal, el controlador de vista dividida presentará su vistas side-by-side, tal como lo haría cuando se muestra en un iPad.

### <a name="overriding-traits"></a>Invalidación de rasgos

Entornos de rasgo descienden del contenedor primario hacia abajo hasta los contenedores secundarios, como se muestra en el gráfico siguiente muestra un controlador de vista dividida en un iPad en la orientación horizontal:

 [![](unified-storyboards-images/cascadingclasses01.png "Un controlador de vista dividida en un iPad en la orientación horizontal")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Puesto que el iPad tiene una clase de tamaño normal en las orientaciones horizontales y verticales, la vista dividida mostrará las vistas de maestro y detalles.

En un iPhone, donde la clase de tamaño son compacta en ambas orientaciones, el controlador de vista Split solo muestra la vista de detalle, tal como se muestra a continuación:

 [![](unified-storyboards-images/cascadingclasses02.png "El controlador de vista Split solo muestra la vista de detalle")](unified-storyboards-images/cascadingclasses02.png#lightbox)

En una aplicación en la que el desarrollador desea mostrar una vista maestra y de detalles en un iPhone en la orientación horizontal, el desarrollador debe insertar un contenedor primario para el controlador de vista de división y reemplazar la colección de rasgo. Tal como se muestra en el gráfico siguiente:

 [![](unified-storyboards-images/cascadingclasses03.png "El desarrollador debe insertar un contenedor primario para el controlador de vista de división y reemplazar la colección de rasgo")](unified-storyboards-images/cascadingclasses03.png#lightbox)

Un `UIView` se establece como el elemento primario del controlador de vista de división y el `SetOverrideTraitCollection` se llama al método en la vista pasando una nueva colección de rasgo y destino es el controlador de vista de división. La nueva colección de rasgo invalida la `HorizontalSizeClass`, si se establece en `Regular`, de modo que el controlador de vista de división se mostrarán las vistas de maestro y detalles en un iPhone en la orientación horizontal.

Tenga en cuenta que el `VerticalSizeClass` se estableció en `unspecified`, que permite que la nueva colección de rasgo que se agregarán a la colección de rasgo en el elemento primario, lo que resulta en un `Compact VerticalSizeClass` para el elemento secundario de controlador de vista de división.

### <a name="trait-changes"></a>Cambios de rasgo

En esta sección examinará, en detalle en cómo las colecciones de rasgo de transición cuando cambie el entorno de rasgo. Por ejemplo, cuando el dispositivo se gira de vertical a horizontal.

 [![](unified-storyboards-images/traittransitions01.png "La vertical a horizontal información general de los cambios de rasgo")](unified-storyboards-images/traittransitions01.png#lightbox)

En primer lugar, iOS 8 hace algún programa de instalación para prepararse para que la transición. A continuación, en el sistema se anima el estado de transición. Por último, iOS 8 limpia cualquier necesarios durante la transición de Estados temporales.

iOS 8 proporciona varias devoluciones de llamada que el programador puede utilizar para participar en el cambio de rasgo tal como se muestra en la tabla siguiente:

|Phase|Callback|Descripción|
|--- |--- |--- |
|Programa de instalación|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Este método obtiene llama al principio de un cambio de rasgo antes de que se establece una colección de rasgo para su nuevo valor.</li><li>El método se llama cuando ha cambiado el valor de la colección de rasgo pero antes de cualquier animación.</li></ul>|
|Animación|`WillTransitionToTraitCollection`|El Coordinador de transición que se pasa a este método tiene un `AnimateAlongside` propiedad que permite al desarrollador agregar animaciones que se va a ejecutar junto con las animaciones de forma predeterminada.|
|Limpieza|`WillTransitionToTraitCollection`|Proporciona un método para que los desarrolladores incluyen su propio código de limpieza después de la transición.|

El `WillTransitionToTraitCollection` método es muy útil para animar los controladores de vista junto con los cambios de la colección de rasgo. El `WillTransitionToTraitCollection` método solo está disponible en los controladores de vista ( `UIViewController`) y no en otros entornos de rasgo como `UIViews`.

El `TraitCollectionDidChange` resulta ideal para trabajar con el `UIView` (clase), donde el desarrollador desea actualizar la interfaz de usuario que están cambiando los rasgos.

### <a name="collapsing-the-split-view-controllers"></a>Contraer los controladores de vista dividida

Ahora vamos a echar un vistazo más de cerca a lo que sucede cuando se contrae un controlador de vista de la división de una columna de dos a una vista de una columna. Como parte de este cambio, hay dos procesos que deben producirse:

-  De forma predeterminada, el controlador de vista dividida usará el controlador de vista principal como la vista después de producirse la contracción. El desarrollador puede invalidar este comportamiento invalidando el `GetPrimaryViewControllerForCollapsingSplitViewController` método de la `UISplitViewControllerDelegate` y proporcionar cualquier controlador de vista que desea mostrar en el estado contraído.
-  El controlador de vista secundaria tiene que combinar en el controlador de vista principal. Por lo general el desarrollador no tendrá que realizar ninguna acción para este paso; el controlador de vista dividida incluye la administración automática de esta fase basándose en el dispositivo de hardware. Sin embargo, puede haber algunos casos especiales donde el desarrollador querrán interactuar con este cambio. Una llamada a la `CollapseSecondViewController` método de la `UISplitViewControllerDelegate` permite que el controlador de vista maestra que se mostrará cuando se produce la contracción, en lugar de la vista de detalles.


### <a name="expanding-the-split-view-controller"></a>Expandir el controlador de vista dividida

Ahora vamos a echar un vistazo más de cerca a lo que sucede cuando se expande un controlador de vista de división en estado contraído. Una vez más, hay dos fases que deben producirse:

-  En primer lugar, defina el nuevo controlador de vista principal. De forma predeterminada, el controlador de vista dividida utilizará automáticamente el controlador de vista principal de la vista contraída. De nuevo, el desarrollador puede invalidar este comportamiento mediante la `GetPrimaryViewControllerForExpandingSplitViewController` método de la `UISplitViewControllerDelegate` .
-  Una vez que se ha elegido el controlador de vista principal, se debe volver a crear el controlador de vista secundaria. De nuevo, el controlador de vista dividida incluye la administración automática de esta fase basándose en el dispositivo de hardware. El desarrollador puede invalidar este comportamiento mediante una llamada a la `SeparateSecondaryViewController` método de la `UISplitViewControllerDelegate` .


En un controlador de vista de división, el controlador de vista principal desempeña un papel importante en la expansión y contracción de las vistas mediante la implementación de la `CollapseSecondViewController` y `SeparateSecondaryViewController` métodos de la `UISplitViewControllerDelegate`. `UINavigationController` Implemente estos métodos para insertar y extraer el controlador de vistas secundarias automáticamente.

### <a name="showing-view-controllers"></a>Controladores de vista que muestra

Otro cambio que Apple haya realizado en iOS 8 está en el modo en que el desarrollador muestra los controladores de vista. En el pasado, si la aplicación tenía un controlador de vista de hoja (por ejemplo, un controlador de vista de tabla) y el desarrollador mostraron uno diferente (por ejemplo, en respuesta al pulsar en una celda de usuario), podría llegar la aplicación a través de la jerarquía de controlador para el Controlador de vista de navegación y llame a la `PushViewController` método en él para mostrar la vista nueva.

Esto presenta un muy estrecho acoplamiento entre el controlador de navegación y el entorno que se estaba ejecutando en. En iOS 8, Apple ha desacoplado esto proporcionando dos nuevos métodos:

-  `ShowViewController` : Se adapta para mostrar el nuevo controlador de vista en función de su entorno. Por ejemplo, en un `UINavigationController` simplemente envía la nueva vista en la pila. En un controlador de vista de división, el nuevo controlador de vista se presenta en el lado izquierdo como el nuevo controlador de vista principal. Si no hay ningún controlador de vista del contenedor está presente, se mostrará la nueva vista como un controlador de vista modales.
-  `ShowDetailViewController` – Funciona de manera similar a `ShowViewController`, pero se implementa en un controlador de vista de división para reemplazar la vista de detalles con el nuevo controlador de vista que se pasa. Si el controlador de vista de la división está contraído (como puede verse en un aplicación de iPhone), la llamada se redirigirán a la `ShowViewController` método y la nueva vista se mostrará como el controlador de vista principal. Nuevamente, si no hay ningún controlador de vista del contenedor está presente, se mostrará la nueva vista como un controlador de vista modales.


Estos métodos funcionan empezando por el controlador de vista de la hoja y recorrer la jerarquía de vistas hasta que encuentran el controlador de vista de contenedor adecuado para controlar la presentación de la nueva vista.

Los desarrolladores pueden implementar `ShowViewController` y `ShowDetailViewController` en sus propios controladores de vista personalizado para obtener el mismo automatizado funcionalidad que `UINavigationController` y `UISplitViewController` proporciona.

### <a name="how-it-works"></a>Cómo funciona

En esta sección, echaremos un vistazo a cómo se implementan estos métodos en realidad en iOS 8. Primero echemos un vistazo a la nueva `GetTargetForAction` método:

 [![](unified-storyboards-images/gettargetforaction.png "El nuevo método GetTargetForAction")](unified-storyboards-images/gettargetforaction.png#lightbox)

Este método recorre la cadena de la jerarquía hasta que se encuentra el controlador de vista del contenedor correcto. Por ejemplo:

1.  Si un `ShowViewController` se llama al método, el primer controlador de vista de la cadena que implementa este método es el controlador de navegación, por lo que se usa como el elemento primario de la nueva vista.
1.  Si un `ShowDetailViewController` se llamó al método en su lugar, el controlador de vista de división es el primer controlador de vista para su implementación, por lo que se usa como el elemento primario.


El `GetTargetForAction` método funciona localizando un controlador de vista que implementa una acción determinada y, a continuación, pedir ese controlador de vista si desea recibir esa acción. Puesto que este método es público, los desarrolladores pueden crear sus propios métodos personalizados que funcionan igual que la compilación en `ShowViewController` y `ShowDetailViewController` métodos.

## <a name="adaptive-presentation"></a>Presentación adaptable

En iOS 8, Apple ha realizado el elemento flotante presentaciones ( `UIPopoverPresentationController`) adaptable también. Por lo que un controlador de vista de presentación de elemento flotante presentará automáticamente la vista de un elemento flotante normal en una clase de tamaño normal, pero lo mostrará la pantalla completa en una clase de tamaño horizontalmente compact (como en un iPhone).

Para dar cabida a los cambios en el sistema unificado de guión gráfico, se ha creado un nuevo objeto de controlador para administrar los controladores de vista presentada: `UIPresentationController`. Este controlador se crea desde el momento en que se presenta el controlador de vista hasta que se cierra. Ya que es una clase de administración, se puede considerar una superclase sobre el controlador de vista cuando responde a los cambios en los dispositivos que afectan el controlador de vista (por ejemplo, la orientación) que pasan a ser cala nuevo en el controlador de vista controla el controlador de presentación.

Cuando el desarrollador presenta un controlador de vista mediante el `PresentViewController` método, la administración del proceso de presentación se entrega a `UIKit`. UIKit controla (entre otras cosas), el controlador correcto para el estilo que se está creando, con la única excepción es cuando un controlador de vista tiene el estilo establecido en `UIModalPresentationCustom`. En este caso, la aplicación puede proporcionar es propio PresentationController en lugar de utilizar el `UIKit` controlador.

### <a name="custom-presentation-styles"></a>Estilos de presentación personalizado

Con un estilo de presentación personalizados, los desarrolladores tienen la opción para usar un controlador personalizado de presentación. Este controlador personalizado puede utilizarse para modificar la apariencia y comportamiento de la vista es allied a.

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>Trabajar con las clases de tamaño

El proyecto de Xamarin fotos adaptable que se incluye con este artículo se ofrece un ejemplo práctico del uso de las clases de tamaño y los controladores de vista adaptable en una aplicación de iOS 8 interfaz unificada.

Mientras que la aplicación crea su interfaz de usuario completamente desde el código, en lugar de usar el Diseñador de IOS y se aplican las mismas técnicas de creación de un guión gráfico unificado. Más adelante en este artículo, mostraremos cómo usar las clases de tamaño con un guión gráfico unificado y el diseñador en una aplicación Xamarin iOS.

Ahora Echemos un vistazo a cómo el proyecto de fotos adaptable implementa algunas de las características de clase de tamaño en iOS 8 para crear una aplicación adaptable.

### <a name="adapting-to-trait-environment-changes"></a>Para adaptarse a los cambios de entorno de rasgo

Cuando se ejecuta la aplicación adaptable fotos en un iPhone, cuando el usuario gira el dispositivo de vertical a horizontal, el controlador de vista dividida mostrará el maestro y detalles de vista:

 [![](unified-storyboards-images/rotation.png "El controlador de vista dividida mostrará a tanto el patrón y vista de detalles, tal como se muestra aquí")](unified-storyboards-images/rotation.png#lightbox)

Esto se logra reemplazando el `UpdateConstraintsForTraitCollection` el controlador de vista y el ajuste de las restricciones en función del valor de la `VerticalSizeClass`. Por ejemplo:

```csharp
public void UpdateConstraintsForTraitCollection (UITraitCollection collection)
{
    var views = NSDictionary.FromObjectsAndKeys (
        new object[] { TopLayoutGuide, ImageView, NameLabel, ConversationsLabel, PhotosLabel },
        new object[] { "topLayoutGuide", "imageView", "nameLabel", "conversationsLabel", "photosLabel" }
    );

    var newConstraints = new List<NSLayoutConstraint> ();
    if (collection.VerticalSizeClass == UIUserInterfaceSizeClass.Compact) {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide][imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.Add (NSLayoutConstraint.Create (ImageView, NSLayoutAttribute.Width, NSLayoutRelation.Equal,
            View, NSLayoutAttribute.Width, 0.5f, 0.0f));
    } else {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]-20-[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));
    }

    if (constraints != null)
        View.RemoveConstraints (constraints.ToArray ());

    constraints = newConstraints;
    View.AddConstraints (constraints.ToArray ());
}
```

### <a name="adding-transition-animations"></a>Agregar animaciones de transición

Cuando el controlador de vista dividida en las fotos adaptable aplicación va de contraído a expandido, las animaciones se agregan a las animaciones de forma predeterminada invalidando el `WillTransitionToTraitCollection` método del controlador de vista. Por ejemplo:

```csharp
public override void WillTransitionToTraitCollection (UITraitCollection traitCollection, IUIViewControllerTransitionCoordinator coordinator)
{
    base.WillTransitionToTraitCollection (traitCollection, coordinator);
    coordinator.AnimateAlongsideTransition ((UIViewControllerTransitionCoordinatorContext) => {
        UpdateConstraintsForTraitCollection (traitCollection);
        View.SetNeedsLayout ();
    }, (UIViewControllerTransitionCoordinatorContext) => {
    });
}
```

### <a name="overriding-the-trait-environment"></a>Reemplazar el entorno de rasgo

Como se muestra anteriormente, la aplicación fotos adaptable fuerza el controlador de vista dividida para mostrar los detalles y las vistas de la maestra cuando el dispositivo iPhone está en la vista horizontal.

Esto se logra mediante el siguiente código en el controlador de vista:

```csharp
private UITraitCollection forcedTraitCollection = new UITraitCollection ();
...

public UITraitCollection ForcedTraitCollection {
    get {
        return forcedTraitCollection;
    }

    set {
        if (value != forcedTraitCollection) {
            forcedTraitCollection = value;
            UpdateForcedTraitCollection ();
        }
    }
}
...

public override void ViewWillTransitionToSize (SizeF toSize, IUIViewControllerTransitionCoordinator coordinator)
{
    ForcedTraitCollection = toSize.Width > 320.0f ?
         UITraitCollection.FromHorizontalSizeClass (UIUserInterfaceSizeClass.Regular) :
         new UITraitCollection ();

    base.ViewWillTransitionToSize (toSize, coordinator);
}

public void UpdateForcedTraitCollection ()
{
    SetOverrideTraitCollection (forcedTraitCollection, viewController);
}
```

### <a name="expanding-and-collapsing-the-split-view-controller"></a>Expandir y contraer el controlador de vista dividida

Siguiente vamos a examinar cómo se implementa el comportamiento de expansión y contracción del controlador de vista dividida en Xamarin. En el `AppDelegate`, cuando se crea el controlador de vista de división, se asigna a su delegado para controlar estos cambios:

```csharp
public class SplitViewControllerDelegate : UISplitViewControllerDelegate
{
    public override bool CollapseSecondViewController (UISplitViewController splitViewController,
        UIViewController secondaryViewController, UIViewController primaryViewController)
    {
        AAPLPhoto photo = ((CustomViewController)secondaryViewController).Aapl_containedPhoto (null);
        if (photo == null) {
            return true;
        }

        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            var viewControllers = new List<UIViewController> ();
            foreach (var controller in ((UINavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containsPhoto");

                if ((bool)method.Invoke (controller, new object[] { null })) {
                    viewControllers.Add (controller);
                }
            }

            ((UINavigationController)primaryViewController).ViewControllers = viewControllers.ToArray<UIViewController> ();
        }

        return false;
    }

    public override UIViewController SeparateSecondaryViewController (UISplitViewController splitViewController,
        UIViewController primaryViewController)
    {
        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            foreach (var controller in ((CustomNavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containedPhoto");

                if (method.Invoke (controller, new object[] { null }) != null) {
                    return null;
                }
            }
        }

        return new AAPLEmptyViewController ();
    }
}
```

El `SeparateSecondaryViewController` método comprueba si una fotografía que se muestra y realiza una acción basada en dicho estado. Si no hay foto es está mostrando lo contrae el controlador de vista secundaria para que se muestre el controlador de vista maestra.

El `CollapseSecondViewController` método se usa al expandir el controlador de vista de división para ver si existen cualquier fotos en la pila, si así se contrae a esa vista.

### <a name="moving-between-view-controllers"></a>Mover entre controladores de vista

A continuación, echemos un vistazo a cómo la aplicación fotos adaptable se mueve entre controladores de vista. En el `AAPLConversationViewController` clase cuando el usuario selecciona una celda de la tabla, el `ShowDetailViewController` método se llama para mostrar la vista de detalles:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    var photo = PhotoForIndexPath (indexPath);
    var controller = new AAPLPhotoViewController ();
    controller.Photo = photo;

    int photoNumber = indexPath.Row + 1;
    int photoCount = (int)Conversation.Photos.Count;
    controller.Title = string.Format ("{0} of {1}", photoNumber, photoCount);
    ShowDetailViewController (controller, this);
}
```

### <a name="displaying-disclosure-indicators"></a>Mostrar indicadores de divulgación

En la aplicación Photo adaptable existen varios lugares donde la divulgación de indicadores se ocultan o se muestra en función de los cambios en el entorno de rasgo. Esto se controla con el código siguiente:

```csharp
public bool Aapl_willShowingViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}

public bool Aapl_willShowingDetailViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingDetailViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}
```

Estos elementos se implementan utilizando la `GetTargetViewControllerForAction` método descrito anteriormente en detalle.

Cuando un controlador de vista de tabla de datos se están mostrando, usa los métodos implementados anteriormente para ver si va a ocurrir una inserción y si se deben mostrar u ocultar el indicador de divulgación según corresponda:

```csharp
public override void WillDisplay (UITableView tableView, UITableViewCell cell, NSIndexPath indexPath)
{
    bool pushes = ShouldShowConversationViewForIndexPath (indexPath) ?
         Aapl_willShowingViewControllerPushWithSender () :
         Aapl_willShowingDetailViewControllerPushWithSender ();

    cell.Accessory = pushes ? UITableViewCellAccessory.DisclosureIndicator : UITableViewCellAccessory.None;
    var conversation = ConversationForIndexPath (indexPath);
    cell.TextLabel.Text = conversation.Name;
}
```

### <a name="new-showdetailtargetdidchangenotification-type"></a>Nuevo `ShowDetailTargetDidChangeNotification` tipo

Apple ha agregado un nuevo tipo de notificación para trabajar con las clases de tamaño y entornos de rasgo desde dentro de un controlador de vista dividida, `ShowDetailTargetDidChangeNotification`. Esta notificación se envía cuando cambia el destino de la vista de detalle para un controlador de vista de división, como cuando el controlador se expande o contrae.

La aplicación fotos adaptable utiliza esta notificación para actualizar el estado del indicador de divulgación cuando cambia el controlador de vista de detalle:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    TableView.RegisterClassForCellReuse (typeof(UITableViewCell), AAPLListTableViewControllerCellIdentifier);
    NSNotificationCenter.DefaultCenter.AddObserver (this, new Selector ("showDetailTargetDidChange:"),
        UIViewController.ShowDetailTargetDidChangeNotification, null);
    ClearsSelectionOnViewWillAppear = false;
}
```

Eche un vistazo más de cerca en la aplicación fotos adaptable para ver todas las formas en que las clases de tamaño, las colecciones de rasgo y controladores de vista adaptable pueden utilizarse para crear fácilmente una aplicación unificada en Xamarin.iOS.

## <a name="unified-storyboards"></a>Guiones gráficos unificados

Nuevo para iOS 8, guiones gráficos unificados permiten al desarrollador crear uno, unificado que puede mostrarse en los dispositivos iPhone y iPad estableciendo como destino varias clases de tamaño de archivo del guión gráfico. Mediante el uso de guiones gráficos unificados, el desarrollador escribe menos código específico de la interfaz de usuario y tiene solo una interfaz diseño para crear y mantener.

Las principales ventajas de guiones gráficos unificados son:

-  Use el mismo archivo de guión gráfico para iPhone e iPad.
-  Implementar con versiones anteriores a iOS 6 e iOS 7.
-  Obtener una vista previa del diseño para distintos dispositivos, orientaciones y versiones del sistema operativo desde el Diseñador de iOS de Xamarin.

Esta característica es totalmente compatible en Visual Studio para Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>Habilitación de las clases de tamaño

De forma predeterminada, cualquier nuevo proyecto de Xamarin.iOS nos pedirá clases de tamaño. Para usar las clases de tamaño y las adaptable dentro de un guion gráfico en un proyecto anterior, debe convertirse primero en el formato de guión gráfico unificado de Xcode 6 desde dentro del Diseñador de iOS.

Hacer esto abre el guión gráfico para convertirse en el diseñador y verificación de iOS la **Use las clases de tamaño** casilla de verificación:

 [![](unified-storyboards-images/sizeclass01.png "La casilla de verificación de las clases de tamaño de uso")](unified-storyboards-images/sizeclass01.png#lightbox)

El Diseñador de iOS se confirmará que el desarrollador desea convertir el formato del guión gráfico para usar las clases de tamaño:

 [![](unified-storyboards-images/sizeclass02.png "El uso de alerta de las clases de tamaño")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> Diseño automático también debe estar activada para que las clases de tamaño para que funcione correctamente.

### <a name="generic-device-types"></a>Tipos de dispositivos genéricos

Una vez que se ha convertido el guión gráfico para usar las clases de tamaño, se volverá a en la superficie de diseño y la **ver como** dispositivo estará genérico:

 [![](unified-storyboards-images/sizeclass03.png "Ver como un tipo de dispositivo genérico")](unified-storyboards-images/sizeclass03.png#lightbox)

Cuando se selecciona el tipo de dispositivo genérico, todos los controladores de vista cambiará de tamaño a un cuadrado de 600 x 600. Este cuadrado representa los tamaños de cualquier ancho y alto de cualquier. Cuando iOS Designer es en este modo, las modificaciones que se aplicarán a todas las clases de tamaño.

El desarrollador también tiene la opción de visualización de la superficie de diseño como un iPhone:

 [![](unified-storyboards-images/sizeclass04.png "Visualización de la superficie de diseño como un iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

O verlo como un iPad:

 [![](unified-storyboards-images/sizeclass05.png "Visualización de la superficie de diseño como un iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Seleccione una clase de tamaño

El botón Selector de clase de tamaño está en la esquina superior izquierda de la superficie de diseño (cerca de la lista desplegable Vista como). Permite al desarrollador seleccionar qué clases de tamaño se está editando:

 [![](unified-storyboards-images/sizeclass06.png "Seleccione una clase de tamaño")](unified-storyboards-images/sizeclass06.png#lightbox)

El selector presenta la selección de la clase de tamaño como una cuadrícula de 3 x 3. Cada uno de los cuadrados en la cuadrícula representa una combinación de una clase de ancho y un alto. El cuadrado central selecciona la clase de cualquier tamaño de alto Width/Any (que es la vista predeterminada de un guión gráfico unificado). Cuando se selecciona este cuadrado, el desarrollador está editando el diseño predeterminado, que es heredado por todas las configuraciones.

El cuadrado de la esquina superior izquierda de la cuadrícula representa la clase de tamaño de Compact compacto de ancho/alto:

 [![](unified-storyboards-images/sizeclass07.png "La clase de tamaño de alto ancho Compact/compacto")](unified-storyboards-images/sizeclass07.png#lightbox)

Este modo se corresponde con un iPhone en la orientación horizontal. El cuadrado de la esquina inferior derecha de la cuadrícula representa la clase de tamaño de alto ancho/normal normal, que representa un iPad:

 [![](unified-storyboards-images/sizeclass08.png "La clase de tamaño de alto ancho normal o normal")](unified-storyboards-images/sizeclass08.png#lightbox)

Para editar el diseño de un iPhone en la orientación vertical, seleccione el cuadrado en la esquina inferior izquierda. Representa la clase de tamaño de alto ancho/normal Compact:

 [![](unified-storyboards-images/sizeclass09.png "La clase de tamaño de alto ancho Compact/normal")](unified-storyboards-images/sizeclass09.png#lightbox)

Haga clic en el cuadrado para seleccionarlo y, a la superficie de diseño cambiará el tamaño de los controladores de vista para que coincida con la nueva selección:

 [![](unified-storyboards-images/sizeclass10.png "Cambia el tamaño de los controladores de vista para que coincida con la nueva selección, como se muestra la superficie de diseño")](unified-storyboards-images/sizeclass10.png#lightbox)

Vea la sección de clase de tamaño de este artículo para obtener más información sobre las clases de tamaño y cómo afectan al diseño para iPhone e iPad.

### <a name="adaptive-segue-types"></a>Tipos de Segue adaptable

Si ha usado por el programador guiones gráficos antes, ya estarán familiarizados con los tipos existentes de segue de **Push**, **Modal** y **elemento flotante**. Cuando se habilitan las clases de tamaño en un archivo de guión gráfico unificado, adaptable Segue de los siguientes (que corresponden a la nueva API de controlador de vista se ha explicado anteriormente) se ponen a disposición: **Mostrar** y **mostrar detalles**.

> [!IMPORTANT]
> Cuando se habilitan las clases de tamaño, cualquier existente, los objetos Segue will convertirse a los nuevos tipos.

Tome como ejemplo un iOS 8 aplicación que usa un guión gráfico unificado en un controlador de vista de división que tiene un menú de navegación de juego sencillo en la vista principal. Si el usuario hace clic en un botón de menú, el controlador de vista del elemento seleccionado debe mostrarse en la sección de detalles del controlador de vista de división cuando se ejecuta en un iPad. En un iPhone, controlador de vista del elemento debe insertarse en la pila de navegación.

Para lograr este efecto, en el Diseñador de iOS control y haga clic en el botón y arrastre una línea para el controlador de vista que se mostrará. Cuando se suelta el botón del mouse, seleccione `Show Detail` en el menú emergente de tipo de Segue:

 [![](unified-storyboards-images/segue01.png "Seleccione Mostrar detalle en el menú emergente de tipo de Segue")](unified-storyboards-images/segue01.png#lightbox)

Se creará el segue nuevo entre el botón y el controlador de vista. Ahora ejecute la aplicación en el simulador de iPhone y se mostrará el menú principal:

 [![](unified-storyboards-images/segue02.png "El menú principal")](unified-storyboards-images/segue02.png#lightbox)

Haga clic en el **seleccione juego** botón y el controlador de vista del elemento se insertará en la pila de navegación:

 [![](unified-storyboards-images/segue03.png "El controlador de vista de elementos se insertarán en la pila de navegación como se muestra")](unified-storyboards-images/segue03.png#lightbox)

Detener el simulador de iPhone y ejecutar la aplicación en el simulador de iPad. Cambiar a la orientación horizontal y principal se muestra de nuevo menú:

 [![](unified-storyboards-images/segue04.png "El menú principal de muestra")](unified-storyboards-images/segue04.png#lightbox)

De nuevo, haga clic en el **seleccione juego** botón y el controlador de vista del elemento que se muestra en la sección de detalles del controlador de vista de división:

 [![](unified-storyboards-images/segue05.png "El controlador de vista se muestra en la sección de detalles del controlador de vista de división de elementos")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Excluir un elemento de una clase de tamaño

Hay veces cuando un elemento determinado (por ejemplo, una vista, Control o una restricción) no se necesita dentro de una clase de tamaño específico. Para excluir un elemento de una clase de tamaño, seleccione el elemento que desee excluir de la **superficie de diseño**. Desplácese hasta la parte inferior de la **Property Explorer** y haga clic en el **engranaje** menú desplegable. Seleccione la combinación de **ancho** y **alto** para excluir el elemento de:

[![](unified-storyboards-images/exclude-a.png "Seleccione la combinación del ancho y alto")](unified-storyboards-images/exclude-a.png#lightbox)

Un nuevo *caso de exclusión* se agregará al elemento en la parte inferior de la **Property Explorer**. A continuación, desactive la **instalado** casilla de verificación de la clase de tamaño indicada:

[![](unified-storyboards-images/exclude-b.png "Desactive la casilla de verificación instalado")](unified-storyboards-images/exclude-b.png#lightbox)

Al ancho y alto que el elemento se ha excluido de la superficie de diseño, se ha quitado de la clase de tamaño determinado, pero no con todo el diseño de interfaz de usuario:

 [![](unified-storyboards-images/exclude02.png "Modificador para el ancho y alto que el elemento se ha excluido de la superficie de diseño")](unified-storyboards-images/exclude02.png#lightbox)

Regreso a la clase de tamaño Any alto Width/Any y el elemento aún está en su lugar:

 [![](unified-storyboards-images/exclude03.png "Regreso a la clase de tamaño Any alto Width/Any")](unified-storyboards-images/exclude03.png#lightbox)

Cuando la aplicación se ejecuta en el simulador de iPad, aparece el elemento:

 [![](unified-storyboards-images/exclude04.png "El elemento que se muestra cuando la aplicación en ejecución en el simulador de iPad")](unified-storyboards-images/exclude04.png#lightbox)

Y cuando la aplicación se ejecuta en el simulador de iPhone, falta el elemento:

 [![](unified-storyboards-images/exclude05.png "El elemento que falta cuando la aplicación en ejecución en el simulador de iPhone")](unified-storyboards-images/exclude05.png#lightbox)

Para quitar un caso de exclusión de un elemento, basta con seleccionar el elemento en el **superficie de diseño**, desplácese hasta la parte inferior de la **Property Explorer** y haga clic en el **-** situado al lado de los casos para quitar.

Para ver una implementación de guiones gráficos unificados, examine el `UnifiedStoryboard` Xamarin iOS 8 aplicación asociado a este documento de ejemplo.

## <a name="dynamic-launch-screens"></a>Pantallas de inicio dinámica

El archivo de la pantalla de inicio se muestra como pantalla de presentación mientras se está iniciando una aplicación de iOS para proporcionar comentarios al usuario que la aplicación es realmente puesta en marcha. Antes de iOS 8, el desarrollador tendría que incluir varios `Default.png` activos para cada dispositivo tipo, orientación y resolución de pantalla que se estarían ejecutando la aplicación en la imagen. Por ejemplo, `Default@2x.png`, `Default-Landscape@2x~ipad.png`, `Default-Portrait@2x~ipad.png`, etcetera.

Debe tener en cuenta el nuevo iPhone 6 e iPhone 6 Plus dispositivos (y los próximos Apple Watch) con todos los dispositivos iPad y iPhone existente, esto representa una matriz grande de tamaños diferentes, orientaciones y resoluciones de `Default.png` activos de imagen de pantalla de inicio deben crear y mantener. Además, estos archivos pueden ser bastante grandes y se "Inflar" el lote de aplicación de entrega, aumentar la cantidad de tiempo necesario para descargar la aplicación de iTunes Store App (posiblemente manteniéndola puedan entregar a través de una red de telefonía móvil) y aumentar la cantidad de almacenamiento necesario en el dispositivo del usuario final.

Nuevo IOS 8, el desarrollador puede crear un único, de forma atómico `.xib` archivo en Xcode que usa el diseño automático y las clases de tamaño para crear un *dinámica de pantalla de inicio* que funcionará para cada dispositivo, la resolución y la orientación. Esto no sólo reduce la cantidad de trabajo necesaria del desarrollador para crear y mantener todos los activos de imagen necesarios, pero reduce en gran medida el tamaño de lote de la aplicación instalada.


Pantallas de inicio dinámica tiene las siguientes limitaciones y consideraciones:

 - Use solo `UIKit` clases.
 - Utilizar una vista de raíz única que es un `UIView` o `UIViewController` objeto.
 - No realice ninguna conexión a código de la aplicación (no agregue **acciones** o **salidas**).
 - No agregue `UIWebView` objetos.
 - No use las clases personalizadas.
 - No utilice los atributos en tiempo de ejecución.

Con las instrucciones anteriores en mente, echemos un vistazo a la adición de una pantalla de inicio de dinámica a un proyecto de iOS 8 Xamarin existente.

Haga lo siguiente:

1. Abra **Visual Studio para Mac** y cargar el **solución** para agregar a la pantalla de inicio dinámica.
2. En el **el Explorador de soluciones**, haga clic en el `MainStoryboard.storyboard` de archivo y seleccione **abrir con** > **Interface Builder de Xcode**:

    [![](unified-storyboards-images/dls01.png "Abrir con Interface Builder de Xcode")](unified-storyboards-images/dls01.png#lightbox)
3. En Xcode, seleccione **archivo** > **New** > **archivo...** :

    [![](unified-storyboards-images/dls02.png "Seleccione el archivo / nuevo")](unified-storyboards-images/dls02.png#lightbox)
4. Seleccione **iOS** > **interfaz de usuario** > **pantalla de inicio** y haga clic en el **siguiente** botón:

    [![](unified-storyboards-images/dls03.png "Seleccione iOS / interfaz de usuario / pantalla de inicio")](unified-storyboards-images/dls03.png#lightbox)
5. Nombre del archivo `LaunchScreen.xib` y haga clic en el **crear** botón:

    [![](unified-storyboards-images/dls04.png "Nombre del archivo LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. Editar el diseño de la pantalla de inicio agregando elementos gráficos y usando las restricciones de diseño para colocarlos de dispositivos determinados, orientaciones y tamaños de pantalla:

    [![](unified-storyboards-images/dls05.png "Editar el diseño de la pantalla de inicio")](unified-storyboards-images/dls05.png#lightbox)
7. Guarde los cambios en `LaunchScreen.xib`.
8. Seleccione el **aplicaciones de destino** y **General** pestaña:

    [![](unified-storyboards-images/dls06.png "Seleccione el destino de las aplicaciones y en la ficha General")](unified-storyboards-images/dls06.png#lightbox)
9. Haga clic en el **elija Info.plist** botón, seleccione el `Info.plist` para la aplicación de Xamarin y haga clic en el **elegir** botón:

    [![](unified-storyboards-images/dls07.png "Seleccione el archivo Info.plist de la aplicación de Xamarin")](unified-storyboards-images/dls07.png#lightbox)
10. En el **iconos de aplicación e imágenes de inicio** sección, abra el **archivo de la pantalla de inicio** lista desplegable y elija el `LaunchScreen.xib` creado anteriormente:

    [![](unified-storyboards-images/dls08.png "Elija la LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. Guarde los cambios en el archivo y vuelva a Visual Studio para Mac.
12. Espere a que Visual Studio para Mac finalizar la sincronización de los cambios con Xcode.
13. En el **el Explorador de soluciones**, haga doble clic en el **recursos** carpeta y seleccione **agregar** > **agregar archivos...** :

    [![](unified-storyboards-images/dls09.png "Seleccione agrega / agrega archivos...")](unified-storyboards-images/dls09.png#lightbox)
14. Seleccione el `LaunchScreen.xib` archivo creado anteriormente y haga clic en el **abierto** botón:

    [![](unified-storyboards-images/dls10.png "Seleccione el archivo LaunchScreen.xib")](unified-storyboards-images/dls10.png#lightbox)
15. Compile la aplicación.

### <a name="testing-the-dynamic-launch-screen"></a>Las pruebas de la pantalla de inicio dinámica

En Visual Studio para Mac, seleccione el simulador de iPhone 4 Retina y ejecute la aplicación. En el formato correcto y la orientación se mostrará la pantalla de inicio dinámica:

[![](unified-storyboards-images/dls11.png "La pantalla de inicio dinámico que se muestra en la orientación vertical")](unified-storyboards-images/dls11.png#lightbox)

Detener la aplicación en Visual Studio para Mac y seleccione un dispositivo de iPad iOS 8. Ejecute la aplicación y se dará formato correctamente la pantalla de inicio para este dispositivo y la orientación:

[![](unified-storyboards-images/dls12.png "La pantalla de inicio dinámico que se muestra en la orientación horizontal")](unified-storyboards-images/dls12.png#lightbox)

Vuelva a Visual Studio para Mac y detener la aplicación se ejecute.

### <a name="working-with-ios-7"></a>Trabajar con iOS 7

Para mantener la compatibilidad con iOS 7, incluir solamente el habitual `Default.png` activos como normal en la aplicación de iOS 8 de imagen. iOS devolverá al comportamiento anterior y usar dichos archivos como la pantalla de inicio cuando se ejecuta en un dispositivo iOS 7.

Para ver una implementación de una pantalla de inicio dinámica de Xamarin, examine el [pantallas de inicio dinámica](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/) aplicación iOS 8 asociado a este documento de ejemplo.

## <a name="summary"></a>Resumen

En este artículo había echado un vistazo rápido a las clases de tamaño y cómo afectan al diseño en dispositivos iPhone y iPad. Explica cómo funcionan los rasgos, entornos de rasgo y colecciones de rasgo con las clases de tamaño para crear Interfaces de unificada. Se tardaron breve vistazo a los controladores de vista adaptable y cómo funcionan con las clases de tamaño dentro de las Interfaces unificada. Parecía a la implementación de las clases de tamaño y unificada Interfaces completamente desde C# código dentro de una aplicación Xamarin iOS 8.

Por último, en este artículo trata los aspectos básicos de creación de guiones gráficos unificados con el diseñador que funcionará en dispositivos iOS de Xamarin iOS y crear una única pantalla de inicio dinámico que se mostrará como pantalla de inicio en todos los dispositivos iOS 8.

## <a name="related-links"></a>Vínculos relacionados

- [Fotos adaptables (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [Ejemplo de StoryboardIntro](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [Pantallas de inicio dinámico (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Diseños dinámicos en iOS8 - 2014 evolucionar (vídeo)](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
