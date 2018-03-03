---
title: Actualizar referencias a componentes a NuGet
description: Reemplace las referencias del componente con paquetes de NuGet en el futuro las aplicaciones.
ms.topic: article
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 11/22/2017
ms.openlocfilehash: f3dbfb52d4fbcb4dd65f695a862f6b041d2b22c0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="updating-component-references-to-nuget"></a>Actualizar referencias a componentes a NuGet

_Reemplace las referencias del componente con paquetes de NuGet en el futuro las aplicaciones._

Esta guía explica cómo actualizar soluciones de Xamarin existentes para cambiar las referencias de componentes a los paquetes de NuGet.

- [Componentes que contienen paquetes de NuGet](#contain)
- [Componentes con reemplazos de NuGet](#replace)

Mayoría de los componentes pertenecen a una de las categorías anteriores.
Si está usando un componente que no parece tener un paquete de NuGet equivalente, lea la [componentes sin una ruta de acceso de migración de NuGet](#require-update) sección más adelante.

Hacer referencia a estas páginas para obtener más instrucciones para agregar paquetes de NuGet en [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) o [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

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

- [Instalar y usar un paquete de NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Incluido un paquete de NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
