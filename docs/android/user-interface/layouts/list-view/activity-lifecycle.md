---
title: ListView y el ciclo de vida de actividad
ms.topic: article
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: b8ee113a321dbc84cf12a7ef4bb5084c5307115b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="listview-and-the-activity-lifecycle"></a>ListView y el ciclo de vida de actividad

Actividades pasan a través de determinados Estados como la aplicación se ejecuta, como el inicio, ejecución, está en pausa y está deteniendo. Para obtener más información e instrucciones específicas sobre cómo controlar las transiciones de estado, consulte la [Tutorial del ciclo de vida de actividad](~/android/app-fundamentals/activity-lifecycle/index.md).
Es importante comprender el ciclo de vida de actividad y el lugar su `ListView` código en las ubicaciones correctas.

Todos los ejemplos de este documento realizan 'las tareas de instalación' en la actividad `OnCreate` (método) y (cuando sea necesario) realizar 'destrucción' en `OnDestroy`. Por lo general, los ejemplos utilizan pequeños conjuntos de datos que no cambian, por lo que no es necesario volver a cargar los datos con más frecuencia.

Sin embargo, si los datos se cambian con frecuencia o usa una gran cantidad de memoria podría ser adecuado utilizar métodos de ciclo de vida diferente para rellenar y actualizar su `ListView`. Por ejemplo, si los datos subyacentes cambian constantemente (o pueden verse afectados por las actualizaciones de las demás actividades), a continuación, crear el adaptador en `OnStart` o `OnResume` garantizará que los datos más recientes se muestran cada vez que se muestra la actividad.

Si el adaptador utiliza los recursos como memoria o un cursor administrado, no olvide liberen estos recursos en el método complementario para que se iniciaron (p. ej. objetos creados en `OnStart` puede eliminarse en `OnStop`).


## <a name="configuration-changes"></a>Cambios de configuración

Es importante recordar que la configuración cambia &ndash; especialmente pantalla visibilidad rotación y teclado &ndash; puede hacer que la actividad actual se destruye y se vuelve a crear (a menos que especifique lo contrario con la `ConfigurationChanges` atributo). Esto significa que en condiciones normales, rotación de un dispositivo pueda causar un `ListView` y `Adapter` volver a crearlo y (a menos que se ha escrito el código de `OnPause` y `OnResume`) Estados de la selección de fila y de la posición de desplazamiento se perderán.

El siguiente atributo impediría que una actividad se destruyen y se vuelven a crear como resultado de cambios de configuración:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

La actividad debe reemplazar, a continuación, `OnConfigurationChanged` para responder a los cambios según corresponda. Para obtener más información sobre cómo controlar los cambios de configuración, consulte la documentación.

