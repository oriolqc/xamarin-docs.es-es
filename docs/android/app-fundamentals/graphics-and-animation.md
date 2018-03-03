---
title: "Gráficos y animación"
description: "Android proporciona un marco muy amplio y diverso para admitir las animaciones y los gráficos 2D. Este tema presenta estos marcos de trabajo y explica cómo crear gráficos personalizados y las animaciones para su uso en una aplicación Xamarin.Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 90a2eb219ae1189e7a48e60cde9761e3e9e93e0b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="graphics-and-animation"></a>Gráficos y animación

_Android proporciona un marco muy amplio y diverso para admitir las animaciones y los gráficos 2D. Este tema presenta estos marcos de trabajo y explica cómo crear gráficos personalizados y las animaciones para su uso en una aplicación Xamarin.Android._

<a name="Overview" />

## <a name="overview"></a>Información general

A pesar de que se ejecutan en dispositivos que son normalmente de limitada de energía, las aplicaciones móviles calificadas mayor a menudo tienen una experiencia de usuario sofisticadas (UX), junto con gráficos de alta calidad y las animaciones que proporcionan una apariencia intuitivo, con capacidad de respuesta y dinámica. Como las aplicaciones móviles get cada vez más sofisticados, los usuarios han comenzado a esperar más de las aplicaciones.

Por suerte para nosotros, plataformas móviles modernas tienen marcos muy eficaces para crear animaciones sofisticadas y gráficos personalizados conservando la facilidad de uso. Esto permite a los programadores agregar interactividad completa con muy poco esfuerzo.

Marcos de la API de la interfaz de usuario en Android aproximadamente se pueden dividir en dos categorías: gráficos y animación.

Gráficos más se dividen en distintos enfoques para realizar gráficos 2D y 3D. Gráficos 3D están disponibles a través de un número de marcos como OpenGL ES (una versión específica móvil de OpenGL) y marcos de terceros, como MonoGame (un multiplataforma Kit de herramientas compatible con el Kit de herramientas XNA) integrados. Aunque los gráficos 3D no están dentro del ámbito de este artículo, examinaremos las técnicas de dibujo 2D integradas.

Android proporciona dos API diferente para la creación de gráficos 2D. Uno es un enfoque declarativo de alto nivel y el otro es una API de bajo nivel mediante programación:

-   **Puede dibujar recursos** &ndash; se usan para crear gráficos personalizados mediante programación o (más normalmente) mediante la incorporación de instrucciones de dibujo en archivos XML. Puede dibujar recursos normalmente se definen como archivos XML que contienen instrucciones o acciones para Android representar un gráfico 2D. 

-   **Lienzo** &ndash; se trata de una API de nivel bajo que implica dibujar directamente en un mapa de bits subyacente. Proporciona un control muy específico sobre lo que se muestra. 

Además de estas técnicas de gráficos 2D, Android también proporciona varias formas de crear animaciones:

-   **Puede dibujar animaciones** &ndash; Android también admite animaciones fotograma a fotograma conocidas como *animación pueden dibujar*. Se trata de la API de animación más sencilla. Android secuencialmente carga y muestra los recursos pueden dibujar en secuencia (igual que un dibujos animados).

-   **Ver animaciones** &ndash; *animaciones de vista* son la animación original de la API de Android y están disponibles en todas las versiones de Android. Esta API está limitada, ya que solo funcionará con objetos de vista y solo se pueden realizar transformaciones simples en esas vistas.
    Vista animaciones normalmente se definen en archivos XML que se encuentran en el `/Resources/anim` carpeta.

-   **Propiedad animaciones** &ndash; 3.0 Android introdujo un nuevo conjunto de API de animación conocido como *animaciones de propiedad*. Estas API nueva introdujo un sistema flexible y extensible que puede usarse para animar las propiedades de cualquier objeto, no solo ver objetos. Esta flexibilidad permite las animaciones que va a encapsular en distintas clases que harán que el uso compartido más fácil de código.


