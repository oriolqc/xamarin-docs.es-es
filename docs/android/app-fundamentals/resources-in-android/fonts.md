---
title: Fuentes
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/09/2018
ms.openlocfilehash: c7953748e79bd43bc14601c1f0ea05d1a36adf08
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013428"
---
# <a name="fonts"></a>Fuentes

## <a name="overview"></a>Información general

A partir de nivel de API 26, el SDK de Android permite las fuentes se traten como recursos, al igual que un diseños o recursos drawable. El [Android soporte biblioteca 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1) le restituir la nueva fuente de la API para aquellas aplicaciones que tienen como destino el nivel de API 14 o superior.

Después de la API 26 tiene como destino o instalando el v26 Android Support Library, hay dos maneras de utilizar las fuentes en una aplicación de Android:

1. **Empaquetar la fuente como un recurso de Android** &ndash; Esto garantiza que la fuente esté siempre disponible para la aplicación, pero aumentará el tamaño del APK.
2. **Descargar las fuentes** &ndash; Android también admite la descarga de una fuente de un _proveedor de fuente_. El proveedor de fuente comprueba si la fuente ya está en el dispositivo. Si es necesario, la fuente se descargará y se almacena en caché en el dispositivo. Esta fuente se puede compartir entre varias aplicaciones.

Fuentes similares (o una fuente que puede tener varios estilos diferentes) puede agruparse en _familias de fuentes_. Esto permite a los desarrolladores especificar ciertos atributos de la fuente, como su peso, y Android seleccionará automáticamente la fuente adecuada de la familia de fuentes.

