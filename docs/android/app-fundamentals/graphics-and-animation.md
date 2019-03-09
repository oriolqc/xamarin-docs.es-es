---
title: Gráficos y animación
description: Android proporciona un marco de trabajo muy amplio y diverso para admitir las animaciones y gráficos 2D. Este tema presenta estos marcos de trabajo y explica cómo crear gráficos personalizados y las animaciones para su uso en una aplicación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7f4f7fd3af1e90307a84037f01ddf8e52b1ee030
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669055"
---
# <a name="graphics-and-animation"></a>Gráficos y animación

_Android proporciona un marco de trabajo muy amplio y diverso para admitir las animaciones y gráficos 2D. Este tema presenta estos marcos de trabajo y explica cómo crear gráficos personalizados y las animaciones para su uso en una aplicación de Xamarin.Android._


## <a name="overview"></a>Información general

A pesar de que se ejecutan en dispositivos que son tradicionalmente de energía limitados, las aplicaciones móviles más valoradas más alta a menudo tienen una experiencia de usuario sofisticadas (UX), junto con gráficos de alta calidad y las animaciones que proporcionan una idea intuitiva, con capacidad de respuesta, dinámica. Como las aplicaciones móviles get cada vez más sofisticados, los usuarios han comenzado a esperar más de las aplicaciones.

Por suerte para nosotros, modernas plataformas móviles tienen marcos muy eficaces para la creación de animaciones sofisticadas y gráficos personalizados conservando la facilidad de uso. Esto permite a los desarrolladores agregar interactividad con muy poco esfuerzo.

Marcos de la API de la interfaz de usuario de Android más o menos se pueden dividir en dos categorías: Gráficos y animación.

Gráficos más se dividen en diferentes enfoques para realizar gráficos 2D y 3D. Gráficos 3D están disponibles a través de un número de marcos como OpenGL ES (una versión específica móvil de OpenGL) y marcos de terceros como MonoGame (un multiplataforma Kit de herramientas compatible con el Kit de herramientas XNA) integrados. Aunque los gráficos 3D no están dentro del ámbito de este artículo, examinaremos las técnicas de dibujos 2D integradas.

Android proporciona dos API diferente para la creación de gráficos 2D. Uno es un enfoque declarativo de alto nivel y el otro es una API de bajo nivel mediante programación:

-   **Recursos drawable** &ndash; se utilizan para crear gráficos personalizados (por lo general) o mediante programación mediante la incorporación de instrucciones de dibujo en archivos XML. Recursos drawable se definen normalmente como archivos XML que contienen instrucciones o acciones para Android representar un gráfico 2D. 

-   **Lienzo** &ndash; se trata de una API de bajo nivel que se trata de dibujar directamente en un mapa de bits subyacente. Proporciona un control muy preciso sobre lo que se muestra. 

Además de estas técnicas de gráficos 2D, Android también proporciona varias formas de crear animaciones:

-   **Las animaciones drawable** &ndash; Android también es compatible con las animaciones de fotograma a fotograma que se conoce como *Drawable animación*. Se trata de la API de animación más sencilla. Android secuencialmente carga y muestra los recursos Drawable en secuencia (muy similar a un dibujo animado).

-   **Ver animaciones** &ndash; *ver animaciones* la animación original de la API de Android y están disponibles en todas las versiones de Android. Esta API está limitada porque solo funciona con objetos de vista y solo puede realizar transformaciones simples en esas vistas.
    Las animaciones de vista suelen definirse en archivos XML que se encuentran en el `/Resources/anim` carpeta.

-   **Animaciones de propiedad** &ndash; Android 3.0 introdujo un nuevo conjunto de API de animación, conocido como *animaciones de propiedad*. Estas API nuevo introdujo un sistema flexible y extensible que puede usarse para animar las propiedades de cualquier objeto, no solo ver objetos. Esta flexibilidad permite a las animaciones que va a encapsular en clases distintas que hará que sea más fácil compartir código.


Las animaciones de vista son más adecuadas para las aplicaciones que deben admitir más antiguos con anterioridad a Android 3.0 de la API (API nivel 11). En caso contrario, las aplicaciones deben usar la API más recientes de animación de propiedades por las razones que se han mencionado anteriormente.

Todos estos marcos son viables opciones, pero siempre que sea posible, debería dará preferencia a las animaciones de propiedad, ya que es una API más flexible para trabajar con. Se permiten animaciones de propiedad para que la lógica de animación que va a encapsular en clases distintas que hace que sea más fácil compartir código y simplifica el mantenimiento del código.


## <a name="accessibility"></a>Accesibilidad

