---
title: watchOS complicaciones en Xamarin
description: Este documento describe cómo trabajar con las complicaciones de watchOS en Xamarin. Describe cómo agregar una complicación, escribir una complicación, plantillas y proporciona código de ejemplo.
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/03/2017
ms.openlocfilehash: 85b0c9b0688e9fb310a8f427018a02fe629404bb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117751"
---
# <a name="watchos-complications-in-xamarin"></a>watchOS complicaciones en Xamarin

_watchOS permite a los desarrolladores escribir complicaciones personalizadas de esferas de reloj_

Esta página explica los diferentes tipos de complicaciones disponibles y cómo agregar una complicación a la aplicación para watchOS 3.

Tenga en cuenta que cada aplicación para watchOS sólo puede tener una complicación.

Empiece por leer [la documentación de Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) para determinar si la aplicación es adecuada para una complicación. Hay 5 `CLKComplicationFamily` tipos de visualización para elegir:

[![](complications-images/all-complications-sml.png "Los 5 tipos CLKComplicationFamily disponibles: Circular pequeño, Modular de pequeño, grande Modular, Utilitarios pequeño, Utilitarios grandes")](complications-images/all-complications.png#lightbox)

Pueden implementar aplicaciones solo estilo o todos los cinco, dependiendo de los datos que se muestran.
También puede admitir el tiempo de viaje, proporciona los valores para ocasiones en las últimas y futuras como el usuario activa la corona Digital.

<a name="adding" />

## <a name="adding-a-complication"></a>Adición de una complicación

### <a name="configuration"></a>Configuración

Complicaciones pueden agregar a una aplicación de inspección durante la creación, o agregadas manualmente a una solución existente.

### <a name="add-new-project"></a>Agregar nuevo proyecto...

El **Agregar nuevo proyecto...**  asistente incluye una casilla de verificación que se creará automáticamente una clase de controlador de complicación y configurará el **Info.plist** archivo:

![](complications-images/file-new-project-sml.png "La casilla Incluir complicación")

### <a name="existing-projects"></a>Proyectos existentes

Para agregar una complicación a un proyecto existente:

1. Cree un nuevo **ComplicationController.cs** archivo de clase e implemente `CLKComplicationDataSource`.
2. Configuración de la aplicación **Info.plist** para exponer la complicación e identidad admiten qué familias de complicación.

Estos pasos se describen más detalladamente a continuación.

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>Clase CLKComplicationDataSource

La siguiente C# plantilla incluye los métodos necesarios mínimos para implementar un `CLKComplicationDataSource`.

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
    }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
    }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
    }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
    }
}
```

Siga el [escribir una complicación](#writing) instrucciones para agregar código a esta clase.

### <a name="infoplist"></a>Info.plist

La extensión de inspección **Info.plist** archivo debe especificar el nombre de la `CLKComplicationDataSource` y qué familias de complicación que desea admitir:

[![](complications-images/complications-config-sml.png "Los tipos de familia de complicación")](complications-images/complications-config.png#lightbox)

El **clase de origen de datos** lista de entradas mostrará los nombres de clase esa subclase `CLKComplicationDataSource` subclase que incluye la lógica de complicación.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Toda la funcionalidad de complicación se implementa en una sola clase, invalidar los métodos desde el `CLKComplicationDataSource` clase abstracta (que implementa el `ICLKComplicationDataSource` interfaz).

### <a name="required-methods"></a>Métodos obligatorios

Debe implementar los métodos siguientes para la complicación ejecutar:

- `GetPlaceholderTemplate` : Devuelve la pantalla estática que se utiliza durante la configuración o cuando la aplicación no puede proporcionar un valor.
- `GetCurrentTimelineEntry` -Permite calcular la presentación correcta cuando se está ejecutando la complicación.
- `GetSupportedTimeTravelDirections` -Devuelve las opciones de `CLKComplicationTimeTravelDirections` como `None`, `Forward`, `Backward`, o `Forward | Backward`.

### <a name="privacy"></a>Privacidad

Complicaciones que muestren los datos personales

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` o `HideOnLockScreen`

Si este método devuelve `HideOnLockScreen` la complicación mostrará un icono o el nombre de la aplicación (y no los datos) cuando se bloquea la inspección.

### <a name="updates"></a>Actualizaciones

- `GetNextRequestedUpdateDate` -Devuelven la hora cuando el sistema operativo deben consultar a continuación la aplicación para que muestre los datos actualizados de complicación.

También puede forzar una actualización de la aplicación de iOS.

### <a name="supporting-time-travel"></a>Auxiliar tiempo de viaje

