---
title: Trabajar con watchOS localización en Xamarin
description: Este documento describe cómo localizar aplicaciones de watchOS compiladas con Xamarin. Se trata de aplicaciones de la inspección, las extensiones de inspección de guión gráfico cadenas en el código, texto, pruebas y mucho más.
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 4f158f1c8699ad5090eb7fade8af8918c8881d95
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790784"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>Trabajar con watchOS localización en Xamarin

_Adaptar aplicaciones watchOS para varios idiomas_

![](localization-images/both-languages-sml.png "Apple Watch mostrar contenido localizado")

aplicaciones de watchOS se localizan con los métodos estándar de iOS:

- Usar **identificador de localización** en los elementos del guión gráfico,
- **.Strings** archivos asociados con el guión gráfico, y
- **Localizable.Strings** archivos de texto utilizado en el código.

Los guiones gráficos de forma predeterminada y los recursos se encuentran en un **Base** directorio y traducciones específica del lenguaje y otros recursos se almacenan en **.lproj** directorios.
iOS y OS inspección usarán automáticamente selección del idioma del usuario para cargar las cadenas correctas y recursos.

Dado que una aplicación de Apple Watch consta de dos partes: aplicación de inspección y extensión Watch - cadena localizada que son necesarios en dos lugares, dependiendo de cómo se usan.

El texto localizado y recursos estarán *diferentes* en la aplicación de inspección y la extensión de inspección.

## <a name="watch-app"></a>Aplicación de inspección

La aplicación de inspección contiene el guión gráfico que describe la interfaz de usuario de la aplicación. Todos los controles (como `Label` y `Image`) que admitir la localización tiene un **identificador de localización**.

Cada específicos del idioma **.lproj** directorio debe contener **.strings** archivos con las traducciones para cada elemento (utilizando el **identificador de localización**), así como imágenes al que hace referencia el guión gráfico.

## <a name="watch-extension"></a>Extensión de inspección

La extensión de supervisión es donde se ejecuta el código de la aplicación. Cualquier texto que se muestra al usuario desde el código debe localizarse en la extensión y no en la aplicación de inspección.

La extensión debe contener también específicos del idioma **.lproj** directorios, pero la **.strings** archivos solo requieren traducciones para el texto que se usa en el código.

## <a name="globalizing-the-watch-solution"></a>Globalizar la solución de inspección

La globalización es el proceso de hacer que una aplicación localizable.
Para las aplicaciones de inspección que esto significa diseñar el guión gráfico con distintas longitudes de texto en mente, asegurándose de cada diseño de pantalla ajusta correctamente según el texto que se muestra. También debe asegurarse de que las cadenas que se hace referencia en el código de extensión de inspección se pueden traducir a través del `LocalizedString` método.

### <a name="watch-app"></a>Aplicación de inspección

De forma predeterminada, la aplicación de inspección no está configurada para la localización. Debe mover el archivo del guión gráfico predeterminado y crear algunos otros directorios para las traducciones:

1. Crear **Base.lproj** directorio y mueva el **Interface.storyboard** en él.

2. Crear  **<language>.lproj** directorios para cada idioma que desea admitir.

3. El **.lproj** directorios deben contener una **Interface.strings** archivo de texto (el nombre de archivo debe coincidir con el nombre del storboard). Si lo desea, puede colocar cualquier imagen que requieren la localización de estos directorios.

El proyecto de aplicación de inspección aspecto una vez realizados estos cambios (sólo inglés y español idioma se han agregado archivos):

  ![](localization-images/watchapp-solution.png "El proyecto de aplicación de inspección con archivos de idioma inglés y español")

#### <a name="storyboard-text"></a>Texto de guión gráfico

