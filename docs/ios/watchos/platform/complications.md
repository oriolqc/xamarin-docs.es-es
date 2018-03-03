---
title: Complicaciones
description: "watchOS permite a los desarrolladores escribir complicaciones personalizadas para caras de inspección"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/03/2017
ms.openlocfilehash: a13de7fbb4b6e1f9fa2853ce599f3a038a5e4040
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="complications"></a>Complicaciones

_watchOS permite a los desarrolladores escribir complicaciones personalizadas para caras de inspección_

Esta página explica los diferentes tipos de complicaciones disponibles y cómo agregar una complicación a la aplicación watchOS 3.

Tenga en cuenta que cada aplicación watchOS solo puede tener una complicación.

Empiece por leer [documentos de Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) para determinar si la aplicación es adecuada para una complicación. Hay 5 `CLKComplicationFamily` tipos de visualización que puede elegir:

[ ![](complications-images/all-complications-sml.png "Los tipos de CLKComplicationFamily 5 disponibles: Circular pequeña, Modular pequeños, grandes Modular, Utilitarios pequeño, Utilitarios grandes")](complications-images/all-complications.png)

Aplicaciones pueden implementar un solo estilo o los cinco, dependiendo de los datos que se va a mostrar.
También puede disponer de viaje en el tiempo, proporciona los valores para las horas anteriores y futuras como el usuario activa la copa Digital.

<a name="adding" />

## <a name="adding-a-complication"></a>Agregar una complicación

### <a name="configuration"></a>Configuración

Complicaciones pueden agregarse a una aplicación de inspección durante la creación, ni se agregan manualmente a una solución existente.

### <a name="add-new-project"></a>Agregar nuevo proyecto...

El **Agregar nuevo proyecto...**  asistente incluye una casilla de verificación que se creará automáticamente una clase de controlador de complicación y configurará el **Info.plist** archivo:

![](complications-images/file-new-project-sml.png "La casilla de verificación Incluir complicación")

### <a name="existing-projects"></a>Proyectos existentes

Para agregar una complicación a un proyecto existente:

1. Crear un nuevo **ComplicationController.cs** archivo de clase e implemente `CLKComplicationDataSource`.
2. Configurar la aplicación **Info.plist** para exponer la complicación y la identidad se admiten las familias de complicación.

Estos pasos se describen con más detalle a continuación.

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>Clase CLKComplicationDataSource

La plantilla de C# siguiente incluye los métodos mínimos necesarios para implementar un `CLKComplicationDataSource`.

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

