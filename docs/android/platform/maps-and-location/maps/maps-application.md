---
title: Aplicación de mapas
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/05/2017
ms.openlocfilehash: b94c65c079b28fe042a42ec04357c11f3516d205
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="maps-application"></a>Aplicación de mapas

La manera más sencilla de trabajar con los mapas de Xamarin.Android es aprovechar la aplicación de mapas integrados que se muestra a continuación:

[![Captura de pantalla de ejemplo de aplicación de Google Maps integrada](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Cuando se utiliza la aplicación de mapas, el mapa no formará parte de la aplicación. En su lugar, la aplicación se inicie la aplicación de mapas y cargar el mapa externamente. La siguiente sección examina cómo usar Xamarin.Android para iniciar mapas como se muestra arriba.


## <a name="creating-the-intent"></a>Crear la intención

Trabajar con mapas de la aplicación es tan fácil como crear un intento con un URI adecuado, establecer la acción en ActionView y llamar al método StartActivity. Por ejemplo, el código siguiente inicia la aplicación de mapas centrada en una latitud y longitud determinada:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Este código es todo lo que necesita para iniciar la asignación que se muestra en la captura de pantalla anterior. Además de especificar la latitud y longitud, el esquema URI para mapas admite varias otras opciones.


## <a name="geo-uri-scheme"></a>Esquema de URI de replicación geográfica

El código anterior utiliza el esquema de replicación geográfica para crear un URI. Este esquema de URI admite varios formatos, como se muestra a continuación:

-   `geo:latitude,longitude` &ndash; Abre la aplicación de mapas centrada en una lat/lon. 

-   `geo:latitude,longitude?z=zoom` &ndash; Abre las asignaciones de aplicación centrada en una lat/lon y ampliado en el nivel especificado. El nivel de zoom puede oscilar entre 1 y 23: 1 muestra la tierra toda y 23 es el nivel de zoom más cercano.

-   `geo:0,0?q=my+street+address` &ndash; Abre la aplicación se asigna a la ubicación de una dirección postal. 

-   `geo:0,0?q=business+near+city` &ndash; Se abre la aplicación de mapas y muestra los resultados de búsqueda anotado. 


Las versiones del URI que llevar a cabo una consulta (es decir, los términos calles dirección o búsqueda) usan servicio de geocoder de Google para recuperar la ubicación que se muestra a continuación, en el mapa. Por ejemplo, el URI `geo:0,0?q=coop+Cambridge` da como resultado la asignación se muestra a continuación:

[![Captura de pantalla de ejemplo muestra Google Maps con un término de búsqueda](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



Para obtener más información acerca de los esquemas de URI de replicación geográfica, consulte [mostrar una ubicación en un mapa](http://developer.android.com/guide/components/intents-common.html#Maps).


## <a name="street-view"></a>Vista postal

Además de la combinación de replicación geográfica, Android también admite la carga vistas postales de un intento. A continuación se muestra un ejemplo de la aplicación de vista postal iniciada desde Xamarin.Android:

[![Captura de pantalla de ejemplo de una vista postal](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Para iniciar una vista calle, use simplemente el `google.streetview` esquema del URI, como se muestra en el código siguiente:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

El esquema URI google.streetview utilizado anteriormente toma la forma siguiente:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Como puede ver, hay varios parámetros compatibles, como se muestra a continuación:

-   `lat` &ndash; La latitud de la ubicación que se mostrará en la vista de la calle.

-   `lng` &ndash; La longitud de la ubicación que se mostrará en la vista de la calle.

-   `pitch` &ndash; Ángulo de panorámica de vista calle, medido desde el centro en grados que 90 grados es recta hacia abajo y -90 grados es hacia arriba.

-   `yaw` &ndash; Centro de la vista de panorámica de vista calle, hacia la derecha mide en grados del Norte.

-   `zoom` &ndash; El multiplicador para la fotografía panorámica postal vista de zoom, donde 1.0 = zoom normal, 2.0 = 2 ampliada x 3.0 = 4 ampliada x etcetera.

-   `mz` &ndash; El nivel de zoom del mapa que se usará al pasar a la aplicación de asignaciones de la vista de la calle.


Trabajar con integrado de la aplicación asigna o la vista postal es una manera fácil de agregar rápidamente compatibilidad de asignación. Sin embargo, la API de mapas de Android ofrece un control más preciso sobre la experiencia de asignación.
