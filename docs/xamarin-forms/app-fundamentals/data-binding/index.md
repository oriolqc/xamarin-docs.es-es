---
title: Enlace de datos
description: Enlace de datos es la técnica de vincular las propiedades de dos objetos para que los cambios en una propiedad se reflejan automáticamente en la otra propiedad. Enlace de datos es una parte integral de la arquitectura de aplicación de Model-View-ViewModel (MVVM).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: ee8481696b0ef85aec949c6def7767e57eb99e17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="data-binding"></a>Enlace de datos

_Enlace de datos es la técnica de vincular las propiedades de dos objetos para que los cambios en una propiedad se reflejan automáticamente en la otra propiedad. Enlace de datos es una parte integral de la arquitectura de aplicación de Model-View-ViewModel (MVVM)._

## <a name="the-data-linking-problem"></a>El problema de vinculación de datos

Una aplicación de Xamarin.Forms está formada por una o varias páginas, cada uno de los cuales normalmente contiene varios objetos de interfaz de usuario llama a *vistas*. Una de las tareas principales del programa es para mantener estas vistas sincronizadas y realizar un seguimiento de los distintos valores o selecciones que representan. A menudo representan las vistas de los valores de un origen de datos subyacente, y el usuario manipula estas vistas para cambiar esos datos. Cuando se cambia la vista, los datos subyacentes deben reflejar ese cambio, y de forma similar, cuando cambian los datos subyacentes, ese cambio debe reflejarse en la vista.

Para controlar este trabajo correctamente, el programa debe recibir una notificación de cambios en estas vistas o los datos subyacentes. La solución habitual consiste en definir eventos que indican cuando se produce un cambio. A continuación, se instala un controlador de eventos que se envía la notificación de estos cambios. Responde al transferir datos de un objeto a otro. Sin embargo, cuando hay muchas vistas, también debe haber muchos controladores de eventos, y una gran cantidad de código obtiene implicado.

## <a name="the-data-binding-solution"></a>La solución de enlace de datos

Enlace de datos automatiza este trabajo y representa los controladores de eventos innecesarios. (Los eventos son todavía es necesarios, sin embargo, dado que usa la infraestructura de enlace de datos). Enlaces de datos se pueden implementar en el código o en XAML, pero son mucho más comunes en XAML que ayudan a reducir el tamaño del archivo de código subyacente. Si se reemplaza el código de procedimiento en controladores de eventos con código declarativo o marcado, la aplicación se simplifica y se ha aclarado.

Uno de los dos objetos implicados en un enlace de datos casi siempre es un elemento que se deriva de `View` y forma parte de la interfaz visual de una página. El otro objeto es:

- Otro `View` derivado, normalmente en la misma página.
- Un objeto en un archivo de código.

En programas de demostración, como los de la [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) de ejemplo, los enlaces de datos entre dos `View` derivados a menudo se muestran para los fines de claridad y simplicidad. Sin embargo, se pueden aplicar los mismos principios a los enlaces de datos entre un `View` y otros objetos. Cuando una aplicación se compila con la arquitectura Model-View-ViewModel (MVVM), la clase con los datos subyacentes se suele denominar un modelo de vista.

Enlaces de datos se describen en la siguiente serie de artículos:

## <a name="basic-bindingsbasic-bindingsmd"></a>[Enlaces básicos](basic-bindings.md)

Obtenga información acerca de la diferencia entre el enlace de datos de origen y de destino y ver los enlaces de datos simple en el código y XAML.

## <a name="binding-modebinding-modemd"></a>[Modo de enlace](binding-mode.md)

Descubra cómo el modo de enlace puede controlar el flujo de datos entre los dos objetos.

## <a name="string-formattingstring-formattingmd"></a>[Formato de cadena](string-formatting.md)

Utilizar un enlace de datos para dar formato y mostrar objetos como cadenas.

## <a name="binding-pathbinding-pathmd"></a>[Enlace de ruta de acceso](binding-path.md)

Profundizar más en la `Path` propiedad del enlace de datos para tener acceso a miembros de la colección y subpropiedades.

## <a name="binding-value-convertersconvertersmd"></a>[Enlace de convertidores de valores](converters.md)

Use los convertidores de valores de enlace para modificar valores en el enlace de datos.

## <a name="the-command-interfacecommandingmd"></a>[Interfaz de comandos](commanding.md)

Implemente el `Command` propiedad con enlaces de datos.



## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos de la libreta de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