Siga el [escribir una complicación](#writing) instrucciones para agregar código para esta clase.

### <a name="infoplist"></a>Info.plist

La extensión de inspección **Info.plist** archivo debe especificar el nombre de la `CLKComplicationDataSource` y qué familias de complicación que desea admitir:

[ ![](complications-images/complications-config-sml.png "Los tipos de familia de complicación")](complications-images/complications-config.png)

El **clase de origen de datos** entrada lista mostrará los nombres de clase que subclase `CLKComplicationDataSource` subclase que incluye la lógica de complicación.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Toda la funcionalidad de complicación se implementa en una sola clase, reemplazando los métodos de la `CLKComplicationDataSource` clase abstracta (que implementa el `ICLKComplicationDataSource` interfaz).

### <a name="required-methods"></a>Métodos necesarios

Debe implementar los métodos siguientes para la compilación ejecutar:

- `GetPlaceholderTemplate` : Permite devolver la visualización estática utilizada durante la configuración, o bien cuando la aplicación no puede proporcionar un valor.
- `GetCurrentTimelineEntry` : Calcular la presentación correcta cuando se está ejecutando la compilación.
- `GetSupportedTimeTravelDirections` -Devuelve opciones de `CLKComplicationTimeTravelDirections` como `None`, `Forward`, `Backward`, o `Forward | Backward`.

### <a name="privacy"></a>Privacidad

Complicaciones que muestren los datos personales

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` o `HideOnLockScreen`

Si este método devuelve `HideOnLockScreen` la complicación mostrará un icono o el nombre de la aplicación (y no los datos) cuando se bloquea la inspección.

### <a name="updates"></a>Actualizaciones

- `GetNextRequestedUpdateDate` -Devuelven la hora cuando el sistema operativo debe consultar a continuación la aplicación para mostrar datos de complicación actualizada.

También puede forzar una actualización de la aplicación de iOS.

### <a name="supporting-time-travel"></a>Admitir viaje en el tiempo

Compatibilidad con tiempo de viaje es opcional y es controlada por el `GetSupportedTimeTravelDirections` método. Si devuelve `Forward`, `Backward`, o `Forward | Backward` , a continuación, debe implementar los métodos siguientes

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>Escribir una complicación

Intervalo de las complicaciones de datos simple para mostrar a imagen complicada y representación de datos con el soporte técnico de viaje en el tiempo. El código siguiente muestra cómo crear una complicación simple, solo de la plantilla.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Código de ejemplo

En este ejemplo solo admite la `UtilitarianLarge` plantilla, por lo que solo puede seleccionarse en las caras de inspección específicos que admiten este tipo de complicación. Cuando *seleccionar* complicaciones en una inspección muestra **COMPLICACIÓN mi** y cuándo *ejecutando* mostrará el texto **minuto _hora_**   (con una parte del tiempo).

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

Hay una serie de diferentes plantillas disponibles para cada estilo de complicación.
El **anillo** plantillas le permiten mostrar un anillo de progreso estilo alrededor de la complicación, que puede utilizarse para mostrar gráficamente progreso o algún otro valor.

[Documentos de CLKComplicationTemplate de Apple](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Pequeño circular

Estos nombres de clase de plantilla van precedidos con `CLKComplicationTemplateCircularSmall`:

- **RingImage** -mostrar una sola imagen, con un anillo de progreso alrededor de ella.
- **RingText** -mostrar una sola línea de texto, con un anillo de progreso alrededor de ella.
- **SimpleImage** -solo muestran una sola imagen pequeña.
- **SimpleText** -solo muestran un pequeño fragmento de texto.
- **StackImage** -mostrar una imagen y una línea de texto, uno encima de otro
- **StackText** -mostrar dos líneas de texto.

### <a name="modular-small"></a>Pequeño modular

Estos nombres de clase de plantilla van precedidos con `CLKComplicationTemplateModularSmall`:

- **ColumnsText** -mostrar una cuadrícula pequeña de valores de texto (2 filas y 2 columnas).
- **RingImage** -mostrar una sola imagen, con un anillo de progreso alrededor de ella.
- **RingText** -mostrar una sola línea de texto, con un anillo de progreso alrededor de ella.
- **SimpleImage** -solo muestran una sola imagen pequeña.
- **SimpleText** -solo muestran un pequeño fragmento de texto.
- **StackImage** -mostrar una imagen y una línea de texto, uno encima de otro
- **StackText** -mostrar dos líneas de texto.

### <a name="modular-large"></a>Modular grande

Estos nombres de clase de plantilla van precedidos con `CLKComplicationTemplateModularLarge`:

- **Columnas** -mostrar una cuadrícula de 3 filas con 2 columnas, puede incluir una imagen a la izquierda de cada fila.
- **StandardBody** -mostrar una cadena de encabezado negrita, con dos filas de texto sin formato. El encabezado, opcionalmente, puede mostrar una imagen de la izquierda.
- **Tabla** -mostrar una cadena de encabezado negrita, con una cuadrícula de 2 x 2 de texto situado debajo de él. El encabezado, opcionalmente, puede mostrar una imagen de la izquierda.
- **TallBody** -mostrar una cadena de encabezado negrita, con una mayor fuente única línea de texto que aparece debajo.

### <a name="utilitarian-small"></a>Utilitarios pequeño

Estos nombres de clase de plantilla van precedidos con `CLKComplicationTemplateUtilitarianSmall`:

- **Plano** -muestra una imagen y texto en una sola línea (el texto debe ser corto).
- **RingImage** -mostrar una sola imagen, con un anillo de progreso alrededor de ella.
- **RingText** -mostrar una sola línea de texto, con un anillo de progreso alrededor de ella.
- **Cuadrado** -mostrar una imagen de cuadrados (40px o 44px cuadrado para los 38 mm o 42 mm Apple Watch respectivamente).

### <a name="utilitarian-large"></a>Utilitarios grandes

Hay sólo una plantilla para este estilo de complicación: `CLKComplicationTemplateUtilitarianLargeFlat`.
Muestra una imagen y texto, todo en una sola línea.



## <a name="related-links"></a>Vínculos relacionados

- [Documentos de Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [WWDC video](https://developer.apple.com/videos/play/wwdc2015-209/)