Cuando se edita el guión gráfico, seleccione cada elemento y observe el **identificador de localización** que aparece en el **propiedades** panel:

  [![](localization-images/storyboard-sml.png "El identificador de localización que aparece en el panel de propiedades")](localization-images/storyboard.png#lightbox)

En el **Base.lproj** carpeta, cree pares de clave y valor tal y como se muestra a continuación, donde la clave está formada por el **identificador de localización** y un nombre de propiedad en el control, se unen mediante un punto (`.`).

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

En este ejemplo, que un **identificador de localización** puede ser una cadena simple de número (p. ej. "0", "1", etcetera) o una cadena más compleja (por ejemplo, "AgC-lista de eventos-Hgc"). `Label` los controles tienen un `Text` propiedad y `Button`tienen un `Title` propiedad, que se refleja en la forma en que se establecen sus valores traducidos: asegúrese de usar el nombre de propiedad en minúsculas, tal como se muestra en el ejemplo anterior.

Cuando se procesa el guión gráfico en el reloj, los valores correctos se extrae y se mostrarán según el idioma seleccionado por el usuario automáticamente.

#### <a name="storyboard-images"></a>Imágenes del guión gráfico

La solución de ejemplo también incluye un **gradient@2x.png** imagen en cada carpeta de idioma. Esta imagen puede ser diferente para cada idioma (p. ej. ha incrustado texto que se debe traducir, o use adaptado iconography).

Basta con establecer la imagen **imagen** propiedad en el guión gráfico y la imagen correcta se representará en el reloj según el idioma seleccionado por el usuario.

![](localization-images/storyboard-image.png "Establecer la propiedad de imagen de las imágenes en el guión gráfico.")

Nota: dado que todos los relojes de Apple tienen pantallas de Retina, solo el **@2x** se requiere la versión de la imagen. No es necesario especificar **@2x** en el guión gráfico.

### <a name="watch-extension"></a>Extensión de inspección

La extensión de supervisión requiere una estructura de directorios similar para admitir la localización, sin embargo no hay ningún guión gráfico. Las cadenas localizadas en la extensión son únicamente aquellos que se hace referencia a código de C#.

![](localization-images/watchextension-solution.png "La estructura de directorios de la extensión de inspección para admitir la localización")

#### <a name="strings-in-code"></a>Cadenas en el código

El **Localizable.strings** archivo tiene una estructura ligeramente distinta a si se asoció con un guión gráfico. En este caso podemos elegir cualquier cadena "clave"; Recomendación de Apple es usar una clave que refleja el texto real que se mostraría en el idioma predeterminado:

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

El `NSBundle.MainBundle.LocalizedString` método se utiliza para resolver las cadenas en sus homólogos traducidos, tal como se muestra en el código siguiente.

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>Imágenes en el código

Las imágenes que se rellenan con el código pueden establecerse de dos maneras.

1. Puede cambiar un `Image` control estableciendo su valor en el nombre de cadena de una imagen que ya existe en la aplicación de inspección, p. ej.

  ```csharp
  displayImage.SetImage("gradient"); // image in Watch App (as shown above)
  ```

2. Puede mover una imagen de la extensión a la inspección mediante `FromBundle` y la aplicación elegirá automáticamente la imagen correcta para la selección del idioma del usuario. En la solución de ejemplo hay una imagen **language@2x.png** en cada idioma carpeta y se muestra en `DetailController` utilizando el código siguiente:

  ```csharp
  using (var image = UIImage.FromBundle ("language")) {
    displayImage.SetImage (image);
  }
  ```

  Tenga en cuenta que no es necesario especificar el **@2x** cuando se hace referencia al nombre de archivo de la imagen.

El segundo método también es aplicable si descarga una imagen desde un servidor remoto que se representará en el reloj; Sin embargo en este caso debe asegurarse de que la imagen que descarga correctamente se localiza según las preferencias del usuario.

## <a name="localization"></a>Localización

Una vez haya configurado la solución, será necesario que los traductores de procesar su **.strings** archivos e imágenes para cada idioma que desea admitir.

Puede crear tantos **.lproj** directorios que necesitan (uno para cada idioma admitido). Se denominan mediante códigos de idioma, como **en**, **es**, **Alemania**, **ja**, **pt-BR**, etc. (en inglés Español, alemán, japonés y portugués (Brasil) respectivamente).

El ejemplo que se adjunta utiliza traducciones (generado por el equipo) para mostrar cómo localizar una aplicación watchOS.

### <a name="watch-app"></a>Aplicación de inspección

Estos valores se utilizan para traducir la interfaz de usuario definida en el guión gráfico de la aplicación de la inspección. El *clave* valor es una combinación de cada control de guión gráfico **identificador de localización** y la propiedad que se está traduciendo.

Se recomienda agregar comentarios que contiene el texto original en el archivo para que los traductores sepa cuál debería ser la traducción.

#### <a name="eslprojinterfacestrings"></a>es.lproj/Interface.strings

A continuación se muestran las cadenas de idioma español (traducido automática) para el guión gráfico. Resulta útil agregar comentarios a cada línea, ya que es difícil saber qué la **identificador de localización** hace referencia a lo contrario:

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Extensión de inspección

Estos valores se utilizan en el código para convertir la información antes de mostrarlo al usuario. El *clave* seleccionados por el desarrollador mientras está escribiendo código y suele contener la cadena real que se deben traducir.

#### <a name="eslprojlocalizablestrings-file"></a>archivo es.lproj/Localizable.strings

Las cadenas de idioma de Spansish (traducido automática):

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>Pruebas

El método para cambiar las preferencias de idioma difiere entre el simulador y dispositivos físicos.

### <a name="simulator"></a>Simulador

En el simulador, seleccione el idioma que pruebe el uso de iOS **configuración** aplicación (el icono de artes gris en la pantalla principal del simulador).

  ![](localization-images/sim-settings-sml.png "La configuración de la localización de configuración de la aplicación de iOS")

### <a name="watch-device"></a>Dispositivo de inspección

Cuando se prueba con un reloj, cambiar idioma de la inspección en la **de Apple Watch** aplicación en el iPhone emparejado.

  ![](localization-images/phone-settings-sml.png "Cambiar idioma de la inspección en la aplicación de Apple Watch en el iPhone emparejado")



## <a name="related-links"></a>Vínculos relacionados

- [WatchLocalization (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchLocalization/)
