---
title: Ajustar los parámetros de la memoria de Java para el Diseñador de Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/02/2018
ms.openlocfilehash: 691be280b80e379863cc09d0f1bba0ff5882cf21
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732926"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Ajustar los parámetros de la memoria de Java para el Diseñador de Android

Los parámetros de memoria predeterminados que se utilizan cuando se inicia el `java` procesar para el Diseñador de Android podría ser incompatible con algunas configuraciones del sistema.

A partir de Xamarin Studio 5.7.2.7 (y versiones posterior, Visual Studio para Mac) y Visual Studio Tools para Xamarin 3.9.344, esta configuración puede personalizarse según un por proyecto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Nuevas propiedades del Diseñador de Android y las opciones correspondientes de Java

Los nombres de propiedad siguientes corresponden a la java indicado [opción de línea de comandos](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** - Xms

- **AndroidDesignerJavaRendererMaxMemory** - Xmx

- **AndroidDesignerJavaRendererPermSize** - XX: MaxPermSize


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Abra su solución en Visual Studio.

2.  Seleccione cada proyecto de Android uno por uno en el Explorador de soluciones y haga clic en [mostrar todos los archivos](https://msdn.microsoft.com/en-us/library/4afxey9h.aspx) dos veces en cada proyecto. Puede omitir los proyectos que no contengan ninguno `.axml` archivos de diseño. Este paso se asegurará de que cada directorio del proyecto contiene un `.csproj.user` archivo.

3.  Salga de Visual Studio.

4.  Busque la `.csproj.user` archivo para cada uno de los proyectos del paso 2.

5.  Editar cada `.csproj.user` archivo en un editor de texto.

6.  Agregue cualquiera de las nuevas propiedades de memoria de diseñador Android dentro de un `<PropertyGroup>` elemento. Puede usar una existente `<PropertyGroup>` o cree uno nuevo. Este es un ejemplo completo `.csproj.user` archivo que incluye todos los atributos de 3 se establece en sus valores predeterminados:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7.  Guarde y cierre todos los archivos `.csproj.user` archivos.

8.  Reinicie Visual Studio y vuelva a abrir la solución.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Abra la solución en Visual Studio para Mac garantizar el directorio de la solución contiene un `.userprefs` archivo.

2.  Salir de Visual Studio para Mac.

3.  Busque la `.userprefs` archivo en el directorio de la solución.

4.  Editar el `.userprefs` archivo en un editor de texto.

5.  Busque el elemento XML existente con el formato siguiente. La última parte de este nombre de elemento coincidirá con el nombre del proyecto: "AndroidApplication1" en este ejemplo:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  Si el `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` el elemento no existe, créelo en cualquier lugar dentro de los incluye `<Properties>` elemento. No olvide reemplazar "AndroidApplication1" con el nombre del proyecto.

7.  Agregue cualquiera de las nuevas propiedades de memoria de diseñador Android como atributos en el elemento. Este es un ejemplo completo `.userprefs` archivo que incluye todos los atributos de 3 se establece en sus valores predeterminados:

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8.  Repita los pasos 5 a 7 para cada proyecto de Android en la solución que contiene cualquier `.axml` archivos de diseño. (Es decir, agregue una `<MonoDevelop.Ide.ItemProperties.ProjectName>` elemento para cada proyecto.)

9.  Guarde y cierre el `.userprefs` archivo.

10. Reinicie Visual Studio para Mac y vuelva a abrir la solución.

-----

