---
title: Actualizar referencias de componentes en NuGet
description: Este documento describe cómo reemplazar las referencias del componente con paquetes de NuGet en el futuro las aplicaciones, ya que se ha interrumpido el Store del componente de Xamarin.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: 70ca9a73c83bed5233b77a6f7be80a13f04f2bcb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122158"
---
# <a name="updating-component-references-to-nuget"></a>Actualizar referencias de componentes en NuGet

> [!IMPORTANT]
> Se ha interrumpido el Store del componente a partir del 15 de mayo de 2018 (este cierre era originalmente [anunció](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) en noviembre de 2017).
>
> Componentes de Xamarin ya no se admiten en Visual Studio y debe reemplazarse por los paquetes de NuGet. Siga las instrucciones siguientes para quitar manualmente las referencias de componentes de sus proyectos.

Consulte estas instrucciones para agregar paquetes de NuGet en [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) o [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Una lista de popular Xamarin [complementos y las bibliotecas](https://github.com/xamarin/XamarinComponents/blob/master/README.md) está disponible para ayudar a encontrar alternativas a los componentes que no están disponibles como paquetes de NuGet.

## <a name="manually-removing-component-references"></a>Quitar manualmente las referencias de componentes

La versión 15.6 de Visual Studio y la versión 7.4 de Visual Studio para Mac ya no admiten componentes en el proyecto. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Si carga un proyecto en Visual Studio, se muestra el cuadro de diálogo siguiente, que explica que debe quitar todos los componentes del proyecto manualmente:

![Cuadro de diálogo que explica que un componente se ha encontrado en el proyecto y se debe quitar de la alerta](component-nuget-images/component-alert-vs.png)

Para quitar un componente de su proyecto:

1. Abra el **.csproj** archivo. Para ello, haga doble clic en el nombre del proyecto y seleccione **descargar el proyecto**. 

2. Haga clic en nuevo en el proyecto descargado y seleccione **editar {your-nombre del proyecto} .csproj**.

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

4. Quite las referencias a `XamarinComponentReference` y guarde el archivo. En el ejemplo anterior, es seguro quitar toda la `ItemGroup`.

5. Una vez que se ha guardado el archivo, haga doble clic en el nombre del proyecto y seleccione **recargar el proyecto**.

6. Repita los pasos anteriores para cada proyecto de la solución.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Si carga un proyecto en Visual Studio para Mac, se muestra el cuadro de diálogo siguiente, que explica que debe quitar todos los componentes del proyecto manualmente:

![Cuadro de diálogo que explica que un componente se ha encontrado en el proyecto y se debe quitar de la alerta](component-nuget-images/component-alert.png)

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

3. Quite las referencias a `XamarinComponentReference` y guarde el archivo. En el ejemplo anterior, es seguro quitar todo el contenido `ItemGroup`

4. Repita los pasos anteriores para cada proyecto de la solución.

-----

> [!WARNING]
> Las siguientes instrucciones solo funcionan con versiones anteriores de Visual Studio.
> El **componentes** nodo ya no está disponible en las versiones actuales de Visual Studio 2017 o Visual Studio para Mac.

Las secciones siguientes explican cómo actualizar las soluciones existentes de Xamarin para cambiar las referencias de componentes a los paquetes de NuGet.

- [Componentes que contienen los paquetes de NuGet](#contain)
- [Componentes con reemplazos de NuGet](#replace)

La mayoría de los componentes se dividen en las categorías anteriores.
Si usa un componente que no parece tener un paquete de NuGet equivalente, lea el [componentes sin una ruta de migración de NuGet](#require-update) sección más adelante.

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>Componentes que contienen los paquetes de NuGet

Muchos componentes ya contienen los paquetes de NuGet y la ruta de migración es simplemente eliminar la referencia al componente.

Puede determinar si el componente ya incluye un paquete de NuGet haciendo doble clic en el componente de la solución:

![Nodo de componentes expandida](component-nuget-images/solution-sml.png)

El **paquetes** ficha enumerará todos los paquetes de NuGet incluidos en el componente:

![Pestaña paquetes contiene NuGet](component-nuget-images/packages-tab-sml.png)

Tenga en cuenta que el **ensamblados** ficha estará vacía:

![Pestaña ensamblados está vacío](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>Actualizar la solución

Para actualizar la solución, elimine el **componente** entrada de la solución:

![Eliminar componente](component-nuget-images/delete-component-sml.png)

El paquete NuGet permanecerá en el **paquetes** nodo y la aplicación se compilará y se ejecutará como de costumbre. En el futuro, se realizarán actualizaciones a este paquete a través de la **Nuget** característica de actualización:

![Actualizar paquete de NuGet](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>Componentes con reemplazos de NuGet

Si la página de información del componente **ensamblados** ficha contiene las entradas tal como se muestra a continuación, deberá buscar manualmente el paquete de NuGet equivalente.

![Contiene los ensamblados](component-nuget-images/assemblies-tab-sml.png)

Tenga en cuenta que el **paquetes** ficha probablemente estará vacía:

![](component-nuget-images/packages-tab-empty-sml.png)

_Puede contener las dependencias de NuGet, pero estas pueden omitirse._

Para confirmar un reemplazo existe el paquete de NuGet, busque en [NuGet.org](https://www.nuget.org/packages), utilizando el nombre del componente, o bien puede por autor.

Por ejemplo, puede encontrar el popular **sqlite-net-pcl** paquete mediante la búsqueda de:

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) : el nombre del producto.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) : perfil del autor.

### <a name="updating-the-solution"></a>Actualizar la solución

Cuando haya confirmado que el componente está disponible en NuGet, siga estos pasos:

#### <a name="delete-the-component"></a>Eliminar el componente

Haga clic con el botón derecho en el componente de la solución y elija **quitar**:

![Quitar componente](component-nuget-images/remove-component-sml.png)

Esta acción eliminará el componente y todas las referencias. Esto interrumpirá la compilación, hasta que agregue el paquete de NuGet equivalente para reemplazarlo.

#### <a name="add-the-nuget-package"></a>Agregue el paquete NuGet

1. Haga doble clic en el **paquetes** nodo y elija **agregar paquetes...** .
2. Busque el reemplazo de NuGet por nombre o autor:

  ![](component-nuget-images/nuget-search-sml.png)

3. Presione **Agregar paquete**.

El paquete de NuGet se agregarán al proyecto, junto con las dependencias.
Esta acción debería resolver la compilación. Si la compilación sigue fallando, investigue cada error para ver si hay diferencias de API entre el componente y el paquete de NuGet.

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>Componentes sin una ruta de migración de NuGet

No se preocupe si no encuentra un reemplazo para los componentes usados en la aplicación inmediatamente. Componentes existentes seguirán funcionando en Visual Studio 15.5 y el **componentes** nodo aparecerá en la solución como de costumbre.

Futuras versiones de Visual Studio, sin embargo, le _no_ restaurar o actualizar los componentes.
Esto significa que si abre la solución en un equipo nuevo, el componente no se descargarán e instalarán. y el autor no pueda proporcionarle las actualizaciones. Debe planear:

* Extraiga los ensamblados desde el componente y hacer referencia a ellos directamente en su proyecto.
* Póngase en contacto con el autor del componente y pregunte sobre los planes para migrar a NuGet.
* Investigue los paquetes de NuGet alternativos o buscar el código fuente si el componente es código abierto.

Muchos proveedores de componentes todavía estén trabajando en la migración a NuGet y otros usuarios (incluidos los productos disponibles comercialmente) es posible que a investigar las opciones de entrega alternativos.


## <a name="related-links"></a>Vínculos relacionados
- [Lista de populares Xamarin Plugins y bibliotecas](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Instalar y usar un paquete de NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Incluir un paquete de NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