Las animaciones de vista son más adecuadas para las aplicaciones que deben admitir anteriores Android preliminar 3.0 de la API (API nivel 11). En caso contrario, las aplicaciones deben utilizar la API más recientes de animación de propiedad por los motivos que se mencionaron anteriormente.

Todos estos marcos son viables opciones, sin embargo siempre que sea posible, preferencias deberían proporcionarse a animaciones de propiedad, ya que es una API para trabajar con más flexible. Permiten animaciones de propiedad para que la lógica de animación que va a encapsular en distintas clases que se obtiene un código más fácil de uso compartido y simplifica el mantenimiento del código.


## <a name="accessibility"></a>Accesibilidad

Gráficos y las animaciones ayudan a crear aplicaciones Android atractiva y divertida a utilizar; Sin embargo, es importante recordar que algunos tipos de interacción se producen a través de screenreaders, dispositivos de entrada alternativos, o con zoom asistida.
Además, algunos tipos de interacción pueden producirse sin capacidades de audio.

Las aplicaciones son más útiles en estas situaciones, si ha sido diseñados teniendo en cuenta la accesibilidad: proporciona sugerencias y asistencia de navegación en la interfaz de usuario y asegurarse de que no hay contenido de texto o las descripciones de los elementos de la interfaz de usuario gráficos.