Gráficos y animaciones ayudan a crear aplicaciones Android atractivo y diversión al usarlas; Sin embargo, es importante recordar que se producen algunos tipos de interacción a través de los lectores de pantalla, dispositivos de entrada alternativos, o con zoom asistido.
Además, algunos tipos de interacción pueden producirse sin capacidades de audio.

Las aplicaciones son más fáciles de utilizar en estas situaciones, si se han diseñado teniendo en mente la accesibilidad: proporcionar sugerencias y asistencia de navegación de la interfaz de usuario y asegurarse de que no hay contenido de texto o descripciones de los elementos visuales de la interfaz de usuario.

Consulte [Guía de accesibilidad de Google](https://developer.android.com/guide/topics/ui/accessibility/) para obtener más información sobre cómo usar la API de accesibilidad de Android.



## <a name="2d-graphics"></a>Gráficos 2D

Recursos drawable son una técnica popular en las aplicaciones de Android. Como con otros recursos, recursos Drawable son declarativos &ndash; se definen en archivos XML. Este enfoque permite una separación limpia de código de los recursos. Esto puede simplificar el desarrollo y mantenimiento porque no es necesario cambiar el código para actualizar o cambiar los gráficos en una aplicación de Android. Sin embargo, mientras los recursos Drawable son útiles para muchos requisitos gráficos simples y comunes, carecen de la eficacia y control de la API de Canvas.

La técnica de otra, mediante el [lienzo](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/) de objetos, es muy similar a otros marcos API tradicionales como System.Drawing o dibujo del núcleo de iOS. Uso del objeto de lienzo proporciona el mayor control de gráficos 2D cómo se crean. Es adecuado para situaciones en un recurso con estas características no funcionarán o será difícil trabajar con. Por ejemplo, puede ser necesaria para dibujar un control personalizado de control deslizante cuya apariencia cambiará según los cálculos relacionados con el valor del control deslizante.

Vamos a examinar los recursos pueden dibujar en primer lugar. Son más sencillas y se mencionan los casos más comunes de dibujos personalizados.


### <a name="drawable-resources"></a>Recursos drawable

Recursos drawable se definen en un archivo XML en el directorio `/Resources/drawable`. A diferencia de incrustación PNG o del JPEG, no es necesario proporcionar versiones específicas de densidad de recursos Drawable.
En tiempo de ejecución, una aplicación de Android cargará estos recursos y usar las instrucciones incluidas en estos archivos XML para crear gráficos en 2D.
Android define varios tipos diferentes de recursos Drawable:

-   [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; se trata de un objeto Drawable que dibuja una forma geométrica primitiva y se aplica un conjunto limitado de efectos gráficos de esa forma. Son muy útiles para cosas como personalizar los botones o establecer el fondo de objetos TextView. Vemos un ejemplo de cómo usar un `ShapeDrawable` más adelante en este artículo.

-   [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; es un recurso Drawable que cambiarán en función del estado de un control de widget de apariencia. Por ejemplo, un botón puede cambiar su apariencia dependiendo de si está presionado o no.

-   [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; este recurso Drawable que se apilarán varios otro recursos drawable uno sobre otro. Un ejemplo de un *LayerDrawable* se muestra en la captura de pantalla siguiente:

    ![Ejemplo de LayerDrawable](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; se trata de un *LayerDrawable* pero con una diferencia. Un *TransitionDrawable* puede animar una capa que aparecen encima de otro.

-   [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; esto es muy similar a un *StateListDrawable* en que se mostrará una imagen basada en determinadas condiciones. Sin embargo, a diferencia de un *StateListDrawable*, *LevelListDrawable* muestra una imagen basada en un valor entero. Un ejemplo de un *LevelListDrawable* sería mostrar la fortaleza de una señal de Wi-Fi. Como la fuerza de los cambios de la señal de Wi-Fi, puede dibujar que se muestra cambiará en consecuencia.

-   [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; como su nombre implica, estos recursos Drawable proporciona escalado y la funcionalidad de recorte. El *ScaleDrawable* escalará otro Drawable, mientras el *ClipDrawable* ajustará Drawable otro.

-   [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; Drawable esta se aplicará márgenes en los lados de otro recurso Drawable. Se usa cuando una vista tiene un fondo que es menor que los límites de la vista reales.

-   XML [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; este archivo es un conjunto de instrucciones en XML, que se van a realizar en un mapa de bits real. Algunas acciones que puede llevar a cabo Android son tramado, segmentación y suavizado de contorno. Uno de los usos muy común de esto es un mapa de bits en el fondo de un diseño en mosaico.


#### <a name="drawable-example"></a>Ejemplo drawable

Veamos un ejemplo rápido de cómo crear una con gráfico 2D un `ShapeDrawable`. Un `ShapeDrawable` puede definir una de las cuatro formas básicas: rectángulo, oval, línea y anillo. También es posible aplicar efectos básicos, como degradado, color y tamaño. El siguiente código XML es un `ShapeDrawable` que pueden encontrarse en el *AnimationsDemo* proyecto complementaria (en el archivo `Resources/drawable/shape_rounded_blue_rect.xml`).
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

Podemos hacer referencia a este recurso Drawable mediante declaración en un diseño o en otro Drawable tal como se muestra en el siguiente código XML:

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

También se pueden aplicar recursos drawable mediante programación. El fragmento de código siguiente muestra cómo establecer mediante programación el fondo de un objeto TextView:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Para ver lo que esto tendría el siguiente aspecto, ejecute el *AnimationsDemo* del proyecto y seleccione el elemento forma puede dibujar en el menú principal. Deberíamos ver algo parecido a la captura de pantalla siguiente:

![Vista de texto con un fondo personalizado, puede dibujar con un degradado y las esquinas redondeadas](graphics-and-animation-images/image1.png)

Para obtener más información acerca de los elementos XML y la sintaxis de recursos Drawable, consulte [documentación de Google](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).


### <a name="using-the-canvas-drawing-api"></a>Mediante la API de dibujo de lienzo

Recursos Drawable es eficaces, pero tiene sus limitaciones. Algunas cosas son no es posible o muy compleja (por ejemplo: aplicar un filtro a una imagen que se tomó una cámara en el dispositivo). Sería muy difícil aplicar reducción de ojos rojos utilizando un recurso puede dibujar.
En su lugar, la API de Canvas permite que una aplicación que tenga un control muy preciso para cambiar los colores de una parte específica de la imagen de manera selectiva.

Una clase que se usa normalmente con el lienzo es el [Paint](https://developer.xamarin.com/api/type/Android.Graphics.Paint/) clase. Esta clase contiene información de color y estilo sobre cómo dibujar. Se utiliza para proporcionar las cosas, color y transparencia.

Usa la API de Canvas el *modelo de pintor* para dibujar gráficos en 2D.
Las operaciones se aplican en los niveles sucesivos se usan en la parte superior entre sí. Cada operación tratará algunas áreas del mapa de bits subyacente. Cuando el área se superpone a un área pintada anteriormente, la pintura nuevo funcionará parcialmente u ocultar completamente la antigua. Se trata de la misma manera que funcionan de muchas otras API de dibujo como System.Drawing y gráficos de núcleo de iOS.

Hay dos maneras de obtener un `Canvas` objeto. La primera manera implica definir una [mapa de bits](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) objeto y, a continuación, crear una instancia de un `Canvas` objeto con él. Por ejemplo, el fragmento de código siguiente crea un nuevo lienzo con un mapa de bits subyacente:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

La otra forma de obtener un `Canvas` objeto es mediante la [OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/) método de devolución de llamada que se proporciona el [vista](https://developer.xamarin.com/api/type/Android.Views.View/) clase base. Android llama a este método cuando decide una vista debe dibujarse a sí mismo y pasa un `Canvas` objeto para trabajar con la vista.

La clase de lienzo expone métodos para proporcionar las instrucciones de dibujo de mediante programación. Por ejemplo:

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash; mapa de bits del lienzo completo se rellena con la pintura especificada.

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash; dibuja la forma geométrica especificada mediante la pintura especificada.

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash; dibuja el texto en el lienzo con el color especificado. El texto se dibuja en la ubicación `x,y` .



#### <a name="drawing-with-the-canvas-api"></a>Dibujar con la API de lienzo

Veamos un ejemplo de la API de Canvas en acción. El fragmento de código siguiente muestra cómo dibujar una vista:

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

El código anterior crea primero una operación de dibujo rojo y un objeto de pintura verde. Rellena el contenido del lienzo de rojo y, a continuación, indica el lienzo para dibujar un rectángulo verde que es el 25% del ancho del lienzo. Se aprecia un ejemplo de esto en `AnimationsDemo` proyecto que se incluye con el código fuente para este artículo. Iniciar la aplicación y seleccionando el elemento de dibujo en el menú principal, se debe una pantalla similar a lo siguiente:

![Pantalla con objetos de pintura verde y rojo paint](graphics-and-animation-images/image3.png)


## <a name="animation"></a>Animación

Los usuarios, como las cosas que se mueven en sus aplicaciones. Las animaciones son una excelente manera de mejorar la experiencia del usuario de una aplicación y hacerla destacar. Las animaciones mejores son los que no tenga en cuenta los usuarios porque creen naturales. Android proporciona la siguiente tres API animaciones:

-   **Ver la animación** &ndash; se trata de la API original. Estas animaciones están asociadas a una vista concreta y pueden realizar transformaciones simples en el contenido de la vista. Debido a su simplicidad, esta API siendo útil para cosas como animaciones alfabéticos, rotaciones y así sucesivamente.

-   **Animación de la propiedad** &ndash; animaciones de propiedad se introdujeron en Android 3.0. Permiten que una aplicación que se va a animar prácticamente cualquier cosa. Animaciones de propiedad pueden usarse para cambiar cualquier propiedad de cualquier objeto, incluso si ese objeto no está visible en la pantalla.

-   **Animación drawable** &ndash; esto afecta a un recurso Drawable especial que se usa para aplicar una animación muy sencilla a los diseños.

En general, animación de la propiedad es el sistema preferido para usar ya que es más flexible y ofrece más características.


### <a name="view-animations"></a>Animaciones de vista

Las animaciones de la vista se limitan a las vistas y solo pueden realizar animaciones en valores de inicio y puntos de conexión, tamaño, rotación y transparencia.
Estos tipos de animaciones se conocen normalmente como *interpolación animaciones*. Se pueden definir dos maneras de ver animaciones &ndash; mediante programación en el código o mediante el uso de archivos XML. Archivos XML son la mejor manera de declarar las animaciones de la vista, ya que son más legible y fácil de mantener.

Los archivos XML de animación se almacenarán en el `/Resources/anim` directorio de un proyecto de Xamarin.Android. Como el elemento raíz, este archivo debe tener uno de los siguientes elementos:

-   `alpha` &ndash; Una animación de fundido de entrada o de fundido de salida.

-   `rotate` &ndash; Una animación de giro.

-   `scale` &ndash; Una animación de cambio de tamaño.

-   `translate` &ndash; Un movimiento horizontal o vertical.

-   `set` &ndash; Un contenedor que puede contener uno o varios de los demás elementos de animación.

De forma predeterminada, todas las animaciones en un archivo XML se aplicará al mismo tiempo. Para que las animaciones se producen de forma secuencial, establezca el `android:startOffset` atributo en uno de los elementos definidos anteriormente.

Es posible que afecten a la tasa de cambio en una animación mediante el uso de un *interpolador*. Un interpolador hace posible que los efectos de animación acelerado, repetidas o desacelerará arroje curva. El marco de trabajo Android proporciona varias interpoladores de fábrica, como (pero sin limitarse a):

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; Estos interpoladores aumentar o disminuir la velocidad de cambio en una animación.

-   `BounceInterpolator` &ndash; el cambio rebota al final.

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

Esta animación llevará a cabo todas las animaciones de forma simultánea. La primera animación de escala se ajustan la imagen horizontalmente y reducirla verticalmente y, a continuación, la imagen al mismo tiempo se gira 45 grados hacia la izquierda y reducir, desaparece de la pantalla.

La animación puede aplicarse mediante programación a una vista, por lo que infla la animación y, a continuación, aplicarla a una vista. Android proporciona la clase auxiliar `Android.Views.Animations.AnimationUtils` que se infla un recurso de animación y devolverá una instancia de `Android.Views.Animations.Animation`. Este objeto se aplica a una vista mediante una llamada a `StartAnimation` y pasando el `Animation` objeto. El fragmento de código siguiente muestra un ejemplo de esto:

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Ahora que tenemos un entendimiento básico de cómo funcionan las animaciones de la vista, permite mover a las animaciones de propiedad.


### <a name="property-animations"></a>Animaciones de propiedad

Animadores de propiedad son una nueva API que se introdujo en Android 3.0.
Proporcionan una API más extensible que puede usarse para animar cualquier propiedad en cualquier objeto.

Todas las animaciones de propiedad se crean las instancias de la [animador](https://developer.xamarin.com/api/type/Android.Animation.Animator/) subclase. Las aplicaciones no utilizan esta clase directamente, en su lugar, usan uno de sus subclases:

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash; esta clase es la clase más importante en la API de animación completo de propiedades. Calcula los valores de propiedades que deben cambiarse. El `ViewAnimator` no actualizar directamente los valores; en su lugar, genera los eventos que pueden usarse para actualizar los objetos animados.

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash; esta clase es una subclase de `ValueAnimator` . Está diseñada para simplificar el proceso de animación de objetos mediante la aceptación de un objeto de destino y propiedad que se va a actualizar.

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash; esta clase es responsable de la coordinación de cómo ejecutan animaciones en la relación entre sí. Las animaciones pueden ejecutar simultáneamente, secuencialmente o con un retraso especificado entre ellas.


*Evaluadores* son clases especiales que animadores se utilizan para calcular los nuevos valores durante una animación. De fábrica, Android proporciona los evaluadores siguientes:

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash; calcula los valores de las propiedades de entero.

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash; calcula los valores de propiedades float.

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash; calcula los valores de propiedades de color.

Si la propiedad que se anima no es un `float`, `int` o color, las aplicaciones pueden crear su propios del evaluador de expresiones mediante la implementación de la `ITypeEvaluator` interfaz. (Implementación evaluadores personalizadas queda fuera del ámbito de este tema.)

#### <a name="using-the-valueanimator"></a>Uso de la ValueAnimator

Hay dos partes a cualquier animación: calcular valores animados y, a continuación, establecer esos valores en las propiedades de un objeto. 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) solo calculará los valores, pero no funcionará en los objetos directamente. En su lugar, se actualizarán los objetos dentro de los controladores de eventos que se invocará durante la duración de la animación. Este diseño permite que varias propiedades que se actualizarán desde un valor animado.

Obtener una instancia de `ValueAnimator` llamando a uno de los siguientes métodos de fábrica:

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

Una vez hecho esto, el `ValueAnimator` instancia debe tener su duración establecida y, a continuación, se puede iniciar. El ejemplo siguiente muestra cómo animar un valor entre 0 y 1 en el intervalo de 1000 milisegundos:

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Pero sí, el fragmento de código anterior no es muy útil &ndash; la animación se ejecutará, pero no hay ningún destino para el valor actualizado. La `Animator` clase provocará el evento de actualización cuando decide que es necesario informar a los agentes de escucha de un nuevo valor. Las aplicaciones pueden proporcionar un controlador de eventos para responder a este evento, como se muestra en el siguiente fragmento de código:

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

Ahora que tenemos una comprensión de `ValueAnimator`, permite obtener más información sobre la `ObjectAnimator`.

#### <a name="using-the-objectanimator"></a>Uso de la ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) es una subclase de `ViewAnimator` que combina el cálculo de motor y el valor de tiempo de la `ValueAnimator` con la lógica necesaria para conectar controladores de eventos. El `ValueAnimator` requiere que las aplicaciones para conectar explícitamente un controlador de eventos &ndash; `ObjectAnimator` se encargará de este paso para nosotros.

La API para `ObjectAnimator` es muy similar a la API para `ViewAnimator`, pero requiere que proporcione el objeto y el nombre de la propiedad que se va a actualizar. El ejemplo siguiente muestra un ejemplo del uso `ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Como puede ver en el fragmento de código anterior, `ObjectAnimator` puede reducir y simplificar el código que es necesario animar un objeto.


### <a name="drawable-animations"></a>Puede dibujables animaciones

La API de animación final es la API de animación con estas características. Las animaciones pueden dibujables cargar una serie de recursos Drawable uno tras otro y mostrarlos de manera secuencial, similar a un dibujo animado de TI de volteo.

Recursos drawable se definen en un archivo XML que tiene un `<animation-list>` elemento como el elemento raíz y una serie de `<item>` elementos que definen cada fotograma de la animación. Este archivo XML se almacena en el `/Resource/drawable` carpeta de la aplicación. El siguiente código XML es un ejemplo de una animación con estas características:

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

Esta animación se ejecutará a través de seis fotogramas. El `android:duration` atributo declara ¿durante cuánto tiempo se muestra cada fotograma. El fragmento de código siguiente muestra un ejemplo de creación de una animación Drawable e iniciarlo cuando el usuario hace clic en un botón en la pantalla:

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

En este punto hemos tratado los fundamentos de la animación API disponibles en una aplicación de Android.


## <a name="summary"></a>Resumen

Este artículo presenta una gran cantidad de nuevos conceptos y API que ayudarán a agregar algunos gráficos a una aplicación Android. En primer lugar tratan varios gráficos 2D de la API y se muestra cómo Android permite que las aplicaciones que se va a dibujar directamente en la pantalla con un objeto de lienzo. También hemos visto algunas técnicas alternativas que permitan gráficos crearse mediante declaración utilizando archivos XML. A continuación, se ha ido a explicar antigua y nueva de la API para crear animaciones en Android.



## <a name="related-links"></a>Vínculos relacionados

- [Demostración de animación (ejemplo)](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [Gráficos y animación](https://developer.android.com/guide/topics/graphics/index.html)
- [Usar animaciones para que sus aplicaciones móviles cobren vida](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [Canvas](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [Objeto Animador](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [Animación de valor](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
