---
title: Inspección de aplicaciones activas
description: Este documento describe cómo usar el Inspector de Xamarin para inspeccionar las aplicaciones. También describe las limitaciones de la herramienta de Xamarin Inspector.
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2def0a01bdd28af5eefb76afc19a0e49fd1df355
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831550"
---
# <a name="inspecting-live-applications"></a>Inspección de aplicaciones activas

Inspección de la aplicación activa está disponible para los clientes empresariales.

1. Abra cualquiera [admite el proyecto de aplicación](~/tools/inspector/install.md#supported-platforms) en Visual Studio para Mac o Visual Studio.
1. Ejecute la aplicación en modo de depuración.
1. Haga clic en el **inspeccionar** botón en la barra de herramientas IDE (en Visual Studio, el **inspeccionar de aplicación actual...**  también está disponible en el elemento de menú el **herramientas** o **depurar** menú).

[![](inspect-images/mac-heres-the-button.png "Haga clic en el botón Inspeccionar en la barra de herramientas IDE")](inspect-images/mac-heres-the-button.png#lightbox)

Se abrirá una nueva ventana de cliente de Xamarin Inspector, con un nuevo símbolo del sistema REPL.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "Se abrirá una nueva ventana de cliente de Xamarin Inspector, con un nuevo símbolo del sistema REPL")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Una vez que aparece esta ventana, tendrá una interactivo C# símbolo del sistema que puede usar para ejecutar y evaluar C# instrucciones y expresiones. ¿Qué hace únicos es que el código se evalúa en el contexto del proceso de destino. En este caso, vamos a presentar el código que se ejecuta en la aplicación de iOS muestra.

Los cambios realizados en el estado de la aplicación realmente se producen en el proceso de destino, por lo que puede usar C# para cambiar la aplicación live, o puede inspeccionar el estado de la aplicación en directo.

Por ejemplo, en iOS, podríamos queremos encontrar nuestra clase de delegado de UIApplication, que es nuestro controlador principal (donde se almacenan en una gran cantidad de estado de la aplicación):

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

(Tenga en cuenta que cada envío se produce en un editor de varias líneas. `Shift + Enter` creará una nueva línea, y `Cmd + Enter` (`Ctrl + Enter` en Windows) enviará el código para la evaluación. `Enter` envía de forma automática cuando la seguridad.)

Una manera más cómoda para llegar a los elementos visuales de la aplicación es mediante el botón "Inspeccionar". Una vez que presione esto, puede seleccionar un elemento de interfaz de usuario haciendo clic en la aplicación. La variable `selectedView` se asignará a apuntar al elemento real en la pantalla. En la captura de pantalla anterior, puede ver cómo acceder y, a continuación, puede editar `selectedView.BarTintColor` en el `UISearchBar` nos hubiéramos seleccionado.

El árbol visual también es muy útil. Representa la instantánea actual de la jerarquía de vistas. Puede seleccionar filas que se va a establecer `selectedView` en REPL y para ver los valores de propiedad de la vista. En Mac, puede interactuar con una visualización seccionada 3D de las vistas en capas. En Windows, puede editar los valores de propiedad de una vista visualmente.

## <a name="known-limitations"></a>Limitaciones conocidas

- Solo se admite la selección de la vista en la pantalla principal.
- Edición de la cuadrícula de propiedades no está disponible para Mac y en Windows se limita a algunos tipos de datos. Utilice la replicación para una edición más eficaz.
- Mientras el complemento/extensión de Inspector está instalada y habilitada en el IDE, vamos a insertar código en la aplicación cada vez que se inicia en modo de depuración. Si observa cualquier comportamiento extraño en su aplicación, vuelva a intentar deshabilitar o desinstalar el complemento/extensión de Inspector, reiniciar el IDE y nueva comprobación. Y, por favor, [archivar errores](~/tools/inspector/install.md#reporting-bugs) para hacernos saber!
- Inspeccionar un elemento de interfaz de usuario hace que ésta cambie en cualquier caso, por favor, [háganoslo saber](~/tools/inspector/install.md#reporting-bugs), ya que esto puede indicar un error.

