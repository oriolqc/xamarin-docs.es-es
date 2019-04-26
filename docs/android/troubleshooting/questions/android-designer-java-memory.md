---
title: Ajuste de los parámetros de memoria de Java para Android Designer
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: 9c564789f704180e9acc9f96dcba5e7d6eb20634
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946743"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Ajuste de los parámetros de memoria de Java para Android Designer

Los parámetros de memoria predeterminados que se usan al iniciar el `java` procesar para el Diseñador de Android podría ser incompatible con algunas configuraciones del sistema.

A partir de Xamarin Studio 5.7.2.7 (y versiones posterior, Visual Studio para Mac) y Visual Studio Tools para Xamarin 3.9.344, estas opciones se pueden personalizar en cada proyecto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Propiedades del Diseñador de Android nueva y las opciones correspondientes de Java

Los nombres de propiedad siguientes corresponden a java indicado [opción de línea de comandos](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Abra su solución en Visual Studio.

2.  Seleccione cada proyecto de Android uno por uno en el Explorador de soluciones y haga clic en [mostrar todos los archivos](https://docs.microsoft.com/en-us/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) dos veces en cada proyecto. Puede omitir los proyectos que no contienen ningún `.axml` archivos de diseño. Este paso garantizará que cada directorio del proyecto contiene un `.csproj.user` archivo.

3.  Salga de Visual Studio.

4.  Busque el `.csproj.user` archivo para cada uno de los proyectos del paso 2.

5.  Edite cada uno `.csproj.user` archivo en un editor de texto.

6.  Agregue cualquiera de las nuevas propiedades de memoria de diseñador Android dentro de un `<PropertyGroup>` elemento. Puede usar una existente `<PropertyGroup>` o cree uno nuevo. Este es un ejemplo completo `.csproj.user` archivo que incluye todos los atributos de 3 establece los valores predeterminados:

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

7.  Guarde y cierre todos los datos actualizados `.csproj.user` archivos.

8.  Reinicie Visual Studio y vuelva a abrir la solución.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Abra la solución en Visual Studio para Mac garantizar el directorio de la solución contiene un `.userprefs` archivo.

2.  Salir de Visual Studio para Mac.

3.  Busque el `.userprefs` archivo en el directorio de la solución.

4.  Editar el `.userprefs` archivo en un editor de texto.

5.  Busque el elemento XML existente con el formato siguiente. La última parte de este nombre de elemento coincidirá con el nombre del proyecto: "AndroidApplication1" en este ejemplo:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  Si el `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` elemento no existe, créelo en cualquier lugar dentro de la envolvente `<Properties>` elemento. No olvide reemplazar "AndroidApplication1" con el nombre del proyecto.

7.  Agregue cualquiera de las nuevas propiedades de memoria de diseñador Android como atributos en el elemento. Este es un ejemplo completo `.userprefs` archivo que incluye todos los atributos de 3 establece los valores predeterminados:

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

8.  Repita los pasos del 5 al 7 para cada proyecto de Android en la solución que incluye cualquier `.axml` archivos de diseño. (Es decir, agregue uno `<MonoDevelop.Ide.ItemProperties.ProjectName>` (elemento) para cada proyecto.)

9.  Guarde y cierre el `.userprefs` archivo.

10. Reinicie Visual Studio para Mac y vuelva a abrir la solución.

-----

