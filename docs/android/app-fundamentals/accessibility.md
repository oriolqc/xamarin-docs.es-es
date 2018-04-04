---
title: Accesibilidad en Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: 2a49d15651b8c6ab7417a69d934af5d20bfc13d0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility-on-android"></a>Accesibilidad en Android

Esta página describe cómo usar las API de accesibilidad Android para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).
Hacer referencia a la [iOS accesibilidad](~/ios/app-fundamentals/accessibility.md) y [accesibilidad de OS X](~/mac/app-fundamentals/accessibility.md) páginas para otra API de la plataforma.


## <a name="describing-ui-elements"></a>Que describe los elementos de interfaz de usuario

Android proporciona un `ContentDescription` propiedad que se utiliza por las API de lectura de pantalla para proporcionar una descripción accesible del propósito del control.

La descripción del contenido puede establecerse en cualquier C# o en el archivo de diseño AXML.

**C#**

La descripción se puede establecer en código para cualquier cadena (o un recurso de cadena):

```csharp
saveButton.ContentDescription = "Save data";
```

**Diseño AXML**

En XML, los diseños de usan el `android:contentDescription` atributo:

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>Utilice la sugerencia para TextView

Para `EditText` y `TextView` controles para la entrada de datos, use la `Hint` propiedad para proporcionar una descripción de la entrada se espera (en lugar de `ContentDescription`).
Cuando se ha especificado algún texto, el texto en sí será "de lectura" en lugar de la sugerencia.

**C#**

Establecer el `Hint` propiedad en el código:

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**Diseño AXML**

En XML se usan archivos de diseño la `android:hint` atributo:

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor vínculos de entrada campos con etiquetas

Para asociar una etiqueta a un control de entrada de datos, use la `LabelFor` propiedad

**C#**

En C#, establezca el `LabelFor` propiedad para el identificador de recurso del control este este contenido describe (normalmente esta propiedad se establece en una etiqueta y hace referencia a algún otro control de entrada):

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Diseño AXML**

En el diseño del uso de XML en el `android:labelFor` propiedad para hacer referencia el identificador de otro control:

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>Anunciar para mejorar la accesibilidad

Use el `AnnounceForAccessibility` control comunicarse un cambio de estado o evento a los usuarios cuando se habilita la accesibilidad de la vista de método en ninguno. Este método no es necesario para la mayoría de las operaciones donde la narración integrada proporciona información suficiente, pero se debe usar información adicional que sería útil para el usuario.

El código siguiente muestra una llamada de ejemplo simple `AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Cambiar la configuración de foco

Navegación accesible se basa en controles que tienen el foco a ayudar al usuario a comprender qué operaciones están disponibles. Android proporciona un `Focusable` propiedad que puede etiquetar controles como específicamente capaz de recibir el foco durante la navegación.

**C#**

Para evitar que un control obtengan el foco con C#, establezca el `Focusable` propiedad `false`:

```csharp
label.Focusable = false;
```

**Diseño AXML**

En diseño de conjunto de archivos de XML del `android:focusable` atributo:

```xml
<android:focusable="false" />
```

También puede controlar el orden de foco con el `nextFocusDown`, `nextFocusLeft`, `nextFocusRight`, `nextFocusUp` atributos, normalmente se establece en el diseño AXML. Usar estos atributos para asegurarse de que el usuario puede navegar fácilmente por los controles en la pantalla.


## <a name="accessibility-and-localization"></a>Accesibilidad y la localización

En los ejemplos anteriores, la descripción de la sugerencia y contenido son establecer directamente en el valor de presentación. Es preferible utilizar valores en un **Strings.xml** archivo, como este:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

Usa texto desde un archivo de cadenas se muestra a continuación en C# y archivos de diseño AXML:

**C#**

En lugar de usar literales de cadena en el código, buscar valores traducidos de los archivos de cadenas con `Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

En diseño XML como atributos de accesibilidad `hint` y `contentDescription` se puede establecer en un identificador de cadena:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

La ventaja de almacenar texto en un archivo independiente es que pueden proporcionar varias traducciones de idioma del archivo en la aplicación. Consulte la [Guía de localización Android](~/android/app-fundamentals/localization.md) para obtener información sobre cómo agregar archivos de cadena traducida a un proyecto de aplicación.


## <a name="testing-accessibility"></a>Probar la accesibilidad

Siga [estos pasos](http://developer.android.com/training/accessibility/testing.html#how-to) para habilitar TalkBack y explorar con el tacto probar la accesibilidad en dispositivos Android.

Puede que necesite instalar [TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) de Google Play si no aparece en **configuración > accesibilidad**.


## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [API de Android accesibilidad](http://developer.android.com/guide/topics/ui/accessibility/index.html)
