---
title: Patrones y expresiones en Xamarin.Mac comunes
description: Este documento describe los patrones comunes de diseño que se usan para crear aplicaciones de Xamarin.Mac. Explica el modelo model-view-controller, los patrones de delegado y de origen de datos y protocolos.
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/17/2016
ms.openlocfilehash: f6bba5575edf2dcbddbd354b590e03f9fed06291
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791542"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Patrones y expresiones en Xamarin.Mac comunes

A lo largo de las API de Apple que se expone a través de C#, determinados patrones y giros de dar una y otra vez. Si tiene experiencia con la programación con Xamarin.iOS, estos pueden ser familiares. Documentación a menudo hará referencia a estos patrones y expresiones varias veces, por lo que tener un conocimiento sólido de ellos le ayudará a tener sentido de la documentación que encuentre.

## <a name="mvc---model-view-controller"></a>MVC - Model View Controller

Model View Controller o MVC para abreviar, es un modelo muy común que se encuentra a lo largo de cacao. Obtener una explicación detallada está fuera del ámbito de este documento, pero en resumen es una forma de estructurar la aplicación en componentes:

- **Modelo** objetos representan el subyacente datos que se va a ver y manipular (por ejemplo, direcciones de una libreta de direcciones)
- **Vista** objetos controlen el dibujo de un objeto determinado en pantalla y controlar la interacción del usuario (un campo de texto que muestra la dirección en la pantalla)
- **Controlador** objetos controlen la interacción entre el modelo y la vista. Cambios en el modelo de inserción "hasta" para actualizar la vista e insertar "abajo" cambios de la vista cuando los usuarios realizar cambios en la interfaz de usuario.

Si está familiarizado con MVVM (Model View ViewModel) de otras bibliotecas, como WPF, el controlador actúa como el modelo de vista, pero a menudo está más estrechamente ligado a los elementos de interfaz de usuario específicos.

Aquí encontrará información más detallada:

- [Aprendizaje de MVC en el sitio Web de Apple](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Model View Controller en Objective C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Trabajar con ventanas](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>Origen de datos / delegar / creación de subclases

Otro patrón muy común en cacao ocupa proporcionen datos a los elementos de interfaz de usuario y reaccionar a las interacciones del usuario. Usar `NSTableView` como un ejemplo, si necesita algún modo proporcionar los datos para cada fila, algunos conjunto de elementos de interfaz de usuario que representan que esa fila, algunos conjunto de comportamientos para reaccionar a las interacciones del usuario y posiblemente cierta cantidad de personalización. Los modelos de origen y el delegado de datos le permiten controlar la mayoría de los casos sin tener que recurrir a la creación de subclases `NSTableView` usted mismo.

- El `DataSource` propiedad se asigna una instancia de una subclase personalizada de `NSTableViewDataSource` que se llama para rellenar la tabla con datos (a través de `GetRowCount` y `GetObjectValue`).

- El `Delegate` propiedad se asigna una instancia de una subclase personalizada de `NSTableViewDelegate` que proporciona la vista para un objeto de modelo determinado (a través de `GetViewForItem`) y administra las interacciones de la interfaz de usuario (a través de `DidClickTableColumn`, `MouseDownInHeaderOfTableColumn`, etcetera).

En algunos casos, deseará personalizar un control de una manera más allá de los enlaces en el delegado u origen de datos y puede subclase la vista directamente. Tenga cuidado de no obstante, en muchos casos invalida configuración predeterminada del comportamiento, a continuación, será necesario administrar todo ese comportamiento (Personalizar el comportamiento de la selección conlleven implementar todos los comportamientos de selección).

En Xamarin.iOS, algunas API, como `UITableView` se han ampliado con una propiedad que implementa el delegado y el origen de datos (`UITableViewSource`). Esto para solucionar la limitación común que una sola clase de C# solo puede tener una clase base y nuestro así como la exposición de protocolos se realiza mediante las clases base.

Para obtener más información sobre cómo trabajar con vistas de tabla en una aplicación Xamarin.Mac, visite nuestro [vista de tabla](~/mac/user-interface/table-view.md) documentación.

## <a name="protocols"></a>Protocolos

Protocolos en Objective C se pueden comparar con interfaces de C# y en muchos casos se utilizan en situaciones similares. Por ejemplo el `NSTableView` ejemplo anterior, el delegado y el origen de datos son realmente protocolos. Xamarin.Mac expone como clases base con métodos virtuales que se puede invalidar. La principal diferencia entre las interfaces de C# y protocolos Objective-C es que algunos métodos en un protocolo pueden ser opcionales para implementar. Tendrá que buscar en la documentación y/o la definición de una API para determinar lo que es opcional.

Obtener más información visite nuestro [delegados, protocolos y eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md) documentación.



## <a name="related-links"></a>Vínculos relacionados

- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Trabajar con ventanas](~/mac/user-interface/window.md)
- [Delegados, protocolos y eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
