---
title: Patrones y expresiones en Xamarin.Mac comunes
description: Este documento describe los patrones de diseño comunes que usan para crear aplicaciones de Xamarin.Mac. Describe el patrón model-view-controller, los patrones de datos de origen y el delegado y protocolos.
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 06/17/2016
ms.openlocfilehash: b4266582ce0cec522e207cd06987f2d0eeff8b2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61090901"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Patrones y expresiones en Xamarin.Mac comunes

A lo largo de las API de Apple que se expone a través de C#, determinadas expresiones y patrones iniciará una y otra vez. Si tiene experiencia con la programación con Xamarin.iOS, estas pueden resultar familiares. Documentación a menudo hará referencia a estos patrones y expresiones varias veces, por lo que tener un conocimiento sólido de ellos le ayudarán a dar sentido a la documentación que encuentre.

## <a name="mvc---model-view-controller"></a>MVC - Model View Controller

Controlador de vista de modelo, o MVC para abreviar, es un patrón muy común encontrar a lo largo de cacao. Obtener una explicación detallada está fuera del ámbito de este documento, pero en resumen es una manera de estructurar la aplicación en componentes:

- **Modelo** objetos representan el subyacente datos que se va a ver y manipular (como las direcciones en una libreta de direcciones)
- **Vista** objetos controlan el dibujo de un objeto determinado en pantalla y controlar la interacción del usuario (un campo de texto que muestra la dirección en la pantalla)
- **Controlador** objetos administran la interacción entre el modelo y la vista. Insertar cambios en el modelo "arriba" para actualizar la vista e insertar "down" cambios de la vista cuando los usuarios realizan cambios en la interfaz de usuario.

Si está familiarizado con MVVM (Model View ViewModel) de otras bibliotecas como WPF, el controlador actúa similar al ViewModel, pero a menudo se enlaza más estrechamente a los elementos de interfaz de usuario específicos.

Aquí encontrará más detalles:

- [Aprendizaje de MVC en el sitio Web de Apple](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Model View Controller en Objective-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Trabajar con Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>Origen de datos / delegar / creación de subclases

Otro patrón muy común en el cacao se ocupa de proporcionar datos a los elementos de interfaz de usuario y reaccionar a las interacciones del usuario. Uso de `NSTableView` como por ejemplo, deberá proporcionar alguna manera los datos para cada fila, establecen algunas de los elementos de interfaz de usuario que representan a los que esa fila, algunos conjunto de comportamientos para reaccionar ante las interacciones del usuario y posiblemente cierta cantidad de personalización. Los patrones de datos de origen y el delegado le permiten controlar la mayoría de los casos sin tener que recurrir a la creación de subclases `NSTableView` usted mismo.

- El `DataSource` propiedad se asigna a una instancia de una subclase personalizada de `NSTableViewDataSource` que se llama para rellenar la tabla con datos (a través de `GetRowCount` y `GetObjectValue`).

- El `Delegate` propiedad se asigna a una instancia de una subclase personalizada de `NSTableViewDelegate` que proporciona la vista para un objeto de modelo determinado (a través de `GetViewForItem`) y administra las interacciones de la interfaz de usuario (a través de `DidClickTableColumn`, `MouseDownInHeaderOfTableColumn`, etcetera).

En algunos casos, desea personalizar un control de una manera más allá de los enlaces en el origen de datos o de delegado y puede subclase la vista directamente. Tenga cuidado de no obstante, en muchos casos invalida configuración predeterminada del comportamiento, a continuación, será necesario que controle todo ese comportamiento (al personalizar el comportamiento de selección puede requerir que implemente todos los comportamientos de selección).

En Xamarin.iOS, algunas API, como `UITableView` se han ampliado con una propiedad que implementa el delegado y el origen de datos (`UITableViewSource`). Este departamento de TI solucionar la limitación común que una sola C# clase solo puede tener una clase base y la superficie de protocolos se realiza a través de las clases base.

Para obtener más información sobre cómo trabajar con vistas de tabla en una aplicación de Xamarin.Mac, consulte nuestra [vista de tabla](~/mac/user-interface/table-view.md) documentación.

## <a name="protocols"></a>Protocolos

Protocolos en Objective-C se pueden comparar con las interfaces de C#y en muchos casos, se usan en situaciones similares. Por ejemplo el `NSTableView` ejemplo anterior, el delegado y el origen de datos son realmente los protocolos. Xamarin.Mac expone como clases base con los métodos virtuales que se puede invalidar. La diferencia principal entre C# interfaces y protocolos de Objective-C es que algunos métodos en un protocolo pueden ser opcionales para implementar. Tendrá que examinar la documentación y la definición de una API para determinar lo que es opcional.

Obtener más información, consulte nuestra [delegados, protocolos y eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md) documentación.



## <a name="related-links"></a>Vínculos relacionados

- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Trabajar con windows](~/mac/user-interface/window.md)
- [Los delegados, protocolos y eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
