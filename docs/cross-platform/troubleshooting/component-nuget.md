---
title: Actualizar referencias a componentes a NuGet
description: Este documento describe cómo reemplazar las referencias del componente con paquetes de NuGet en el futuro las aplicaciones, ya que se ha cancelado el almacén de componentes de Xamarin.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: 34f2f5149be63af6abe5aaab197472d067122069
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782039"
---
# <a name="updating-component-references-to-nuget"></a>Actualizar referencias a componentes a NuGet

> [!IMPORTANT]
> Se ha cancelado el almacén de componentes a partir del 15 de mayo de 2018 (este cierre originalmente era [anunció](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) en noviembre de 2017).
>
> Componentes de Xamarin ya no se admiten en Visual Studio y debe reemplazarse por los paquetes de NuGet. Siga las instrucciones siguientes para quitar manualmente las referencias de componentes de los proyectos.

Hacer referencia a estas instrucciones para agregar paquetes de NuGet en [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) o [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Una lista de popular Xamarin [complementos y las bibliotecas](https://github.com/xamarin/XamarinComponents/blob/master/README.md) está disponible para ayudar a encontrar alternativas a componentes que no están disponibles como paquetes de NuGet.

## <a name="manually-removing-component-references"></a>Quitar manualmente las referencias a componentes

La versión de Visual Studio 15.6 y 7.4 versión de Visual Studio para Mac ya no admiten componentes en el proyecto. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Si carga un proyecto en Visual Studio, se muestra el cuadro de diálogo siguiente, que explica que debe quitar todos los componentes de su proyecto manualmente:

![Alerta de cuadro de diálogo que explica que un componente se ha encontrado en el proyecto y se debe quitar](component-nuget-images/component-alert-vs.png)

Para quitar un componente de su proyecto:

1. Abra la **.csproj** archivo. Para ello, haga doble clic en el nombre del proyecto y seleccione **descargar el proyecto**. 

2. Haga clic en nuevo en el proyecto descargado y seleccione **editar .csproj {your--nombre del proyecto}**.

3. Buscar todas las referencias en el archivo para `XamarinComponentReference`. Debe ser similar al ejemplo siguiente:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. Quite las referencias a `XamarinComponentReference` y guarde el archivo. En el ejemplo anterior, resulta seguro quitar toda la matriz `ItemGroup`.

5. Una vez que se ha guardado el archivo, haga doble clic en el nombre del proyecto y seleccione **recargar el proyecto**.

6. Repita los pasos anteriores para cada proyecto de la solución.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Si carga un proyecto en Visual Studio para Mac, se muestra el cuadro de diálogo siguiente, que explica que debe quitar todos los componentes de su proyecto manualmente:

![Alerta de cuadro de diálogo que explica que un componente se ha encontrado en el proyecto y se debe quitar](component-nuget-images/component-alert.png)

Para quitar un componente de su proyecto:

1. Abra el archivo .csproj. Para ello, haga doble clic en el nombre del proyecto y seleccione **Herramientas > Editar archivo**.

2. Buscar todas las referencias en el archivo para `XamarinComponentReference`. Debe ser similar al ejemplo siguiente:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. Quite las referencias a `XamarinComponentReference` y guarde el archivo. En el ejemplo anterior, resulta seguro quitar toda la matriz `ItemGroup`

4. Repita los pasos anteriores para cada proyecto de la solución.

-----

> [!WARNING]
> Las instrucciones siguientes solo funcionan con versiones anteriores de Visual Studio.
> El **componentes** nodo ya no está disponible en las versiones actuales de 2017 de Visual Studio o Visual Studio para Mac.

En las siguientes secciones se explican cómo actualizar soluciones de Xamarin existentes para cambiar las referencias de componentes a los paquetes de NuGet.

- [Componentes que contienen paquetes de NuGet](#contain)
- [Componentes con reemplazos de NuGet](#replace)

Mayoría de los componentes pertenecen a una de las categorías anteriores.
Si está usando un componente que no parece tener un paquete de NuGet equivalente, lea la [componentes sin una ruta de acceso de migración de NuGet](#require-update) sección más adelante.

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>Componentes que contienen paquetes de NuGet

Muchos componentes ya contienen los paquetes de NuGet, y la ruta de acceso de migración es simplemente eliminar la referencia al componente.

Puede determinar si el componente ya incluye un paquete de NuGet haciendo doble clic en el componente de la solución:

![Expandidas el nodo de componentes](component-nuget-images/solution-sml.png)

El **paquetes** ficha enumera los paquetes de NuGet incluidos en el componente:

![Pestaña de paquetes contiene NuGet](component-nuget-images/packages-tab-sml.png)

Tenga en cuenta que la **ensamblados** ficha estará vacía:

![Pestaña ensamblados está vacío](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>Actualización de la solución

Para actualizar la solución, eliminar el **componente** entrada de la solución:

![Eliminar componentes](component-nuget-images/delete-component-sml.png)

El paquete de NuGet permanecerá enumerado en la **paquetes** nodo y la aplicación se compilará y ejecutará de forma habitual. En el futuro, las actualizaciones de este paquete se realizará a través de la **Nuget** característica de actualización:

![Actualizar paquete de NuGet](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>Componentes con reemplazos de NuGet

Si la página de información de componente **ensamblados** ficha contiene las entradas tal y como se muestra a continuación, deberá buscar manualmente el paquete de NuGet equivalente.

![Contiene ensamblados](component-nuget-images/assemblies-tab-sml.png)

Tenga en cuenta que la **paquetes** ficha probablemente estará vacía:

![](component-nuget-images/packages-tab-empty-sml.png)

_Puede contener dependencias de NuGet, pero se puede hacer caso omiso._

Para confirmar un reemplazo existe el paquete de NuGet, busque [NuGet.org](https://www.nuget.org/packages), con el nombre del componente, o bien por el autor.

Por ejemplo, puede encontrar el popular **sqlite-net-pcl** paquete mediante la búsqueda de:

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) : el nombre del producto.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) – perfil del autor.

### <a name="updating-the-solution"></a>Actualización de la solución

Una vez que haya confirmado que el componente está disponible en NuGet, siga estos pasos:

#### <a name="delete-the-component"></a>Eliminar el componente

Haga clic con el botón secundario en el componente de la solución y elija **quitar**:

![Quitar componentes](component-nuget-images/remove-component-sml.png)

Esto eliminará el componente y todas las referencias. Esto interrumpirá la compilación, hasta que se agregue el paquete de NuGet equivalente para reemplazarlo.

#### <a name="add-the-nuget-package"></a>Agregue el paquete de NuGet

1. Haga doble clic en el **paquetes** nodo y elija **agregar paquetes...** .
2. Busque el reemplazo de NuGet por nombre o autor:

  ![](component-nuget-images/nuget-search-sml.png)

3. Presione **Agregar paquete**.

El paquete de NuGet se agregarán al proyecto, junto con todas las dependencias.
Esta acción debería resolver la compilación. Si siguen produciéndose errores en la compilación, investigue los errores para ver si hay diferencias de API entre el componente y el paquete de NuGet.

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>Componentes sin una ruta de acceso de migración de NuGet

No se preocupe si no encuentra un reemplazo para los componentes usados en la aplicación inmediatamente. Componentes existentes continuarán funcionando en 15,5 de Visual Studio y el **componentes** nodo aparecerá en la solución como de costumbre.

Las versiones futuras de Visual Studio, sin embargo, le _no_ restaurar o actualizar los componentes.
Esto significa que si abre la solución en un equipo nuevo, el componente no se descargará e instalado; y el autor no podrá proporcionarle las actualizaciones. Debería pensar en:

* Extraiga los ensamblados desde el componente y hacer referencia a ellos directamente en el proyecto.
* Póngase en contacto con el autor del componente y pregunte acerca de los planes para migrar a NuGet.
* Investigue los paquetes de NuGet alternativos o buscar el código fuente si el componente es código abierto.

Muchos proveedores de componentes todavía está trabajando en la migración de NuGet y otros usuarios (incluidos productos disponibles comercialmente) pueden estar investigando este asunto opciones de entrega alternativo.


## <a name="related-links"></a>Vínculos relacionados
- [Lista de populares Xamarin Plugins y bibliotecas](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Instalar y usar un paquete de NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Incluido un paquete de NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
