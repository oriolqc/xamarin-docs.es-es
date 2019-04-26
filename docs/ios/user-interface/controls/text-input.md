---
title: Entrada de texto en Xamarin.iOS
description: Este documento describe la entrada de texto en una aplicación de Xamarin.iOS. Describe el uso de interfaz de usuario y UITextVIew mediante programación y en el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: b309cbdf37acaa71740a4d5d03e4824efd40f359
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200987"
---
# <a name="text-input-in-xamarinios"></a>Entrada de texto en Xamarin.iOS

Aceptar la entrada de texto del usuario se logra con la `UITextField` para entradas de línea y UITextView de varias líneas de texto editable. Puede arrastrar cualquiera de estos controles a una pantalla y haga doble clic para establecer el texto inicial.

Las capturas de pantalla siguiente muestran los iconos de estos controles, que se encuentra en el panel cuadro de herramientas en Visual Studio para Mac:

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

Una vez que haya asignado un nombre de la toma de corriente y guardar el archivo de guión gráfico, Visual Studio para Mac se actualizará el `.designer.cs` clase parcial y puede agregar C# código que hace referencia al control al archivo de clase. Cada control tiene sus propias propiedades únicas y los eventos que se pueden acceder en su C# código.

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

El `UITextField` control más a menudo se usa para aceptar una sola línea de entrada de texto como un nombre de usuario o contraseña. Aquí se muestran algunas de las opciones disponibles para personalizar el control:

 [![](text-input-images/image15a.png "Propiedades de la interfaz de usuario")](text-input-images/image15a.png#lightbox)

Estos controles se explican a continuación:

-  **Marcador de posición** : Esto es opcional. Si se establece, se muestra cuando el campo de texto está vacío, normalmente para explicar al usuario qué entrada se espera.
-  **Botón Borrar** – controla cuando aparezca el botón Borrar estándar (el círculo gris con (X)) en el campo de texto, como una forma para el usuario borrar texto rápidamente. Puede ser ocultos de forma permanente, visible de forma permanente o se muestra, dependiendo de si se está editando el campo.
-  **Tamaño de fuente mínimo** y **ajustar al tamaño** : permite que el tamaño de fuente se ajustan automáticamente para ajustarse a la longitud del texto y evitar el truncamiento, pero limitada a ningún menor que el tamaño especificado.
-  **Uso de mayúsculas** : si desea aprovechar automáticamente las palabras, frases o todas las entradas.
-  **Corrección** : indica si se habilitan el corrector ortográfico y sugerencias.
-  **Teclado** : controles que muestran el estilo de teclado para la entrada y, por lo tanto, qué claves están disponibles en el teclado. Esto incluye del teclado numérico, panel de teléfono, correo electrónico, dirección URL junto con otras opciones.
-  **Apariencia** : controla el estilo de apariencia del teclado y se cualquier oscuro o claro con temas.
-  **Devolver clave** : cambiar la etiqueta de la tecla ENTRAR para reflejar mejor qué acción se realizará. Los valores admitidos son Go, combinación, siguiente, ruta, de hecho y búsqueda.
-  **Proteger** – identifica si se enmascara la entrada (por ejemplo, una entrada de contraseña).


Si llama una interfaz de usuario `textfield1` se ha agregado a una pantalla con el diseñador, puede establecer o cambiar sus propiedades en C# como sigue:

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin.iOS proporciona enumeraciones cuando sea apropiado para que sea fácil seleccionar la configuración que desee, como el `UIKeyboardType` y `UIReturnKeyType` en el fragmento de código anterior.

### <a name="display-text-programmatically"></a>Mostrar texto mediante programación

Si no desea diseñar la pantalla con el diseñador, o si desea agregar algún texto dinámicamente en tiempo de ejecución, puede crear y mostrar una interfaz de usuario mediante programación en el `ViewDidLoad` método de un controlador de vista similar al siguiente:

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

El `UITextView` control puede usarse para mostrar texto de sólo lectura o para aceptar la entrada de texto de varias líneas. Muchas de las mismas opciones que tiene el `UITextField` (por ejemplo, la capitalización, corrección, etcetera).

 [![](text-input-images/image16a.png "Propiedades de UITextView")](text-input-images/image16a.png#lightbox)

Propiedades específicas incluyen:

-  **Comportamiento** : indica si el texto es modificable o de solo lectura.
-  **Detección** : detecta y convierte los datos de entrada en los elementos seleccionables, como números de teléfono que pueden desencadenar una llamada, que se convierten en direcciones vincula a los mapas, direcciones URL que se abren en Safari o fechas y horas que se convierten en los eventos de calendario.


Si se ha agregado un UITextView a una pantalla con el diseñador, puede establecer o cambiar sus propiedades similar al siguiente:

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
