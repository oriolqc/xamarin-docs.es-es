---
title: Entrada de texto en Xamarin.iOS
description: Este documento describe la entrada de texto en una aplicación de Xamarin.iOS. Describe el uso UITextField y UITextVIew mediante programación y en el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 5d8648f5830a7adcd32d253b92fae45098f12a83
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790219"
---
# <a name="text-input-in-xamarinios"></a>Entrada de texto en Xamarin.iOS

Aceptar entradas de texto del usuario se logra con la `UITextField` para las entradas de línea y UITextView de varias líneas de texto editable. Puede arrastrar cualquiera de estos controles a una pantalla y haga doble clic para establecer el texto inicial.

Las capturas de pantalla siguiente muestran los iconos de estos controles, que se encuentra en el panel del cuadro de herramientas de Visual Studio para Mac:

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

Una vez que se ha asignado un nombre de la toma de corriente y guardado el archivo del guión gráfico, Visual Studio para Mac actualizará la `.designer.cs` clase parcial y se puede agregar código de C# que hace referencia el control al archivo de clase. Cada control tiene sus propias propiedades únicas y los eventos que son accesibles en el código de C#.

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

El `UITextField` control más a menudo se usa para aceptar una sola línea de entrada de texto como un nombre de usuario o una contraseña. Aquí se muestran algunas de las opciones disponibles para personalizar el control:

 [![](text-input-images/image15a.png "Propiedades de UITextField")](text-input-images/image15a.png#lightbox)

A continuación se explican estos controles:

-  **Marcador de posición** : Esto es opcional. Si establece esta opción, se muestra cuando el campo de texto está vacío, normalmente para explicar al usuario se espera que la entrada.
-  **Botón Borrar** – controla cuando aparezca el botón Borrar estándar (el círculo gris con (X)) en el campo de texto, como una manera para que el usuario va a borrar texto rápidamente. Puede ser oculto de forma permanente, visible de forma permanente o se muestra, dependiendo de si se está editando el campo.
-  **Tamaño de fuente mínimo** y **ajuste para ajustar** – permite que el tamaño de fuente se ajustan automáticamente para la longitud del texto se ajusta y evitar el truncamiento, pero limitado en no menor que el tamaño especificado.
-  **Uso de mayúsculas** : si se debe poner en mayúsculas automáticamente palabras, frases o todas las entradas.
-  **Corrección** : indica si se habilitan la revisión ortográfica y sugerencias.
-  **Teclado** : controles que muestran el estilo de teclado para la entrada y, por lo tanto, ¿qué claves están disponibles en el teclado. Esto incluye del teclado numérico, panel de teléfono, correo electrónico, dirección URL junto con otras opciones.
-  **Apariencia** : controla el estilo de la apariencia del teclado se y cualquier oscuro o claro con temas.
-  **Devolver clave** : cambiar la etiqueta de la tecla ENTRAR para reflejar mejor la acción que se van a realizar. Los valores admitidos incluyen Go, combinación, siguiente, ruta, de hecho y la búsqueda.
-  **Proteger** – identifica si se enmascara la entrada (por ejemplo, para una entrada de contraseña).


Si llama a un UITextField `textfield1` se ha agregado a una pantalla con el diseñador, puede establecer o cambiar sus propiedades en C# como sigue:

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin.iOS proporciona enumeraciones cuando sea necesario que resulte sencillo seleccionar la configuración que desee, como el `UIKeyboardType` y `UIReturnKeyType` en el fragmento de código anterior.

### <a name="display-text-programmatically"></a>Mostrar texto mediante programación

Si no desea que la pantalla se han diseñado con el diseñador, o si desea agregar algún texto dinámicamente en tiempo de ejecución, puede crear y mostrar un UITextField mediante programación en el `ViewDidLoad` método de un controlador de vista similar al siguiente:

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

El `UITextView` control puede utilizarse para mostrar texto de sólo lectura o para aceptar la entrada de texto de varias líneas. Tiene muchas de las mismas opciones que el `UITextField` (por ejemplo, mayúsculas y minúsculas, corrección, etcetera).

 [![](text-input-images/image16a.png "Propiedades de UITextView")](text-input-images/image16a.png#lightbox)

Propiedades específicas incluyen:

-  **Comportamiento** : indica si el texto es modificable o de solo lectura.
-  **Detección** : detecta y convierte los datos de entrada en elementos seleccionables, como números de teléfono que pueden desencadenar una llamada, que se convierten en direcciones vincula a asignaciones, las direcciones URL que se abren en Safari o fechas y horas que se convierten en eventos de calendario.


Si se ha agregado una UITextView a una pantalla con el diseñador, puede establecer o cambiar sus propiedades similar al siguiente:

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
