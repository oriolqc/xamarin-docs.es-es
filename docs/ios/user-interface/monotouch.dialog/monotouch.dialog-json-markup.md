---
title: Marcado JSON de MonoTouch.Dialog
description: Este documento describe la sintaxis JSON que puede usarse para crear una interfaz de usuario de Xamarin.iOS mediante MonoTouch.Dialog.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: bc6842871a2f59c9851e90adbc6609707a7ecd1f
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039657"
---
# <a name="monotouchdialog-json-markup"></a>Marcado JSON de MonoTouch.Dialog

Esta página describe el marcado Json MonoTouch.Dialog aceptado [JsonElement](xref:MonoTouch.Dialog.JsonElement)

Empecemos con un ejemplo. El siguiente es un archivo Json completo que se puede pasar a JsonElement.

```json
{     
    "title": "Json Sample",
    "sections": [ 
        {
            "header": "Booleans",
            "footer": "Slider or image-based",
            "id": "first-section",
            "elements": [
                { 
                    "type": "boolean",
                    "caption": "Demo of a Boolean",
                    "value": true
                }, {
                    "type": "boolean",
                    "caption": "Boolean using images",
                    "value": false,
                    "on": "favorite.png",
                    "off": "~/favorited.png"
                }, {
                    "type": "root",
                    "title": "Tap for nested controller",
                    "sections": [
                        {
                            "header": "Nested view!",
                            "elements": [
                                {
                                    "type": "boolean",
                                    "caption": "Just a boolean",
                                    "id": "the-boolean",
                                    "value": false
                                }, {
                                    "type": "string",
                                    "caption": "Welcome to the nested controller"
                                }
                            ]
                        }
                    ]
                }
            ]
        }, {
            "header": "Entries",
            "elements" : [
                {
                    "type": "entry",
                    "caption": "Username",
                    "value": "",
                    "placeholder": "Your account username"
                }
            ]
        }
    ]
}
```

El marcado anterior genera la interfaz de usuario siguiente:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "La interfaz de usuario creado por el marcado determinado")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Cada elemento en el árbol puede contener la propiedad `"id"`. Es posible en tiempo de ejecución hacer referencia a elementos mediante el indizador JsonElement o secciones individuales. Así:

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>Sintaxis de elemento raíz

El elemento raíz contiene los siguientes valores:

-  `title`
-  `sections` (opcional)


El elemento raíz puede aparecer dentro de una sección como un elemento para crear un controlador anidado. En ese caso, la propiedad adicional `"type"` debe establecerse en `"root"`

 <a name="url" />


### <a name="url"></a>url

Si el `"url"` propiedad está establecida, si el usuario pulsa este RootElement, el código solicitará un archivo de la dirección url especificada y hará que el contenido de la nueva información de muestra. Se puede usar para crear extender la interfaz de usuario desde el servidor según el usuario pulsa.

 <a name="group" />


### <a name="group"></a>grupo

Si se establece, esto establece el nombre de grupo para el elemento raíz. Los nombres de grupo se utilizan para elegir un resumen que se muestra como el valor del elemento raíz de uno de los elementos anidados en el elemento. Este es el valor de una casilla de verificación o el valor de un botón de radio.

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

Identifica el elemento de radio está seleccionado en elementos anidados

 <a name="title" />


### <a name="title"></a>título

Si está presente, será el título que se usa para el RootElement

 <a name="type" />


### <a name="type"></a>type

Debe establecerse en `"root"` cuando aparece en una sección (Esto se usa para anidar los controladores).

 <a name="sections" />


### <a name="sections"></a>secciones

Se trata de una matriz Json con secciones individuales

 <a name="Section_Syntax" />


## <a name="section-syntax"></a>Sintaxis de sección

La sección contiene:

-  `header` (opcional)
-  `footer` (opcional)
-  Matriz `elements`


 <a name="header" />


### <a name="header"></a>header

Si está presente, se muestra el texto del encabezado como un título de la sección.

 <a name="footer" />


### <a name="footer"></a>Pie de página

Si está presente, el pie de página se muestra en la parte inferior de la sección.

 <a name="elements" />


### <a name="elements"></a>elementos

Se trata de una matriz de elementos. Cada elemento debe contener al menos una clave, el `"type"` clave que se usa para identificar el tipo de elemento que se va a crear.
Algunos de los elementos comparten algunas propiedades comunes, como `"caption"` y `"value"`. Se trata de la lista de elementos admitidos:

-  `string` elementos (tanto con y sin estilo)
-  `entry` líneas (normal o contraseña)
-  `boolean` valores (mediante conmutadores o imágenes)


Elementos de cadena se pueden utilizar como botones, ya que proporciona un método que se invoca cuando el usuario pulsa en la celda o el accesorio,

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>Representación de elementos

La representación de elementos se basa en el C# StringElement y StyledStringElement y se puede representar la información de varias maneras y es posible representarlos en diversas formas. Se pueden crear los elementos más simple similar al siguiente:

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

