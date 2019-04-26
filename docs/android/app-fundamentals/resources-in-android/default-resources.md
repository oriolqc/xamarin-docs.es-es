---
title: Recursos predeterminados
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 20865b71cce16f57b84a1c54986bd84180d3e190
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013268"
---
# <a name="default-resources"></a>Recursos predeterminados

Los recursos predeterminados son elementos que no son específicos a cualquier dispositivo en particular o el factor de forma y, por lo tanto, son la opción predeterminada por el sistema operativo Android si no hay más específicas se pueden encontrar los recursos. Por lo tanto, son el tipo más común de recurso que desee crear. Que están organizados en los subdirectorios de la **recursos** directorio según su tipo de recurso:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Archivos de recursos predeterminados](default-resources-images/01-resource-files-vs.png)

En la imagen anterior, el proyecto tiene valores predeterminados para los recursos drawable, diseños y valores (archivos XML que contienen valores simples).

A continuación se proporciona una lista completa de tipos de recursos:

-  **animador** &ndash; archivos XML que describen las animaciones de propiedad.
   Animaciones de propiedad se introdujeron en el nivel de API 11 (Android 3.0) y se proporciona para la animación de propiedades en un objeto. Animaciones de propiedad son una forma más eficaz y flexible para describir las animaciones en cualquier tipo de objeto.

-  **anim** &ndash; archivos XML que describen *interpolación* animaciones. Las animaciones de interpolación son una serie de instrucciones de animación para realizar transformaciones en el contenido de un giro del objeto, o un ejemplo, ver una imagen o aumentar el tamaño del texto. Las animaciones de interpolación se limitan a ver solo los objetos.

-  **color** &ndash; archivos XML que describen una lista de estado de colores. Para entender las listas de estado de color, considere la posibilidad de un widget de interfaz de usuario, como un botón.
   Puede tener distintos Estados, como presionado o deshabilitado, y el botón puede cambiar el color con cada cambio de estado. La lista se expresa en una lista de estado.

-  **drawable** &ndash; Drawable recursos son un concepto general de los gráficos que se pueden compilar en la aplicación y, a continuación, accediendo a las llamadas de API o hace referencia a otros recursos XML.
   Algunos ejemplos de recursos drawable son archivos de mapa de bits (.png, .gif, .jpg), mapas de bits redimensionable especial conocida como [parches de nueve](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), muestra el estado de formas genéricas definidas en XML, etcetera.
 
-  **diseño** &ndash; archivos XML que describen un diseño de interfaz de usuario, como una actividad o una fila en una lista.

-  **menú** &ndash; archivos XML que describen los menús de la aplicación, como *opciones menús*, *menús contextuales*, y *submenús*. Para obtener un ejemplo de menús, vea el [demostración del menú emergente](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/) o [controles estándar](https://developer.xamarin.com/samples/mobile/StandardControls/) ejemplo.

-  **RAW** &ndash; archivos arbitrarios que se guardan en su formato binario sin formato. Estos archivos se compilan en una aplicación de Android en un formato binario.

-  **valores** &ndash; archivos XML que contienen valores simples. Un archivo XML en el directorio de los valores no define un único recurso, pero en su lugar, puede definir varios recursos. Por ejemplo, un archivo XML puede albergar una lista de valores de cadena, mientras que otro archivo XML puede contener una lista de valores de color.

-  **XML** &ndash; archivos XML que son una función similar a los archivos de configuración. NET. Se trata de XML arbitrario que se puede leer en tiempo de ejecución por la aplicación.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Archivos de recursos predeterminados](default-resources-images/01-resource-files-xs.png)

En la imagen anterior, el proyecto tiene valores predeterminados para los recursos drawable, diseños y valores (archivos XML que contienen valores simples).

A continuación se proporciona una lista completa de tipos de recursos:

-  **animador** &ndash; archivos XML que describen las animaciones de propiedad.
   Animaciones de propiedad se introdujeron en el nivel de API 11 (Android 3.0) y se proporciona para la animación de propiedades en un objeto. Animaciones de propiedad son una forma más eficaz y flexible para describir las animaciones en cualquier tipo de objeto.

-  **anim** &ndash; archivos XML que describen *interpolación* animaciones. Las animaciones de interpolación son una serie de instrucciones de animación para realizar transformaciones en el contenido de un giro del objeto, o un ejemplo, ver una imagen o aumentar el tamaño del texto. Las animaciones de interpolación se limitan a ver solo los objetos.

-  **color** &ndash; archivos XML que describen una lista de estado de colores. Para entender las listas de estado de color, considere la posibilidad de un widget de interfaz de usuario, como un botón.
   Puede hacer que tiene distintos Estados, como presionado o deshabilitado, y el botón puede cambiar el color con cada cambio de estado. La lista se expresa en una lista de estado.

-  **fuente** &ndash; a partir de nivel de API 26, es posible incrustar fuentes como un recurso en una aplicación de Android. El 26 de biblioteca de soporte técnico va a restituir fuentes a nivel de API 14. La incrustación de fuentes permite que las aplicaciones cargar las fuentes personalizadas directamente desde los diseños XML sin necesidad de importarlas como recursos antes de su uso.

-  **asignación de MIP** &ndash; Drawable recursos son un concepto general de los gráficos que se pueden compilar en la aplicación y, a continuación, accediendo a las llamadas de API o hace referencia a otros recursos XML.
   Algunos ejemplos de recursos drawable son archivos de mapa de bits (.png, .gif, .jpg), mapas de bits redimensionable especial conocida como [parches de nueve](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), muestra el estado de formas genéricas definidas en XML, etcetera.

-  **diseño** &ndash; archivos XML que describen un diseño de interfaz de usuario, como una actividad o una fila en una lista.

-  **menú** &ndash; archivos XML que describen los menús de la aplicación, como *opciones menús*, *menús contextuales*, y *submenús*. Para obtener un ejemplo de menús, vea el [demostración del menú emergente](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/) o [controles estándar](https://developer.xamarin.com/samples/mobile/StandardControls/) ejemplo.

-  **RAW** &ndash; archivos arbitrarios que se guardan en su formato binario sin formato. Estos archivos se compilan en una aplicación de Android en un formato binario.

-  **valores** &ndash; archivos XML que contienen valores simples. Un archivo XML en el directorio de los valores no define un único recurso, pero en su lugar, puede definir varios recursos. Por ejemplo, un archivo XML puede albergar una lista de valores de cadena, mientras que otro archivo XML puede contener una lista de valores de color.

-  **XML** &ndash; archivos XML que son una función similar a los archivos de configuración. NET. Estos son XML arbitrario que se puede leer en tiempo de ejecución por la aplicación

-----
