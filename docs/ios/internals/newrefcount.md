---
title: Nueva referencia de recuento de sistema de Xamarin.iOS
description: Este documento describe el sistema, habilitado de forma predeterminada en todas las aplicaciones de Xamarin.iOS de recuento de referencia mejorada de Xamarin.
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: 8c7b1a88284156cb5d4261f18d5659ed66dfaf64
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879334"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>Nueva referencia de recuento de sistema de Xamarin.iOS

Xamarin.iOS 9.2.1 introdujo la referencia mejorada del sistema para todas las aplicaciones de recuento de forma predeterminada. Puede usar para eliminar muchos problemas de memoria que resultaban difíciles realizar un seguimiento y corregir en versiones anteriores de Xamarin.iOS.

## <a name="enabling-the-new-reference-counting-system"></a>Habilitar el nuevo sistema de recuento de referencias

A partir de Xamarin 9.2.1 está habilitada el ref nuevo recuento de sistema para **todas** aplicaciones de forma predeterminada.

Si está desarrollando una aplicación existente, puede comprobar el archivo .csproj para asegurarse de que todas las apariciones de `MtouchUseRefCounting` se establecen en `true`, como a continuación:

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

Si se establece en `false` la aplicación no usa las nuevas herramientas.

### <a name="using-older-versions-of-xamarin"></a>Con versiones anteriores de Xamarin

Xamarin.iOS 7.2.1 y encima de las características de una vista previa mejorada nuestro nuevo recuento de referencias del sistema.

**API clásica:**

Para habilitar este nuevo sistema de recuento de referencia, compruebe el **usar extensión de recuento de referencias** casilla se encuentra en la **avanzadas** ficha de su proyecto **opciones de compilación de iOS** , como se muestra a continuación: 

[![](newrefcount-images/image1.png "Habilitar el nuevo sistema de recuento de referencia")](newrefcount-images/image1.png#lightbox)

Tenga en cuenta que estas opciones se han quitado en versiones más recientes de Visual Studio para Mac.

 **[API unificada:](~/cross-platform/macios/unified/index.md)**

 La nueva extensión de recuento de referencia es necesaria para la API unificada y debe habilitarse de forma predeterminada. Las versiones anteriores de su IDE no pueden tener este valor comprueban automáticamente y es posible que deba colocar una comprobación por ella.

    
> [!IMPORTANT]
> Una versión anterior de esta característica ha estado presente desde MonoTouch 5.2, pero sólo estaba disponible para **sgen** como una versión preliminar experimental. Esta versión nueva y mejorada ahora también está disponible para el **Boehm** recolector de elementos no utilizados.


Históricamente, ha habido dos tipos de objetos administrados por Xamarin.iOS: aquellos que fueron simplemente un contenedor en torno a un objeto nativo (objetos del mismo nivel) y aquellos que extendido o incorporar la nueva funcionalidad (objetos derivados) - normalmente por mantener el estado en memoria adicional. Anteriormente era posible que nos podríamos aumentar un objeto del mismo nivel con estado (por ejemplo agregando un C# controlador de eventos), pero que se deja que el objeto vaya sin referencia y, a continuación, recopilados. Esto podría provocar un bloqueo más adelante (por ejemplo, si el tiempo de ejecución Objective-C se vuelve a llamar el objeto administrado).

El nuevo sistema actualiza automáticamente los objetos del mismo nivel en objetos administrados por el tiempo de ejecución cuando almacena cualquier información adicional.

Esto soluciona varios bloqueos que se produjeron en situaciones como esta:

```csharp
class MyTableSource : UITableViewSource {
   public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath) {
        var cell = tableView.DequeueReusableCell ("myId");
        if (cell != null)
                return cell;

        cell = new UITableViewCell (UITableViewCellStyle.Default, "myId");
        var txt = new UITextField ();
        txt.TouchDown += delegate { Console.WriteLine ("...."); };
        cell.ContentView.AddSubview (txt);
        return cell;
   }
}
```

Sin la extensión de recuento de referencia este código podría bloquearse porque `cell` pasa a ser recopilables de modo que su `TouchDown` delegar, lo que se traducirá en un puntero pendiente.

La extensión de recuento de referencia garantiza el objeto administrado permanece activo e impide que su colección, siempre se conserva el objeto nativo por código nativo.

El nuevo sistema también elimina la necesidad de *mayoría* privada campos que se utilizan en enlaces - que es el método predeterminado para mantener activa la instancia de respaldo. El vinculador administrado es lo suficientemente inteligente como para quitar todos los *innecesarios* campos desde las aplicaciones que usan la nueva referencia de extensión de recuento.

Esto significa que cada instancia de objeto administrado consume menos memoria que antes. También resuelve un problema relacionado donde algunos campos de respaldo contendría las referencias que ya no se necesitan el tiempo de ejecución Objective-C, lo que dificulta reclamar memoria.
