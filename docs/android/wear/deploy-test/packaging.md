---
title: Desgaste de empaquetado de aplicaciones
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 585c276b327a9092bdd13fa633307477017558c5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276813"
---
# <a name="packaging-wear-apps"></a>Desgaste de empaquetado de aplicaciones

Aplicaciones de Android Wear se empaquetan con una aplicación completa de Android para su distribución en Google Play. 

## <a name="automatic-packaging"></a>Empaquetado automática

A partir de Xamarin Android 5.0, la aplicación Wear se empaqueta de manera automática como un recurso en la aplicación de dispositivo de mano cuando creas una referencia de proyecto desde el proyecto de equipo de mano para el proyecto de desgaste. Puede usar los pasos siguientes para crear esta asociación: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Si su aplicación Wear ya no forma parte de su solución de mano, haga clic en el nodo de solución y seleccione **Agregar > Agregar proyecto existente...** .

2. Navegue hasta la **.csproj** archivo de la aplicación Wear, selecciónelo y haga clic en **abierto**. El proyecto de aplicación Wear ahora debe estar visible en la solución de mano.

3. Haga clic en el **referencias** nodo y seleccione **Agregar referencia**.

4. En el **Administrador de referencias** cuadro de diálogo, habilitar, a continuación, haga clic en el desgaste del proyecto (haga clic para agregar una marca de verificación) **Aceptar**.

5. Cambiar el nombre del paquete para el proyecto de desgaste para que coincida con el nombre del paquete del proyecto de equipo de mano (se puede cambiar el nombre del paquete bajo **Propiedades > manifiesto de Android**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Si su aplicación Wear ya no forma parte de su solución de mano, haga clic en el nodo de solución y seleccione **Agregar > Agregar proyecto existente...** .

2. Navegue hasta la **.csproj** archivo de la aplicación Wear, selecciónelo y haga clic en **abierto**. El proyecto de aplicación Wear ahora debe estar visible en la solución de mano.

3. Haga clic en el nodo de proyecto de equipo de mano en la solución y haga clic en **editar referencias...** .

4. En el **editar referencias** cuadro de diálogo, habilitar, a continuación, haga clic en el desgaste del proyecto (haga clic para agregar una marca de verificación) **Aceptar**.

5. Cambiar el nombre del paquete para el proyecto de desgaste para que coincida con el nombre del paquete del proyecto de equipo de mano (se puede cambiar el nombre del paquete bajo **opciones de proyecto > aplicación de Android**).

-----


Tenga en cuenta que habrá un **XA5211** error si el nombre del paquete de la aplicación Wear no coincide con el nombre del paquete de la aplicación de dispositivo de mano. Por ejemplo:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

Para corregir este error, cambie el nombre del paquete de la aplicación Wear para que coincida con el nombre del paquete de la aplicación de dispositivo de mano.

Al hacer clic en **compilar > compilar todo**, esta asociación desencadena automática empaquetado del proyecto desgaste en el proyecto de equipo de mano (Phone) principal. La aplicación de desgaste está compilado automáticamente y se incluye como recurso en la aplicación de dispositivo de mano.

El ensamblado que genera el proyecto de aplicación Wear no se utiliza como una referencia de ensamblado en el proyecto de equipo de mano (Phone). En su lugar, el proceso de compilación hace lo siguiente:

-   Comprueba que el paquete de los nombres de coincidencia. 

-   Genera el XML y lo agrega al proyecto de dispositivo de mano para asociarlo con la aplicación de desgaste. Por ejemplo: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

-   Agrega la aplicación Wear como un **raw** recurso para el proyecto de equipo de mano. 


## <a name="manual-packaging"></a>Empaquetado manual

Puede escribir aplicaciones de Android Wear en Xamarin.Android antes de la versión 5.0, pero debe seguir estas instrucciones de empaquetado manual para distribuir la aplicación: 

1. Asegúrese de que los proyectos de equipo de mano (Phone) y proyecto portátiles tienen el mismo nombre de paquete y el número de versión.

2. Compilar manualmente el proyecto portátiles como un **versión** de compilación.

3. Agregar manualmente la versión **. APK** del paso (2) en el **recursos o sin formato** directorio del proyecto de equipo de mano (Phone).

4. Agregar manualmente un nuevo recurso XML **Resources/xml/wearable_app_desc.xml** en el proyecto de equipo de mano que hace referencia al dispositivo ponible **APK** del paso (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. Agregar manualmente un `<meta-data />` elemento para el proyecto de equipo de mano **AndroidManifest.xml** `<application>` elemento que hace referencia al nuevo recurso XML:

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

Consulte también el sitio para desarrolladores de Android [envases presenten manual instrucciones](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually).

