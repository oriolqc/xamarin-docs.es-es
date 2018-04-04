---
title: Empaquetar aplicaciones de uso
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: af96c0f8cf862b7a208beb5b91ecbb30598b09d9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="packaging-wear-apps"></a>Empaquetar aplicaciones de uso

Aplicaciones de Android desgaste se empaquetan con una aplicación de Android completa para la distribución en Google Play. 

## <a name="automatic-packaging"></a>Empaquetado automática

A partir de Xamarin Android 5.0, la aplicación de uso se empaqueta de manera automática como un recurso en la aplicación de mano cuando se crea una referencia de proyecto desde el proyecto de equipo de mano para el proyecto de uso. Puede usar los siguientes pasos para crear esta asociación: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Si la aplicación de uso ya no forma parte de la solución de mano, haga clic en el nodo de la solución y seleccione **Agregar > Agregar proyecto existente...** .

2. Navegue hasta la **.csproj** archivos de la aplicación de uso, selecciónelo y haga clic en **abiertos**. El proyecto de aplicación de uso ahora debe estar visible en la solución de mano.

3. Haga clic en el **referencias** nodo y seleccione **Agregar referencia**.

4. En el **Administrador de referencias** cuadro de diálogo, habilite el proyecto de desgaste (haga clic para agregar una marca de verificación), a continuación, haga clic en **Aceptar**.

5. Cambiar el nombre del paquete para el proyecto de uso para que coincida con el nombre del paquete del proyecto de equipo de mano (se puede cambiar el nombre del paquete en **Propiedades > manifiesto Android**).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Si la aplicación de uso ya no forma parte de la solución de mano, haga clic en el nodo de la solución y seleccione **Agregar > Agregar proyecto existente...** .

2. Navegue hasta la **.csproj** archivos de la aplicación de uso, selecciónelo y haga clic en **abiertos**. El proyecto de aplicación de uso ahora debe estar visible en la solución de mano.

3. Haga clic en el nodo del proyecto de equipo de mano en la solución y haga clic en **editar referencias...** .

4. En el **editar referencias** cuadro de diálogo, habilite el proyecto de desgaste (haga clic para agregar una marca de verificación), a continuación, haga clic en **Aceptar**.

5. Cambiar el nombre del paquete para el proyecto de uso para que coincida con el nombre del paquete del proyecto de equipo de mano (se puede cambiar el nombre del paquete en **Project Options > aplicación Android**).

-----


Tenga en cuenta que se le devolverá una **XA5211** error si el nombre del paquete de la aplicación de uso no coincide con el nombre del paquete de la aplicación de mano. Por ejemplo:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

Para corregir este error, cambie el nombre del paquete de la aplicación de uso para que coincida con el nombre del paquete de la aplicación de mano.

Al hacer clic en **generar > compilar todos los**, esta asociación desencadena empaquetado automática del proyecto desgaste en el proyecto de equipo de mano (Phone) principal. La aplicación de desgaste está compilado automáticamente y se incluye como recurso en la aplicación de mano.

El ensamblado que genera el proyecto de aplicación de uso no se utiliza como una referencia de ensamblado en el proyecto de equipo de mano (Phone). En su lugar, el proceso de compilación hace lo siguiente:

-   Comprueba que el paquete nombres a coinciden con. 

-   Genera el XML y lo agrega al proyecto de equipo de mano para asociarlo con la aplicación de uso. Por ejemplo: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

-   Agrega la aplicación de uso como un **sin formato** recurso para el proyecto de equipo de mano. 


## <a name="manual-packaging"></a>Empaquetado manual

Puede escribir aplicaciones Android desgaste en Xamarin.Android antes de la versión 5.0, pero deben seguir estas instrucciones de empaquetado manual para distribuir la aplicación: 

1. Asegúrese de que los proyectos de equipo de mano (Phone) y proyecto Wearable tienen el mismo nombre de paquete y el número de versión.

2. Compilar manualmente el proyecto Wearable como un **versión** de compilación.

3. Agregar manualmente la versión **. APK** del paso (2) en el **recursos/raw** directorio del proyecto de equipo de mano (Phone).

4. Agregar manualmente un nuevo recurso XML **Resources/xml/wearable_app_desc.xml** en el proyecto de equipo de mano que hace referencia a para llevar **APK** del paso (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. Agregar manualmente una `<meta-data />` elemento para el proyecto de equipo de mano **AndroidManifest.xml** `<application>` elemento que hace referencia al nuevo recurso XML:

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

Vea también el sitio para desarrolladores de Android [envases presenten manual instrucciones](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually).