El v26 Android Support Library va a restituir compatibilidad con fuentes a nivel de API 26. Cuando el destino de los niveles de API anteriores, es necesario declarar el `app` espacio de nombres XML y entre los distintos atributos de fuente mediante la `android:` espacio de nombres y el `app:` espacio de nombres. Si solo el `android:` se utiliza el espacio de nombres, las fuentes no se muestran dispositivos que ejecutan el nivel de API 25 o menos. Por ejemplo, este fragmento de código XML declara un nuevo [ _familia de fuentes_ ](#font_families) recursos que funcionarán en el nivel de API 14 o superior:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

     <font  android:font="@font/sourcesanspro_regular"
            android:fontStyle="normal"
            android:fontWeight="400"
            app:font="@font/sourcesanspro_regular"
            app:fontStyle="normal"
            app:fontWeight="400" />

</font-family>
```

Se proporcionan fuentes a una aplicación Android de forma adecuada, siempre que se pueden aplicar a un widget de interfaz de usuario estableciendo el [ `fontFamily` atributo](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily). Por ejemplo, el fragmento de código siguiente muestra cómo mostrar una fuente en una vista de texto:

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Esta guía se explique cómo usar las fuentes como un recurso de Android en primer lugar y, a continuación, pasar a explicar cómo descargar fuentes en tiempo de ejecución.

## <a name="fonts-as-a-resource"></a>Fuentes como un recurso

Empaquetar una fuente en un APK de Android, se garantiza que siempre esté disponible para la aplicación. Un archivo de fuentes (ya sea una. THF o. Archivo OTF) se agrega a una aplicación de Xamarin.Android, al igual que cualquier otro recurso, copiando los archivos en un subdirectorio en el **recursos** carpeta de un proyecto de Xamarin.Android. Los recursos de fuentes se mantienen un **fuente** : el subdirectorio de la **recursos** carpeta del proyecto.

> [!NOTE]
> Las fuentes deben tener un **acción de compilación** de **AndroidResource** o no se empaquetarán en el APK final. La acción de compilación debe establecerse automáticamente por el IDE.

Cuando hay muchos archivos de fuente similares (por ejemplo, la misma fuente con diferentes ponderaciones o estilos) es posible agruparlos en una familia de fuentes.

<a name="font_families" />

### <a name="font-families"></a>Familias de fuentes

Una familia de fuentes es un conjunto de fuentes que tienen los estilos y pesos diferentes. Por ejemplo, puede haber archivos de fuentes independiente para las fuentes en negrita o cursiva. La familia de fuentes se define mediante `font` elementos en un archivo XML que se mantiene en el **recursos/fuente** directory. Cada familia de fuentes debe tener su propio archivo XML.

Para crear una familia de fuentes, primero agregue todas las fuentes a la **recursos/fuente** carpeta. A continuación, cree un nuevo archivo XML en la carpeta de fuentes para la familia de fuentes. El nombre del archivo XML no tiene ninguna afinidad o la relación que las fuentes que se hace referencia; el archivo de recursos puede ser cualquier nombre de archivo de recursos de Android legal. Este archivo XML tendrá una raíz `font-family` elemento que contiene uno o varios `font` elementos. Cada `font` elemento declara los atributos de una fuente.

El siguiente código XML es un ejemplo de una familia de fuentes para el _orígenes SAN Pro_ fuente que define los pesos de muchas fuentes diferentes. Esto se guarda como archivo en el **recursos/fuente** carpeta denominada **sourcesanspro.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto">
    <font android:font="@font/sourcesanspro_regular"
          android:fontStyle="normal"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_regular"
          app:fontStyle="normal"
          app:fontWeight="400" />
    <font android:font="@font/sourcesanspro_bold"
          android:fontStyle="normal"
          android:fontWeight="800"
          app:font="@font/sourcesanspro_bold"
          app:fontStyle="normal"
          app:fontWeight="800" />
    <font android:font="@font/sourcesanspro_italic"
          android:fontStyle="italic"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_italic"
          app:fontStyle="italic"
          app:fontWeight="400" />
</font-family>
```

El `fontStyle` atributo tiene dos valores posibles:

* **normal** &ndash; una fuente normal
* **cursiva** &ndash; una fuente en cursiva

El `fontWeight` atributo corresponde a la hoja CSS `font-weight` atributo y hace referencia el grosor de la fuente. Se trata de un valor en el intervalo de 100 a 900. En la lista siguiente se describe los valores de peso de fuente comunes y su nombre:

* **Thin** &ndash; 100
* **Extra claro** &ndash; 200
* **Luz** &ndash; 300
* **Normal** &ndash; 400
* **Medio** &ndash; 500
* **Negrita semi** &ndash; 600
* **Bold** &ndash; 700
* **Extra-negrita** &ndash; 800
* **Negro** &ndash; 900

Una vez que se ha definido una familia de fuentes, se puede utilizar mediante declaración estableciendo el `fontFamily`, `textStyle`, y `fontWeight` atributos en el archivo de diseño.  Por ejemplo, el fragmento XML siguiente establece una fuente 600 peso (normal) y un estilo de texto en cursiva:

```xml
<TextView
    android:text="Sans Source Pro semi-bold italic, 600 weight, italic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:fontFamily="@font/sourcesanspro"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_horizontal"
    android:fontWeight="600"
    android:textStyle="italic"
    />
```

### <a name="programmatically-assigning-fonts"></a>Asignar mediante programación las fuentes

Las fuentes se pueden establecer mediante programación utilizando la [ `Resources.GetFont` ](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) método para recuperar un [ `Typeface` ](https://developer.android.com/reference/android/graphics/Typeface.html) objeto. Muchas de las vistas tienen un `TypeFace` propiedad que puede usarse para asignar la fuente para el widget. Este fragmento de código muestra cómo establecer mediante programación la fuente en una vista de texto:

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

El `GetFont` método cargará automáticamente la primera fuente dentro de una familia de fuentes.  Para cargar una fuente que coincide con un estilo determinado, use el `Typeface.Create` método. Este método intentará cargar una fuente que coincide con el estilo especificado. Por ejemplo, este fragmento de código intentará cargar negrita `Typeface` objeto a partir de una familia de fuentes que se define en **recursos/fuentes**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>Descarga de fuentes

Empaquetar fuentes como un recurso de aplicación, en lugar de Android puede descargar fuentes desde un origen remoto. Esto tendrá el efecto de reducir el tamaño del APK deseable.

Las fuentes se descargan con la Ayuda de un _proveedor de fuente_. Se trata de un proveedor de contenido especializado que administra la descarga y el almacenamiento en caché de fuentes para todas las aplicaciones en el dispositivo. Android 8.0 incluye un proveedor de fuente para descargar fuentes desde el [fuente de Google Repository](http://fonts.google.com). Este proveedor de fuente predeterminado es usado en el nivel de API 14 con v26 de la biblioteca de compatibilidad de Android.

Cuando una aplicación realiza una solicitud para una fuente, el proveedor de fuente en primer lugar comprueba si la fuente ya está en el dispositivo. Si no es así, a continuación, intentará descargar la fuente. Si la fuente no puede ser descargado, Android, a continuación, usará la fuente predeterminada del sistema. Una vez que se ha descargado la fuente, está disponible para todas las aplicaciones en el dispositivo, no solo a la aplicación que realizó la solicitud inicial.

Cuando se realiza una solicitud para descargar una fuente, la aplicación no consultar directamente el proveedor de fuente. En su lugar, las aplicaciones usarán una instancia de la [ `FontsContract` ](https://developer.android.com/reference/android/provider/FontsContract.html) API (o la [ `FontsContractCompat` ](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) si se usa el 26 de biblioteca de soporte técnico).  

Android 8.0 admite descargar fuentes de dos maneras diferentes:

1. **Declarar las fuentes que se pueden descargar como un recurso** &ndash; una aplicación puede declarar las fuentes descargables para Android a través de los archivos de recursos XML. Estos archivos contendrá todos los metadatos que se debe descargar las fuentes de forma asincrónica cuando se inicia la aplicación y almacenarlos en memoria caché en el dispositivo Android.
2. **Mediante programación** &ndash; API en el nivel de API de Android 26 permiten que una aplicación descargar las fuentes mediante programación, mientras se está ejecutando la aplicación. Aplicaciones creará un `FontRequest` de objetos para una fuente determinada y pasar este objeto a la `FontsContract` clase. El `FontsContract` toma el `FontRequest` y recupera la fuente de un _proveedor de fuente_. Android sincrónicamente descargará la fuente. Un ejemplo de cómo crear un `FontRequest` se mostrarán más adelante en esta guía.

Independientemente del enfoque que se usa, se pueden descargar los archivos de recursos que deben agregarse a la aplicación de Xamarin.Android antes de fuentes. En primer lugar, se deben declarar las fuentes en un archivo XML en el **recursos/fuente** directorio como parte de una familia de fuentes. Este fragmento de código es un ejemplo de cómo descargar fuentes desde el [colección de código abierto de fuentes de Google](https://fonts.google.com) utilizando el proveedor de fuente predeterminado que se incluye con Android 8.0 (o biblioteca de compatibilidad v26):

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto"
             android:fontProviderAuthority="com.google.android.gms.fonts"
             android:fontProviderPackage="com.google.android.gms"
             android:fontProviderQuery="VT323"
             android:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
             app:fontProviderAuthority="com.google.android.gms.fonts"
             app:fontProviderPackage="com.google.android.gms"
             app:fontProviderQuery="VT323"
             app:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
>
</font-family>
```

El `font-family` elemento contiene los atributos siguientes, que se declara la información que requiere Android para descargar las fuentes:

1. **fontProviderAuthority** &ndash; la autoridad del proveedor de fuente que se usará para la solicitud.
2. **fontPackage** &ndash; el paquete para el proveedor de fuente que se usará para la solicitud. Esto se usa para comprobar la identidad del proveedor.
3. **fontQuery** &ndash; es una cadena que le ayudarán al proveedor de fuente para localizar la fuente solicitada. Obtener más información sobre la consulta de fuente es específicos del proveedor de fuente. El [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) clase en el [fuentes descargables](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) aplicación de ejemplo proporciona cierta información sobre el formato de consulta para las fuentes de la colección de origen abierto de fuentes de Google.
4. **fontProviderCerts** &ndash; con la lista de conjuntos de valores hash de los certificados que el proveedor debe estar firmado con una matriz de recursos.

Una vez que se definen las fuentes, puede ser necesario proporcionar información sobre la _certificados fuente_ implicada en la descarga.

### <a name="font-certificates"></a>Certificados de fuente

Si el proveedor de fuente no está preinstalado en el dispositivo, o si la aplicación usa el `Xamarin.Android.Support.Compat` biblioteca, Android requiere que los certificados de seguridad del proveedor de fuente. Estos certificados se mostrarán en un archivo de recursos de la matriz que se mantiene en **recursos/valores** directory.

Por ejemplo, el siguiente código XML denominado **Resources/values/fonts_cert.xml** y almacena los certificados para el proveedor de fuente de Google:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="com_google_android_gms_fonts_certs">
        <item>@array/com_google_android_gms_fonts_certs_dev</item>
        <item>@array/com_google_android_gms_fonts_certs_prod</item>
    </array>
    <string-array name="com_google_android_gms_fonts_certs_dev">
        <item>
            MIIEqDCCA5CgAwIBAgIJANWFuGx90071MA0GCSqGSIb3DQEBBAUAMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTAeFw0wODA0MTUyMzM2NTZaFw0zNTA5MDEyMzM2NTZaMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBANbOLggKv+IxTdGNs8/TGFy0PTP6DHThvbbR24kT9ixcOd9W+EaBPWW+wPPKQmsHxajtWjmQwWfna8mZuSeJS48LIgAZlKkpFeVyxW0qMBujb8X8ETrWy550NaFtI6t9+u7hZeTfHwqNvacKhp1RbE6dBRGWynwMVX8XW8N1+UjFaq6GCJukT4qmpN2afb8sCjUigq0GuMwYXrFVee74bQgLHWGJwPmvmLHC69EH6kWr22ijx4OKXlSIx2xT1AsSHee70w5iDBiK4aph27yH3TxkXy9V89TDdexAcKk/cVHYNnDBapcavl7y0RiQ4biu8ymM8Ga/nmzhRKya6G0cGw8CAQOjgfwwgfkwHQYDVR0OBBYEFI0cxb6VTEM8YYY6FbBMvAPyT+CyMIHJBgNVHSMEgcEwgb6AFI0cxb6VTEM8YYY6FbBMvAPyT+CyoYGapIGXMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbYIJANWFuGx90071MAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEEBQADggEBABnTDPEF+3iSP0wNfdIjIz1AlnrPzgAIHVvXxunW7SBrDhEglQZBbKJEk5kT0mtKoOD1JMrSu1xuTKEBahWRbqHsXclaXjoBADb0kkjVEJu/Lh5hgYZnOjvlba8Ld7HCKePCVePoTJBdI4fvugnL8TsgK05aIskyY0hKI9L8KfqfGTl1lzOv2KoWD0KWwtAWPoGChZxmQ+nBli+gwYMzM1vAkP+aayLe0a1EQimlOalO762r0GXO0ks+UeXde2Z4e+8S/pf7pITEI/tP+MxJTALw9QUWEv9lKTk+jkbqxbsh8nfBUapfKqYn0eidpwq2AzVp3juYl7//fKnaPhJD9gs=
        </item>
    </string-array>
    <string-array name="com_google_android_gms_fonts_certs_prod">
        <item>
            MIIEQzCCAyugAwIBAgIJAMLgh0ZkSjCNMA0GCSqGSIb3DQEBBAUAMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDAeFw0wODA4MjEyMzEzMzRaFw0zNjAxMDcyMzEzMzRaMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBAKtWLgDYO6IIrgqWbxJOKdoR8qtW0I9Y4sypEwPpt1TTcvZApxsdyxMJZ2JORland2qSGT2y5b+3JKkedxiLDmpHpDsz2WCbdxgxRczfey5YZnTJ4VZbH0xqWVW/8lGmPav5xVwnIiJS6HXk+BVKZF+JcWjAsb/GEuq/eFdpuzSqeYTcfi6idkyugwfYwXFU1+5fZKUaRKYCwkkFQVfcAs1fXA5V+++FGfvjJ/CxURaSxaBvGdGDhfXE28LWuT9ozCl5xw4Yq5OGazvV24mZVSoOO0yZ31j7kYvtwYK6NeADwbSxDdJEqO4k//0zOHKrUiGYXtqw/A0LFFtqoZKFjnkCAQOjgdkwgdYwHQYDVR0OBBYEFMd9jMIhF1Ylmn/Tgt9r45jk14alMIGmBgNVHSMEgZ4wgZuAFMd9jMIhF1Ylmn/Tgt9r45jk14aloXikdjB0MQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xEDAOBgNVBAsTB0FuZHJvaWQxEDAOBgNVBAMTB0FuZHJvaWSCCQDC4IdGZEowjTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBBAUAA4IBAQBt0lLO74UwLDYKqs6Tm8/yzKkEu116FmH4rkaymUIE0P9KaMftGlMexFlaYjzmB2OxZyl6euNXEsQH8gjwyxCUKRJNexBiGcCEyj6z+a1fuHHvkiaai+KL8W1EyNmgjmyy8AW7P+LLlkR+ho5zEHatRbM/YAnqGcFh5iZBqpknHf1SKMXFh4dd239FJ1jWYfbMDMy3NS5CTMQ2XFI1MvcyUTdZPErjQfTbQe3aDQsQcafEQPD+nqActifKZ0Np0IS9L9kR/wbNvyz6ENwPiTrjV2KRkEjH78ZMcUQXg0L3BYHJ3lc69Vs5Ddf9uUGGMYldX3WfMBEmh/9iFBDAaTCK
        </item>
    </string-array>
</resources>
```

Con estos archivos de recursos en su lugar, la aplicación es capaz de descargar las fuentes.

### <a name="declaring-downloadable-fonts-as-resources"></a>Declarar que se puede descargar fuentes como recursos

Enumerando las fuentes que se pueden descargar en el **AndroidManifest.XML**, Android descargará las fuentes de forma asincrónica cuando la aplicación se inicia por primera vez. La fuente de sí mismos se muestran en un archivo de recursos de matriz, similar a ésta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

Para descargar estas fuentes, tienen que ser declarados en **AndroidManifest.XML** agregando `meta-data` como elemento secundario de la `application` elemento. Por ejemplo, si se declaran las fuentes que se pueden descargar en un archivo de recursos en **Resources/values/downloadable_fonts.xml**, a continuación, este fragmento de código tendría que se agregó al manifiesto:

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>Descargar una fuente con las API de fuente

Es posible descargar mediante programación una fuente creando un [ `FontRequest` ](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) objeto y que se va a pasar el `FontContractCompat.RequestFont` método. El `FontContractCompat.RequestFont` método compruebe primero si existe la fuente en el dispositivo y, a continuación, si es necesario le asincrónicamente consulta el proveedor de fuente e intente descargar la fuente de la aplicación. Si `FontRequest` es no se puede descargar la fuente, a continuación, va a usar la fuente predeterminada del sistema Android.

Un `FontRequest` objeto contiene información que se usará el proveedor de fuente para buscar y descargar una fuente. Un `FontRequest` requiere cuatro fragmentos de información:

1. **Entidad de proveedor de fuente** &ndash; la autoridad del proveedor de fuente que se usará para la solicitud.
2. **Paquete de fuentes** &ndash; el paquete para el proveedor de fuente que se usará para la solicitud. Esto se usa para comprobar la identidad del proveedor.
3. **Consulta de fuente** &ndash; es una cadena que le ayudarán al proveedor de fuente para localizar la fuente solicitada. Obtener más información sobre la consulta de fuente es específicos del proveedor de fuente. Los detalles de la cadena son específicos del proveedor de fuente. El [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) clase en el [fuentes descargables](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) aplicación de ejemplo proporciona cierta información sobre el formato de consulta para las fuentes de la colección de origen abierto de fuentes de Google.
4. **Certificados del proveedor de fuente** &ndash; una matriz de recursos con la lista de conjuntos de códigos hash para los certificados con el proveedor debería iniciar sesión.

Este fragmento de código es un ejemplo de instancias de un nuevo `FontRequest` objeto:

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

En el fragmento anterior `FontToDownload` es una consulta que le ayudarán a la fuente de la colección de código abierto de fuentes de Google.

Antes de pasar el `FontRequest` a la `FontContractCompat.RequestFont` método, hay dos objetos que se deben crear:

* **`FontsContractCompat.FontRequestCallback`** &ndash; Se trata de una clase abstracta que se debe extender. Es una devolución de llamada que se invoca cuando `RequestFont` haya finalizado. Una aplicación de Xamarin.Android debe subclase `FontsContractCompat.FontRequestCallback` e invalidar la `OnTypefaceRequestFailed` y `OnTypefaceRetrieved`, que proporciona las acciones que se realizarán cuando la descarga se produce un error o se realiza correctamente, respectivamente.
* **`Handler`** &ndash; Se trata de un `Handler` que se usará en `RequestFont` para descargar la fuente en un subproceso, si es necesario. Las fuentes deben **no** descargarse en el subproceso de interfaz de usuario.

Este fragmento de código es un ejemplo de un C# clase asincrónicamente, se descargará una fuente de colección de código abierto de fuentes de Google. Implementa el `FontRequestCallback` interfaz y genera un C# eventos cuando `FontRequest` ha finalizado.

```csharp
public class FontDownloadHelper : FontsContractCompat.FontRequestCallback
{
    // A very simple font query; replace as necessary
    public static readonly String FontToDownload = "Courgette";

    Android.OS.Handler Handler = null;

    public event EventHandler<FontDownloadEventArg> FontDownloaded = delegate
    {
        // just an empty delegate to avoid null reference exceptions.  
    };


    public void DownloadFonts(Context context)
    {
        FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms",FontToDownload , Resource.Array.com_google_android_gms_fonts_certs);
        FontsContractCompat.RequestFont(context, request, this, GetHandlerThreadHandler());
    }

    public override void OnTypefaceRequestFailed(int reason)
    {
        base.OnTypefaceRequestFailed(reason);
        FontDownloaded(this, new FontDownloadEventArg(null));
    }

    public override void OnTypefaceRetrieved(Android.Graphics.Typeface typeface)
    {
        base.OnTypefaceRetrieved(typeface);
        FontDownloaded(this, new FontDownloadEventArg(typeface));
    }

    Handler GetHandlerThreadHandler()
    {
        if (Handler == null)
        {
            HandlerThread handlerThread = new HandlerThread("fonts");
            handlerThread.Start();
            Handler = new Handler(handlerThread.Looper);
        }
        return Handler;
    }
}

public class FontDownloadEventArg : EventArgs
{
    public FontDownloadEventArg(Android.Graphics.Typeface typeface)
    {
        Typeface = typeface;
    }
    public Android.Graphics.Typeface Typeface { get; private set; }
    public bool RequestFailed
    {
        get
        {
            return Typeface != null;
        }
    }
}
```

Para usar esta aplicación auxiliar, un nuevo `FontDownloadHelper` se crea, y se le asigna un controlador de eventos:  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) =>
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>Resumen

Esta guía describe las nuevas API de Android 8.0 para admitir las fuentes descargables y las fuentes como recursos. También se han descrito cómo incrustar fuentes existentes en un APK y se usan en un diseño. También describe cómo Android 8.0 admite descargar fuentes de un proveedor de fuente, mediante programación o mediante la declaración de los metadatos fuente en los archivos de recursos.

## <a name="related-links"></a>Vínculos relacionados

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Tipo de letra](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Compatibilidad con Android biblioteca 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [Uso de fuentes en Android](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [Especificación de peso de fuente CSS](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Colección de código abierto de fuentes de Google](https://fonts.google.com/)
- [Código fuente Sans Pro](https://fonts.google.com/specimen/Source+Sans+Pro)
