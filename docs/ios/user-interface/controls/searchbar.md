---
title: "Barra de búsqueda"
ms.topic: article
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 03f90a506d22752c9158650de3f3a109d07b4396
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="search-bar"></a>Barra de búsqueda

El UISearchBar se utiliza para buscar a través de una lista de valores. 

Contiene tres componentes principales: 

- Un campo que se utiliza para escribir texto. Los usuarios pueden utilizar esta opción para especificar el término de búsqueda.
- Un botón Borrar para quitar cualquier texto del campo de búsqueda.
- Un botón Cancelar para salir de la función de búsqueda.

![Barra de búsqueda](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implementación de la barra de búsqueda

Para implementar el inicio de la barra de búsqueda creando una nueva:

```csharp
searchBar = new UISearchBar();
```

Y, a continuación, colóquelo. En el ejemplo siguiente muestra cómo colocarlo en la barra de navegación o en el HeaderView de una tabla:

```csharp
NavigationItem.TitleView = searchBar;

\\or

TableView.TableHeaderView = searchBar;
```

Establecer las propiedades en la barra de búsqueda:

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![Propiedades de la barra de búsqueda](searchbar-images/image6.png)

Generar el `SearchButtonClicked` evento cuando se presiona el botón de búsqueda. Esto llamará a la lógica de búsqueda:

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

Para obtener información acerca de cómo administrar la presentación de la barra de búsqueda y los resultados de la búsqueda, consulte la [controlador de búsqueda ](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/) receta.

## <a name="using-the-search-bar-in-the-designer"></a>En la barra de búsqueda del diseñador

El diseñador ofrece dos opciones para implementar una barra de búsqueda en el diseñador

- Barra de búsqueda
- Barra de búsqueda con el controlador de pantalla de búsqueda (en desuso)

![Controles de barra de búsqueda en el diseñador](searchbar-images/image2.png)

Utilice el Panel de propiedades para establecer las propiedades en la barra de búsqueda

![Diseñador de propiedades de barra de búsqueda](searchbar-images/image3.png)

A continuación se explican estas propiedades:

- **Mensaje de texto, marcador de posición,** : estas propiedades se usan para sugerir e indicar a cómo los usuarios deben utilizar la barra de búsqueda. Por ejemplo, si la aplicación muestra una lista de almacenes de podría utilizar la propiedad de símbolo del sistema para indicar que los usuarios pueden "escribir una ciudad, el nombre del artículo o el código postal"
- **Buscar el estilo** : puede establecer la barra de búsqueda para ser **Prominent** o **mínimo**. Usar los destacados teñir todo lo demás en pantalla, excepto para la búsqueda de la barra, haciendo que el enfoque que se dibujará en la barra de búsqueda. La barra de búsqueda de estilo mínima se combinarán con su entorno.
- **Capacidades** : habilitar estas propiedades solo muestra el elemento de interfaz de usuario. La funcionalidad debe implementarse para estas opciones cuando se genera el evento correcto como se detalla en el [documentos de API de la barra de búsqueda](https://developer.xamarin.com/api/type/UIKit.UISearchBar/)
    - Muestra los resultados de la búsqueda / botón marcadores: muestra un icono de resultados de la búsqueda o marcadores en la barra de búsqueda
    - Muestra el botón Cancelar: permite a los usuarios para salir de la función de búsqueda. Se recomienda que esta opción está seleccionada.
    - Muestra a la barra de ámbito: Esto permite a los usuarios limitar el ámbito de la búsqueda. Por ejemplo, al buscar en la aplicación de música, el usuario puede seleccionar si va a buscar música de Apple o su biblioteca para una canción en concreto o un intérprete. Para mostrar varias opciones, agregue una matriz de títulos para el **ScopeBarTitles** propiedad.
    ![Títulos de ámbito de barra de búsqueda](searchbar-images/image4.png)

- **El comportamiento de texto** : estas opciones se usan para direccionar cómo se da formato a la entrada del usuario al que está escribiendo. Uso de mayúsculas y establecerá el inicio de cada palabra o frase, o todos los caracteres como letra mayúscula. Corrección y ortográfica con preguntar al usuario con sugerencias de palabras a medida que escriben.
- **Teclado** : controles que muestran el estilo de teclado para la entrada y, por lo tanto, ¿qué claves están disponibles en el teclado. Esto incluye del teclado numérico, panel de teléfono, correo electrónico, dirección URL junto con otras opciones.
- **Apariencia** : controla el estilo de la apariencia del teclado se y cualquier oscuro o claro con temas.
- **Devolver clave** : cambiar la etiqueta de la tecla ENTRAR para reflejar mejor la acción que se van a realizar. Los valores admitidos incluyen Go, combinación, siguiente, ruta, de hecho y la búsqueda.
- **Proteger** – identifica si se enmascara la entrada (por ejemplo, para una entrada de contraseña).

## <a name="related-links"></a>Vínculos relacionados

- [Controlador de búsqueda](https://developer.xamarin.com/recipes/ios/content_controls/search-controller/)
