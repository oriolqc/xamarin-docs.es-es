---
title: Introducción a guiones gráficos en Xamarin.Mac
description: En este artículo se proporciona una introducción al trabajo con guiones gráficos en una aplicación de Xamarin.Mac. En él, se describe cómo crear y mantener la interfaz de usuario de la aplicación mediante guiones gráficos e Interface Builder de Xcode.
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 8a5a2f87c16a5dd040cefb2fbc615b01431ebcf5
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832301"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Introducción a guiones gráficos en Xamarin.Mac

_En este artículo se proporciona una introducción al trabajo con guiones gráficos en una aplicación de Xamarin.Mac. Describe cómo crear y mantener la interfaz de usuario de la aplicación mediante guiones gráficos e Interface Builder de Xcode._

Guiones gráficos le permiten desarrollar una interfaz de usuario para la aplicación de Xamarin.Mac que incluye no solo los controles y las definiciones de la ventana, pero también contiene los vínculos entre diferentes ventanas (mediante objetos Segue) y Estados de vista.

[![](images/intro01.png "Un ejemplo de interfaz de usuario en Xcode")](images/intro01.png#lightbox)

Este artículo ofrecerá una introducción al uso de guiones gráficos para definir la interfaz de usuario de una aplicación de Xamarin.Mac.

<a name="What-are-Storyboards" />

## <a name="what-are-storyboards"></a>¿Cuáles son los guiones gráficos?

Mediante el uso de guiones gráficos, todo de interfaz de usuario de una aplicación de Xamarin.Mac se pueden definir en una sola ubicación con toda la navegación entre sus elementos individuales y las interfaces de usuario. Guiones gráficos para Xamarin.Mac, funcionan de manera muy similar a los guiones gráficos para Xamarin.iOS. Sin embargo, que contienen un conjunto diferente de _Segue tipos_ debido a las expresiones de interfaz diferente.

<a name="Working-with-Scenes" />

### <a name="working-with-scenes"></a>Trabajando en segundo plano

Como se indicó anteriormente, un guión gráfico define todos los de la interfaz de usuario para una aplicación determinada que se divide en una introducción a las funciones de su _controladores de vista_. En Interface Builder de Xcode, cada uno de estos controladores se encuentra en su propio _escena_.

[![](images/intro02.png "Un controlador de vista de ejemplo")](images/intro02.png#lightbox)

Cada escena representa una vista y un par de controladores de vista determinado con un conjunto de líneas (denominados objetos Segue) que se conectan cada escena en la interfaz de usuario, lo que permite mostrar las relaciones entre ellas. Algunos objetos Segue definir cómo un controlador de vista contiene uno o más vistas secundarias o controladores de vista. Otro objetos Segue, definen las transiciones entre el controlador de vista (por ejemplo, para mostrar un cuadro de diálogo o el elemento flotante). 

[![](images/intro03.png "Un segue de ejemplo")](images/intro03.png#lightbox)

Lo más importante a tener en cuenta es que cada Segue representa el flujo de algún tipo de datos entre el elemento especificado de la interfaz de usuario de la aplicación.

<a name="Working-with-View-Controllers" />

### <a name="working-with-view-controllers"></a>Trabajar con controladores de vista

Controladores de vista definen las relaciones entre una vista determinada de información dentro de una aplicación de Mac y el modelo de datos que proporciona esa información. Cada escena de nivel superior en el guión gráfico representa un controlador de vista en el código de la aplicación de Xamarin.Mac.

[![](images/intro04.png "Un ejemplo pospone el controlador de vista")](images/intro04.png#lightbox)

De este modo, cada controlador de vista es un emparejamiento independiente y reutilizables de la representación visual de la información (vista) y la lógica para presentar y controlar esa información.

Dentro de la escena, puede hacer todo lo que normalmente habría sido controlados por individuo `.xib` archivos: 

- Lugar subviews y controles (como botones y cuadros de texto).
- Definir las posiciones de los elementos y las restricciones de diseño automático.
- Transmisión las acciones y salidas para exponer elementos de interfaz de usuario al código.

<a name="Working-with-Segues" />

### <a name="working-with-segues"></a>Trabajar con objetos Segue

Como se indicó anteriormente, los objetos Segue proporcionan las relaciones entre todas las escenas que definen la interfaz de usuario de la aplicación. Si está familiarizado con los guiones gráficos trabaje en iOS, sabe que los objetos Segue para iOS normalmente definen las transiciones entre las vistas de pantalla completa. Esto difiere de macOS, cuando los objetos Segue normalmente definen "contención" (donde una escena es el elemento secundario de un elemento primario escena).

En macOS, mayoría de las aplicaciones tiende a agrupar sus vistas juntos dentro de la misma ventana mediante elementos de interfaz de usuario, como vistas de división y pestañas. A diferencia de iOS, donde se necesitan vistas que se pueden pasar en y fuera de la pantalla, Mostrar espacio debido a la física limitada.

Dadas las tendencias de macOS hacia la contención, existen situaciones donde _objetos Segue de presentación_ se usan, como Windows Modal, vistas de hoja y Popovers.

Al usar los objetos Segue de presentación, puede invalidar el `PrepareForSegue` método del controlador de vista principal para su presentación para inicializar y las variables y proporcionar los datos para el controlador de vista que se presentan.

<a name="Design-and-Run-Times" />

### <a name="design-and-run-times"></a>Diseño y tiempos de ejecución

En tiempo de diseño (al diseño de la interfaz de usuario en Interface Builder de Xcode), cada elemento de interfaz de usuario de la aplicación se desglosa en sus elementos constituyentes:

- **Segundo plano** , que se componen de:
    - **Ver controlador** -que definen las relaciones entre las vistas y los datos que los respaldan.
    - **Las vistas y subvistas** -los elementos reales que componen la interfaz de usuario.
    - **Objetos Segue contención** -que definen las relaciones de elementos primarios y secundarios entre bastidores.
- **Objetos Segue de presentación** -que definen los modos de presentación individuales. 

Al definir cada elemento de esta manera, permite para la carga diferida de cada elemento solo según sea necesario durante el tiempo de ejecución. En macOS, todo el proceso se diseñó para permitir que el desarrollador crear Interfaces de usuario complejas y flexibles que requieren un mínimo de seguridad de código para que funcionen, todo mientras está tan eficaz con los recursos del sistema como sea posible.

<a name="Storyboard-Quick-Start" />

## <a name="storyboard-quick-start"></a>Inicio rápido de guión gráfico

En el [inicio rápido de guión gráfico](~/mac/platform/storyboards/quickstart.md) guía, vamos a crear una sencilla aplicación de Xamarin.Mac que presenta los conceptos básicos de trabajar con guiones gráficos para crear una interfaz de usuario. La aplicación de ejemplo se compone de una vista Dividir que contiene un _área de contenido_ y un _Inspector área_ y presentará una ventana simple del cuadro de diálogo Preferencias. Vamos a usar objetos Segue para unir todos los elementos de interfaz de usuario.

<a name="Working-with-Storyboards" />

## <a name="working-with-storyboards"></a>Trabajo con guiones gráficos

Esta sección tratan los detalles pormenorizados de [trabajar con guiones gráficos](~/mac/platform/storyboards/indepth.md) en una aplicación de Xamarin.Mac. Echamos una mirada exhaustiva de segundo plano y cómo están formados por los controladores de vista y vista. A continuación, le echamos un vistazo a cómo están vinculados escenas junto con los objetos Segue. Por último, le echamos un vistazo a cómo se trabaja con tipos personalizados de Segue. 

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Ejemplo de guion gráfico complejo

Para obtener un ejemplo de un ejemplo de cómo trabajar con guiones gráficos en una aplicación de Xamarin.Mac complejo, vea el [aplicación de ejemplo de SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter es un editor de código fuente simple que proporciona compatibilidad con la finalización de código y el resaltado de sintaxis simple.

El código de SourceWriter se ha comentado completamente y, si están disponibles, se han proporcionado vínculos de métodos o tecnologías clave a información relevante en la documentación de las guías de Xamarin.Mac.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado un vistazo al trabajo con guiones gráficos en una aplicación de Xamarin.Mac. Hemos visto cómo crear una nueva aplicación mediante guiones gráficos y cómo definir una interfaz de usuario. También vimos cómo navegar entre diferentes ventanas y Estados de vista mediante objetos Segue.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Hello, Mac (ejemplo)](https://developer.xamarin.com/samples/mac/Hello_Mac/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
