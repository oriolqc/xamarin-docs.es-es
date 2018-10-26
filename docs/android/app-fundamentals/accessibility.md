---
title: Accesibilidad en Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 4eb4a97a346f3906c925dc9e324ed9378af0b560
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116191"
---
# <a name="accessibility-on-android"></a>Accesibilidad en Android

Esta página describe cómo usar las API de accesibilidad de Android para compilar aplicaciones de acuerdo con la [lista de comprobación de accesibilidad](~/cross-platform/app-fundamentals/accessibility.md).
Hacer referencia a la [iOS accesibilidad](~/ios/app-fundamentals/accessibility.md) y [accesibilidad OS X](~/mac/app-fundamentals/accessibility.md) páginas para otra API de plataforma.


## <a name="describing-ui-elements"></a>Que describe los elementos de interfaz de usuario

Android proporciona un `ContentDescription` propiedad que se utiliza por las API de lectura de pantalla para proporcionar una descripción accesible del propósito del control.

La descripción del contenido puede establecerse en C# o en el archivo de diseño AXML.

**C#**

La descripción se puede establecer en el código para cualquier cadena (o un recurso de cadena):

```csharp
saveButton.ContentDescription = "Save data";
```

**Diseño AXML**

En XML usan diseños el `android:contentDescription` atributo:

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>Use la sugerencia de vista de texto

Para `EditText` y `TextView` controles para entrada de datos, utilice el `Hint` propiedad para proporcionar una descripción de qué entrada se espera (en lugar de `ContentDescription`).
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


### <a name="labelfor-links-input-fields-with-labels"></a>Los campos con etiquetas de entrada de LabelFor vínculos

Para asociar una etiqueta con un control de entrada de datos, use el `LabelFor` propiedad

**C#**

En C#, establezca el `LabelFor` describe la propiedad para el identificador de recurso del control este este contenido (normalmente, esta propiedad se establece en una etiqueta y hace referencia a algún otro control de entrada):

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Diseño AXML**

En el diseño del uso de XML en el `android:labelFor` propiedad para hacer referencia al identificador de otro control:

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

Use el `AnnounceForAccessibility` método en cualquier control para comunicar un cambio de estado o de eventos a los usuarios cuando se habilita la accesibilidad de vista. Este método no es necesario para la mayoría de las operaciones donde la narración integrada proporciona información suficiente, pero se debe usar información adicional que sería útil para el usuario.

El código siguiente muestra una llamada de ejemplo sencillo `AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Cambie la configuración de foco

Navegación accesible se basa en los controles que tienen el foco para ayudar al usuario a comprender qué operaciones están disponibles. Android proporciona un `Focusable` propiedad que se puede etiquetar controles como específicamente capaz de recibir el foco durante la navegación.

**C#**

Para impedir que un control obtengan el foco con C#, establezca el `Focusable` propiedad `false`:

```csharp
label.Focusable = false;
```

**Diseño AXML**

En diseño de conjunto de archivos de XML de la `android:focusable` atributo:

```xml
<android:focusable="false" />
```

También puede controlar el orden de foco con el `nextFocusDown`, `nextFocusLeft`, `nextFocusRight`, `nextFocusUp` atributos, normalmente se establece en el diseño AXML. Use estos atributos para asegurarse de que el usuario puede navegar fácilmente por los controles de la pantalla.


## <a name="accessibility-and-localization"></a>Localización y accesibilidad

En los ejemplos anteriores son la descripción de la sugerencia y contenido establece directamente en el valor de presentación. Es preferible utilizar los valores de un **Strings.xml** archivo, como esto:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

Mediante el texto de un archivo de cadenas se muestra a continuación en C# y archivos de diseño AXML:

**C#**

En lugar de literales de cadena en el código, buscar valores traducidos de los archivos de las cadenas con `Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

En el diseño XML como atributos de accesibilidad `hint` y `contentDescription` se puede establecer en un identificador de cadena:

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

La ventaja de almacenar texto en un archivo independiente es que se pueden proporcionar varias traducciones de lenguaje del archivo en la aplicación. Consulte la [guía localización Android](~/android/app-fundamentals/localization.md) para obtener información sobre cómo agregar archivos de cadena localizada a un proyecto de aplicación.


## <a name="testing-accessibility"></a>Probar la accesibilidad

Siga [estos pasos](http://developer.android.com/training/accessibility/testing.html#how-to) para habilitar TalkBack y explorar con el tacto probar la accesibilidad en los dispositivos Android.

Es posible que deba instalar [TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) desde Google Play si no aparece en **configuración > accesibilidad**.


## <a name="related-links"></a>Vínculos relacionados

- [Accesibilidad de multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [API de accesibilidad de Android](http://developer.android.com/guide/topics/ui/accessibility/index.html)