Esto mostrará una cadena simple con todos los valores predeterminados: fuente, fondo, color del texto y adornos. Es posible enlazar las acciones a estos elementos y hacen que se comporten como botones estableciendo el `"ontap"` propiedad o el `"onaccessorytap"` propiedades:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

Los pasos anteriores invocará al método "ShowPhotos" en la clase "Acme.PhotoLibrary". El `"onaccessorytap"` es similar, pero solo se invocará si el usuario pulsa el accesorio en lugar de puntear en la celda. Para habilitar esta opción, también debe establecer el accesorio:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Representación de elementos puede mostrar dos cadenas a la vez, uno es el título y otro es el valor. Cómo se procesan estas cadenas dependen el estilo, se puede establecer utilizando la `"style"` propiedad. El valor predeterminado mostrará el título a la izquierda y el valor de la derecha. Consulte la sección sobre el estilo para obtener más detalles. Los colores se codifican utilizando el símbolo "#" seguido de números hexadecimales que representan los valores para los valores de rojos, verdes, azules y alfa quizás. El contenido se puede codificar de forma abreviada (3 o 4 dígitos hexadecimales) que representa los valores RGB o RGBA. O el formato largo (6 u 8 dígitos) que representan los valores RGB o RGBA. La versión corta es una forma abreviada para escribir dos veces el mismo dígito hexadecimal. Por lo que la constante "#1bc" es interpretará como rojo = 0 x 11, verde = 0xbb y azul = 0xcc. Si el valor alfa no está presente, el color es opaco. Algunos ejemplos:

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>Accesorio

Determina el tipo de accesorio que se mostrará en el elemento de representación, los valores posibles son:

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


Si el valor no está presente, no se muestra ningún accesorio

 <a name="background" />


### <a name="background"></a>fondo

La propiedad background establece el color de fondo de la celda. El valor es una dirección URL a una imagen (en este caso, se invocará el descargador del programa de imagen asincrónica y el fondo se actualizará una vez que se descarga la imagen) o puede ser un color especificado mediante la sintaxis de color.

 <a name="caption" />


### <a name="caption"></a>Título

La cadena principal que se mostrará en el elemento de representación. Se pueden personalizar la fuente y color estableciendo el `"textcolor"` y `"font"` propiedades. El estilo de representación viene determinada por la `"style"` propiedad.

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>color y detailcolor

El color que se usará para el texto principal o el texto detallado.

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>DetailFont y la fuente

La fuente que se usará para la leyenda o el texto de detalle. El formato de una especificación de la fuente es el nombre de fuente seguido opcionalmente por un guión y el tamaño de punto.
Estas son las especificaciones de fuente válidas:

-  "Helvetica"
-  "Helvetica-14"


 <a name="linebreak" />


### <a name="linebreak"></a>linebreak

Determina cómo se dividen las líneas. Los valores posibles son:

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


Ambos `character-wrap` y `word-wrap` puede utilizarse junto con el `"lines"` propiedad establecida en cero para convertir el elemento de representación en un elemento de varias líneas.

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>ONTAP y onaccessorytap

Estas propiedades deben apuntar a un nombre de método estático en la aplicación que toma un objeto como parámetro. Cuando se crea la jerarquía mediante los métodos JsonDialog.FromFile o JsonDialog.FromJson puede pasar un valor de objeto opcional. Este valor de objeto, a continuación, se pasa a los métodos. Puede usar esto para pasar un contexto para el método estático. Por ejemplo:

```csharp
class Foo {
    Foo ()
    {
        root = JsonDialog.FromJson (myJson, this);
    }

    static void Callback (object obj)
    {
        Foo myFoo = (Foo) obj;
        obj.Callback ();
    }
}
```

 <a name="lines" />


### <a name="lines"></a>líneas

Si se establece en cero, lo hará que el tamaño automático de elemento según el contenido de las cadenas contenidas. Para que funcione, debe establecer también la `"linebreak"` propiedad `"character-wrap"` o `"word-wrap"`.

 <a name="style" />


### <a name="style"></a>estilo

El estilo determina el tipo de estilo de celda que se usará para representar el contenido y se corresponden con los valores de enumeración UITableViewCellStyle.
Los valores posibles son:

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` : texto con un subtítulo.


 <a name="subtitle" />


### <a name="subtitle"></a>Subtítulo

El valor que se usará para el subtítulo. Se trata de un acceso directo para establecer el estilo `"subtitle"` y establezca el `"value"` propiedad en una cadena.
Esto lo hará con una sola entrada.

 <a name="textcolor" />


### <a name="textcolor"></a>textcolor

El color que se utilizará para el texto.

 <a name="value" />


### <a name="value"></a>value

El valor secundario que se mostrará en el elemento de representación. El diseño de esto se ve afectado por la `"style"` configuración. Se pueden personalizar la fuente y color estableciendo el `"detailfont"` y `"detailcolor"`.

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>Elementos booleanos

Los elementos booleanos deben establecer el tipo en `"bool"`, puede contener un `"caption"` para mostrar y el `"value"` se establece en true o false. Si el `"on"` y `"off"` se establecen las propiedades, se supone que las imágenes. Las imágenes se resuelven en relación con el directorio de trabajo actual en la aplicación. Si desea hacer referencia a los archivos relacionados con el paquete, puede usar el `"~"` como un acceso directo para representar el directorio del paquete de aplicación. Por ejemplo `"~/favorite.png"` será el favorite.png contenidas en el archivo del paquete. Por ejemplo:

```json
{ 
    "type": "boolean",
    "caption": "Demo of a Boolean",
    "value": true
},

