---
title: Progreso y los indicadores de actividad de Xamarin.iOS
description: Este documento describe cómo usar los indicadores de progreso y actividad de Xamarin.iOS. Describe cómo se usan tanto mediante programación con un guión gráfico.
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: cb56af300444020a543c16afb0dfb48015fc2153
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102599"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Progreso y los indicadores de actividad de Xamarin.iOS

Es probable que la aplicación tendrá que llevar a cabo de larga ejecución de tareas como la carga o procesamiento de datos y que este retraso puede provocar un retraso en la actualización de la interfaz de usuario. Durante este tiempo siempre se debe utilizar un indicador de progreso para asegurar que el usuario que el sistema está ocupado realizando trabajo. Esto proporciona el control de usuario que la aplicación está trabajando en su solicitud, que no está esperando su aprobación, y puede proporcionar un medio para que detalla exactamente cuánto tiene que esperar.

iOS ofrece dos maneras principales para proporcionar esta indicación del progreso de la aplicación: indicadores de actividad (incluida una determinada _red_ indicador de actividad) y barras de progreso.

## <a name="activity-indicator"></a>Indicador de actividad

Indicadores de actividad se deben mostrar cuando la aplicación se está ejecutando un proceso largo, pero no conoce la longitud exacta de tiempo que requerirá la tarea.

Apple tiene las siguientes sugerencias para trabajar con indicadores de actividad:

- **Siempre que sea posible, utilice las barras de progreso en cambio** : porque un indicador de actividad no proporciona al usuario comentarios sobre el tiempo se tardará el proceso en ejecución, utilice siempre una barra de progreso si la longitud se conoce (por ejemplo, el número de bytes para descargar en un archivo).
- **Mantener el indicador animado** -los usuarios para relacionar un indicador de actividad estacionarios a una aplicación detenida por lo que siempre debe tener el indicador animado mientras se está mostrando.
- **Describir la tarea que se está procesando** -mostrar simplemente el indicador de actividad por sí solo no es suficiente, el usuario necesita para mantenerse informado sobre el proceso que están esperando. Incluir una etiqueta significativa (normalmente una frase única y completa) que defina claramente la tarea.

### <a name="implementing-an-activity-indicator"></a>Implementación de un indicador de actividad

Un indicador de actividad se implementa a través de la [ `UIActivityIndictorView` ](https://developer.xamarin.com/api/type/UIKit.UIActivityIndicatorView/) clase para indicar que un `UIActivity` se lleva a cabo.

### <a name="activity-indicators-and-storyboards"></a>Guiones gráficos e indicadores de actividad

Si usa el Diseñador de iOS para crear la interfaz de usuario, el indicador de actividad pueden agregarse para el diseño del cuadro de herramientas. Las siguientes propiedades se pueden ajustar desde el panel de propiedades:

![Panel de propiedades](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>Administrar el comportamiento del indicador de actividad

Use la `StartAnimating()` y `StopAnimating()` métodos para iniciar y detener la animación del indicador de actividad.

Establecer el `HidesWhenStopped` propiedad `true` para realizar el indicador de actividad que desaparezcan tras `StopAnimating()` se ha llamado. Se establece en `true` de forma predeterminada. En cualquier momento puede ver si el indicador de actividad se ejecuta la animación de giro, compruebe el `IsAnimating` propiedad. 


### <a name="managing-activity-indicator-appearances"></a>Administrar los aspectos visuales del indicador de actividad

El `UIActivityIndicatorViewStyle` enumeración puede pasarse como parámetro al crear una instancia del indicador de actividad. Se puede usar para establecer el estilo visual `Gray`, `White`, o `WhiteLarge`, por ejemplo:

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

Puede invalidar el color proporcionado por `UIActivityIndicatorViewStyle` estableciendo el `Color` propiedad.

## <a name="progress-bar"></a>ProgressBar

Una barra de progreso se presenta como una línea que se rellena con color para indicar el estado y la longitud de una tarea que requieren mucho tiempo. Barras de progreso siempre se debe usar cuando la longitud de las tareas se conoce o se puede calcular.

Apple tiene las siguientes sugerencias para trabajar con las barras de progreso:

- **Notificar el progreso con precisión** -barras de progreso siempre debe ser una representación precisa del tiempo necesario para completar una tarea. No falsificar nunca el tiempo para que la aplicación aparezca ocupado.
- **Uso de las duraciones de Well-Defined** -barra de progreso no sólo debe mostrar que está teniendo una tarea larga colocar, pero proporcionan al usuario y la indicación de qué parte de la tarea se completa y una estimación del tiempo restante.

### <a name="implementing-an-progress-bar"></a>Implementación de una barra de progreso

Se crea una barra de progreso de la creación de instancias de un [`UIProgressView`](https://developer.xamarin.com/api/type/UIKit.UIProgressView/)

### <a name="progress-bars-and-storyboards"></a>Barras de progreso y los guiones gráficos

También puede agregar una barra de progreso a la interfaz de usuario cuando se usa el Diseñador de iOS. Busque **curso** en el **cuadro de herramientas** y arrástrelo a la vista.

En el panel de propiedades, se pueden ajustar las propiedades siguientes:

![Panel de propiedades](progress-activity-indicator-images/progress-indicator3.png)


### <a name="managing-progress-bar-behavior"></a>Administrar el comportamiento de la barra de progreso

Se puede establecer inicialmente el progreso de la barra utilizando el `Progress` propiedad:

```csharp
ProgressBar.Progress = 0f;
```

Se puede ajustar el progreso mediante el `SetProgress` método y pasando un valor booleano que declara si desea que los cambios que se anima o no.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

Para obtener más información sobre el uso de la barra de progreso, consulte el [informes de progreso](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress) receta y el [UICatalog tvOS ejemplo](https://developer.xamarin.com/samples/monotouch/tvos/UICatalog/).

### <a name="managing-progress-bar-appearance"></a>Administrar el aspecto de la barra de progreso

Similar a un indicador de actividad, el `UIProgressViewStyle` enumeración puede pasarse como parámetro al crear una instancia de la barra de progreso.

El progreso y la imagen de seguimiento y el Color de tono se pueden ajustar mediante el uso de las siguientes propiedades:

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```



