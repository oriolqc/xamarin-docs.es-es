---
title: "Error de paquetes que faltan después de actualizar paquetes de Nuget"
ms.topic: article
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: f330590132e4881484eeae3efafe570d991a509a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>Error de paquetes que faltan después de actualizar paquetes de Nuget

Este problema ha notificado principalmente en soluciones de aplicación de ejemplo de Xamarin.Forms, pero la posibilidad de que este problema puede ocurrir en cualquier proyecto que utilice paquetes de NuGet. 

Si después de actualizar paquetes de Nuget en el proyecto o solución, verá un error que hace referencia a los números de versión de paquete antiguo, como:

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.  
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)

```

En este ejemplo *Xamarin.Forms.1.3.1.6296* es el número de versión anterior que se quitó con la actualización del paquete de Nuget.

Esto puede suceder si se agregan manualmente los elementos XML en el archivo .csproj que hacer referencia al número de versión de paquete antiguo o editado, Nuget no quitará o actualizarlas si se hubieran agregado o editado manualmente, por lo que ahora se examina el proyecto para los paquetes que se han eliminar. 

Para corregir este problema, editar los archivos .csproj manualmente y eliminar todos los elementos que hacen referencia el número de versión anterior. 

Elementos de ejemplo que se va a quitar (si tienen el número de versión del paquete antiguo):

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />

```

