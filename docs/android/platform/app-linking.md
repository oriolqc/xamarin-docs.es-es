---
title: Vincular a la aplicación en Android
description: Esta guía describe cómo Android 6.0 admite la vinculación de aplicación, una técnica que permite a las aplicaciones móviles responder a las direcciones URL en los sitios Web. Veremos qué aplicación vinculación es, cómo implementar la vinculación a la aplicación en una aplicación de Android 6.0 y cómo configurar un sitio Web para conceder permisos a la aplicación móvil para un dominio.
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 84185bb616597ee62a35c1acacc5e3664f500c21
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436575"
---
# <a name="app-linking-in-android"></a>Vincular a la aplicación en Android

_Esta guía describe cómo Android 6.0 admite la vinculación de aplicación, una técnica que permite a las aplicaciones móviles responder a las direcciones URL en los sitios Web. Veremos qué aplicación vinculación es, cómo implementar la vinculación a la aplicación en una aplicación de Android 6.0 y cómo configurar un sitio Web para conceder permisos a la aplicación móvil para un dominio._

## <a name="app-linking-overview"></a>Información general de la vinculación de aplicaciones

Aplicaciones móviles ya no viven en un silo &ndash; en muchos casos son un importante de los componentes de sus negocios, junto con su sitio Web. Es conveniente para las empresas conectar fácilmente su presencia en la web y aplicaciones móviles, con vínculos en un sitio Web de inicio de aplicaciones móviles y mostrar el contenido relevante en la aplicación móvil. *Vinculación de aplicación* (también denominada *vinculación profunda*) es una técnica que permite a un dispositivo móvil responder a un URI e inicie una aplicación móvil que corresponde a ese URI.

Android controla la vinculación a la aplicación a través de la *sistema intención* &ndash; cuando el usuario hace clic en un vínculo en un explorador móvil, el explorador móvil enviará un intento de Android suplantará a una aplicación registrada. Por ejemplo, al hacer clic en un vínculo en un sitio Web de cocina se abra una aplicación móvil que está asociada a ese sitio Web y muestre una receta específica al usuario. Si hay más de una aplicación registrada para controlar dicha intención, Android, producirá lo que se conoce como un *diálogo desambiguación* que le preguntará a un usuario de la aplicación para seleccionar la aplicación que debe controlar la intención, para ejemplo:

