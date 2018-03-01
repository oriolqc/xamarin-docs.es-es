---
title: "IBTool Error: No se pudo completar la operación."
ms.topic: article
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: dd668859428da1abfa3a8e46a0810b2de6645fe2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>IBTool Error: No se pudo completar la operación.

## <a name="fixed-in-xcode-611"></a>Corregido en Xcode 6.1.1

Apple [fijo](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) esto `ibtool` error en Xcode 6.1.1, por lo que actualiza a Xcode 6.1.1 o versiones posteriores es la solución más sencilla.

* * *

## <a name="description-of-the-problem"></a>Descripción del problema

El `ibtool` comando en Xcode 6.0 tenía un error en OS X 10.10 Yosemite. Xamarin.iOS utiliza del Xcode `ibtool` para compilar los guiones gráficos y `XIB` archivos.

Para obtener más información sobre el error en relación con Xcode puede encontrarse en la siguiente entrada de desbordamiento de la pila: [http://stackoverflow.com/questions/25754763/cant-open-storyboard](http://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Mensaje de error

> No se pudo abrir el documento "MainStoryboard.storyboard". No se pudo completar la operación. (error de com.apple.InterfaceBuilder -1).

## <a name="workarounds-for-xcode-60"></a>Soluciones alternativas (para Xcode 6.0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Opción 1: Administrar todos los `UIImageView.Image` propiedades en código

En lugar de establecer la `Image` propiedad de un `UIImageView` en el guión gráfico o `.xib` archivo, puede establecer la propiedad en uno de la vista de los métodos de invalidación de ciclo de vida en el controlador de vista (por ejemplo, en `ViewDidLoad()`). Vea también [trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) para obtener sugerencias sobre el uso de `UIImage.FromBundle()` frente a `UIImage.FromFile()`.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Opción 2: Mover todos los recursos de imagen para el nivel superior `Resources` carpeta

Después de mover las imágenes al nivel superior `Resources` carpeta, deberá actualizar el guión gráfico y `.xib` archivos que se utilizarán las nuevas rutas de acceso de imagen.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Opción 3: Establecer el `LogicalName` para los activos de imagen problemático, por lo que se copian en el nivel superior de la`.app` agrupación

Por ejemplo, que el original `.csproj` archivo contiene la siguiente entrada:

`<BundleResource Include="Resources\Images\image.png" />`

Puede cambiar este elemento y agregar un `LogicalName` para que la imagen en su lugar, se copiará en el nivel superior de la `.app `agrupación:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

En Visual Studio para Mac la `LogicalName` también se puede establecer utilizando la `Resource ID` field para la imagen en **Vista > rellena > propiedades**. (Vea también: [http://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](http://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

Después de este cambio, debe actualizar el guión gráfico y `.xib` archivos que se utilizarán las nuevas rutas de acceso de imagen de nivel superior. Visual Studio para Mac actualizará automáticamente la lista de autocompletions para el `Image` propiedad en el Diseñador de iOS. En Visual Studio, debe editar manualmente la ruta de acceso. El Diseñador de iOS, a continuación, mostrará como una imagen que faltan, pero el proyecto se compile y ejecute correctamente.

### <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, vea [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo registre un error nuevo si es necesario. 

