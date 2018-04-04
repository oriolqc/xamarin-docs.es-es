---
title: Introducción a Renderscript
description: Esta guía presenta Renderscript y explica cómo utilizar la función intrínseca Renderscript API en una aplicación Xamarin.Android ese nivel de API de destinos 17 o superior.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: f9e21a51c409c5444f137a63eb2c6fadfef03cbe
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="an-introduction-to-renderscript"></a>Introducción a Renderscript

_Esta guía presenta Renderscript y explica cómo utilizar la función intrínseca Renderscript API en una aplicación Xamarin.Android ese nivel de API de destinos 17 o superior._

## <a name="overview"></a>Información general

RenderScript es un marco de programación creado por Google con el fin de mejorar el rendimiento de aplicaciones Android que requieren muchos recursos de cálculo. Es una API basada en bajo nivel, de alto rendimiento [C99](http://en.wikipedia.org/wiki/C99). Dado que es un API que se ejecutará en la CPU, GPU o DSP de bajo nivel, Renderscript es adecuada para aplicaciones Android que quizás deba realizar cualquiera de las siguientes acciones:

* Gráficos
* Procesamiento de imágenes
* Cifrado
* Procesamiento de señales
* Rutinas matemáticas

Va a usar RenderScript `clang` y compilar los scripts de código de bytes LLVM que está integrado en el APK. Cuando la aplicación se ejecuta por primera vez, se compilará el código de bytes LLVM en código máquina para los procesadores en el dispositivo. Esta arquitectura permite que una aplicación Android aprovechar las ventajas de código máquina sin los desarrolladores a sí mismos tener que escribirla para cada procesador en el dispositivo por sí mismos.

Hay dos componentes a una rutina de Renderscript:

1. **El tiempo de ejecución de Renderscript** &ndash; se trata de las API nativas que son responsables de ejecutar la Renderscript. Esto incluye cualquier Renderscripts escritas para la aplicación.

2. **Contenedores administrados desde el marco de trabajo Android** &ndash; a clases administradas que permiten una aplicación Android controlar e interactuar con el tiempo de ejecución Renderscript y secuencias de comandos. Además de las clases de framework proporcionada para controlar el tiempo de ejecución de Renderscript, la cadena de herramientas Android se examina el código de origen de Renderscript y generar clases de contenedor administrado para su uso por la aplicación Android.

El diagrama siguiente muestra cómo se relacionan estos componentes:

![Diagrama que ilustra cómo el marco de trabajo Android interactúa con el Renderscript Runtime](renderscript-images/renderscript-01.png)

Hay tres conceptos importantes para usar Renderscripts en una aplicación de Android:

1. **Un contexto** &ndash; API proporcionada por el SDK de Android que asigna recursos a Renderscript y permite que la aplicación Android pasar y recibir datos desde el Renderscript administrado.

2. **A _proceso kernel_**  &ndash; también conocido como el _kernel raíz_ o _kernel_, esta una rutina que realiza el trabajo. El kernel es muy similar a una función de C; es una rutina paralelizar que se va a ejecutar a través de todos los datos en la memoria asignada.

3. **Memoria asignada** &ndash; es pasar datos a y desde un kernel a través de un  _[asignación](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_. Un núcleo puede tener una entrada o una asignación de salida.

El [Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/) espacio de nombres contiene las clases para interactuar con el tiempo de ejecución Renderscript. En concreto, el [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) clase va a administrar el ciclo de vida y los recursos del motor de Renderscript. La aplicación de Android debe inicializar uno o varios [ `Android.Renderscripts.Allocation` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/) objetos. Una asignación es una API administrada que se encarga de asignación y obtener acceso a la memoria que se comparte entre la aplicación de Android y el tiempo de ejecución Renderscript. Normalmente, se crea una asignación para la entrada y, opcionalmente, otra asignación se crea para contener el resultado del kernel. El motor de tiempo de ejecución Renderscript y las clases de contenedor administrado asociado va a administrar el acceso a la memoria mantenida por las asignaciones, no hay ninguna necesidad de un desarrollador de aplicaciones Android realizar cualquier trabajo adicional.

Contiene una asignación de uno o varios [Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/).
Los elementos son un tipo especializado que describen los datos de cada asignación.
Los tipos de elemento de la salida de asignación debe coincidir con el los tipos de elemento de entrada. Cuando se ejecuta, un Renderscript se iterar sobre cada elemento en la asignación de entrada en paralelo y escribir los resultados a la salida de asignación. Hay dos tipos de elementos:

- **tipo simple** &ndash; conceptualmente es igual a un tipo de datos de C, `float` o `char`.

- **tipo complejo** &ndash; este tipo es similar a una C `struct`.

El motor de Renderscript lleva a cabo una comprobación en tiempo de ejecución para asegurarse de que los elementos de cada asignación son compatibles con lo que se necesita el kernel. Si el tipo de datos de los elementos de la asignación no coincide con el tipo de datos que está esperando el kernel, se producirá una excepción.

Todos los núcleos de Renderscript se ajustará mediante un tipo que es un descendiente de la [ `Android.Renderscripts.Script` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/) clase. El `Script` clase se utiliza para establecer los parámetros de un Renderscript, establezca la correspondiente `Allocations`, y ejecute el Renderscript. Hay dos `Script` subclases en el SDK de Android:


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; Algunas de las tareas más comunes de Renderscript están incluidas en el SDK de Android y tener acceso a una subclase de la [ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/) clase. No es necesario para que un desarrollador realizar ningún paso adicional para usar estos scripts en su aplicación, como ya se proporcionan.

- **`ScriptC_XXXXX`** &ndash; También se denomina _secuencias de comandos de usuario_, estos son scripts que se escriben los programadores y empaquetados en el APK. En tiempo de compilación, la cadena de herramientas Android generará clases contenedoras administradas que permiten a las secuencias de comandos que se usará en la aplicación de Android.
  El nombre de estas clases generadas es el nombre del archivo Renderscript, con el prefijo `ScriptC_`. Escribir y la incorporación de las secuencias de comandos de usuario no se admiten oficialmente por Xamarin.Android y fuera del ámbito de esta guía.

De estos dos tipos, solo el `StringIntrinsic` es compatible con Xamarin.Android. Esta guía describe cómo usar scripts intrínsecas en una aplicación Xamarin.Android.

## <a name="requirements"></a>Requisitos

Esta guía es para las aplicaciones de Xamarin.Android ese nivel de API de destino 17 o superior. El uso de _secuencias de comandos de usuario_ no se trata en esta guía.

El [biblioteca de compatibilidad de V8 Xamarin.Android](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) backports el intrínsecas Renderscript API para las aplicaciones que tienen como destino versiones anteriores de Android SDK. Agregar este paquete a un proyecto Xamarin.Android debe permitir que las aplicaciones destinadas a versiones anteriores de Android SDK para aprovechar las secuencias de comandos intrínsecos.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Uso de intrínsecos Renderscripts en Xamarin.Android

Las secuencias de comandos intrínsecos son una excelente manera de realizar las tareas de computación intensivo con una cantidad mínima de código adicional. Han sido ajustado para ofrecer un rendimiento óptimo en un corte transversal grande de dispositivos de mano.
No es raro que un script para que se ejecute 10 veces más rápido que el código administrado y 2 a 3 veces la después de que una implementación personalizada de C intrínseco. Muchos de los escenarios de procesamiento normal están cubiertos por las secuencias de comandos intrínsecos. Esta lista de las secuencias de comandos intrínsecas describen las secuencias de comandos actuales en Xamarin.Android:

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash; RGB convierte a RGBA mediante una tabla de búsqueda 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh rendimiento Renderscript APIs a [BLAS](http://www.netlib.org/blas/). BLAS (básico álgebra lineal subprogramas) son rutinas que ofrecen los bloques de creación estándares para llevar a cabo vector básica y las operaciones de matriz. 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash; combina dos asignaciones de memoria entre sí.

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash; se aplica un desenfoque gaussiano a una asignación.

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash; se aplica a una matriz de colores para una asignación (es decir, cambiar colores, ajustar el tono).

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash; se aplica una matriz de 3 x 3 colores a una asignación.

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash; aplica una matriz de color 5 x 5 a una asignación.

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash; un filtro de histograma intrínseco.

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash; se aplica a una tabla de búsqueda al canal a un búfer.

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash; secuencia de comandos para realizar el cambio de tamaño de una asignación 2D.

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash; convierte un búfer YUV en RGB.

Consulte la documentación de API para obtener detalles sobre cada una de las secuencias de comandos intrínsecos.

Los pasos básicos para usar Renderscript en una aplicación de Android se describen a continuación.

**Crear un contexto de Renderscript** &ndash; el [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) clase es un contenedor administrado en el contexto de Renderscript y se controlan la inicialización, administración de recursos y limpiar. El objeto Renderscript se crea utilizando el `RenderScript.Create` método de fábrica, que toma un contexto de Android (por ejemplo, una actividad) como un parámetro. La siguiente línea de código muestra cómo inicializar el contexto de Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Crear asignaciones** &ndash; dependiendo de la secuencia de comandos intrínseco, puede ser necesario crear uno o dos `Allocation`s. El [ `Android.Renderscripts.Allocation` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/) clase tiene varios métodos de generador para ayudar a crear instancias de una asignación para una función intrínseca. Por ejemplo, el fragmento de código siguiente muestra cómo crear una asignación de mapas de bits.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

A menudo, será necesario crear un `Allocation` para contener los datos de salida de una secuencia de comandos. Este fragmento de código siguiente muestra cómo utilizar el `Allocation.CreateTyped` auxiliar para crear instancias de un segundo `Allocation` que el mismo tipo que el original:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Crear una instancia del contenedor de secuencias de comandos** &ndash; cada una de las clases contenedoras de función intrínseca de la secuencia de comandos debe tener métodos auxiliares (suele denominarse `Create`) para crear instancias de un objeto contenedor de ese script. El siguiente fragmento de código es un ejemplo de cómo crear una instancia de un `ScriptIntrinsicBlur` difuminar el objeto. El `Element.U8_4` método auxiliar creará un elemento que describe un tipo de datos que es 4 campos de valores de entero de 8 bits sin signo, adecuados para almacenar los datos de un `Bitmap` objeto:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Asignar Allocation(s), establecer parámetros de & ejecutar Script** &ndash; el `Script` clase proporciona un `ForEach` método al que se ejecutan realmente el Renderscript. Este método establece una iteración por cada `Element` en el `Allocation` que contiene los datos de entrada. En algunos casos, es posible que sea necesario proporcionar un `Allocation` que contiene el resultado.
`ForEach` se sobrescribirá el contenido de la salida de asignación. Para llevar con los fragmentos de código de los pasos anteriores, este ejemplo muestra cómo asignar una asignación de entrada, establecer un parámetro y, a continuación, por último, ejecute la secuencia de comandos (copiar los resultados a la salida de asignación):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Puede que desee extraer del repositorio el [desenfoque una imagen con Renderscript](https://developer.xamarin.com/recipes/android/other_ux/drawing/blur_an_image_with_renderscript/) receta, es un ejemplo completo de cómo usar un script intrínseco en Xamarin.Android.

## <a name="summary"></a>Resumen

Esta guía introdujo Renderscript y cómo usarlo en una aplicación Xamarin.Android. Brevemente explica qué es Renderscript y cómo funciona en una aplicación Android. Describen algunos de los principales componentes de Renderscript y la diferencia entre _secuencias de comandos de usuario_ y _scripts intrínsecas_. Por último, en esta guía se describe los pasos en el uso de una secuencia de comandos intrínseca en una aplicación Xamarin.Android.



## <a name="related-links"></a>Vínculos relacionados

- [Espacio de nombres Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [Desenfoque una imagen con Renderscript](https://developer.xamarin.com/recipes/android/other_ux/drawing/blur_an_image_with_renderscript/)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Tutorial: Introducción a Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
