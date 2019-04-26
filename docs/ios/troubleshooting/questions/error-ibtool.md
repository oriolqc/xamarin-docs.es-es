---
title: 'Error de IBTool: The operation couldn’t be completed.'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: c2f727b55b21dc3bd976f0b41c71b794841cfca4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422001"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Error de IBTool: The operation couldn’t be completed.

## <a name="fixed-in-xcode-611"></a>Se ha corregido en Xcode 6.1.1

Apple [fijo](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) esto `ibtool` errores en Xcode 6.1.1, por lo que la actualización a Xcode 6.1.1 o superior es la solución más sencilla.

* * *

## <a name="description-of-the-problem"></a>Descripción del problema

El `ibtool` comandos de Xcode 6.0 tenían un error en OS X 10.10 Yosemite. Xamarin.iOS utiliza Xcode `ibtool` para compilar los guiones gráficos y `XIB` archivos.

Para obtener más información sobre el error en relación con Xcode puede encontrarse en la siguiente entrada de Stack Overflow: [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Mensaje de error

> No se pudo abrir el documento "MainStoryboard.storyboard". The operation couldn’t be completed. (error com.apple.InterfaceBuilder -1).

## <a name="workarounds-for-xcode-60"></a>Soluciones alternativas (para Xcode 6.0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Opción 1: Administrar todos `UIImageView.Image` propiedades en el código

En lugar de establecer el `Image` propiedad de un `UIImageView` en el guión gráfico o `.xib` archivo, puede establecer la propiedad en uno de la vista de ciclo de vida de los métodos de invalidación en el controlador de vista (por ejemplo, en `ViewDidLoad()`). Vea también [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) para obtener sugerencias sobre cómo usar `UIImage.FromBundle()` frente a `UIImage.FromFile()`.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Opción 2: Mover todos los recursos de imagen a la observación inicial `Resources` carpeta

Después de mover las imágenes a la observación inicial `Resources` carpeta, deberá actualizar el guión gráfico y `.xib` archivos que se usarán las rutas de acceso de imagen nuevo.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Opción 3: Establecer el `LogicalName` para los activos de imagen problemático, por lo que se copian en el nivel superior de la`.app` agrupación

Por ejemplo, el original `.csproj` archivo contiene la entrada siguiente:

`<BundleResource Include="Resources\Images\image.png" />`

Puede cambiar este elemento y agregar un `LogicalName` para que la imagen en su lugar, se copiará en el nivel superior de la `.app `agrupación:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

En Visual Studio para Mac el `LogicalName` también se puede establecer mediante el `Resource ID` campo para la imagen en **Vista > paneles > propiedades**. (Vea también: [ https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545 ](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Después de este cambio, deberá actualizar el guión gráfico y `.xib` archivos que se usarán las rutas de acceso de nivel superior de imagen nuevo. Visual Studio para Mac actualizará automáticamente la lista de autocompletions para el `Image` propiedad en el Diseñador de iOS. En Visual Studio, deberá editar manualmente la ruta de acceso. El Diseñador de iOS, a continuación, mostrará como una imagen que faltan, pero el proyecto compilará y ejecutará correctamente.

### <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, consulte la sección [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo archivar un error nuevo si es necesario. 

