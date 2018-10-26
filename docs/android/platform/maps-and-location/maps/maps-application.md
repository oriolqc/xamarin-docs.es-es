---
title: Iniciar la aplicación de mapas
description: Cómo iniciar la aplicación de mapas integrada desde dentro de la aplicación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: cd80154602cc22668768fe217da7371b77ded003
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112394"
---
# <a name="launching-the-maps-application"></a>Iniciar la aplicación de mapas

La manera más sencilla de trabajar con los mapas de Xamarin.Android es aprovechar la aplicación mapas integrados que se muestra a continuación:

[![Captura de pantalla de ejemplo de aplicación de Google Maps integrada](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Cuando se usa la aplicación de mapas, el mapa no formará parte de la aplicación. En su lugar, la aplicación se inicie la aplicación de mapas y cargar el mapa externamente. La siguiente sección examina cómo usar Xamarin.Android para iniciar mapas como el anterior.


## <a name="creating-the-intent"></a>Creación de la intención

Trabajar con los mapas de aplicación es tan fácil como crear una intención con un URI adecuado, estableciendo la acción a ActionView y llamar al método StartActivity. Por ejemplo, el código siguiente inicia la aplicación de mapas centrada en una latitud y longitud dada:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Este código es todo lo que es necesario para iniciar la asignación que se muestra en la captura de pantalla anterior. Además de especificar la latitud y longitud, el esquema URI para mapas admite muchas otras opciones.


## <a name="geo-uri-scheme"></a>Esquema de URI geográfica

El código anterior usa el esquema geográfica para crear un URI. Este esquema de URI admite varios formatos, como se muestra a continuación:

-   `geo:latitude,longitude` &ndash; Abre la aplicación de mapas centrada en lon lat /. 

-   `geo:latitude,longitude?z=zoom` &ndash; Abre los mapas de aplicación centrada en lon lat/y ampliado en el nivel especificado. El nivel de zoom puede oscilar entre 1 y 23: 1 muestra la tierra toda y 23 es el nivel de zoom más cercano.

-   `geo:0,0?q=my+street+address` &ndash; Abre la aplicación se asigna a la ubicación de una dirección postal. 

-   `geo:0,0?q=business+near+city` &ndash; Abre la aplicación de mapas y muestra los resultados de búsqueda anotado. 


Las versiones del URI que toman una consulta (es decir, los términos calles dirección o búsqueda) usan servicio de geocoder de Google para recuperar la ubicación que se muestra a continuación, en el mapa. Por ejemplo, el URI `geo:0,0?q=coop+Cambridge` da como resultado la asignación se muestra a continuación:

[![Captura de pantalla de ejemplo con Google Maps con un término de búsqueda](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



Para obtener más información acerca de los esquemas de URI geográfica, consulte [mostrar una ubicación en un mapa](http://developer.android.com/guide/components/intents-common.html#Maps).


## <a name="street-view"></a>Vista de calle

Además de la combinación de geo Android también admite la carga de las vistas de calles desde un intento. A continuación se muestra un ejemplo de la aplicación de vista calle iniciada desde Xamarin.Android:

[![Captura de pantalla de ejemplo de una vista de calle](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Para iniciar una vista de la calle, basta con utilizar el `google.streetview` esquema del URI, como se muestra en el código siguiente:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

El esquema URI google.streetview usado anteriormente toma la forma siguiente:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Como puede ver, hay varios parámetros que admite, como se muestra a continuación:

-   `lat` &ndash; La latitud de la ubicación que se mostrará en la vista de calle.

-   `lng` &ndash; La longitud de la ubicación que se mostrará en la vista de calle.

-   `pitch` &ndash; Es el ángulo de panorámica de la vista de calle, medido desde el centro en grados que 90 grados es directamente hacia abajo y -90 grados hacia arriba.

-   `yaw` &ndash; Centro de vista de panorámica de la vista de calle, medido hacia la derecha en grados del Norte.

-   `zoom` &ndash; Multiplicador de panorámica de la calle de vista de zoom, donde 1.0 = zoom normal, 2.0 = 2 ampliada x 3.0 = ampliada de 4 x, etcetera.

-   `mz` &ndash; El nivel de zoom del mapa que se usará al pasar a la aplicación de mapas de la vista de calle.


Trabajar con integrado de la aplicación asigna o la vista calle es una manera fácil de agregar rápidamente la compatibilidad con la asignación. Sin embargo, la API de mapas de Android ofrece un mayor control sobre la experiencia de asignación.
