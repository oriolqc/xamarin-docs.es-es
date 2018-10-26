---
title: Una introducción a Renderscript
description: Esta guía presenta Renderscript y explica cómo utilizar el intrínseco Renderscript API en una aplicación de Xamarin.Android ese nivel destinos API 17 o posterior.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 5369542552a41100443c5e91ceca9e110c5c7c3c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108735"
---
# <a name="an-introduction-to-renderscript"></a>Una introducción a Renderscript

_Esta guía presenta Renderscript y explica cómo utilizar el intrínseco Renderscript API en una aplicación de Xamarin.Android ese nivel destinos API 17 o posterior._

## <a name="overview"></a>Información general

RenderScript es un marco de programación creado por Google con el fin de mejorar el rendimiento de las aplicaciones de Android que requieren muchos recursos informáticos. Es una API basada en bajo nivel, de alto rendimiento [C99](http://en.wikipedia.org/wiki/C99). Dado que es un API que se ejecutará en las CPU, GPU o DSP de bajo nivel, Renderscript es ideal para aplicaciones Android que necesitan realizar alguna de las siguientes:

* Gráficos
* Procesamiento de imágenes
* Cifrado
* Procesamiento de señales
* Rutinas matemáticas

Usará RenderScript `clang` y compilar los scripts de código de byte LLVM que está integrado en el APK. Cuando se ejecuta la aplicación por primera vez, el código de bytes LLVM se compilarán en código máquina para los procesadores en el dispositivo. Esta arquitectura permite que una aplicación Android aprovechar las ventajas de código máquina sin los desarrolladores tienen que escribirla para cada procesador en el dispositivo a sí mismos.

Hay dos componentes a una rutina Renderscript:

1. **El tiempo de ejecución Renderscript** &ndash; trata las API nativas que son responsables de ejecutar el Renderscript. Esto incluye cualquier Renderscripts escrito para la aplicación.

2. **Contenedores administrados desde el marco de trabajo Android** &ndash; clases administradas que permiten una aplicación Android controlar e interactuar con el tiempo de ejecución Renderscript y secuencias de comandos. Además de las clases de framework proporcionado para controlar el tiempo de ejecución Renderscript, la cadena de herramientas Android examinará el código fuente de Renderscript y generar clases de contenedor administrado para su uso por la aplicación Android.

El siguiente diagrama ilustra cómo se relacionan estos componentes:

![Diagrama que ilustra cómo interactúa el marco de trabajo Android con el Renderscript Runtime](renderscript-images/renderscript-01.png)

Hay tres conceptos importantes para usar Renderscripts en una aplicación de Android:

1. **Un contexto** &ndash; API proporcionada por el SDK de Android que asigna los recursos a Renderscript y permite que la aplicación Android pasar y recibir datos de la Renderscript administrada.

2. **Un _kernel de proceso_**  &ndash; también conocido como el _kernel raíz_ o _kernel_, esta una rutina que realiza el trabajo. El kernel es muy similar a una función de C; es una rutina que se pueden paralelizar que se va a ejecutar todos los datos en la memoria asignada.

3. **Memoria asignada** &ndash; se pasan datos hacia y desde un kernel a través de un  _[asignación](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_. Un núcleo puede tener una entrada o una asignación de salida.

El [Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/) espacio de nombres contiene las clases para interactuar con el tiempo de ejecución Renderscript. En concreto, el [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) clase va a administrar el ciclo de vida y los recursos del motor Renderscript. La aplicación de Android debe inicializar uno o más [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
Objetos. Una asignación es una API administrada que se encarga de asignación y el acceso a la memoria que se comparte entre la aplicación de Android y el tiempo de ejecución Renderscript. Normalmente, se crea una asignación para la entrada y, opcionalmente, otra asignación se crea para contener el resultado del kernel. El motor de tiempo de ejecución Renderscript y las clases contenedoras administradas asociado va a administrar el acceso a la memoria que mantiene las asignaciones, no es necesario para un desarrollador de aplicaciones Android realizar cualquier trabajo adicional.

Contendrá una asignación de uno o varios [Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/).
Los elementos son un tipo especializado que se describen los datos de cada asignación.
Los tipos de elemento de la salida de asignación debe coincidir con los tipos de elemento de entrada. Cuando se ejecuta, un Renderscript se recorrer en iteración cada elemento en la asignación de entrada en paralelo y escribir los resultados en la salida de asignación. Hay dos tipos de elementos:

- **tipo simple** &ndash; conceptualmente es igual que un tipo de datos C `float` o `char`.

- **tipo complejo** &ndash; este tipo es similar a una C `struct`.

El motor Renderscript llevará a cabo una comprobación en tiempo de ejecución para asegurarse de que los elementos de cada asignación son compatibles con lo que requiere el kernel. Si el tipo de datos de los elementos de la asignación no coincide con el tipo de datos que está esperando el kernel, se producirá una excepción.

Todos los kernels Renderscript se ajustará mediante un tipo que es un descendiente de la [`Android.Renderscripts.Script`](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/)
clase. El `Script` clase se utiliza para definir los parámetros para un Renderscript, establezca adecuado `Allocations`, y ejecute el Renderscript. Hay dos `Script` subclases en el SDK de Android:


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; Algunas de las tareas más comunes de Renderscript están agrupados en el SDK de Android y son accesibles por una subclase de la [ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/) clase. No hay ninguna necesidad de un desarrollador tomar ninguna acción adicional para usar estos scripts en su aplicación, como ya se proporcionan.

- **`ScriptC_XXXXX`** &ndash; También se denomina _secuencias de comandos de usuario_, estos son los scripts que se escriben los desarrolladores y empaquetados en el APK. En tiempo de compilación, la cadena de herramientas Android generará las clases contenedoras administradas que permitirá que las secuencias de comandos que se usará en la aplicación de Android.
  El nombre de estas clases generadas es el nombre del archivo Renderscript, con el prefijo `ScriptC_`. Escribir y la incorporación de scripts de usuario no se admiten oficialmente en Xamarin.Android y escapa al alcance de esta guía.

De estos dos tipos, solo el `StringIntrinsic` es compatible con Xamarin.Android. Esta guía describe cómo usar scripts intrínsecos en una aplicación de Xamarin.Android.

## <a name="requirements"></a>Requisitos

Esta guía es para aplicaciones de Xamarin.Android ese nivel de API de destino 17 o posterior. El uso de _secuencias de comandos de usuario_ no se tratan en esta guía.

El [Xamarin.Android V8 Support Library](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) backports el intrínsecas Renderscript API para las aplicaciones que tienen como destino versiones anteriores de Android SDK. Agregar este paquete a un proyecto de Xamarin.Android debe permitir las aplicaciones destinadas a versiones anteriores del SDK de Android aprovechar las secuencias de comandos intrínsecos.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Uso de intrínsecos Renderscripts en Xamarin.Android

Las secuencias de comandos intrínsecos son una excelente manera de realizar las tareas informáticas intensivas con una cantidad mínima de código adicional. Han sido ajustado para ofrecer un rendimiento óptimo en un gran corte transversal de los dispositivos de mano.
No es raro que un script ejecutar 10 veces más rápido que el código administrado y 2-3 x veces después de que una implementación personalizada de C intrínseco. Muchos de los escenarios de procesamiento normales están cubiertos por las secuencias de comandos intrínsecos. Esta lista de las secuencias de comandos intrínsecas describe las secuencias de comandos actuales en Xamarin.Android:

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash; RGB convierte al modo RGBA utilizando una tabla de búsqueda 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh rendimiento Renderscript APIs a [BLAS](http://www.netlib.org/blas/). BLAS (subprogramas de álgebra lineal básica) son rutinas que proporcionan los bloques de creación estándar para llevar a cabo vector básica y las operaciones de matriz. 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash; combina dos asignaciones entre sí.

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash; se aplica un desenfoque gaussiano a una asignación.

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash; se aplica una matriz de colores a una asignación (es decir, cambiar colores, ajustar el tono).

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash; se aplica una matriz de 3 x 3 colores a una asignación.

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash; se aplica una matriz de colores de 5 x 5 a una asignación.

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash; un filtro de histograma intrínseco.

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash; se aplica a una tabla de búsqueda por canal a un búfer.

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash; secuencia de comandos para realizar el cambio de tamaño de una asignación de 2D.

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash; convierte un búfer YUV a RGB.

Consulte la documentación de API para obtener más información sobre cada una de las secuencias de comandos intrínsecos.

A continuación se describen los pasos básicos para usar Renderscript en una aplicación de Android.

**Crear un contexto Renderscript** &ndash; el [`Renderscript`](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)
clase es un contenedor administrado alrededor del contexto Renderscript y se controlan la inicialización, administración de recursos y limpiar. El objeto Renderscript se crea utilizando el `RenderScript.Create` método de fábrica, que toma un contexto de Android (por ejemplo, una actividad) como un parámetro. La siguiente línea de código muestra cómo inicializar el contexto de Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Crear asignaciones** &ndash; dependiendo de la secuencia de comandos intrínseco, puede ser necesario crear uno o dos `Allocation`s. El [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
clase tiene varios métodos de generador para crear instancias de una asignación para una función intrínseca. Por ejemplo, el fragmento de código siguiente muestra cómo se crea la asignación de mapas de bits.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

A menudo, será necesario crear un `Allocation` para contener los datos de salida de una secuencia de comandos. Este fragmento de código siguiente muestra cómo usar el `Allocation.CreateTyped` auxiliar para crear instancias de un segundo `Allocation` que el mismo tipo que el original:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Crear una instancia del contenedor de secuencias de comandos** &ndash; cada una de las clases contenedoras de intrínseco de la secuencia de comandos debe tener métodos auxiliares (suele denominarse `Create`) para crear instancias de un objeto contenedor para ese script. El fragmento de código siguiente es un ejemplo de cómo crear una instancia de un `ScriptIntrinsicBlur` desenfoque de objeto. El `Element.U8_4` método auxiliar creará un elemento que describe un tipo de datos es 4 campos de valores de entero de 8 bits sin signo, adecuados para alojar los datos de un `Bitmap` objeto:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Asignar Allocation(s), establecer parámetros de & ejecutar Script** &ndash; el `Script` clase proporciona un `ForEach` método para ejecutar el Renderscript. Este método creará una iteración por cada `Element` en el `Allocation` que contiene los datos de entrada. En algunos casos, puede ser necesario proporcionar un `Allocation` que contiene la salida.
`ForEach` se sobrescribirá el contenido de la salida de asignación. Para llevar la cabo con los fragmentos de código de los pasos anteriores, este ejemplo muestra cómo asignar una asignación de entrada, establecer un parámetro y, a continuación, por último, ejecute el script (copiar los resultados a la salida de asignación):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Desea retirar el [desenfoque de una imagen con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) receta, es un ejemplo completo de cómo usar un script intrínseco en Xamarin.Android.

## <a name="summary"></a>Resumen

Esta guía presentan Renderscript y cómo usarlo en una aplicación de Xamarin.Android. Tratan brevemente qué es Renderscript y cómo funciona en una aplicación de Android. Describen algunos de los componentes clave de Renderscript y la diferencia entre _secuencias de comandos de usuario_ y _intrínsecas scripts_. Finalmente, en esta guía se analizan los pasos de con un script intrínseco de una aplicación de Xamarin.Android.



## <a name="related-links"></a>Vínculos relacionados

- [Espacio de nombres Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [Desenfoca una imagen con Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Tutorial: Introducción a Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