![Captura de pantalla de ejemplo de un cuadro de diálogo de desambiguación](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 mejora en el objeto, use el control de vínculo automático. Es posible para Android registrar automáticamente una aplicación como el controlador predeterminado para un URI &ndash; se inician y navegar directamente a la actividad relevante automáticamente la aplicación. Cómo se decide Android 6.0 controlar un clic URI depende de los siguientes criterios:

1. **Una aplicación existente ya está asociada con el URI** &ndash; el usuario puede ya ha asociado una aplicación existente a un URI. En ese caso, Android sigue usando esa aplicación.
2. **Ninguna aplicación existente está asociada con el URI, pero se instala una aplicación auxiliar** &ndash; en este escenario, el usuario no ha especificado una aplicación existente, por lo que Android utilizará la aplicación auxiliar instalada para atender la solicitud.
3. **Ninguna aplicación existente está asociada con el URI, pero se instalan las aplicaciones auxiliares** &ndash; porque hay varias aplicaciones que admiten el URI, se mostrará el cuadro de diálogo de anulación de ambigüedades y el usuario debe seleccionar qué aplicación le controlar el URI.

Si el usuario no tiene ninguna aplicación instalada que admiten el URI y uno está instalado posteriormente, a continuación, Android establecerá esa aplicación como controlador predeterminado para el URI después de comprobar la asociación con el sitio Web que está asociado con el URI.

Esta guía describe cómo configurar una aplicación de Android 6.0 y cómo crear y publicar el archivo de vínculos de activos digitales para admitir la vinculación a la aplicación en Android 6.0.

## <a name="requirements"></a>Requisitos

Esta guía requiere Xamarin.Android 6.1 y una aplicación que tenga como destino Android 6.0 (nivel de API 23) o superior.

La vinculación de aplicación es posible en versiones anteriores de Android mediante el uso de la [paquete NuGet remaches](https://www.nuget.org/packages/Rivets/) desde el almacén de componentes de Xamarin. El paquete remaches no es compatible con la vinculación a la aplicación en Android 6.0; no admite la vinculación de aplicación de Android 6.0.

## <a name="configuring-app-linking-in-android-60"></a>Configuración de la vinculación de aplicación en Android 6.0

Configurar vínculos de aplicación en Android 6.0 implica dos pasos principales:

1. **Agregar uno o más intención-filtros para del sitio Web de URI** &ndash; los filtros intención ayude a Android cómo controlar un clic de dirección URL en un explorador móvil.
2. **Publicar un *JSON de vínculos activos digitales* archivo en el sitio Web** &ndash; se trata de un archivo que se carga en un sitio Web y Android se usa para comprobar la relación entre la aplicación móvil y el dominio del sitio Web. Sin esto, Android no puede instalar la aplicación como el identificador de forma predeterminada para el URI; el usuario debe hacerlo manualmente.

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>Configuración del filtro de intención

Es necesario configurar un filtro de intentos que se asigna un URI (o posible un conjunto de identificadores URI) de un sitio Web a una actividad en una aplicación de Android. En Xamarin.Android, esta relación se establece mediante etiquetar una actividad con el [IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/). El filtro intención debe declarar la siguiente información:

* **`Intent.ActionView`** &ndash; Esto registrará el filtro intención para responder a solicitudes para ver información
* **`Categories`** &ndash;  El filtro intención debe registrar ambos **[Intent.CategoryBrowsable](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryBrowsable/)** y **[Intent.CategoryDefault](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryDefault/)** para poder correctamente controlar el URI de la web.
* **`DataScheme`** &ndash; Debe declarar el filtro intención `http` o `https`. Estas son las combinaciones válidas solo dos.
* **`DataHost`** &ndash; Este es el dominio que se originan los URI.
* **`DataPathPrefix`** &ndash; Se trata de una ruta de acceso opcional a los recursos en el sitio Web.
* **`AutoVerify`** &ndash; El `autoVerify` atributo indica a Android para comprobar la relación entre la aplicación y el sitio Web. Esto se explicará más a continuación.

En el ejemplo siguiente se muestra cómo utilizar el [IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) para administrar los vínculos de `https://www.recipe-app.com/recipes` y de `http://www.recipe-app.com/recipes`:

```csharp
[IntentFilter(new [] { Intent.ActionView },
              Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
              DataScheme = "http",
              DataHost = "recipe-app.com",
              DataPathPrefix = "/recipe",
              AutoVerify=true)]
public class RecipeActivity : Activity
{
    // Code for the activity omitted
}
```

Android comprobará todos los hosts que se identifican mediante los filtros de intención en el archivo de activos digitales en el sitio Web antes de registrar la aplicación como el controlador predeterminado para un URI. Todos los filtros intención deben pasar comprobación antes de Android puedan establecer la aplicación como controlador predeterminado.

### <a name="creating-the-digital-assets-link-file"></a>Crear el archivo de vínculo de activos digitales

Android 6.0 vinculación a la aplicación requiere que Android, compruebe la asociación entre la aplicación y el sitio Web antes de configurar la aplicación como el controlador predeterminado para el URI. Esta comprobación se producirá cuando se instala la aplicación por primera vez. El *vínculos activos digitales* archivo es un archivo JSON que está hospedado por el webdomain(s) relevante.

> [!NOTE]
> El `android:autoVerify` atributo debe estar configurado por el filtro intención &ndash; en caso contrario Android no llevará a cabo la comprobación.

El archivo se encuentra por el administrador del dominio en la ubicación **https://domain/.well-known/assetlinks.json**.

El archivo de activos digitales contiene metadatos necesaria para Android comprobar la asociación. Un **assetlinks.json** archivo tiene los siguientes pares de clave y valor:

* `namespace` &ndash; el espacio de nombres de la aplicación Android.
* `package_name` &ndash; el nombre del paquete de la aplicación de Android (declarado en el manifiesto de aplicación).
* `sha256_cert_fingerprints` &ndash; las huellas digitales de SHA256 de la aplicación firmada. Consulte la guía [buscar el almacén de claves MD5 o SHA1 firma](~/android/deploy-test/signing/keystore-signature.md) para obtener más información sobre cómo obtener la huella SHA1 de una aplicación.

El fragmento de código siguiente es un ejemplo de **assetlinks.json** con una sola aplicación enumerados:

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"com.example",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

Es posible registrar más de una huella digital de SHA256 para admitir distintas versiones o compilaciones de la aplicación. Esto a continuación **assetlinks.json** archivo es un ejemplo de registro de varias aplicaciones:

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.puppies.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   },
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.monkeys.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

El [sitio Web de Google Digital Asset vínculos](https://developers.google.com/digital-asset-links/tools/generator) tiene una herramienta en línea que puede ayudar a crear y probar el archivo de activos digitales.

### <a name="testing-app-links"></a>Vínculos de la aplicación de prueba

Después de implementar los vínculos de la aplicación, los distintos elementos deben probarse para asegurarse de que funcionan según lo previsto.

Es posible confirmar que el archivo de activos digitales es correctamente con el formato y hospedado mediante la API de vínculos de activos digitales de Google, tal como se muestra en este ejemplo:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

Hay dos pruebas que se pueden realizar para asegurarse de que los filtros intención se han configurado correctamente y que la aplicación se establece como el controlador predeterminado para un URI:

1.  El archivo de activos digitales está hospedado correctamente como se describió anteriormente. La primera prueba enviará un intento de Android debe redirigir a la aplicación móvil. La aplicación Android debe iniciar y mostrar la actividad registrada para la dirección URL. En un símbolo del sistema, escriba:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2.  Mostrar el vínculo existente control de directivas para las aplicaciones instaladas en un dispositivo determinado. El siguiente comando volcará una lista de las directivas de vínculo para cada usuario en el dispositivo con la siguiente información. En el símbolo del sistema, escriba el siguiente comando:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    * **`Package`** &ndash; El nombre del paquete de la aplicación.
    * **`Domain`** &ndash; Los dominios (separados por espacios) cuyos vínculos web será procesado por la aplicación
    * **`Status`** &ndash; Este es el estado actual de control de vínculo de la aplicación. Un valor de **siempre** significa que la aplicación tiene `android:autoVerify=true` declarado y ha pasado la comprobación del sistema. Va seguido de un número hexadecimal que representa el registro del sistema Android de la preferencia.

    Por ejemplo:

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>Resumen

Esta guía describe cómo la aplicación vinculación funciona en Android 6.0. A continuación, se trata cómo configurar una aplicación de Android 6.0 para admitir y responder a los vínculos de la aplicación. También se describe cómo probar la vinculación a la aplicación en una aplicación Android.


## <a name="related-links"></a>Vínculos relacionados

- [Buscar la firma MD5 o SHA1 de su almacén de claves](~/android/deploy-test/signing/keystore-signature.md)
- [Las actividades y propósitos](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Vínculos de activos digitales de Google](https://developers.google.com/digital-asset-links/)
- [Herramienta de comprobación y el generador de lista de instrucción](https://developers.google.com/digital-asset-links/tools/generator)
