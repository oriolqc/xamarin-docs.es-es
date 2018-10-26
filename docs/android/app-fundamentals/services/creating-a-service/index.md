---
title: Crear un servicio
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: 24d86827ab93dcf7dfc4da39c4a03a0a2805f332
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107435"
---
# <a name="creating-a-service"></a>Crear un servicio

Servicios de Xamarin.Android deben obedecer dos reglas inviolables de servicios de Android:

* Debe extender el [ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/).
* Se deben señalar con el [ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/).

Otro requisito de servicios de Android es que debe estar registrados en el **AndroidManifest.xml** y proporcionar un nombre único. Xamarin.Android se registrarán automáticamente el servicio en el manifiesto en tiempo de compilación con el atributo XML necesario.

Este fragmento de código es el ejemplo más sencillo de creación de un servicio en Xamarin.Android que cumpla estos requisitos:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

En tiempo de compilación, Xamarin.Android se registrará el servicio mediante la inserción en el siguiente elemento XML en **AndroidManifest.xml** (tenga en cuenta que Xamarin.Android genera un nombre aleatorio para el servicio):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

Es posible compartir un servicio con otras aplicaciones de Android por _exportar_ lo. Esto se consigue estableciendo la `Exported` propiedad en el `ServiceAttribute`. Al exportar un servicio, el `ServiceAttribute.Name` también se debe establecer la propiedad para proporcionar un nombre descriptivo público para el servicio. Este fragmento de código muestra cómo exportar y el nombre de un servicio:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

El **AndroidManifest.xml** (elemento) para este servicio, a continuación, será algo como:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

Los servicios tienen su propio ciclo de vida con métodos de devolución de llamada que se invoca como el servicio se crea. Exactamente qué métodos se invocan depende del tipo de servicio. Un servicio iniciado debe implementar los métodos de ciclo de vida diferente de un servicio dependiente, mientras que un servicio híbrido debe implementar los métodos de devolución de llamada para un servicio iniciado y un servicio dependiente. Estos métodos son todos los miembros de la `Service` clase; cómo se inicia el servicio determinará qué métodos del ciclo de vida se invocará. Estos métodos de ciclo de vida se tratará con más detalle más adelante.

De forma predeterminada, se iniciará un servicio en el mismo proceso que una aplicación de Android. Es posible iniciar un servicio en su propio proceso estableciendo el `ServiceAttribute.IsolatedProcess` propiedad en true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

El paso siguiente es examinar cómo iniciar un servicio y, a continuación, pasar a examinar cómo implementar los tres tipos diferentes de servicios.

> [!NOTE]
> Un servicio se ejecuta en el subproceso de interfaz de usuario, por lo que si cualquier trabajo es que realiza lo que bloquea la interfaz de usuario, el servicio debe utilizar subprocesos para realizar el trabajo.

## <a name="starting-a-service"></a>A partir de un servicio

Es la manera más sencilla de iniciar un servicio de Android enviar un `Intent` que contiene metadatos para ayudar a identificar qué servicio se debe iniciar. Hay dos estilos diferentes de intenciones que pueden usarse para iniciar un servicio:

-   **Intención explícita** &ndash; una _intención explícita_ identificará exactamente qué servicio se debe usar para completar una acción determinada. Una intención explícita puede considerarse como una carta que tiene una dirección específica; Android enrutará la intención al servicio que se identifica explícitamente. Este fragmento de código es un ejemplo del uso de un objeto Intent explícito para iniciar un servicio denominado `DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **Intent implícita** &ndash; imprecisa identifica este tipo de la intención de la de la acción que el usuario desea realizar, pero el servicio para completar esa acción exacto se desconoce. Un objeto Intent implícito puede considerarse como una letra que está había dirigido a "To Whom TI mayo preocupación...".
    Android se examine el contenido de la intención y determinar si hay un servicio existente que coincide con la intención.

    Un _filtro de intento_ se emplearán para coincidir con la intención implícita con un servicio registrado. Un filtro de intento es un elemento XML que se agrega a **AndroidManifest.xml** que contiene los metadatos necesarios para que coincida con un servicio con una intención implícita.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Si Android tiene más de una coincidencia posible para un objeto intent implícita, puede solicitar al usuario seleccionar el componente para controlar la acción:

![Captura de pantalla de un cuadro de diálogo de desambiguación](images/creating-a-service-01.png "captura de pantalla de un cuadro de diálogo de desambiguación")

> [!IMPORTANT]
> A partir de Android 5.0 (nivel de Asia Pacífico 21) un objeto intent implícito no se puede usar para iniciar un servicio.

Siempre que sea posible, las aplicaciones deben usar las intenciones explícitas para iniciar un servicio. Un objeto Intent implícito no pide un servicio específico iniciar &ndash; es una solicitud para algún servicio instalado en el dispositivo para atender la solicitud. Esta solicitud ambigua puede dar lugar equivocado servicio que administra la solicitud o innecesariamente iniciar otra aplicación (lo que aumenta la presión de los recursos del dispositivo).

Cómo se distribuye la intención depende del tipo de servicio y se describe con más detalle posteriormente en las guías específicas para cada tipo de servicio.


### <a name="creating-an-intent-filter-for-implicit-intents"></a>Crear un filtro de intento de intenciones implícitas

Para asociar un servicio con una intención implícita, una aplicación Android debe proporcionar algunos metadatos para identificar las capacidades del servicio. Estos metadatos se proporcionan por _filtros intención_. Intención de filtros contienen alguna información, como una acción o un tipo de datos, que deben estar presentes en un intento de iniciar un servicio. En Xamarin.Android, el filtro de intento se registra en **AndroidManifest.xml** mediante la decoración de un servicio con el [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). Por ejemplo, el código siguiente agrega un filtro con una acción asociada de intención `com.xamarin.DemoService`:

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

Con los aspectos básicos de un servicio fuera de la vista de Xamarin.Android, vamos a examinar los diferentes subtipos de los servicios con más detalle.


## <a name="related-links"></a>Vínculos relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