Hacer referencia a [Accessibility Guide de Google](http://developer.android.com/guide/topics/ui/accessibility/) para obtener más información acerca de cómo utilizar la accesibilidad de Android API.


<a name="2D_Graphics" />

## <a name="2d-graphics"></a>Gráficos 2D

Puede dibujar recursos son una técnica popular en aplicaciones de Android. Igual que con otros recursos, puede dibujar recursos declarativos &ndash; están definidos en archivos XML. Este enfoque permite una separación clara de código de recursos. Esto puede simplificar desarrollo y mantenimiento porque no es necesario cambiar el código para actualizar o cambiar los gráficos en una aplicación Android. Sin embargo, mientras que los recursos con estas características son útiles para muchos requisitos gráficos simples y comunes, que carecen de la eficacia y control de la API del lienzo.

La otra técnica, usando la [lienzo](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/) de objetos, es muy similar a otros marcos de API tradicionales como System.Drawing o dibujo de núcleo de iOS. Uso del objeto de lienzo proporciona el máximo control de gráficos 2D cómo se crean. Es adecuado para situaciones en un recurso con estas características no funcionarán o será difícil trabajar con. Por ejemplo, puede ser necesario dibujar un control personalizado de control deslizante cuya apariencia cambiará basándose en cálculos relacionados con el valor del control deslizante.

Vamos a examinar los recursos pueden dibujar en primer lugar. Son más sencillas y cubren los casos más comunes de dibujo personalizados.

<a name="Drawable Resources" />

### <a name="drawable-resources"></a>Puede dibujar recursos

Recursos con estas características se definen en un archivo XML en el directorio `/Resources/drawable`. A diferencia de incrustar PNG o de JPEG, no es necesario proporcionar versiones específicas de la densidad de recursos con estas características.
En tiempo de ejecución, una aplicación Android cargará estos recursos y siga las instrucciones contenidas en estos archivos XML para crear gráficos 2D.
Android define varios tipos de recursos con estas características:

-   [ShapeDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; se trata de un objeto se puede dibujar que dibuja una forma geométrica primitiva y se aplica un conjunto limitado de efectos gráficos de esa forma. Son muy útiles para acciones como la personalización de botones o establecer el fondo de TextViews. Vemos un ejemplo de cómo usar un `ShapeDrawable` más adelante en este artículo.

-   [StateListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; se trata de un recurso puede dibujar que cambiarán en función del estado de un control de widget de apariencia. Por ejemplo, un botón puede cambiar su apariencia dependiendo de si está presionado o no.

-   [LayerDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; este recurso con estas características que se apilarán varios otros drawables uno encima de otro. Un ejemplo de un *LayerDrawable* se muestra en la captura de pantalla siguiente:

    ![Ejemplo de LayerDrawable](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; se trata de un *LayerDrawable* pero con una diferencia. A *TransitionDrawable* puede animar una capa aparece encima de otro.

-   [LevelListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; es muy similar a un *StateListDrawable* en que se mostrará una imagen basada en determinadas condiciones. Sin embargo, a diferencia de un *StateListDrawable*, *LevelListDrawable* muestra una imagen basada en un valor entero. Un ejemplo de un *LevelListDrawable* sería mostrar el nivel de una señal de Wi-Fi. Como el nivel de seguridad de los cambios de la señal de Wi-Fi, la puede dibujar que se muestra cambiará en consecuencia.

-   [ScaleDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; como su nombre implica, estos Drawables proporcionan tanto escala y el recorte de funcionalidad. El *ScaleDrawable* escalará otro puede dibujar, mientras el *ClipDrawable* ajustará Drawable otro.

-   [InsetDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; este puede dibujar se aplicarán márgenes en los lados de otro recurso con estas características. Se usa cuando una vista tiene un fondo que sea menor que los límites reales de la vista.

-   XML [BitmapDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; este archivo es un conjunto de instrucciones, en XML, que se va a realizar en un mapa de bits real. Algunas acciones que puede llevar a cabo Android son segmentación, simulación y suavizado. Uno de los usos muy común de este es un mapa de bits en el fondo de un diseño en mosaico.


#### <a name="drawable-example"></a>En el ejemplo se puede dibujar

Echemos un vistazo a un ejemplo rápido de cómo crear una con gráfico 2D un `ShapeDrawable`. Un `ShapeDrawable` puede definir uno de las cuatro formas básicas: rectángulo, elipse, línea y anillo. También es posible aplicar efectos básicos, como el tamaño, color y degradado. El siguiente código XML es un `ShapeDrawable` que pueden encontrarse en el *AnimationsDemo* proyecto complementaria (en el archivo `Resources/drawable/shape_rounded_blue_rect.xml`).
Define un rectángulo con un fondo degradado de color púrpura y esquinas redondeadas:

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
<!-- Specify a gradient for the background -->
<gradient android:angle="45"
          android:startColor="#55000066"
          android:centerColor="#00000000"
          android:endColor="#00000000"
          android:centerX="0.75" />

<padding android:left="5dp"
          android:right="5dp"
          android:top="5dp"
          android:bottom="5dp" />

<corners android:topLeftRadius="10dp"
          android:topRightRadius="10dp"
          android:bottomLeftRadius="10dp"
          android:bottomRightRadius="10dp" />
</shape>
```

Podemos hacer referencia a este recurso con estas características mediante declaración en un diseño o en otro Drawable tal y como se muestra en el siguiente código XML:

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="#33000000">
    <TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:layout_centerInParent="true"
              android:background="@drawable/shape_rounded_blue_rect"
              android:text="@string/message_shapedrawable" />
</RelativeLayout>
```

También puede dibujar recursos pueden aplicarse mediante programación. El siguiente fragmento de código muestra cómo establecer mediante programación el fondo de un control TextView:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Para ver este aspecto, ejecute el *AnimationsDemo* del proyecto y seleccione el elemento forma puede dibujar en el menú principal. Deberíamos ver algo similar a la captura de pantalla siguiente:

![TextView con un fondo personalizado, puede dibujar con un esquinas redondeadas y degradado](graphics-and-animation-images/image1.png)

Para obtener más información acerca de los elementos XML y la sintaxis de recursos con estas características, consulte [documentación de Google](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).

<a name="Using the Canvas Drawing API" />

### <a name="using-the-canvas-drawing-api"></a>Mediante la API de dibujo de lienzo

Drawables son eficaces, pero tiene sus limitaciones. Algunas cosas son no es posible o muy compleja (por ejemplo: aplicar un filtro a una imagen que se tomó una cámara en el dispositivo). Sería muy difícil de reducción de ojos rojos se aplican mediante el uso de un recurso con estas características.
En su lugar, la API de lienzo permite que una aplicación tenga un control muy específico para cambiar los colores en una parte específica de la imagen de forma selectiva.

Una clase que se utiliza normalmente con el lienzo es el [Paint](https://developer.xamarin.com/api/type/Android.Graphics.Paint/) clase. Esta clase contiene información de color y estilo sobre cómo dibujar. Se utiliza para proporcionar cosas como un color y transparencia.

Utiliza la API de lienzo el *modelo de pintor* para dibujar gráficos 2D.
Las operaciones se aplican en los niveles sucesivos uno sobre otro. Cada operación explica algunas áreas del mapa de bits subyacente. Cuando el área superpone a un área de pintura anteriormente, la nueva pintura se parcial o completamente interferir con la antigua. Se trata de la misma manera que otras API de dibujo como System.Drawing y gráficos de núcleo de iOS trabajo.

Hay dos maneras de obtener un `Canvas` objeto. Trata de la primera forma de definir un [mapa de bits](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) objeto y, a continuación, crear instancias de un `Canvas` objeto con él. Por ejemplo, el siguiente fragmento de código crea un nuevo lienzo con un mapa de bits subyacente:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

La otra forma de obtener un `Canvas` objeto es por la [OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/) método de devolución de llamada que se proporciona el [vista](https://developer.xamarin.com/api/type/Android.Views.View/) clase base. Android llama a este método cuando decida una vista debe dibujarse a sí mismo y pasa un `Canvas` objeto para trabajar con la vista.

La clase de lienzo expone métodos para proporcionar mediante programación las instrucciones de dibujo. Por ejemplo:

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash; rellena el mapa de bits del lienzo completo con la pintura especificada.

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash; dibuja la forma geométrica especificada mediante la pintura especificada.

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash; dibuja el texto en el lienzo con el color especificado. El texto se dibuja en la ubicación `x,y` .


<a name="Drawing with the Canvas API" />

#### <a name="drawing-with-the-canvas-api"></a>Dibujar con el lienzo de API

Veamos un ejemplo de la API de lienzo en acción. El fragmento de código siguiente muestra cómo dibujar una vista:

```csharp
public class MyView : View
{
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        Paint green = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0x99, 0xcc, 0),
        };
        green.SetStyle(Paint.Style.FillAndStroke);

        Paint red = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0xff, 0x44, 0x44)
        };
        red.SetStyle(Paint.Style.FillAndStroke);

        float middle = canvas.Width * 0.25f;
        canvas.DrawPaint(red);
        canvas.DrawRect(0, 0, middle, canvas.Height, green);
    }
}
```

Este código anterior crea primero una operación de dibujo rojo y un objeto de paint verde. Rellena el contenido del lienzo de rojo y, a continuación, indica el lienzo para dibujar un rectángulo verde que es el 25% del ancho del lienzo. Puede ver un ejemplo de esto en `AnimationsDemo` proyecto que se incluye con el código fuente de este artículo. Al iniciar la aplicación y seleccionar el elemento de dibujo en el menú principal, se debería una pantalla similar al siguiente:

![Pantalla con paint rojo y los objetos de dibujo verde](graphics-and-animation-images/image3.png)

<a name="Animation" />

## <a name="animation"></a>Animación

Los usuarios como cosas que se mueven en sus aplicaciones. Las animaciones son una excelente manera de mejorar la experiencia del usuario de una aplicación y ayudar a destacarse. Las animaciones recomendadas son las que los usuarios no tenga en cuenta porque se sienten naturales. Android proporciona la siguiente tres API para animaciones:

-   **Ver animación** &ndash; se trata de la API original. Estas animaciones están asociadas a una vista específica y pueden realizar transformaciones simples en el contenido de la vista. Debido a su simplicidad, esta API siendo útil para cosas como animaciones alfabéticos, rotaciones y así sucesivamente.

-   **Animación de la propiedad** &ndash; animaciones de propiedad se introdujeron en Android 3.0. Permiten a una aplicación animar prácticamente cualquier cosa. Las animaciones de propiedad se pueden utilizar para cambiar cualquier propiedad de cualquier objeto, incluso si ese objeto no está visible en la pantalla.

-   **Puede dibujar animación** &ndash; esto afecta a un recurso puede dibujar especial que se usa para aplicar una animación muy sencilla a los diseños.

En general, animación de la propiedad es el sistema preferido para utilizar como es más flexible y proporciona más características.

<a name="View Animations" />

### <a name="view-animations"></a>Animaciones de vista

Las animaciones de vista están limitadas a vistas y solo pueden realizar animaciones en valores como iniciar y dos puntos finales, tamaño, rotación y transparencia.
Estos tipos de animaciones se conocen normalmente como *animaciones de interpolaciones*. Se pueden definir dos maneras de ver animaciones &ndash; mediante programación en el código o mediante el uso de archivos XML. Archivos XML son la mejor manera de declarar las animaciones de vista, ya que son más legible y fácil de mantener.

Los archivos XML de animación se almacenará en la `/Resources/anim` directorio de un proyecto de Xamarin.Android. Como el elemento raíz, este archivo debe tener uno de los siguientes elementos:

-   `alpha` &ndash; Una animación de fundido de entrada o Fundido de salida.

-   `rotate` &ndash; Una animación de giro.

-   `scale` &ndash; Una animación de cambio de tamaño.

-   `translate` &ndash; Un movimiento horizontal o vertical.

-   `set` &ndash; Un contenedor que puede contener uno o varios de los demás elementos de la animación.

De forma predeterminada, todas las animaciones en un archivo XML se aplicarán al mismo tiempo. Para aumentar las animaciones se producen secuencialmente, establezca el `android:startOffset` atributo en uno de los elementos definidos anteriormente.

Es posible modificar la tasa de cambio en una animación mediante un *interpolador*. Un interpolador permite a los efectos de animación a accelerated, repetirse o desacelerará arroje curva. El marco de trabajo Android proporciona varios interpoladores de fábrica, como (pero sin limitarse a):

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; Estos interpoladores aumentar o disminuir la tasa de cambio en una animación.

-   `BounceInterpolator` &ndash; el cambio de devoluciones de mensajes al final.

-   `LinearInterpolator` &ndash; la tasa de cambios es constante.


El siguiente XML muestra un ejemplo de un archivo de animación que combina algunos de estos elementos:

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android=http://schemas.android.com/apk/res/android
     android:shareInterpolator="false">

    <scale android:interpolator="@android:anim/accelerate_decelerate_interpolator"
           android:fromXScale="1.0"
           android:toXScale="1.4"
           android:fromYScale="1.0"
           android:toYScale="0.6"
           android:pivotX="50%"
           android:pivotY="50%"
           android:fillEnabled="true"
           android:fillAfter="false"
           android:duration="700" />

    <set android:interpolator="@android:anim/accelerate_interpolator">
        <scale android:fromXScale="1.4"
               android:toXScale="0.0"
               android:fromYScale="0.6"
               android:toYScale="0.0"
               android:pivotX="50%"
               android:pivotY="50%"
               android:fillEnabled="true"
               android:fillBefore="false"
               android:fillAfter="true"
               android:startOffset="700"
               android:duration="400" />

        <rotate android:fromDegrees="0"
                android:toDegrees="-45"
                android:toYScale="0.0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fillEnabled="true"
                android:fillBefore="false"
                android:fillAfter="true"
                android:startOffset="700"
                android:duration="400" />
    </set>
</set>
```

Esta animación llevará a cabo todas las animaciones de forma simultánea. La primera animación de escala se estire la imagen horizontalmente y reducir su verticalmente, y, a continuación, la imagen se simultáneamente girados 45 grados a la izquierda y reducir, desaparece de la pantalla.

La animación se puede aplicar mediante programación a una vista por lo que infla la animación y, a continuación, aplicarlo a una vista. Android proporciona la clase auxiliar `Android.Views.Animations.AnimationUtils` que aumentar un recurso de animación y devolver una instancia de `Android.Views.Animations.Animation`. Este objeto se aplica a una vista mediante una llamada a `StartAnimation` y pasando el `Animation` objeto. El fragmento de código siguiente muestra un ejemplo de esto:

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Ahora que tenemos unos conocimientos básicos sobre cómo funcionan las animaciones de vista, permite pasar a las animaciones de propiedad.

<a name="Property Animations" />

### <a name="property-animations"></a>Animaciones de propiedad

Animadores de propiedad son una nueva API que se introdujo en Android 3.0.
Proporcionan una API más extensible que puede usarse para animar cualquier propiedad en cualquier objeto.

Todas las animaciones de propiedad se crean instancias de la [animación](https://developer.xamarin.com/api/type/Android.Animation.Animator/) subclase. Las aplicaciones no utilizan esta clase directamente, en su lugar, usan uno de sus subclases:

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash; esta clase es la clase más importante en la API de animación completo de propiedades. Calcula los valores de propiedades que deben cambiarse. El `ViewAnimator` no directamente actualizar dichos valores; en su lugar, genera eventos que pueden usarse para actualizar objetos animados.

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash; esta clase es una subclase de `ValueAnimator` . Está diseñado para simplificar el proceso de animación de objetos al aceptar un objeto de destino y una propiedad que se va a actualizar.

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash; esta clase es responsable de la coordinación de cómo las animaciones se ejecutan en la relación entre sí. Las animaciones pueden ejecutar simultáneamente, secuencialmente o con un retraso especificado entre ellas.


*Evaluadores de* son clases especiales que se utilizan por animadores para calcular los nuevos valores durante una animación. De fábrica, Android proporciona los evaluadores siguientes:

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash; calcula los valores de propiedades de entero.

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash; calcula los valores de propiedades de tipo float.

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash; calcula los valores de propiedades de color.

Si la propiedad que se anima no es un `float`, `int` o color, las aplicaciones pueden crear su propio evaluador implementando la `ITypeEvaluator` interfaz. (Implementación evaluadores personalizadas queda fuera del ámbito de este tema.)

#### <a name="using-the-valueanimator"></a>Uso de la ValueAnimator

Hay dos partes a cualquier animación: calcular valores animados y, a continuación, establecer los valores en las propiedades en un objeto. 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) solo calculará los valores, pero no funcionará en objetos directamente. En su lugar, se actualizan los objetos dentro de los controladores de eventos que se invocará durante la duración de la animación. Este diseño permite varias propiedades que se actualizarán desde un valor animado.

Obtener una instancia de `ValueAnimator` llamando a uno de los siguientes métodos de fábrica:

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

Una vez hecho esto, el `ValueAnimator` instancia debe tener su duración establecida y, a continuación, puede iniciarse. En el ejemplo siguiente se muestra cómo animar un valor entre 0 y 1 en el intervalo de 1000 milisegundos:

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Pero sí mismo, el fragmento de código anterior no resulta muy útil &ndash; se ejecutará la animación pero no hay ningún destino para el valor actualizado. La `Animator` clase generará el evento de actualización cuando decide que es necesario informar a los agentes de escucha de un nuevo valor. Las aplicaciones pueden proporcionar un controlador de eventos para responder a este evento, como se muestra en el siguiente fragmento de código:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

animator.Update += (object sender, ValueAnimator.AnimatorUpdateEventArgs e) =>
{
    int newValue = (int) e.Animation.AnimatedValue;
    // Apply this new value to the object being animated.
    myObj.SomeIntegerValue = newValue;
};
```

Ahora que tenemos una descripción de `ValueAnimator`, permite obtener más información acerca de la `ObjectAnimator`.

#### <a name="using-the-objectanimator"></a>Uso de la ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) es una subclase de `ViewAnimator` que combina el cálculo de motor y el valor de tiempo de la `ValueAnimator` con la lógica necesaria para la conexión de controladores de eventos. El `ValueAnimator` requiere aplicaciones para conectarlo explícitamente un controlador de eventos &ndash; `ObjectAnimator` se encargará de este paso para nosotros.

La API de `ObjectAnimator` es muy similar a la API de `ViewAnimator`, pero requiere que proporcione el objeto y el nombre de la propiedad que se va a actualizar. En el ejemplo siguiente se muestra un ejemplo del uso `ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Como puede ver en el fragmento de código anterior, `ObjectAnimator` puede reducir y simplificar el código que es necesario animar un objeto.

<a name="Drawable Animations" />

### <a name="drawable-animations"></a>Puede dibujar animaciones

La API de animación final es la API de animación con estas características. Puede dibujar animaciones cargar una serie de recursos puede dibujar uno tras otro y mostrarlos de forma secuencial, similar a un dibujos animados flip-it.

Recursos con estas características se definen en un archivo XML que tiene un `<animation-list>` elemento como el elemento raíz y una serie de `<item>` elementos que definen cada fotograma de la animación. Este archivo XML se almacena en la `/Resource/drawable` carpeta de la aplicación. El siguiente código XML es un ejemplo de una animación con estas características:

```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/asteroid01" android:duration="100" />
  <item android:drawable="@drawable/asteroid02" android:duration="100" />
  <item android:drawable="@drawable/asteroid03" android:duration="100" />
  <item android:drawable="@drawable/asteroid04" android:duration="100" />
  <item android:drawable="@drawable/asteroid05" android:duration="100" />
  <item android:drawable="@drawable/asteroid06" android:duration="100" />
</animation-list>
```

Esta animación se ejecutará a través de seis fotogramas. El `android:duration` atributo declara cuánto tiempo se mostrará cada fotograma. El fragmento de código siguiente muestra un ejemplo de creación de una animación puede dibujar e iniciarlo cuando el usuario hace clic en un botón en la pantalla:

```csharp
AnimationDrawable _asteroidDrawable;

protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    _asteroidDrawable = (Android.Graphics.Drawables.AnimationDrawable)
    Resources.GetDrawable(Resource.Drawable.spinning_asteroid);

    ImageView asteroidImage = FindViewById<ImageView>(Resource.Id.imageView2);
    asteroidImage.SetImageDrawable((Android.Graphics.Drawables.Drawable) _asteroidDrawable);

    Button asteroidButton = FindViewById<Button>(Resource.Id.spinAsteroid);
    asteroidButton.Click += (sender, e) =>
    {
        _asteroidDrawable.Start();
    };
}
```

En este punto hemos tratado los fundamentos de la animación API disponibles en una aplicación Android.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo introdujo una gran cantidad de nuevos conceptos y API del ayudar a agregar algunos gráficos a una aplicación Android. En primer lugar describe los gráficos 2D diversas API y muestra cómo Android permite a las aplicaciones dibujar directamente en la pantalla utilizando un objeto de lienzo. También hemos visto algunas técnicas alternativas que permitan gráficos que se pueden crear mediante declaración mediante archivos XML. A continuación, hemos explicado explicar antigua y nueva de la API para crear animaciones en Android.



## <a name="related-links"></a>Vínculos relacionados

- [Demostración de animación (ejemplo)](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [Animación y gráficos](http://developer.android.com/guide/topics/graphics/index.html)
- [Uso de animaciones para cobren vida a las aplicaciones móviles](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [Canvas](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [Animación de objeto](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [Animación de valor](https://developer.xamarin.comhttps://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