Compatibilidad con tiempo de viaje es opcional y controlado por la `GetSupportedTimeTravelDirections` método. Si devuelve `Forward`, `Backward`, o `Forward | Backward` , a continuación, debe implementar los métodos siguientes

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>Escribir una complicación

Intervalo de las complicaciones de datos simple Mostrar imagen complicada y la representación de datos con compatibilidad con el desplazamiento de tiempo. El código siguiente muestra cómo crear una complicación simple, solo de plantilla.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Código de ejemplo

En este ejemplo solo se admite la `UtilitarianLarge` plantilla, por lo que solo se puede seleccionar en esferas de reloj específico que admiten ese tipo de complicación. Cuando *seleccionando* complicaciones en una inspección, muestra **COMPLICACIÓN mi** y cuándo *ejecutando* muestra el texto **minuto _hora_**   (con una parte del tiempo).

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```


<a name="templates" />

## <a name="complication-templates"></a>Plantillas de complicación

Hay una serie de plantillas diferentes para cada estilo de complicación.
El **anillo** plantillas le permiten mostrar un anillo de progreso de estilo en torno a la complicación, que puede usarse para mostrar el progreso o algún otro valor gráficamente.

[La documentación de Apple CLKComplicationTemplate](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Circular pequeño

Estos nombres de clase de plantilla tienen antepuesto el prefijo `CLKComplicationTemplateCircularSmall`:

- **RingImage** -mostrar una sola imagen, con un anillo de progreso alrededor de ella.
- **RingText** -mostrar una sola línea de texto, con un anillo de progreso alrededor de ella.
- **SimpleImage** -solo muestran una sola imagen pequeña.
- **SimpleText** -simplemente mostrar un pequeño fragmento de texto.
- **StackImage** -mostrar una imagen y una línea de texto, uno encima del otro
- **StackText** -mostrar dos líneas de texto.

### <a name="modular-small"></a>Modular de pequeño tamaño

Estos nombres de clase de plantilla tienen antepuesto el prefijo `CLKComplicationTemplateModularSmall`:

- **ColumnsText** -mostrar una cuadrícula pequeña de valores de texto (2 filas y 2 columnas).
- **RingImage** -mostrar una sola imagen, con un anillo de progreso alrededor de ella.
- **RingText** -mostrar una sola línea de texto, con un anillo de progreso alrededor de ella.
- **SimpleImage** -solo muestran una sola imagen pequeña.
- **SimpleText** -simplemente mostrar un pequeño fragmento de texto.
- **StackImage** -mostrar una imagen y una línea de texto, uno encima del otro
- **StackText** -mostrar dos líneas de texto.

### <a name="modular-large"></a>Modular de gran tamaño

Estos nombres de clase de plantilla tienen antepuesto el prefijo `CLKComplicationTemplateModularLarge`:

- **Columnas** -mostrar una cuadrícula de 3 filas con 2 columnas, puede incluir una imagen a la izquierda de cada fila.
- **StandardBody** -mostrar una cadena de encabezado en negrita, con dos filas de texto sin formato. El encabezado, opcionalmente, puede mostrar una imagen de la izquierda.
- **Tabla** -mostrar una cadena de encabezado en negrita, con una cuadrícula de 2 x 2 de texto situado debajo de él. El encabezado, opcionalmente, puede mostrar una imagen de la izquierda.
- **TallBody** -mostrar una cadena de encabezado en negrita, con una mayor fuente única línea de texto que aparece debajo.

### <a name="utilitarian-small"></a>Utilitarios pequeño

Estos nombres de clase de plantilla tienen antepuesto el prefijo `CLKComplicationTemplateUtilitarianSmall`:

- **Plano** -muestra una imagen y texto en una sola línea (el texto debe ser corto).
- **RingImage** -mostrar una sola imagen, con un anillo de progreso alrededor de ella.
- **RingText** -mostrar una sola línea de texto, con un anillo de progreso alrededor de ella.
- **Cuadrado** -mostrar una imagen de cuadrados (40px o 44px cuadrado para los 38 mm o Apple Watch de 42 mm, respectivamente).

### <a name="utilitarian-large"></a>Utilitarios grandes

Hay sólo una plantilla para este estilo de complicación: `CLKComplicationTemplateUtilitarianLargeFlat`.
Muestra una sola imagen y texto, en una sola línea.



## <a name="related-links"></a>Vínculos relacionados

- [La documentación de Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [Vídeo de la sesión WWDC](https://developer.apple.com/videos/play/wwdc2015-209/)
