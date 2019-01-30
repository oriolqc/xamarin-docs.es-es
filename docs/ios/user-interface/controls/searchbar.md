---
title: Barras de búsqueda de Xamarin.iOS
description: Este documento describe cómo usar las barras de búsqueda en Xamarin.iOS. Describe cómo crear barras de búsqueda en un guión gráfico y mediante programación.
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: 3c46aa1244699671d46560b0029197981a86d005
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233333"
---
# <a name="search-bars-in-xamarinios"></a>Barras de búsqueda de Xamarin.iOS

El UISearchBar se usa para buscar a través de una lista de valores. 

Contiene tres componentes principales: 

- Un campo que se usa para escribir texto. Los usuarios pueden usar esta opción para especificar su término de búsqueda.
- Botón Borrar, quitar cualquier texto del campo de búsqueda.
- Un botón Cancelar para salir de la función de búsqueda.

![Barra de búsqueda](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implementación de la barra de búsqueda

Para implementar el inicio de la barra de búsqueda creando una nueva:

```csharp
searchBar = new UISearchBar();
```

Y, a continuación, colóquelo. El ejemplo siguiente muestra cómo colocarlo en la barra de navegación o en el HeaderView de una tabla:

```csharp
NavigationItem.TitleView = searchBar;

\\or

TableView.TableHeaderView = searchBar;
```

Establecer propiedades en la barra de búsqueda:

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![Propiedades de la barra de búsqueda](searchbar-images/image6.png)

Elevar el `SearchButtonClicked` eventos cuando se presiona el botón de búsqueda. Esto llamará a la lógica de búsqueda:

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

Para obtener información acerca de cómo administrar la presentación de la barra de búsqueda y resultados de búsqueda, consulte el [búsqueda controlador ](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller) receta.

## <a name="using-the-search-bar-in-the-designer"></a>En la barra de búsqueda del diseñador

El diseñador ofrece dos opciones para implementar una barra de búsqueda en el diseñador

- Barra de búsqueda
- Barra de búsqueda con la controladora de pantalla de búsqueda (en desuso)

![Controles de barra de búsqueda en el diseñador](searchbar-images/image2.png)

Utilice el Panel de propiedades para establecer las propiedades de la barra de búsqueda

![Diseñador de propiedades de barra de búsqueda](searchbar-images/image3.png)

A continuación se explican estas propiedades:

- **Mensaje de texto, el marcador de posición,** – estas propiedades se usan para sugerir e indicarle cómo los usuarios deben utilizar la barra de búsqueda. Por ejemplo, si la aplicación muestra una lista de almacenes podría usar la propiedad de símbolo del sistema para indicar que los usuarios pueden "escribir una ciudad, el nombre del artículo o el código postal"
- **Buscar el estilo** : puede establecer la barra de búsqueda para ser **Prominent** o **mínimo**. Utilizando los destacados teñir todo lo demás en pantalla, excepto en la barra de búsqueda, provocando el enfoque que se dibujará en la barra de búsqueda. La barra de búsqueda de estilo mínima se mezcla con su entorno.
- **Capacidades** : habilitación de estas propiedades solo muestra el elemento de interfaz de usuario. Se debe implementar la funcionalidad para ellos, se genera el evento correcto tal como se detalla en el [documentos de la API de la barra de búsqueda](xref:UIKit.UISearchBar)
    - Muestra los resultados de búsqueda / botón marcadores: muestra un icono de resultados de búsqueda o marcadores en la barra de búsqueda
    - Muestra el botón Cancelar: permite a los usuarios para salir de la función de búsqueda. Se recomienda que esta opción está seleccionada.
    - Muestra a la barra de ámbito: Esto permite a los usuarios limitar el ámbito de la búsqueda. Por ejemplo, al realizar búsquedas en la aplicación de música, el usuario puede seleccionar si desean buscar música o su biblioteca para una canción determinada o un intérprete. Para mostrar varias opciones, agregue una matriz de títulos para el **ScopeBarTitles** propiedad.
    ![Títulos de ámbito de barra de búsqueda](searchbar-images/image4.png)

- **Comportamiento de texto** : estas opciones se usan para tratar cómo se da formato a la entrada del usuario al que está escribiendo. Uso de mayúsculas establecerá el inicio de cada palabra o frase, o todos los caracteres como letra mayúscula. Corrección y el corrector ortográfico con preguntar al usuario con sugerencias de palabras a medida que escriben.
- **Teclado** : controles que muestran el estilo de teclado para la entrada y, por lo tanto, qué claves están disponibles en el teclado. Esto incluye del teclado numérico, panel de teléfono, correo electrónico, dirección URL junto con otras opciones.
- **Apariencia** : controla el estilo de apariencia del teclado y se cualquier oscuro o claro con temas.
- **Devolver clave** : cambiar la etiqueta de la tecla ENTRAR para reflejar mejor qué acción se realizará. Los valores admitidos son Go, combinación, siguiente, ruta, de hecho y búsqueda.
- **Proteger** – identifica si se enmascara la entrada (por ejemplo, una entrada de contraseña).

## <a name="related-links"></a>Vínculos relacionados

- [Controlador de búsqueda](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
