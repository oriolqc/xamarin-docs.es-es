---
title: Progreso y los indicadores de actividad
ms.topic: article
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: b91c0d7504b391630beded7a52e240a0d043152f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="progress-and-activity-indicators"></a>Progreso y los indicadores de actividad

Es probable que tenga la aplicación para llevar a cabo larga ejecución de tareas como la carga o procesamiento de datos y que este retraso puede provocar un retraso en la actualización de la interfaz de usuario. Durante este período, debe utilizar siempre un indicador de progreso para asegurar que el usuario que el sistema está realizando trabajo. Esto proporciona al control de usuario que la aplicación funcione en su solicitud, que no está esperando su entrada, y puede proporcionar un medio para que detalla exactamente cuánto tiempo tiene que esperar.

iOS proporciona dos mecanismos principales para proporcionar esta indicación del progreso de la aplicación: indicadores de actividad (incluido un determinado _red_ indicador de actividad) y barras de progreso.

## <a name="activity-indicator"></a>Indicador de actividad

Indicadores de actividad se deben mostrar cuando la aplicación ejecuta un proceso largo, pero no conoce la longitud exacta de tiempo que requerirá la tarea.

Apple tiene las siguientes sugerencias para trabajar con indicadores de actividad:

- **Siempre que sea posible, utilice progreso barras en su lugar** : porque un indicador de actividad no proporciona al usuario comentarios sobre el tiempo que se tarda el proceso que se va a ejecutar, utilice siempre una barra de progreso si se conoce la longitud (por ejemplo, el número de bytes para descargar en un archivo).
- **Mantener el indicador animado** -los usuarios para relacionar un indicador de actividad estacionarios a una aplicación detenida por lo que siempre debe tener el indicador animado mientras se está mostrando.
- **Describir la tarea que se está procesando** -mostrar simplemente el indicador de actividad por sí solo no es suficiente, el usuario debe ser informado sobre el proceso que están esperando. Incluir una etiqueta significativa (normalmente, una frase única y completa) que define claramente la tarea.

### <a name="implementing-an-activity-indicator"></a>Implementar un indicador de actividad

Un indicador de actividad se implementa a través de la [ `UIActivityIndictorView` ](https://developer.xamarin.com/api/type/UIKit.UIActivityIndicatorView/) clase para indicar que un `UIActivity` se lleva a cabo.

### <a name="activity-indicators-and-storyboards"></a>Guiones gráficos e indicadores de actividad

Si se utiliza el Diseñador de iOS para crear la interfaz de usuario, el indicador de actividad pueden agregarse para el diseño del cuadro de herramientas. Desde el panel de propiedades, se pueden ajustar las propiedades siguientes:

![Panel de propiedades](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>Administrar el comportamiento del indicador de actividad

Use la `StartAnimating()` y `StopAnimating()` métodos para iniciar y detener la animación de indicador de actividad.

Establecer el `HidesWhenStopped` propiedad `true` para hacer que el indicador de actividad desaparezcan tras `StopAnimating()` se ha llamado. Esto se establece en `true` de forma predeterminada. En cualquier momento puede ver si el indicador de actividad se ejecuta la animación de giro activando el `IsAnimating` propiedad. 


### <a name="managing-activity-indicator-appearances"></a>Administración de repeticiones del indicador de actividad

El `UIActivityIndicatorViewStyle` enumeración puede pasarse como un parámetro al crear una instancia el indicador de actividad. Ya puede utilizarla para establecer el estilo visual `Gray`, `White`, o `WhiteLarge`, por ejemplo:

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

Puede invalidar el color proporcionado por `UIActivityIndicatorViewStyle` estableciendo el `Color` propiedad.

## <a name="progress-bar"></a>ProgressBar

Una barra de progreso se presenta como una línea que se rellena con color para indicar el estado y la longitud de una tarea que consumen muchos recursos. Barras de progreso siempre debe utilizarse cuando la longitud de las tareas se conoce o se puede calcular.

Apple tiene las siguientes sugerencias para trabajar con barras de progreso:

- **Notificar el progreso con precisión** -barras de progreso siempre debe ser una representación precisa del tiempo necesario para completar una tarea. No falsificar nunca el tiempo para que la aplicación aparezca ocupado.
- **Uso de las duraciones de Well-Defined** -barra de progreso no sólo debe mostrar que tiene una tarea larga coloca, pero proporcionan al usuario y la indicación de la cantidad de la tarea se completa y una estimación del tiempo restante.

### <a name="implementing-an-progress-bar"></a>Implementar una barra de progreso

Se crea una barra de progreso de la creación de instancias de un [`UIProgressView`](https://developer.xamarin.com/api/type/UIKit.UIProgressView/)

### <a name="progress-bars-and-storyboards"></a>Barras de progreso y los guiones gráficos

También puede agregar una barra de progreso a la interfaz de usuario cuando se usa el Diseñador de iOS. Busque **curso** en el **cuadro de herramientas** y arrástrelo a la vista.

En el panel de propiedades, se pueden ajustar las propiedades siguientes:

![Panel de propiedades](progress-activity-indicator-images/progress-indicator3.png)


### <a name="managing-progress-bar-behavior"></a>Administrar el comportamiento de la barra de progreso

El progreso de la barra se puede establecer inicialmente mediante el `Progress` propiedad:

```csharp
ProgressBar.Progress = 0f;
```

El progreso se puede ajustar mediante el `SetProgress` método y pasar un valor booleano declarar si desea que los cambios que se anima o no.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

Para obtener más información sobre el uso de la barra de progreso, consulte el [Reporting Progress](https://developer.xamarin.com/recipes/cross-platform/networking/download_progress/#Reporting_Progress_in_iOS) receta y el [UICatalog tvOS ejemplo](https://developer.xamarin.com/samples/monotouch/tvos/UICatalog/).

### <a name="managing-progress-bar-appearance"></a>Administrar el aspecto de la barra de progreso

Similar a un indicador de actividad, el `UIProgressViewStyle` enumeración puede pasarse como un parámetro al crear una instancia de la barra de progreso.

El progreso y la imagen de seguimiento y el Color del matiz se pueden ajustar mediante las siguientes propiedades:

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```