{
    "type": "boolean",
    "caption": "Boolean using images",
    "value": false,
    "on": "favorite.png",
    "off": "~/favorited.png"
}
```

 <a name="type" />


### <a name="type"></a>type

Tipo puede establecerse en `"boolean"` o `"checkbox"`. Si establece en un valor booleano se utiliza un UISlider o imágenes (si ambos `"on"` y `"off"` se establecen). Si se establece en la casilla de verificación, se utiliza un control checkbox. El `"group"` propiedad puede usarse para etiquetar un elemento booleano como pertenecientes a un grupo determinado. Esto es útil si el contenedor raíz también tiene un `"group"` propiedad como la raíz resumirá los resultados con un recuento de todos los valores booleanos (o casillas de verificación) que pertenecen al mismo grupo.

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>Elementos de entrada

Utilice los elementos de entrada para permitir que el usuario escriba datos. El tipo de los elementos de entrada es `"entry"` o `"password"`. El `"caption"` propiedad se establece en el texto para mostrar de la derecha y el `"value"` se establece en el valor inicial para la entrada. El `"placeholder"` se usa para mostrar una sugerencia al usuario las entradas vacías (se muestra atenuado). A continuación se muestran algunos ejemplos:

```json
{
    "type": "entry",
    "caption": "Username",
    "value": "",
    "placeholder": "Your account username"
}, {
    "type": "password",
    "caption": "Password",
    "value": "",
    "placeholder": "You password"
}, {
    "type": "entry",
    "caption": "Zip Code",
    "value": "01010",
    "placeholder": "your zip code",
    "keyboard": "numbers"
}, {
    "type": "entry",
    "return-key": "route",
    "caption": "Entry with 'route'",
    "placeholder": "captialization all + no corrections",
    "capitalization": "all",
    "autocorrect": "no"
}
```

 <a name="autocorrect" />


### <a name="autocorrect"></a>Autocorrección

Determina el estilo de corrección automática que se usará para la entrada. Los valores posibles son true o false (o las cadenas `"yes"` y `"no"`).

 <a name="capitalization" />


### <a name="capitalization"></a>uso de mayúsculas

El estilo de mayúsculas y minúsculas que se usará para la entrada. Los valores posibles son:

-  `all`
-  `none`
-  `sentences`
-  `words`


 <a name="caption" />


### <a name="caption"></a>Título

El título que se usará para la entrada

 <a name="keyboard" />


### <a name="keyboard"></a>teclado

El tipo de teclado que se usará para la entrada de datos. Los valores posibles son:

-  `ascii`
-  `decimal`
-  `default`
-  `email`
-  `name`
-  `numbers`
-  `numbers-and-punctuation`
-  `twitter`
-  `url`


 <a name="placeholder" />


### <a name="placeholder"></a>Marcador de posición

El texto de sugerencia que se muestra cuando la entrada tiene un valor vacío.

 <a name="return-key" />


### <a name="return-key"></a>return-key

La etiqueta utilizada para la tecla ENTRAR. Los valores posibles son:

-  `default`
-  `done`
-  `emergencycall`
-  `go`
-  `google`
-  `join`
-  `next`
-  `route`
-  `search`
-  `send`
-  `yahoo`


 <a name="value" />


### <a name="value"></a>value

El valor inicial para la entrada

 <a name="Radio_Elements" />


## <a name="radio-elements"></a>Elementos de radio

Elementos de radio tengan tipo `"radio"`. Se selecciona el elemento seleccionado por el `radioselected` propiedad en su elemento raíz que contiene.
Además, si se establece un valor para el `"group"` propiedad, este botón de radio pertenece a ese grupo.

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>Fecha y hora elementos

Los tipos de elemento `"datetime"`, `"date"` y `"time"` se utilizan para representar las fechas con las horas, fechas u horas. Estos elementos toman como parámetros de un título y un valor. El valor puede escribirse en cualquier formato admitido por la función DateTime.Parse. NET. Ejemplo:

```json
"header": "Dates and Times",
"elements": [
    {
        "type": "datetime",
        "caption": "Date and Time",
        "value": "Sat, 01 Nov 2008 19:35:00 GMT"
    }, {
        "type": "date",
        "caption": "Date",
        "value": "10/10"
    }, {
        "type": "time",
        "caption": "Time",
        "value": "11:23"
    }                       
]
```

 <a name="Html/Web_Element" />


## <a name="htmlweb-element"></a>Elemento de HTML/Web

Puede crear una celda que cuando se puntea insertará un UIWebView que representa el contenido de una dirección URL especificada, ya sea local o remoto mediante el `"html"` tipo. Son las dos únicas propiedades para este elemento `"caption"` y `"url"`:

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```
