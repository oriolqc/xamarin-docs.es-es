---
title: "Introducción a guiones gráficos"
description: "Este artículo proporciona una introducción a trabajar con guiones gráficos en una aplicación Xamarin.Mac. En él, se describe cómo crear y mantener la interfaz de usuario de la aplicación mediante guiones gráficos e Interface Builder de Xcode."
ms.topic: article
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 21e35b056293e422b577b0ee8b51e8c43dbbf07d
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="introduction-to-storyboards"></a>Introducción a guiones gráficos

_Este artículo proporciona una introducción a trabajar con guiones gráficos en una aplicación Xamarin.Mac. Describe cómo crear y mantener la interfaz de usuario de la aplicación mediante guiones gráficos y el generador de interfaz de Xcode._

Guiones gráficos le permiten desarrollar una interfaz de usuario para la aplicación de Xamarin.Mac que no solo incluye las definiciones de ventanas y controles, pero también contiene los vínculos entre varias ventanas (a través de segues) y los Estados de vista.

[ ![](images/intro01.png "Un ejemplo de interfaz de usuario en Xcode.")](images/intro01.png)

Este artículo proporciona una introducción al uso de guiones gráficos para definir la interfaz de usuario de la aplicación de Xamarin.Mac.

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>¿Cuáles son los guiones gráficos?

Mediante el uso de guiones gráficos, todos de interfaz de usuario de la aplicación de Xamarin.Mac se pueden definir en una sola ubicación con todas las de la navegación entre sus elementos individuales y las interfaces de usuario. Guiones gráficos para Xamarin.Mac, funciona de forma muy similar a guiones gráficos para Xamarin.iOS. Sin embargo, que contienen un conjunto diferente de _desplazará tranquilamente tipos_ debido a las expresiones de interfaz diferente.

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>Trabajo con escenas

Como se mencionó anteriormente, un guión gráfico define todos los de la interfaz de usuario para una aplicación determinada que se divide en una introducción a las funciones de su _ver controladores de_. En el generador de interfaz de Xcode, cada uno de estos controladores vive en su propio _escena_.

[ ![](images/intro02.png "Un controlador de vista de ejemplo")](images/intro02.png)

Cada escena representa una vista determinada y un par de controladores de vista con un conjunto de líneas (denominado Segues) que se conectan cada escena en la interfaz de usuario, lo que permite mostrar las relaciones entre ellas. Algunos Segues definir cómo un controlador de vista contiene una o varias vistas secundarias o controladores de la vista. Otros Segues, definen las transiciones entre View Controller (por ejemplo, para mostrar un cuadro de diálogo o popover). 

[ ![](images/intro03.png "Un segue de ejemplo")](images/intro03.png)

Lo más importante a tener en cuenta es que cada Segue representa el flujo de algún tipo de datos entre el elemento especificado de la interfaz de usuario de la aplicación.

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>Trabajar con controladores de la vista

Ver controladores definen las relaciones entre una vista determinada de información dentro de una aplicación de Mac y el modelo de datos que proporciona dicha información. Cada escena de nivel superior en el guión gráfico representa un controlador de vista en el código de la aplicación Xamarin.Mac.

[ ![](images/intro04.png "Un ejemplo pospone view-controller")](images/intro04.png)

De este modo, cada controlador de vista es un emparejamiento independiente y reutilizables de la representación visual de la información (vista) y la lógica para presentar y controlar esa información.

Dentro de una escena determinada, puede hacer todo lo que normalmente se habría controlan individuo `.xib` archivos: 

 - Lugar subviews y los controles (como botones y cuadros de texto).
 - Definir las posiciones de los elementos y las restricciones de diseño automático.
 - El cable telefónico acciones y las salidas para exponer elementos de interfaz de usuario al código.

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>Trabajar con Segues

