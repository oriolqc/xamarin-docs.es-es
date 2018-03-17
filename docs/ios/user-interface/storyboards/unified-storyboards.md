---
title: "Guiones gráficos unificados"
description: "Guiones gráficos unificados permiten iOS desarrollador crear la interfaz de usuario con un solo guión gráfico, en lugar de varios guiones gráficos, para cubrir la expansión variedad de tamaños de pantalla del dispositivo. Este artículo está diseñado para proporcionar una descripción más detallada en la operación del guión gráfico unificado en Xamarin.iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 77808ae03f5801dd3628b8966e05a574b8501f37
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="unified-storyboards"></a>Guiones gráficos unificados

_Guiones gráficos unificados permiten iOS desarrollador crear la interfaz de usuario con un solo guión gráfico, en lugar de varios guiones gráficos, para cubrir la expansión variedad de tamaños de pantalla del dispositivo. Este artículo está diseñado para proporcionar una descripción más detallada en la operación del guión gráfico unificado en Xamarin.iOS._

iOS 8 incluye un mecanismo nuevo y más fácil de usar para crear la interfaz de usuario, el guión gráfico unificado. Con un solo guión gráfico para cubrir todos los tamaños de pantalla de hardware diferente, se pueden crear vistas rápidas y con capacidad de respuesta en un "diseño-una vez, utilice varios" estilo.

Como el programador ya no se necesita crear un guion gráfico a independiente y específico para dispositivos iPhone y iPad, tienen la flexibilidad para diseñar aplicaciones con una interfaz común y, a continuación, personalizar esa interfaz para las clases de tamaño diferente. De este modo, puede adaptar una aplicación a la importancia de cada factor de forma y cada interfaz de usuario se puede optimizar para ofrecer la mejor experiencia.

<a name="size-classes" />

## <a name="size-classes"></a>Clases de tamaño

Antes de iOS 8, usado por el programador `UIInterfaceOrientation` y `UIInterfaceIdiom` para diferenciar entre los modos vertical y horizontal y entre los dispositivos iPhone y iPad. En iOS8, orientación y el dispositivo se determinan mediante *clases de tamaño*.

Los dispositivos se definen mediante las clases de tamaño, en los ejes horizontales y verticales, y hay dos tipos de clases de tamaño en iOS 8:

-  **Regular** : se trata de un tamaño de pantalla de gran tamaño (por ejemplo, un iPad) o un gadget que da la impresión de un tamaño grande (como un `UIScrollView`
-  **Compact** : se trata de dispositivos más pequeños (por ejemplo, un iPhone). Este tamaño tiene en cuenta la orientación del dispositivo.


Si los dos conceptos se usan juntos, el resultado es una cuadrícula de 2 x 2 que define los posibles tamaños diferentes que pueden utilizarse en las orientaciones que no son iguales, tal como se muestra en el diagrama siguiente:

 [![](unified-storyboards-images/sizeclassgrid.png "Una cuadrícula de 2 x 2 que define los posibles tamaños diferentes en los que se pueden usar en las orientaciones normal y compacto")](unified-storyboards-images/sizeclassgrid.png#lightbox)

El programador puede crear un controlador de vista que usa cualquiera de las posibilidades que cuatro hay que se crearán en diseños diferentes (como se muestra en el gráfico anterior).

### <a name="ipad-size-classes"></a>iPad clases de tamaño

El iPad, debido al tamaño tiene un **regular** tamaño para ambas orientaciones de la clase.

 [![](unified-storyboards-images/image1.png "iPad clases de tamaño")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>Clases de tamaño de iPhone

IPhone consta de las clases de tamaño distinto en función de la orientación del dispositivo:

 [![](unified-storyboards-images/iphonesizeclasses.png "Clases de tamaño de iPhone")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  Cuando el dispositivo está en modo vertical, la pantalla tiene una **compact** clase horizontalmente y **regular** verticalmente
-  Cuando el dispositivo esté en modo horizontal, se invierten las clases de pantalla de modo vertical.

### <a name="iphone-6-plus-size-classes"></a>Clases de tamaño de iPhone 6 Plus

Los tamaños son los mismos que los anterior iPhone en orientación vertical, pero es diferente en horizontal:

[![](unified-storyboards-images/iphone6sizeclasses.png "Clases de tamaño de iPhone 6 Plus")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Dado que el iPhone 6 Plus tiene una pantalla lo suficientemente grande, puede tener una clase de tamaño ancho normal en el modo horizontal.

### <a name="support-for-a-new-screen-scale"></a>Compatibilidad con una nueva escala de pantalla

IPhone 6 Plus usa una nueva pantalla de Retina HD con un factor de escala de la pantalla de 3.0 (tres veces la original iPhone resolución de pantalla). Para proporcionar la mejor experiencia posible en estos dispositivos, incluir ilustraciones nuevas diseñadas para esta escala de la pantalla. En Xcode 6 y versiones posteriores, asset catálogos pueden incluir imágenes en 1 x, x 2 y 3 tamaños de x; basta con agregar los nuevos activos de imagen y iOS elegirá los activos correctos cuando se ejecuta en un iPhone 6 Plus.

La imagen de comportamiento en iOS de carga también reconoce un `@3x` sufijo en archivos de imagen. Por ejemplo, si el desarrollador incluye un recurso de imagen (con distintas resoluciones) en el paquete de la aplicación con los siguientes nombres de archivo: `MonkeyIcon.png`, `MonkeyIcon@2x.png`, y `MonkeyIcon@3x.png`. En el iPhone 6 Plus la `MonkeyIcon@3x.png` imagen se usará automáticamente si el desarrollador carga una imagen mediante el código siguiente:

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
O si asigna la imagen a un elemento de interfaz de usuario utilizando el archivo iOS diseñador como `MonkeyIcon.png`, el `MonkeyIcon@3x.png` se usará, nuevo de forma automática, en el iPhone 6 Plus.

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>Pantallas de inicio dinámica

El archivo de la pantalla de inicio se muestra como una pantalla de presentación mientras se iniciaba una aplicación de iOS para proporcionar comentarios al usuario que la aplicación es realmente puesta en marcha. Antes de iOS 8, el desarrollador tendría que incluir varios `Default.png` activos para cada dispositivo tipo, orientación y resolución de pantalla que se estaría ejecutando la aplicación en la imagen.

Nuevo en iOS 8, el programador puede crear una única atómica `.xib` archivo en Xcode que usa clases de tamaño y diseño automático para crear un *dinámica pantalla iniciar* que funcionará para cada dispositivo, la resolución y la orientación. Esto no solo reduce la cantidad de trabajo requerida del desarrollador para crear y mantener todos los activos de imagen necesaria, pero reduce el tamaño de paquete de la aplicación instalada.

## <a name="traits"></a>Rasgos

Rasgos son propiedades que pueden utilizarse para determinar cómo se cambia un diseño como cambios en su entorno. Estas constan de un conjunto de propiedades (la `HorizontalSizeClass` y `VerticalSizeClass` basado en `UIUserInterfaceSizeClass`), así como el idioma de interfaz ( `UIUserInterfaceIdiom`) y la escala de la pantalla.

Todos los estados anteriores se envuelve en un contenedor que Apple hace referencia como una colección de rasgo ( `UITraitCollection`), que contiene no solo las propiedades sino también sus valores.

## <a name="trait-environment"></a>Entorno de rasgo

Entornos de rasgo son una nueva interfaz en iOS 8 y pueden devolver una colección de rasgo de los siguientes objetos:

-  Pantallas ( `UIScreens` ).
-  Windows ( `UIWindows` ).
-  Ver controladores ( `UIViewController` ).
-  Vistas ( `UIView` ).
-  Controlador de presentación ( `UIPresentationController` ).


El programador usa la colección de rasgo devuelta por un entorno de rasgo para determinar cómo debe disponerse una interfaz de usuario.

Todos los entornos de rasgo de realizar una jerarquía, tal como se muestra en el diagrama siguiente:

 [![](unified-storyboards-images/viewhierarchy.png "El diagrama de jerarquía de entornos de rasgo")](unified-storyboards-images/viewhierarchy.png#lightbox)

La colección de rasgo que tienen cada uno de los entornos de rasgo anterior fluirá, de forma predeterminada, desde el elemento primario en el entorno secundario.

Además de obtener la colección de rasgo actual, el entorno de rasgo tiene un `TraitCollectionDidChange` método, que se puede invalidar en las subclases de vista o controlador de vista. El programador puede utilizar este método para modificar cualquiera de los elementos de interfaz de usuario que dependen de rasgos cuando han cambiado los rasgos.

## <a name="typical-trait-collections"></a>Colecciones de rasgo típico

Esta sección explica los tipos de colecciones de rasgo que experimentará el usuario cuando se trabaja con iOS 8 típicos.

La siguiente es una colección de rasgo típico que vea el programador en un iPhone:

|Property|Valor|
|--- |--- |
|`HorizontalSizeClass`|Compactar|
|`VerticalSizeClass`|Estándar|
|`UserInterfaceIdom`|Teléfono|
|`DisplayScale`|2.0|

El conjunto anterior representaría una colección de rasgo totalmente cualificado, ya que tiene valores de todas sus propiedades de rasgo.

También es posible tener una colección de rasgo que faltan algunos de sus valores (que Apple hace referencia a como *Unspecified*):

|Property|Valor|
|--- |--- |
|`HorizontalSizeClass`|Compactar|
|`VerticalSizeClass`|Sin especificar|
|`UserInterfaceIdom`|Sin especificar|
|`DisplayScale`|Sin especificar|

Por lo general, sin embargo, cuando el programador solicita el entorno de rasgo de su colección de rasgo, devolverá un conjunto completo tal como se muestra en el ejemplo anterior.

Si un entorno de rasgo (por ejemplo, una vista o View Controller) no está dentro de la jerarquía de la vista actual, el programador podría volver sin especificar valores para una o varias de las propiedades de rasgo.

El desarrollador puede obtener una colección de rasgo parciales si usan uno de los métodos de creación proporcionados por Apple, como `UITraitCollection.FromHorizontalSizeClass`, para crear una nueva colección.

Una operación que se pueden realizar en varias colecciones de rasgo es comparándolos entre sí, lo que implica pedir una colección de rasgo si contiene otro. Se entiende por *contención* es que, para cualquier rasgo especificado en la segunda colección, el valor debe coincidir con exactamente con el valor de la primera colección.

Para probar dos rasgos usar la `Contains` método de la `UITraitCollection` pasando el valor del rasgo que se va a probar.

El desarrollador puede realizar las comparaciones manualmente en el código para determinar cómo las vistas de diseño o controladores de la vista. Sin embargo, `UIKit` este método se utiliza internamente para proporcionar parte de su funcionalidad, como en el Proxy de apariencia, por ejemplo.

## <a name="appearance-proxy"></a>Proxy de apariencia

El Proxy de apariencia se introdujo en versiones anteriores de iOS para permitir a los desarrolladores personalizar las propiedades de sus vistas. Se amplió en iOS 8 para admitir colecciones de rasgo.

Servidores proxy de apariencia ahora incluye un nuevo método `AppearanceForTraitCollection`, que devuelve un nuevo Proxy de apariencia de la colección especificada de rasgo que se haya pasado. Las personalizaciones que el programador lleva a cabo en ese aspecto Proxy sólo surtirán efecto en las vistas que se ajustan a la colección especificada de rasgo.

Por lo general el desarrollador va a pasar de una colección de rasgo parcialmente especificado en el `AppearanceForTraitCollection` método, por ejemplo, uno que acaba de especificar un Horizontal tamaño clase de compactar, para que puede personalizar cualquier vista de la aplicación que es compact horizontalmente.

## <a name="uiimage"></a>UIImage

Otra clase que Apple ha agregado a la colección de rasgo es `UIImage`. En el pasado el desarrollador tenía que especifique una @1X y @2x versión de cualquier activo de gráficos de mapa de bits que se va a incluir en la aplicación (por ejemplo, un icono).

iOS 8 se ha ampliado para permitir que el desarrollador debe incluir varias versiones de una imagen en un catálogo de imagen basados en una colección de rasgo. Por ejemplo, el programador podría incluir una imagen más pequeña para trabajar con una clase de rasgo Compact y una imagen con tamaño total de cualquier otra colección.

Cuando se utiliza una de las imágenes dentro de un `UIImageView` (clase), la vista de la imagen, mostrará automáticamente la versión correcta de la imagen para su colección de rasgo. Si el entorno de rasgo cambia (por ejemplo, el usuario cambia el dispositivo de vertical a horizontal), la vista de imagen seleccionará automáticamente el nuevo tamaño de imagen que coincida con la nueva colección de rasgo y cambiar su tamaño para que coincida con la versión actual de la imagen que se va a muestra.

## <a name="uiimageasset"></a>UIImageAsset

Apple ha agregado una nueva clase para iOS 8 denominada `UIImageAsset` para dar al programador tener un mayor control sobre la selección de imagen.

Un recurso de imagen envuelve todas las distintas versiones de una imagen y permite al desarrollador pedir una imagen específica que coincida con una colección de rasgo que se haya pasado. Las imágenes se pueden agregarse ni quitarse desde un recurso de imagen, sobre la marcha.

Para obtener más información sobre los activos de imagen, consulte de Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) documentación.

## <a name="combining-trait-collections"></a>Combinar colecciones de rasgo

Es otra función que un desarrollador puede realizar en las colecciones de rasgo sumar dos dicho tendrán como resultado de la colección combinada, donde los valores no especificados de una colección se reemplazan por los valores especificados en un segundo. Esto se realiza mediante la `FromTraitsFromCollections` método de la `UITraitCollection` clase.

Como se mencionó anteriormente, si cualquiera de los rasgos no está especificado en una de las colecciones de rasgo y se especifica en el otro, se establecerá el valor a la versión especificada. Sin embargo, si hay varias versiones de un determinado valor especificado, el valor de la última recopilación de rasgo será el valor que se utiliza.

## <a name="adaptive-view-controllers"></a>Controladores de la vista adaptable

Esta sección explica los detalles de cómo la vista y ver controladores de iOS han adoptado los conceptos de rasgos y clases de tamaño automáticamente sea más adaptable en las aplicaciones del desarrollador.

### <a name="split-view-controller"></a>Controlador de vista de división

Una de las clases de controlador de vista que ha cambiado el máximo partido de iOS 8 es el `UISplitViewController` clase. En el pasado, el desarrollador utilizaría a menudo un controlador de vista de división en la versión de iPad de la aplicación y, a continuación, tendría que proporcionar una versión completamente diferente de su jerarquía de vista para la versión de iPhone de la aplicación.

En iOS 8, la `UISplitViewController` clase está disponible en ambas plataformas (iPad y iPhone), que permite al desarrollador crear una jerarquía de controlador de vista que funcionará para iPhone e iPad.

Una vez un iPhone en horizontal, el controlador de vista de división presentará su vistas side-by-side, tal como lo haría cuando se muestre en un iPad.

### <a name="overriding-traits"></a>Reemplazar rasgos

Entornos de rasgo en cascada desde el contenedor primario hasta los contenedores secundarios, como se muestra en el gráfico siguiente muestra un controlador de vista de división en un iPad en la orientación horizontal:

 [![](unified-storyboards-images/cascadingclasses01.png "Un controlador de vista de división en un iPad en la orientación horizontal")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Puesto que el iPad tiene una clase de tamaño normal en las orientaciones horizontales y verticales, la vista de división se mostrará vistas maestra y de detalle.

En un iPhone, donde la clase de tamaño es compact en ambas orientaciones, el controlador de vista de división sólo muestra la vista de detalle, tal como se muestra a continuación:

 [![](unified-storyboards-images/cascadingclasses02.png "El controlador de vista de división sólo muestra la vista de detalle")](unified-storyboards-images/cascadingclasses02.png#lightbox)

En una aplicación donde el desarrollador desea mostrar una vista maestra y de detalles en un iPhone en la orientación horizontal, el desarrollador debe insertar un contenedor primario para el controlador de vista de división y reemplazar la colección de rasgo. Tal como se muestra en el gráfico siguiente:

 [![](unified-storyboards-images/cascadingclasses03.png "El desarrollador debe insertar un contenedor primario para el controlador de vista de división y reemplazar la colección de rasgo")](unified-storyboards-images/cascadingclasses03.png#lightbox)

A `UIView` se establece como el elemento primario del controlador de vista de división y el `SetOverrideTraitCollection` método se llama en la vista, el controlador de vista de la división de destino y pasar de una colección de rasgo de nuevo. La nueva colección de rasgo invalida la `HorizontalSizeClass`, si se establece en `Regular`, de modo que el controlador de vista de división mostrará vistas maestra y de detalles en un iPhone en la orientación horizontal.

Tenga en cuenta que la `VerticalSizeClass` se estableció en `unspecified`, que permite que la nueva colección de rasgo va a agregar a la colección de rasgo en el elemento primario, lo que produce un `Compact VerticalSizeClass` para el elemento secundario de controlador de vista de división.

### <a name="trait-changes"></a>Cambios de rasgo

Esta sección examinará, en detalle en cómo las colecciones de rasgo de transición cuando cambia el entorno de rasgo. Por ejemplo, cuando el dispositivo se gira de vertical a horizontal.

 [![](unified-storyboards-images/traittransitions01.png "El vertical a horizontal información general de los cambios de rasgo")](unified-storyboards-images/traittransitions01.png#lightbox)

En primer lugar, iOS 8 hace algún programa de instalación para prepararse para que la transición que se realicen. A continuación, el sistema anima el estado de transición. Por último, iOS 8 limpia los Estados de cualesquiera temporales necesarios durante la transición.

iOS 8 proporciona varias devoluciones de llamada que el programador puede utilizar para participar en el cambio de rasgo tal como se muestra en la tabla siguiente:

|Phase|Callback|Descripción|
|--- |--- |--- |
|Programa de instalación|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Este método se llamará al principio de un cambio de rasgo antes de una colección de rasgo queda establecida en su nuevo valor.</li><li>Se llama al método cuando ha cambiado el valor de la colección de rasgo pero antes de que realiza cualquier animación.</li></ul>|
|Animación|`WillTransitionToTraitCollection`|El Coordinador de transición que se pasa a este método tiene un `AnimateAlongside` propiedad que permite al desarrollador agregar las animaciones que se ejecutarán junto con las animaciones de forma predeterminada.|
|Limpieza|`WillTransitionToTraitCollection`|Proporciona un método para que los desarrolladores incluyen su propio código de limpieza después de que realiza la transición.|

El `WillTransitionToTraitCollection` método es muy útil para animar controladores de la vista junto con los cambios en la colección de rasgo. El `WillTransitionToTraitCollection` método solo está disponible en los controladores de vista ( `UIViewController`) y no en otros entornos de rasgo, como `UIViews`.

El `TraitCollectionDidChange` es muy útil para trabajar con el `UIView` (clase), donde el desarrollador desea actualizar la interfaz de usuario a medida que se modifican los rasgos.

### <a name="collapsing-the-split-view-controllers"></a>Contraer los controladores de la vista de división

Ahora vamos a echar un vistazo más de cerca a lo que ocurre cuando se contrae un controlador de vista de la división de un dos columnas a una vista de una columna. Como parte de este cambio, hay dos procesos que necesitan que se produzca:

-  De forma predeterminada, el controlador de vista de división usará el controlador de vista principal como la vista después de producirse el que se contrae. El desarrollador puede invalidar este comportamiento invalidando el `GetPrimaryViewControllerForCollapsingSplitViewController` método de la `UISplitViewControllerDelegate` y proporcionar cualquier controlador de vista que desea mostrar en el estado contraído.
-  El controlador de vista secundario tiene que combinar en el controlador principal de la vista. Por lo general el desarrollador no necesita realizar ninguna acción para este paso; el controlador de vista de división incluye el control automático de esta fase basándose en el dispositivo de hardware. Sin embargo, puede haber algunos casos especiales en los que el desarrollador desee interactuar con este cambio. Llamar a la `CollapseSecondViewController` método de la `UISplitViewControllerDelegate` permite al controlador de vista maestra que se mostrará cuando se produce el que se contrae, en lugar de la vista de detalles.


### <a name="expanding-the-split-view-controller"></a>Al expandir el controlador de vista de división

Ahora vamos a echar un vistazo más de cerca a lo que sucede cuando un controlador de vista de división se expande desde un estado contraído. Una vez más, hay dos fases que necesitan que se produzca:

-  En primer lugar, defina el nuevo controlador de vista principal. De forma predeterminada, el controlador de vista de división utilizará automáticamente el controlador de vista principal de la vista contraída. Una vez más, el desarrollador puede invalidar este comportamiento mediante la `GetPrimaryViewControllerForExpandingSplitViewController` método de la `UISplitViewControllerDelegate` .
-  Una vez que se ha elegido el controlador de vista principal, el controlador de vista secundario deben volver a crearse. Una vez más, el controlador de vista de división incluye el control automático de esta fase basándose en el dispositivo de hardware. El desarrollador puede invalidar este comportamiento mediante una llamada a la `SeparateSecondaryViewController` método de la `UISplitViewControllerDelegate` .


En un controlador de vista de división, el controlador de vista principal juega un papel en la expansión y contracción de las vistas mediante la implementación de la `CollapseSecondViewController` y `SeparateSecondaryViewController` métodos de la `UISplitViewControllerDelegate`. `UINavigationController` implementa estos métodos para insertar y extraer el controlador de la vista secundaria de automáticamente.

### <a name="showing-view-controllers"></a>Controladores de la vista que muestra

Otro cambio realizados por Apple IOS 8 está en el modo en que el desarrollador muestra los controladores de la vista. En el pasado, si la aplicación tenía un controlador de vista de hoja (por ejemplo, un controlador de vista de tabla), y el desarrollador mostraba uno diferente (por ejemplo, en respuesta a la pulsar de usuario en una celda), la aplicación llegarían hacia atrás por la jerarquía de controlador para el Controlador de vista de navegación y llame al método el `PushViewController` método con el que se muestre la nueva vista.

Esto presenta un acoplamiento muy estrecho entre el controlador de navegación y el entorno que se estaba ejecutando en. En iOS 8, Apple ha desacoplado esto proporcionando dos nuevos métodos:

-  `ShowViewController` : Se adapta para mostrar el nuevo controlador de vista en función de su entorno. Por ejemplo, en un `UINavigationController` simplemente inserta la nueva vista en la pila. En un controlador de vista de división, se presentará el nuevo controlador de vista a la izquierda como el nuevo controlador de vista principal. Si no hay ningún controlador de vista de contenedor está presente, la nueva vista se mostrará como un controlador de vista Modal.
-  `ShowDetailViewController` – Funciona de manera similar a `ShowViewController`, pero se implementa en un controlador de vista de división para reemplazar la vista de detalles con el nuevo controlador de vista que se pasan. Si el controlador de vista de división se contrae (como puede verse en un aplicación de iPhone), la llamada se redirigirán a la `ShowViewController` método y la nueva vista se mostrará como el controlador principal de la vista. De nuevo, si no hay ningún controlador de vista de contenedor está presente, la nueva vista se mostrará como un controlador de vista Modal.


Estos métodos empezando por el controlador de vista de la hoja de trabajo y recorrer la jerarquía de la vista hasta que encuentran el controlador de vista de contenedor derecho a administrar la presentación de la nueva vista.

Los desarrolladores pueden implementar `ShowViewController` y `ShowDetailViewController` en sus propios controladores de vista personalizada para obtener el mismo automatizada funcionalidad que `UINavigationController` y `UISplitViewController` proporciona.

### <a name="how-it-works"></a>Cómo funciona

En esta sección tomaremos un vistazo a cómo se implementan estos métodos en realidad en iOS 8. Primer Echemos un vistazo a la nueva `GetTargetForAction` método:

 [![](unified-storyboards-images/gettargetforaction.png "El nuevo método GetTargetForAction")](unified-storyboards-images/gettargetforaction.png#lightbox)

Este método recorre la cadena de jerarquía hasta que se encuentra el controlador de vista de contenedor correcto. Por ejemplo:

1.  Si un `ShowViewController` se llama al método, el primer controlador de vista de la cadena que implementa este método es el controlador de navegación, por lo que se usa como el elemento primario de la nueva vista.
1.  Si un `ShowDetailViewController` método se llamó en su lugar, el controlador de vista de división es el primer controlador de vista para implementarlo, por lo que se usa como el elemento primario.


El `GetTargetForAction` método funciona localizando un controlador de vista que implementa una acción determinada y, a continuación, solicitar ese controlador de vista si desea recibir esa acción. Puesto que este método es público, los desarrolladores pueden crear sus propios métodos personalizados que funcionan igual que integrado en `ShowViewController` y `ShowDetailViewController` métodos.

## <a name="adaptive-presentation"></a>Presentación adaptable

En iOS 8, Apple ha realizado presentaciones Popover ( `UIPopoverPresentationController`) adaptable así. Por lo que un controlador de vista de presentación de Popover automáticamente presentará una vista normal de Popover en una clase de tamaño normal, pero se mostrará pantalla completa en una clase de tamaño horizontalmente compact (como en un iPhone).

Para dar cabida a los cambios en el sistema unificado de guión gráfico, se ha creado un nuevo objeto de controlador para administrar los controladores de vista presentado: `UIPresentationController`. Este controlador se crea desde el momento en que el controlador de vista se presenta hasta que se cierra. Ya que es una clase de administración, se puede considerarse una superclase sobre el controlador de vista como responder a cambios en los dispositivos que afectan a View Controller (como la orientación) que pasan a ser cala nuevo en el controlador de vista controla el controlador de presentación.

Cuando el programador presenta un controlador de vista con el `PresentViewController` método, que se van a pasar la administración del proceso de presentación a `UIKit`. UIKit administra (entre otras cosas) el controlador correcto para el estilo que se va a crear, con la única excepción es cuando un controlador de vista tiene el estilo establecido en `UIModalPresentationCustom`. En este caso, la aplicación puede proporcionar es propia PresentationController en lugar de usar el `UIKit` controlador.

### <a name="custom-presentation-styles"></a>Estilos de presentación personalizado

Con un estilo de presentación personalizado, los desarrolladores tienen la opción de usar un dispositivo de presentación personalizado. Este controlador personalizado puede utilizarse para modificar la apariencia y el comportamiento de la vista es allied a.

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>Trabajar con clases de tamaño

El proyecto de Xamarin adaptable de fotos que se incluye en este artículo se proporciona un ejemplo de uso de clases de tamaño y adaptable controladores de la vista en una aplicación de interfaz unificada iOS 8.

Mientras la aplicación crea su interfaz de usuario completamente desde el código, en lugar de utilizar el Diseñador de IOS y creación de un guión gráfico unificado, se aplican las mismas técnicas. Más adelante en este artículo, le mostraremos cómo usar las clases de tamaño con un guión gráfico unificado y el diseñador en una aplicación Xamarin de iOS.

Ahora vamos a echar un vistazo más de cerca de cómo el proyecto de fotos adaptable implementa algunas de las características de clase de tamaño en iOS 8 para crear una aplicación adaptable.

### <a name="adapting-to-trait-environment-changes"></a>Adaptación a los cambios en el entorno rasgo

Cuando se ejecuta la aplicación fotos adaptable en un iPhone, cuando el usuario gira el dispositivo de vertical a horizontal, el controlador de vista de división mostrará la vista maestra y de detalles:

 [![](unified-storyboards-images/rotation.png "El controlador de vista de división se mostrará a tanto el patrón y vista de detalles, como se ve aquí")](unified-storyboards-images/rotation.png#lightbox)

Esto se logra mediante la invalidación del `UpdateConstraintsForTraitCollection` método View Controller y ajustar las restricciones en función del valor de la `VerticalSizeClass`. Por ejemplo:

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

Si el controlador de vista dividida en las fotos adaptable aplicación va de contraído a expandida, animaciones se agregan a las animaciones predeterminada invalidando el `WillTransitionToTraitCollection` método del controlador de vista. Por ejemplo:

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

Como se muestra anteriormente, la aplicación fotos adaptable fuerza el controlador de vista de división para mostrar los detalles y las vistas maestras cuando el dispositivo iPhone está en la vista horizontal.

Esto se consigue mediante el siguiente código en el controlador de vista:

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

### <a name="expanding-and-collapsing-the-split-view-controller"></a>Expandir y contraer el controlador de vista de división

Siguiente vamos a examinar cómo se implementa el comportamiento de expansión y contracción del controlador de vista dividida en Xamarin. En el `AppDelegate`, cuando se crea el controlador de vista de división, se asigna su delegado para controlar estos cambios:

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

El `SeparateSecondaryViewController` método comprueba si una foto se está mostrando y toma medidas en función de ese estado. Si no hay foto es está mostrando contrae el controlador de vista secundario para que se muestre el controlador de vista maestra.

El `CollapseSecondViewController` método se usa al expandir el controlador de vista de división para ver si existen cualquier fotos en la pila, si así contrae a esa vista.

### <a name="moving-between-view-controllers"></a>Mover entre los controladores de vista

A continuación, echemos un vistazo a cómo la aplicación fotos adaptable se mueve entre los controladores de la vista. En el `AAPLConversationViewController` clase cuando el usuario selecciona una celda de la tabla, el `ShowDetailViewController` método se llama para mostrar la vista de detalles:

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

### <a name="displaying-disclosure-indicators"></a>Mostrar indicadores de divulgación de información

En la aplicación de fotografía adaptable hay varios lugares donde se oculta o se muestra en función de los cambios en el entorno de rasgo de indicadores de divulgación de información. Esto se controla con el código siguiente:

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

Estas se implementan utilizando la `GetTargetViewControllerForAction` método descrito anteriormente en detalle.

Cuando los datos se están mostrando un controlador de vista de tabla, utiliza los métodos implementados por encima para ver si una inserción va a producirse y si desea o no mostrar u ocultar el indicador de divulgación de información según corresponda:

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

### <a name="new-showdetailtargetdidchangenotification-type"></a>Nueva `ShowDetailTargetDidChangeNotification` tipo

Apple ha incorporado un nuevo tipo de notificación para trabajar con clases de tamaño y los entornos de rasgo desde dentro de un controlador de vista de división, `ShowDetailTargetDidChangeNotification`. Esta notificación se envía cada vez que cambia la vista de detalle para un controlador de vista de la división de destino, por ejemplo, cuando el controlador se expande o contrae.

La aplicación de fotos adaptable utiliza esta notificación para actualizar el estado del indicador de divulgación de información cuando cambia el controlador de vista de detalle:

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

Eche un vistazo más de cerca a la aplicación de fotos adaptable para ver todas las formas que las clases de tamaño, colecciones de rasgo y adaptable controladores de la vista pueden utilizarse para crear fácilmente una aplicación unificada en Xamarin.iOS.

## <a name="unified-storyboards"></a>Guiones gráficos unificados

Nuevo para iOS 8, guiones gráficos unificado permiten a los programadores crear uno, unificado archivo del guión gráfico que se pueden mostrar en los dispositivos iPhone y iPad estableciendo como destino varias clases de tamaño. Con guiones gráficos unificado, el desarrollador escribe menos código específico de la interfaz de usuario y tiene solo una interfaz diseño para crear y mantener.

Las ventajas principales de los guiones unificado son:

-  Utilizar el mismo archivo de guión gráfico para iPhone e iPad.
-  Implementar con versiones anteriores a iOS 6 e iOS 7.
-  Obtener una vista previa el diseño para diferentes dispositivos, orientaciones y versiones del sistema operativo desde el Diseñador de iOS de Xamarin.

Esta característica es totalmente compatible en Visual Studio para Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>Habilitar clases de tamaño

De forma predeterminada, cualquier nuevo proyecto Xamarin.iOS nos pedirá cambiar el tamaño de las clases. Para usar las clases de tamaño y Segues adaptable dentro de un guión gráfico de un proyecto anterior, primero se convierte al formato de guión gráfico unificado de Xcode 6 en el Diseñador de iOS.

Para hacer esto abre el guión gráfico que se conviertan en el diseñador y verificación de iOS la **utilizar clases de tamaño** casilla de verificación:

 [![](unified-storyboards-images/sizeclass01.png "La casilla de verificación de las clases de tamaño de uso")](unified-storyboards-images/sizeclass01.png#lightbox)

El Diseñador de iOS confirmará que el desarrollador desea convertir el formato del guión gráfico para usar clases de tamaño:

 [![](unified-storyboards-images/sizeclass02.png "El uso de alerta de clases de tamaño")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> **Tenga en cuenta**: diseño automático también debe comprobarse para que las clases de tamaño para que funcione correctamente.

### <a name="generic-device-types"></a>Tipos de dispositivos genéricos

Una vez que se ha convertido el guión gráfico para usar clases de tamaño, se volverá a en la superficie de diseño y la **vista como** dispositivo será genérico:

 [![](unified-storyboards-images/sizeclass03.png "Ver como un tipo de dispositivo genérico")](unified-storyboards-images/sizeclass03.png#lightbox)

Cuando se selecciona el tipo de dispositivo genérico, todos los controladores de la vista se ajustará a un cuadrado de 600 x 600. Este cuadrado representa tamaños de cualquier ancho y cualquier alto. Cuando el archivo iOS diseñador está en este modo, las modificaciones se aplicarán a todas las clases de tamaño.

El desarrollador también tiene la opción de ver la superficie de diseño como un iPhone:

 [![](unified-storyboards-images/sizeclass04.png "Ver la superficie de diseño como un iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

O bien, ver como un iPad:

 [![](unified-storyboards-images/sizeclass05.png "Ver la superficie de diseño como un iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Seleccione una clase de tamaño

Es el botón Selector de clase de tamaño en la esquina superior izquierda de la superficie de diseño (cerca de la lista desplegable Vista como). Permite al programador seleccionar qué clases de tamaño se está editando actualmente:

 [![](unified-storyboards-images/sizeclass06.png "Seleccione una clase de tamaño")](unified-storyboards-images/sizeclass06.png#lightbox)

El selector presenta la selección de la clase de tamaño como una cuadrícula de 3 x 3. Cada uno de los cuadrados en la cuadrícula representa una combinación de una clase de ancho y un alto. El cuadrado central selecciona la clase de cualquier tamaño de alto Width/Any (que es la vista predeterminada de un guión gráfico unificado). Cuando se selecciona esta casilla, el programador está editando el diseño predeterminado, que es heredado por todas las configuraciones.

El cuadrado de la esquina superior izquierda de la cuadrícula representa la clase de tamaño de Compact compacto de ancho/alto:

 [![](unified-storyboards-images/sizeclass07.png "La clase de tamaño de alto ancho Compact/compacto")](unified-storyboards-images/sizeclass07.png#lightbox)

Este modo se corresponde con un iPhone en la orientación horizontal. El cuadrado de la esquina inferior derecha de la cuadrícula representa la clase normal de tamaño de alto ancho/normal, que representa un iPad:

 [![](unified-storyboards-images/sizeclass08.png "La clase de tamaño de alto ancho Regular/normal")](unified-storyboards-images/sizeclass08.png#lightbox)

Para editar el diseño de un iPhone en la orientación vertical, seleccione el cuadrado de la esquina inferior izquierda. Representa la clase de tamaño de Compact normal de ancho/alto:

 [![](unified-storyboards-images/sizeclass09.png "La clase de tamaño de alto ancho Compact/normal")](unified-storyboards-images/sizeclass09.png#lightbox)

Haga clic en el cuadrado para seleccionarlo y la superficie de diseño se cambiará el tamaño de los controladores de vista para que coincida con la nueva selección:

 [![](unified-storyboards-images/sizeclass10.png "Cambia el tamaño de los controladores de vista para que coincida con la nueva selección tal como se muestra la superficie de diseño")](unified-storyboards-images/sizeclass10.png#lightbox)

Vea la sección de la clase de tamaño de este artículo para obtener más información sobre las clases de tamaño y cómo afectan al diseño de iPhone e iPad.

### <a name="adaptive-segue-types"></a>Adaptable desplazará tranquilamente tipos

Si el programador ha utilizado guiones gráficos antes, estará familiarizados con los tipos de segue existentes de **Push**, **Modal** y **Popover**. Cuando están habilitadas las clases de tamaño en un archivo de guión gráfico unificado, los siguientes adaptable desplazará tranquilamente tipos (que corresponde a la nueva API de controlador de vista descritos anteriormente) se ponen a disposición: **mostrar** y **mostrar detalle** .

> [!IMPORTANT]
> **Tenga en cuenta**: clases de tamaño cuando están habilitadas, cualquier existente segues will se convierten a los nuevos tipos.

Tome como ejemplo un iOS 8 aplicación que utiliza un guión gráfico unificado con un controlador de vista de división que tiene un menú de navegación de juego sencillo en la vista maestra. Si el usuario hace clic en un botón de menú, se debe mostrar controlador de vista del elemento seleccionado en la sección de detalles del controlador de vista de división cuando se ejecuta en un iPad. En un iPhone View Controller del elemento se deben insertar en la pila de navegación.

Para lograr este efecto, en el Diseñador de iOS control y haga clic en el botón y arrastre una línea para el controlador de vista que se mostrará. Cuando se suelta el botón del mouse, seleccione `Show Detail` en el menú emergente de tipo desplazará tranquilamente:

 [![](unified-storyboards-images/segue01.png "Seleccione Mostrar detalles en el menú emergente de tipo desplazará tranquilamente")](unified-storyboards-images/segue01.png#lightbox)

Se creará el nuevo segue entre el botón y el controlador de vista. Ahora ejecute la aplicación en el simulador de iPhone y se mostrará el menú principal:

 [![](unified-storyboards-images/segue02.png "El menú principal")](unified-storyboards-images/segue02.png#lightbox)

Haga clic en el **seleccione juego** botón y View Controller del elemento se insertarán en la pila de navegación:

 [![](unified-storyboards-images/segue03.png "Los elementos View Controller se insertarán en la pila de navegación, como se muestra")](unified-storyboards-images/segue03.png#lightbox)

Detener el simulador de iPhone y ejecutar la aplicación en el simulador iPad. Cambiar a la orientación horizontal y los principales menú aparece de nuevo:

 [![](unified-storyboards-images/segue04.png "El menú principal")](unified-storyboards-images/segue04.png#lightbox)

De nuevo, haga clic en el **seleccione juego** botón y el controlador de vista del elemento se muestra en la sección de detalles del controlador de vista de división:

 [![](unified-storyboards-images/segue05.png "Los elementos de controlador de vista se muestra en la sección de detalles del controlador de vista de división")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Excluir un elemento de una clase de tamaño

Hay ocasiones en un elemento determinado (por ejemplo, una vista, Control o una restricción) no es necesario dentro de una clase específica de tamaño. Para excluir un elemento de una clase de tamaño, seleccione el elemento que desee excluir de la **superficie de diseño**. Desplácese hasta la parte inferior de la **Property Explorer** y haga clic en el **engranaje** menú desplegable. Seleccione la combinación de **ancho** y **alto** para excluir el elemento de:

[![](unified-storyboards-images/exclude-a.png "Seleccione la combinación de ancho y alto")](unified-storyboards-images/exclude-a.png#lightbox)

Un nuevo *caso de exclusión* se agregará al elemento en la parte inferior de la **Property Explorer**. A continuación, desactive la **instalado** casilla de verificación de la clase de tamaño determinado:

[![](unified-storyboards-images/exclude-b.png "Desactive la casilla de verificación instalado")](unified-storyboards-images/exclude-b.png#lightbox)

Cambiar la superficie de diseño para el ancho y alto que se excluyó el elemento, se ha quitado de la clase de tamaño especificado, pero no con todo el diseño de interfaz de usuario:

 [![](unified-storyboards-images/exclude02.png "Cambiar la superficie de diseño para el ancho y alto que el elemento se ha excluido de")](unified-storyboards-images/exclude02.png#lightbox)

Regreso a la clase de tamaño de cualquier alto Width/Any y el elemento todavía está en vigor:

 [![](unified-storyboards-images/exclude03.png "Regreso a la clase de tamaño de cualquier alto Width/Any")](unified-storyboards-images/exclude03.png#lightbox)

Cuando la aplicación se ejecuta en el simulador iPad, aparece el elemento:

 [![](unified-storyboards-images/exclude04.png "El elemento se muestra cuando la aplicación en ejecución en el simulador de iPad")](unified-storyboards-images/exclude04.png#lightbox)

Y cuando la aplicación se ejecuta en el simulador de iPhone, el elemento falta:

 [![](unified-storyboards-images/exclude05.png "El elemento que falta cuando la aplicación en ejecución en el simulador de iPhone")](unified-storyboards-images/exclude05.png#lightbox)

Para quitar un caso de exclusión de un elemento, basta con seleccionar el elemento en el **superficie de diseño**, desplácese hasta la parte inferior de la **Property Explorer** y haga clic en el  **-** botón junto al caso a quitar.

Para ver una implementación de guiones gráficos unificado, mire el `UnifiedStoryboard` Xamarin iOS 8 aplicación adjunta a este documento de ejemplo.

## <a name="dynamic-launch-screens"></a>Pantallas de inicio dinámica

El archivo de la pantalla de inicio se muestra como una pantalla de presentación mientras se iniciaba una aplicación de iOS para proporcionar comentarios al usuario que la aplicación es realmente puesta en marcha. Antes de iOS 8, el desarrollador tendría que incluir varios `Default.png` activos para cada dispositivo tipo, orientación y resolución de pantalla que se estaría ejecutando la aplicación en la imagen. Por ejemplo, `Default@2x.png`, `Default-Landscape@2x~ipad.png`, `Default-Portrait@2x~ipad.png`, etcetera.

Factorización nueva iPhone 6 e iPhone 6 Plus dispositivos (como el próximo Apple Watch) con todos los dispositivos iPad y iPhone existente, esto representa una matriz grande de diferentes tamaños, orientaciones y resoluciones de `Default.png` activos de imagen de pantalla de inicio deben creación y el mantenimiento. Además, estos archivos pueden ser bastante grandes y se "inundación" el paquete de aplicación de entrega, aumenta la cantidad de tiempo necesario para descargar la aplicación en iTunes App Store (posiblemente manteniéndola capaz de ser entregados a través de una red de telefonía móvil) y aumenta la cantidad de almacenamiento necesario en el dispositivo del usuario final.

Nuevo en iOS 8, el programador puede crear una única atómica `.xib` archivo en Xcode que usa clases de tamaño y diseño automático para crear un *dinámica pantalla iniciar* que funcionará para cada dispositivo, la resolución y la orientación. Esto no solo reduce la cantidad de trabajo requerida del desarrollador para crear y mantener todos los activos de imagen necesaria, pero reduce en gran medida el tamaño de paquete de la aplicación instalada.


Pantallas de inicio dinámica tiene las siguientes limitaciones y consideraciones:

 - Usar solo `UIKit` clases.
 - Utilizar una vista de raíz única que es un `UIView` o `UIViewController` objeto.
 - No conviene que todas las conexiones al código de la aplicación (no agregue **acciones** o **tomas**).
 - No agregue `UIWebView` objetos.
 - No use las clases personalizadas.
 - No use los atributos en tiempo de ejecución.

Con las instrucciones anteriores en mente, echemos un vistazo a la adición de una pantalla de inicio dinámica a un proyecto de iOS 8 Xamarin existente.

Haga lo siguiente:

1. Abra **Visual Studio para Mac** y cargar la **solución** para agregar a la pantalla de inicio dinámica.
2. En el **el Explorador de soluciones**, haga clic en el `MainStoryboard.storyboard` de archivos y seleccione **abrir con** > **Xcode interfaz generador**:

    [![](unified-storyboards-images/dls01.png "Abrir con el generador de interfaz de Xcode")](unified-storyboards-images/dls01.png#lightbox)
3. En Xcode, seleccione **archivo** > **New** > **archivo...** :

    [![](unified-storyboards-images/dls02.png "Seleccione el archivo / nuevo")](unified-storyboards-images/dls02.png#lightbox)
4. Seleccione **iOS** > **interfaz de usuario** > **iniciar pantalla** y haga clic en el **siguiente** botón:

    [![](unified-storyboards-images/dls03.png "Seleccione iOS / interfaz de usuario / pantalla Inicio")](unified-storyboards-images/dls03.png#lightbox)
5. Asignar nombre al archivo `LaunchScreen.xib` y haga clic en el **crear** botón:

    [![](unified-storyboards-images/dls04.png "Nombre del archivo LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. Editar el diseño de la pantalla de inicio agregando elementos gráficos y usando las restricciones de diseño para colocarlos de dispositivos determinados, orientaciones y tamaños de pantalla:

    [![](unified-storyboards-images/dls05.png "Editar el diseño de la pantalla de inicio")](unified-storyboards-images/dls05.png#lightbox)
7. Guardar los cambios realizados en `LaunchScreen.xib`.
8. Seleccione el **aplicaciones destino** y **General** ficha:

    [![](unified-storyboards-images/dls06.png "Seleccione el destino de las aplicaciones y en la ficha General")](unified-storyboards-images/dls06.png#lightbox)
9. Haga clic en el **elegir Info.plist** botón, seleccione la `Info.plist` para la aplicación de Xamarin y haga clic en el **elegir** botón:

    [![](unified-storyboards-images/dls07.png "Seleccione el Info.plist de la aplicación de Xamarin")](unified-storyboards-images/dls07.png#lightbox)
10. En el **iconos de aplicación e iniciar imágenes** sección, abra el **archivo de pantalla de inicio** lista desplegable y elija la `LaunchScreen.xib` creado anteriormente:

    [![](unified-storyboards-images/dls08.png "Elija la LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. Guarde los cambios en el archivo y vuelva a Visual Studio para Mac.
12. Espere a que Visual Studio para Mac finalizar la sincronización de cambios con Xcode.
13. En el **el Explorador de soluciones**, haga doble clic en el **recursos** carpeta y seleccione **agregar** > **agregar archivos...** :

    [![](unified-storyboards-images/dls09.png "Seleccione agrega / agrega archivos...")](unified-storyboards-images/dls09.png#lightbox)
14. Seleccione el `LaunchScreen.xib` archivo creado anteriormente y haga clic en el **abiertos** botón:

    [![](unified-storyboards-images/dls10.png "Seleccione el archivo LaunchScreen.xib")](unified-storyboards-images/dls10.png#lightbox)
15. Compile la aplicación.

### <a name="testing-the-dynamic-launch-screen"></a>Probar la pantalla de inicio dinámica

En Visual Studio para Mac, seleccione el simulador de Retina iPhone 4 y ejecute la aplicación. Se mostrará la pantalla de inicio dinámica en el formato correcto y orientación:

[![](unified-storyboards-images/dls11.png "La pantalla de inicio dinámica aparece en la orientación vertical")](unified-storyboards-images/dls11.png#lightbox)

Detener la aplicación en Visual Studio para Mac y seleccione un dispositivo de iOS 8 iPad. Ejecute la aplicación y la pantalla de inicio se dará formato correctamente para este dispositivo y orientación:

[![](unified-storyboards-images/dls12.png "La pantalla de inicio dinámica aparece en la orientación horizontal")](unified-storyboards-images/dls12.png#lightbox)

Vuelva a Visual Studio para Mac y detener la aplicación se ejecute.

### <a name="working-with-ios-7"></a>Trabajar con iOS 7

Para mantener la compatibilidad con iOS 7, simplemente incluyen el habitual `Default.png` activos como es habitual en la aplicación de iOS 8 de la imagen. iOS devolverá al comportamiento anterior y usar dichos archivos como la pantalla de inicio cuando se ejecuta en un dispositivo iOS 7.

Para ver una implementación de una pantalla de inicio dinámico en Xamarin, mire el [pantallas de inicio dinámica](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/) aplicación de iOS 8 adjunta a este documento de ejemplo.

## <a name="summary"></a>Resumen

En este artículo se tardó un vistazo rápido a las clases de tamaño y cómo afectan al diseño de los dispositivos iPhone y iPad. Explica cómo funcionan los rasgos, entornos de rasgo y colecciones de rasgo con las clases de tamaño para crear Interfaces unificado. Se tardaron breve vistazo adaptable controladores de la vista y cómo funcionan con las clases de tamaño dentro de las Interfaces unificado. Busca en implementar clases de tamaño e Interfaces unificado completamente desde el código de C# dentro de una aplicación de iOS 8 de Xamarin.

Por último, en este artículo se trata los conceptos básicos de creación de guiones gráficos unificado con el diseñador que funcionará en dispositivos iOS de iOS de Xamarin y crear una única pantalla Iniciar dinámico que se mostrará como la pantalla de inicio en todos los dispositivos iOS 8.

## <a name="related-links"></a>Vínculos relacionados

- [Adaptables fotos (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [Ejemplo de StoryboardIntro](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [Pantallas de inicio dinámica (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Diseños dinámicos en iOS8 - 2014 evolucionar (vídeo)](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
