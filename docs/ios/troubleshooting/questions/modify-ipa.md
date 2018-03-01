---
title: "¿Puedo agregar archivos a o quitar archivos de un archivo IPA después de compilar en Visual Studio?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: be792c95de7aa66d64278e47b2ca6b354e611273
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>¿Puedo agregar archivos a o quitar archivos de un archivo IPA después de compilar en Visual Studio?

Sí, es posible, pero normalmente habrá que volver a firmar el `.app` paquete después de realizar el cambio.

Tenga en cuenta que la modificación del `.ipa` archivo no es necesario en el uso normal. En este artículo se proporciona exclusivamente con fines informativos.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Ejemplo: quitar un archivo de un `.ipa` archivo

En este ejemplo se supone que es el nombre del proyecto Xamarin.iOS `iPhoneApp1` y `generated session id` es `cc530d20d6b19da63f6f1c6f67a0a254`

1.  Compilar el `.ipa` de archivos como normal de Visual Studio.

2.  Cambiar el host de compilación de Mac.

3.  Busque la compilación en la `~/Library/Caches/Xamarin/mtbs/builds` carpeta. Puede pegar esta ruta de acceso en **buscador > Ir > Ir a la carpeta** para buscar la carpeta en el buscador. Busque la carpeta que coincida con el nombre del proyecto. Dentro de esa carpeta, busque la carpeta que coincida con el `generated session id` de la compilación. Probablemente será la subcarpeta con el tiempo de modificación más reciente.

4.  Abra una nueva `Terminal.app` ventana.

5.  Tipo de `cd ` en la ventana de Terminal.app y, a continuación, arrastrar y colocar el `generated session id` carpeta, en la `Terminal.app` ventana:

    ![](modify-ipa-images/session-id-folder.png "Buscar la carpeta de Id. de sesión generada en Finder")

6.  Escriba la clave del valor devuelta para cambiar el directorio a la `generated session id` carpeta.

7.  Descomprima el `.ipa` archivo en un archivo temporal `old/` carpeta mediante el siguiente comando. Ajustar la `Ad-Hoc` y `iPhoneApp1` nombres según sea necesario para un proyecto concreto.

    > Ditto x - KB bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa anterior /

8.  Mantener la `Terminal.app` ventana abierta.

9.  Eliminar los archivos deseados de la `.ipa`. Puede moverlos a la Papelera mediante buscador o eliminarlos de la línea de comandos usando `Terminal.app`. Para ver el contenido de la `Payload/iPhone` archivo en Finder, Control y haga clic en el archivo y seleccione **mostrar contenido del paquete**.

10.  El mismo enfoque general como en el paso 3, busque el archivo de registro en `~/Library/Logs/Xamarin/MonoTouchVS/` que tiene el nombre de proyecto y la `generated session id` en el nombre: ![ ] (modify-ipa-images/build-log.png "ubicar el registro de compilación de proyecto en Finder")

11.  Abra el registro de compilación del paso 10, por ejemplo, haciendo doble clic en él.

12.  Busque la línea que incluya `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13.  Tipo de `/usr/bin/codesign ` en la ventana Terminal.app del paso 8.

14.  Copiar todos los argumentos a partir de `-v` desde la línea en el paso 12 y péguelos en la ventana de Terminal.app.

15.  Cambiar el último argumento para que sea el `.app` agrupación situado dentro de la `old/Payload/` carpeta y vuelva a ejecutar el comando.

```bash
/usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
```

16.  Cambiar a la `old/` directorio en Terminal:

```bash
cd old
```

17.  Comprimir el contenido del directorio en un nuevo `.ipa` archivo utilizando el `zip` comando. Puede cambiar la `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` argumento para generar la `.ipa` de archivos donde quiere que:

```bash
zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
```

## <a name="common-error-messages"></a>Mensajes de error comunes

Si ve `Invalid Signature. A sealed resource is missing or invalid.`, que generalmente significa que algo ha cambiado en el `.app` agrupación y que la `.app` agrupación no estaba correctamente volver a firmado posteriormente. Tenga en cuenta también que si desea crear un `.ipa` con un perfil de distribución, _debe_ compilar original `.ipa` con un perfil de distribución. En caso contrario, el `Entitlements.xcent` es incorrecto.

Para conceder a un ejemplo concreto de cómo puede producirse este error, si ejecuta el siguiente `codesign --verify` comando en la ventana de Terminal después del paso 9, verá el error junto con el motivo exacto del error:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

Y el proceso de comprobación de la tienda de aplicaciones va a notificar un mensaje de error similar:

> ERROR ITMS-90035: "firma no válida. Un recurso sellado es no válida o ausente. El archivo binario en la ruta de acceso [iPhoneApp1.app/iPhoneApp1] contiene una firma no válida. Asegúrese de que ha iniciado la aplicación con un certificado de distribución, no un certificado de ad hoc o un certificado de desarrollo. Compruebe que la configuración de firma de código en Xcode es correcta en el nivel de destino (que invalidan los valores en el nivel de proyecto). Además, asegúrese de que la agrupación que se va a cargar se compiló mediante un destino de la versión en Xcode, no un destino del simulador. Si está seguro de que es correcta la configuración de firma de código, elija "Limpia todo" en Xcode, eliminar el directorio "build" en la herramienta de búsqueda y volver a generar el versión de destino. Para obtener más información, consulte [https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