Como se mencionó anteriormente, Segues proporcionan las relaciones entre todas las escenas que definen la interfaz de usuario de la aplicación. Si está familiarizado con trabajar en guiones gráficos en iOS, sabrá que Segues para iOS normalmente definen las transiciones entre las vistas de pantalla completa. Esto difiere de macOS, cuando Segues normalmente definen "contención" (donde una escena es el elemento secundario de un elemento primario escena).

En Mac OS, mayoría de las aplicaciones tiende a agrupar sus vistas en la misma ventana usando los elementos de interfaz de usuario, como vistas de división y pestañas. A diferencia de iOS, donde vistas deben pasarse en y fuera de la pantalla, debido a limitadas física muestra espacio.

Proporciona tendencias del macOS hacia contención, hay situaciones donde _Segues presentación_ está acostumbrado, como ventanas modales, vistas de hoja y Popovers.

Al utilizar Segues de presentación, puede invalidar el `PrepareForSegue` método del controlador de vista de elemento primario para su presentación para inicializar y las variables y proporcionar los datos a la controladora de vista que se presentan.

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>Diseño y tiempos de ejecución

En tiempo de diseño (al diseño de la interfaz de usuario en el generador de interfaz de Xcode), cada elemento de interfaz de usuario de la aplicación se divide en sus elementos constituyentes:

- **Plano** , que se compone de:
    - **Ver controlador** -que definen las relaciones entre las vistas y los datos que las admiten.
    - **Vistas y subvistas** -los elementos reales que componen la interfaz de usuario.
    - **Contención Segues** -que definen las relaciones de elementos primarios y secundarios entre bastidores.
- **Presentación Segues** -que definen los modos de presentación individuales. 

Mediante la definición de cada elemento de esta manera, permite la carga diferida de cada elemento solo según sea necesario en tiempo de ejecución. En Mac OS, todo el proceso se diseñó para permitir que los desarrolladores pueden crear Interfaces de usuario complejo y flexible que requieren una configuración mínima de realizar una copia de código para hacer que el trabajo, mientras está tan eficaz con los recursos del sistema como sea posible.

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>Inicio rápido de guión gráfico

En el [inicio rápido de guión gráfico](~/mac/platform/storyboards/quickstart.md) guía, vamos a crear una aplicación sencilla de Xamarin.Mac que presenta los conceptos básicos sobre cómo trabajar con guiones gráficos para crear una interfaz de usuario. La aplicación de ejemplo constará de una vista Dividir que contiene un _área de contenido_ y _Inspector área_ y presentará una ventana de cuadro de diálogo Preferencias simple. Usaremos Segues para unir todos los elementos de interfaz de usuario.

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>Trabajar con guiones gráficos

Esta sección cubre los detalles detallados de [trabajar con guiones gráficos](~/mac/platform/storyboards/indepth.md) en una aplicación Xamarin.Mac. Se hace una visión detallada de escenas y cómo están formados por controladores de la vista y vista. A continuación, se echará un vistazo a cómo está vinculada plano junto con Segues. Por último, echaremos un vistazo al trabajar con tipos personalizados de Segue. 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Ejemplo de guión gráfico complejo

Para obtener un ejemplo de un ejemplo complejo de trabajar con guiones gráficos en una aplicación Xamarin.Mac, consulte el [aplicación de ejemplo de SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter es un editor de código fuente simple que proporciona compatibilidad con la finalización de código y el resaltado de sintaxis simple.

El código de SourceWriter se ha comentado completamente y, si están disponibles, se han proporcionado vínculos de métodos o tecnologías clave a información relevante en la documentación de las guías de Xamarin.Mac.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado un vistazo al trabajar con guiones gráficos en una aplicación Xamarin.Mac. Hemos visto cómo crear una nueva aplicación con guiones gráficos y cómo definir una interfaz de usuario. También hemos visto cómo navegar entre varias ventanas y Estados de vista utilizando segues.


## <a name="related-links"></a>Vínculos relacionados

- [Hola, Mac (ejemplo)](https://developer.xamarin.com/samples/mac/Hello_Mac/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con ventanas](~/mac/user-interface/window.md)
- [Directrices de interfaz de sistema operativo X humanos](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
