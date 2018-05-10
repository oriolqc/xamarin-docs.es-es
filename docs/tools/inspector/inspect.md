---
title: Inspeccionar las aplicaciones activas
description: Visualizar y depurar la aplicación en vivo
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: 8bcdc5ac50a0f03086ad9e9c3265e3ce86b06704
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="inspecting-live-applications"></a>Inspeccionar las aplicaciones activas

Inspección de la aplicación activa está disponible para los clientes de empresa.


1. [Instalar Xamarin libros & Inspector.](~/tools/inspector/install.md)

1. Abrir cualquier [admite el proyecto de aplicación](~/tools/inspector/install.md#supported-platforms) en Visual Studio para Mac o en Visual Studio.
1. Ejecute la aplicación en modo de depuración.
1. Haga clic en el **inspeccionar** botón en la barra de herramientas IDE (en Visual Studio, el **inspeccionar de aplicación actual...**  elemento de menú también está disponible en la **herramientas** o **depurar** menú).



[![](inspect-images/mac-heres-the-button.png "Haga clic en el botón Inspeccionar en la barra de herramientas IDE")](inspect-images/mac-heres-the-button.png#lightbox)

Se abrirá una nueva ventana de cliente de Xamarin Inspector, con un nuevo símbolo del sistema de replicación.

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "Se abrirá una nueva ventana de cliente de Xamarin Inspector, con una nueva solicitud de replicación")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

Cuando aparezca esta ventana, tendrá un aviso interactivo en C# que puede usar para ejecutar y evaluar expresiones e instrucciones de C#. ¿Qué hace único es que el código se evalúa en el contexto del proceso de destino. En este caso, vamos a presentar el código que se ejecuta en la aplicación de iOS que se muestran.

Los cambios realizados en el estado de la aplicación realmente se producen en el proceso de destino, por lo que puede usar C# para cambiar la aplicación live o puede inspeccionar el estado de la aplicación en vivo.

Por ejemplo, en iOS, que nos convenga buscar la clase de delegado UIApplication, que es nuestro controlador principal (donde se almacena en una gran cantidad de estado de la aplicación):

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

(Tenga en cuenta que cada envío se produce en un editor de varias líneas. `Shift + Enter` se creará una nueva línea, y `Cmd + Enter` (`Ctrl + Enter` en Windows) enviará el código para la evaluación. `Enter` envía automáticamente cuándo es seguro.)

Una forma más conveniente de acceder a los elementos visuales de la aplicación es mediante el botón "Inspeccionar". Una vez que presione esto, puede seleccionar un elemento de interfaz de usuario, haga clic en la aplicación. La variable `selectedView` se asignará a apuntar al elemento real en la pantalla. En la captura de pantalla anterior, puede ver cómo se tiene acceso y, a continuación, editar `selectedView.BarTintColor` en el `UISearchBar` seleccionamos.

Live visual tree también es muy útil. Representa la instantánea actual de la jerarquía de vista. Puede seleccionar filas para establecer `selectedView` en la replicación y para ver los valores de propiedad de la vista. En el equipo Mac, puede interactuar con una visualización seccionada 3D de las vistas en capas. En Windows, puede editar los valores de propiedad de una vista visualmente.

## <a name="known-limitations"></a>Limitaciones conocidas

 - Solo se admite la selección de la vista en la pantalla principal.
 - Edición de cuadrícula de propiedades no está disponible para Mac y en Windows se limita a algunos tipos de datos. Utilice la replicación para una edición más eficaz.
 - Mientras el Inspector addin/extensión está instalada y habilitada en el IDE, nos estamos insertar código en su aplicación cada vez que inicia en modo de depuración. Si observa cualquier comportamiento extraño en la aplicación, realice una intente deshabilitar o desinstalar el complemento de Inspector/extensión, reinicie el IDE y comprobación. Y vuelva [informar de errores](~/tools/inspector/install.md#reporting-bugs) para hacernos saber!
 - Caso inspeccionar un elemento de interfaz de usuario hace que se cambie de todos modos, [háganoslo saber](~/tools/inspector/install.md#reporting-bugs), ya que esto puede indicar un error.

