---
title: Representar controles personalizados en la vista previa XAML
description: En este artículo se describe cómo mostrar los controles personalizados en la vista previa de XAML.
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 977c29312e0be8b92f216c224414f9bd03f8562d
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58858975"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>Representar controles personalizados en la vista previa XAML

_A veces, los controles personalizados no funcionan según lo previsto en la vista previa de XAML. Use las instrucciones de este artículo para conocer las limitaciones de vista previa de los controles personalizados._

## <a name="basic-preview-mode"></a>Modo de vista previa básico

Incluso si no ha creado el proyecto, el controlador de vista previa de XAML se representarán las páginas. Hasta que se compile, cualquier control que se basa en el código subyacente mostrará su tipo base de Xamarin.Forms. Cuando se compila el proyecto, el controlador de vista previa de XAML intentará mostrar controles personalizados con la representación en tiempo de diseño habilitada. Si se produce un error en la representación, mostrará el tipo base de Xamarin.Forms.

## <a name="enable-design-time-rendering-for-custom-controls"></a>Habilitar la representación en tiempo de diseño para controles personalizados

Si se realiza sus propios controles personalizados o usar los controles de una biblioteca de terceros, el controlador de vista previa aparecer incorrectamente. Controles personalizados deben participar en el tiempo de representación para aparecer en la vista previa, si escribió el control o se importa desde una biblioteca de diseño. Con los controles que ha creado, agregue el [ `[DesignTimeVisible(true)]` ](xref:System.ComponentModel.DesignTimeVisibleAttribute) a la clase del control para mostrarlo en la vista previa:

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

Use [clase base de ImageCirclePlugin de James Montemagno](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs) como ejemplo.


## <a name="skiasharp-controls"></a>Controles de SkiaSharp

Actualmente, solo se admiten controles de SkiaSharp, cuando es una vista previa en iOS. No se representarán en la versión preliminar de Android.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="check-your-xamarinforms-version"></a>Comprobar la versión de Xamarin.Forms
Asegúrese de tener al menos 3.6 Xamarin.Forms instalado. Puede actualizar su versión de Xamarin.Forms en NuGet.

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>Incluso con `[DesignTimeVisible(true)]`, mi control personalizado no está representando correctamente.
Controles personalizados que dependen en gran medida de los datos de código subyacente o de back-end no siempre funcionan en la vista previa de XAML. Puede probar:
* Mover el control, por lo que no inicializa si [está habilitado el modo de diseño](index.md#detect-design-mode)
* Configurar [datos en tiempo de diseño](design-time-data.md) para mostrar datos falsos desde el back-end

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>El controlador de vista previa de XAML muestra el error "Los controles personalizados no están representar correctamente"
Intente limpiar y recompilar el proyecto, o cerrar y volver a abrir el archivo XAML.
