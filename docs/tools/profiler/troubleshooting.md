---
title: Solución de problemas de Profiler de Xamarin
description: Este documento proporciona información para solucionar problemas relacionados con el Profiler de Xamarin. Describe problemas relacionados con el registro y diagnóstico, el IDE y otros temas.
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: lobrien
ms.author: laobri
ms.date: 10/27/2017
ms.openlocfilehash: e4a4376291ff56433c8cd9785989af2983a80c1c
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832147"
---
# <a name="xamarin-profiler-troubleshooting"></a>Solución de problemas de Profiler de Xamarin

## <a name="logging-and-diagnostics"></a>Registro y diagnóstico

El equipo de Xamarin puede ayudar a realizar un seguimiento de problemas si se nos proporcione información, incluidos:

- Una demostración del problema, bloqueo, o error y el flujo de trabajo que hicieron nacer a él.
- Salidas de registro (ver abajo).
- El **.mlpd** generadas para la sesión de generación de perfiles (ver abajo).

### <a name="getting-log-outputs"></a>Introducción a las salidas de registro

En Mac, los registros se guardan en `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`.

En Windows se guardan en `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` , incluya el registro más reciente cada vez que envíe un problema.

Estamos agregando más de registro a medida que avancemos, por lo que esta salida debe crecer y ser más útil con el tiempo.

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>Generar archivos .mlpd

Un **.mlpd** archivo es el resultado del generador de perfiles de ejecución mono comprimido. La interfaz gráfica de usuario de Xamarin Profiler lee los datos de un **.mlpd** y se muestra para el usuario. **.mlpd** archivos son útiles herramientas de depuración para Xamarin ya que ayudan a nuestros ingenieros de diagnosticar los problemas que pueda tener la Profiler con los datos.

El **.mlpd** de la sesión actual se guarda automáticamente en su Mac `/tmp` directorio y pueden identificarse mediante la marca de tiempo. Si activa el registro, la primera salida será la ruta de acceso a la **.mlpd** archivo. El **.mlpd** normalmente se guardará el archivo en el directorio a partir de ~/var/carpetas...

El **.mlpd** para una sesión actual también se puede guardar eligiendo **archivo > Guardar como...** desde el menú de Profiler:

**Visual Studio para Mac**:

![](troubleshooting-images/image17.png "Guardando archivo .mlpd en Visual Studio para Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "Guardando archivo .mlpd en Visual Studio")

Es importante tener en cuenta que **.mlpd** contienen una gran cantidad de información y el tamaño del archivo será grande.

## <a name="troubleshooting"></a>solución de problemas

La siguiente lista muestra las trampas comunes, las soluciones alternativas y sugerencias y trucos para usar al Profiler.

> [!NOTE]
> Debe ser un Visual Studio **Enterprise** suscriptor para desbloquear esta característica en cualquier proyecto de Visual Studio Enterprise en Windows o Visual Studio para Mac.

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>No puedo ver la opción de generador de perfiles de iOS o se atenúa [Visual Studio y Visual Studio para Mac]

Compruebe la configuración siguiente para resolver este problema:

- Asegúrese de que está usando la configuración de depuración
- Asegúrese de que está usando el recolector de elementos no utilizados de SGen.
- Asegúrese de que la plataforma es [admite](~/tools/profiler/index.md#Profiler_Support).
- Asegúrese de que tiene la licencia correcta.
- Asegúrese de que ha iniciado en y correctamente autenticado.
- [Visual Studio] Debe usar [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/) y tiene una licencia válida de la empresa.

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>Obtengo un error al intentar iniciar el generador de perfiles

Si experimenta este cuadro de error cuando se usa el generador de perfiles en Visual Studio:

![](troubleshooting-images/error.png "Cuadro de error cuando se usa el generador de perfiles en Visual Studio")

Suele ser debido a que no se pueda iniciar en el simulador o emulador. Pruebe y ejecute la aplicación con normalidad, corrija los problemas que proporciona y, a continuación, intente volver a usar al Profiler.

#### <a name="to-watch-a-specific-thread"></a>Para ver un subproceso concreto

Si tiene un subproceso que desea ver en concreto, lo ideal sería que el subproceso al principio de su creación para obtener el nombre `ThreadName` en lugar de `0x0`. Por ejemplo, para establecer el nombre del subproceso como `UI`, podría usar el código siguiente:

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>Vínculos relacionados

- [Tutorial: usar al Profiler de Xamarin](~/tools/profiler/index.md)
- [Prácticas recomendadas de memoria y rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Notas de la versión](https://developer.xamarin.com/releases/profiler/preview/)
