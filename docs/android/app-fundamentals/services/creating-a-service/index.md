---
title: Crear un servicio
ms.topic: article
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/01/2018
ms.openlocfilehash: 3d692ffbffd85f8cc2b51fd72c13527031058daf
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="creating-a-service"></a>Crear un servicio

Servicios de Xamarin.Android deben obedecer dos reglas inviolables de servicios Android:

* Debe extender el [ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/).
* Debe señalar con el [ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/).

Otro requisito de Android servicios es que deben estar registrados en el **AndroidManifest.xml** y proporcionar un nombre único. Xamarin.Android registrará automáticamente el servicio en el manifiesto en tiempo de compilación con el atributo XML necesario.

Este fragmento de código se muestra el ejemplo más simple de crear un servicio en Xamarin.Android que cumpla estos dos requisitos:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

En tiempo de compilación Xamarin.Android registrará el servicio insertando el elemento XML siguiente en **AndroidManifest.xml** (Observe que Xamarin.Android genera un nombre aleatorio para el servicio):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

Es posible compartir un servicio con otras aplicaciones Android por _exportar_ lo. Esto se consigue estableciendo la `Exported` propiedad en el `ServiceAttribute`. Al exportar un servicio, el `ServiceAttribute.Name` propiedad debe establecerse para proporcionar un nombre descriptivo público para el servicio. Este fragmento de código muestra cómo exportar y el nombre de un servicio:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

El **AndroidManifest.xml** (elemento) para este servicio, a continuación, será similar:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

Los servicios tienen su propio ciclo de vida con métodos de devolución de llamada que se invocan cuando se crea el servicio. Exactamente qué métodos se invocan depende del tipo de servicio. Un servicio iniciado debe implementar los métodos del ciclo de vida diferente que un servicio dependiente, mientras que un servicio híbrido debe implementar los métodos de devolución de llamada para un servicio iniciado y un servicio dependiente. Estos métodos son todos los miembros de la `Service` clase; la forma en que se inicia el servicio determinará qué métodos del ciclo de vida se invocará. Estos métodos del ciclo de vida se tratarán con más detalle más adelante.

De forma predeterminada, se iniciará un servicio en el mismo proceso que una aplicación Android. Es posible iniciar un servicio en su propio proceso estableciendo el `ServiceAttribute.IsolatedProcess` propiedad en true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

El paso siguiente es examinar cómo iniciar un servicio y, a continuación, pasar a examinar cómo implementar los tres tipos diferentes de servicios.

> [!NOTE]
> Un servicio se ejecuta en el subproceso de interfaz de usuario, por lo que si cualquier trabajo es como realizadas que bloquea la interfaz de usuario, el servicio debe utilizar subprocesos para realizar el trabajo.

## <a name="starting-a-service"></a>A partir de un servicio

La manera más sencilla de iniciar un servicio en Android consiste en enviar un `Intent` que contiene metadatos para ayudar a identificar qué servicio se debe iniciar. Hay dos estilos diferentes de intentos que pueden usarse para iniciar un servicio:

-   **Intención explícita** &ndash; una _intención explícita_ identificará exactamente qué servicio debería usarse para completar una acción determinada. Un intento explícito puede considerarse como una letra que tiene una dirección específica; Android enrutará el propósito para el servicio que está identificado explícitamente. Este fragmento de código es un ejemplo del uso de un intento explícito para iniciar un servicio denominado `DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **Intención implícita** &ndash; flexible identifica este tipo de la intención de la de acción que debe realizarse, pero el servicio exacto que se complete dicha acción es desconocido. Un intento implícita puede considerarse como una letra que está había dirigido a "To Whom TI mayo preocupación...".
    Android examinará el contenido de la intención y determinar si hay un servicio existente que coincida con la intención.

    Un _filtro intención_ se usa para coincidir con la intención implícita con un servicio registrado. Un filtro intención es un elemento XML que se agrega a **AndroidManifest.xml** que contiene los metadatos necesarios para ayudar a coincidir con un servicio con un intento implícita.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Si el sistema Android tiene más de una coincidencia posible para un intento implícita, puede solicitar al usuario seleccionar el componente para manejar la acción:

![Captura de pantalla de un cuadro de diálogo de desambiguación](images/creating-a-service-01.png "captura de pantalla de un cuadro de diálogo de desambiguación")

> [!IMPORTANT]
> A partir de Android 5.0 (nivel de Asia Pacífico 21) un intento implícita no puede utilizarse para iniciar un servicio.

Siempre que sea posible, las aplicaciones deben usar intentos explícitas para iniciar un servicio. Un intento implícita no pide un servicio específico iniciar &ndash; es una solicitud para algún servicio instalado en el dispositivo para atender la solicitud. Puede dar lugar a esta solicitud ambigua en el servicio equivocado de control de la solicitud u otra aplicación a partir de innecesariamente (lo que aumenta la presión de recursos en el dispositivo).

¿Cómo se envía la intención depende del tipo de servicio y se describen con más detalle posteriormente en las guías específicas para cada tipo de servicio.


### <a name="creating-an-intent-filter-for-implicit-intents"></a>Crear un filtro de intención de calidades implícita

Para asociar un servicio con un intento implícita, una aplicación Android debe proporcionar algunos metadatos para identificar las capacidades del servicio. Estos metadatos se proporcionan por _filtros intención_. Intención de filtros contienen cierta información, como una acción o un tipo de datos, que deben encontrarse en un intento de iniciar un servicio. En Xamarin.Android, se registra el filtro intención en **AndroidManifest.xml** decorando un servicio con el [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). Por ejemplo, el código siguiente agrega un filtro con una acción asociada de intención `com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

Esto da como resultado una entrada que se incluye en el **AndroidManifest.xml** archivo &ndash; una entrada que se incluye con la aplicación de forma similar al ejemplo siguiente:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Con los conceptos básicos de un servicio de Xamarin.Android fuera de la vista, vamos a examinar la los diferentes subtipos de servicios con más detalle.


## <a name="related-links"></a>Vínculos relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
