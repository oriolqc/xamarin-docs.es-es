---
title: Enlace de datos en Xamarin.Forms
description: Enlace de datos es la técnica de vincular las propiedades de dos objetos para que los cambios en una propiedad se reflejan automáticamente en la otra propiedad. Enlace de datos es una parte integral de la arquitectura de aplicación Model-View-ViewModel (MVVM).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2018
ms.openlocfilehash: def97ab77781c7a7156d4c4178097184614f3e8b
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2018
ms.locfileid: "35240358"
---
# <a name="xamarinforms-data-binding"></a>Enlace de datos en Xamarin.Forms

_Enlace de datos es la técnica de vincular las propiedades de dos objetos para que los cambios en una propiedad se reflejan automáticamente en la otra propiedad. Enlace de datos es una parte integral de la arquitectura de aplicación Model-View-ViewModel (MVVM)._

## <a name="the-data-linking-problem"></a>El problema de vinculación de datos

Una aplicación de Xamarin.Forms consta de una o varias páginas, cada uno de los cuales generalmente contiene varios objetos de interfaz de usuario denominados *vistas*. Una de las tareas principales del programa es para mantener estas vistas sincronizadas y realizar un seguimiento de los distintos valores o selecciones que representan. A menudo representan las vistas de los valores de un origen de datos subyacente y el usuario manipula estas vistas para cambiar esos datos. Cuando se cambia la vista, los datos subyacentes deben reflejar ese cambio, y de forma similar, cuando cambian los datos subyacentes, ese cambio debe reflejarse en la vista.

Para controlar este trabajo correctamente, el programa debe recibir una notificación de cambios en estas vistas o los datos subyacentes. La solución habitual consiste en definir los eventos que indican cuando se produce un cambio. A continuación, se instala un controlador de eventos que se le informa de estos cambios. Responde mediante la transferencia de datos de un objeto a otro. Sin embargo, cuando existen muchas vistas, también debe haber muchos controladores de eventos y se implique una gran cantidad de código.

## <a name="the-data-binding-solution"></a>La solución de enlace de datos

Enlace de datos automatiza este trabajo y representa los controladores de eventos innecesarios. (Los eventos son todavía es necesarios, sin embargo, dado que usa la infraestructura de enlace de datos). Enlaces de datos pueden implementarse en el código o en XAML, pero son mucho más común en XAML que ayudan a reducir el tamaño del archivo de código subyacente. Al reemplazar el código de procedimientos en los controladores de eventos con código declarativo o marcado, la aplicación está simplificada y se ha aclarado.

Uno de los dos objetos implicados en un enlace de datos casi siempre es un elemento que se deriva de `View` y forma parte de la interfaz visual de una página. El otro objeto es:

- Otro `View` derivados, normalmente en la misma página.
- Un objeto en un archivo de código.

En programas de demostración, como los de la [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) de ejemplo, los enlaces de datos entre dos `View` derivados a menudo se muestran con fines de claridad y simplicidad. Sin embargo, se pueden aplicar los mismos principios a los enlaces de datos entre un `View` y otros objetos. Cuando se compila una aplicación con la arquitectura Model-View-ViewModel (MVVM), la clase con los datos subyacentes a menudo se denomina un modelo de vista.

Se abordan los enlaces de datos en la siguiente serie de artículos:

## <a name="basic-bindingsbasic-bindingsmd"></a>[Enlaces básicos](basic-bindings.md)

Obtenga información sobre la diferencia entre el origen y destino de enlace de datos y ver los enlaces de datos simple en el código y XAML.

## <a name="binding-modebinding-modemd"></a>[Modo de enlace](binding-mode.md)

Descubra cómo el modo de enlace puede controlar el flujo de datos entre los dos objetos.

## <a name="string-formattingstring-formattingmd"></a>[Formato de cadena](string-formatting.md)

Utilice un enlace de datos para dar formato y mostrar los objetos como cadenas.

## <a name="binding-pathbinding-pathmd"></a>[Enlace de ruta de acceso](binding-path.md)

Profundiza en el `Path` propiedad del enlace de datos para tener acceso a las subpropiedades y miembros de la colección.

## <a name="binding-value-convertersconvertersmd"></a>[Enlace de convertidores de valores](converters.md)

Usar enlace de convertidores para modificar valores en el enlace de datos.

## <a name="binding-fallbacksbinding-fallbacksmd"></a>[Reservas de enlace](binding-fallbacks.md)

Fortalecer los enlaces de datos mediante la definición de valores de reserva para su uso si se produce un error en el proceso de enlace.

## <a name="the-command-interfacecommandingmd"></a>[Interfaz de comandos](commanding.md)

Implemente el `Command` propiedad con los enlaces de datos.

## <a name="compiled-bindingscompiled-bindingsmd"></a>[Enlaces compilados](compiled-bindings.md)

Usar enlaces compilados para mejorar el rendimiento de enlace de datos.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
