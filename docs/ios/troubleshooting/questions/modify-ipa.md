---
title: ¿Puedo agregar archivos a o quitar archivos de un archivo IPA después de crearlo en Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: bf135755f64e4d17db2c187d58572c525dfee559
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420897"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>¿Puedo agregar archivos a o quitar archivos de un archivo IPA después de crearlo en Visual Studio?

Sí, es posible, pero normalmente será necesario que vuelva a firmar el `.app` lote después de realizar el cambio.

Tenga en cuenta que al modificar el `.ipa` archivo no es necesario en el uso normal. En este artículo se proporciona únicamente con fines informativos.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Ejemplo: quitar un archivo desde un `.ipa` archive

Este ejemplo se supone que es el nombre del proyecto de Xamarin.iOS `iPhoneApp1` y `generated session id` es `cc530d20d6b19da63f6f1c6f67a0a254`

1.  Compilar el `.ipa` de archivos con normalidad desde Visual Studio.

2.  Cambiar el host de compilación de Mac.

3.  Busque la compilación en el `~/Library/Caches/Xamarin/mtbs/builds` carpeta. Puede pegar esta ruta de acceso en **buscador > Ir > vaya a la carpeta** para buscar la carpeta en el buscador. Busque la carpeta que coincide con el nombre del proyecto. Dentro de esa carpeta, busque la carpeta que coincide con el `generated session id` de la compilación. Esto será más probable es que la subcarpeta que contenga la hora de modificación más reciente.

4.  Abra una nueva `Terminal.app` ventana.

5.  Tipo `cd ` en la ventana Terminal.app y, a continuación, arrastrar y colocar la `generated session id` carpeta en el `Terminal.app` ventana:

    ![](modify-ipa-images/session-id-folder.png "Buscar la carpeta de Id. de sesión generada en Finder")

6.  Escriba la clave del valor devuelta para cambiar el directorio a la `generated session id` carpeta.

7.  Descomprima el `.ipa` archivo en un archivo temporal `old/` carpeta mediante el siguiente comando. Ajustar el `Ad-Hoc` y `iPhoneApp1` nombres según sea necesario para su proyecto específico.

    > lo mismo que bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa - xk antiguo /

8.  Mantener la `Terminal.app` abierta la ventana.

9.  Elimine los archivos deseados de la `.ipa`. Puede moverlos a la Papelera con Finder o eliminarlos de la línea de comandos usando `Terminal.app`. Para ver el contenido de la `Payload/iPhone` archivo en el Finder, Control y haga clic en el archivo y seleccione **mostrar contenido del paquete**.

10.  Con el mismo enfoque general como en el paso 3, busque el archivo de registro en `~/Library/Logs/Xamarin/MonoTouchVS/` que tiene el nombre de proyecto y el `generated session id` en el nombre: ![](modify-ipa-images/build-log.png "Busque el registro de compilación del proyecto en el Finder")

11.  Abra el registro de compilación del paso 10, por ejemplo, haciendo doble clic en él.

12.  Busque la línea que incluye `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13.  Tipo `/usr/bin/codesign ` en la ventana Terminal.app del paso 8.

14.  Copiar todos los argumentos a partir de `-v` desde la línea en el paso 12 y péguelos en la ventana Terminal.app.

15.  Cambiar el último argumento para que sea el `.app` agrupación ubicados dentro de la `old/Payload/` carpeta y, a continuación, ejecute el comando.

```bash
/usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
```

16.  Cambie a la `old/` directorio en Terminal:

```bash
cd old
```

17.  Comprima el contenido del directorio en un nuevo `.ipa` archivo empleando el `zip` comando. Puede cambiar el `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` argumento para generar el `.ipa` archivo donde desee:

```bash
zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
```

## <a name="common-error-messages"></a>Mensajes de error comunes

Si ve `Invalid Signature. A sealed resource is missing or invalid.`, que generalmente significa que algo ha cambiado en el `.app` agrupación y que la `.app` agrupación no fue volver a firmado correctamente después. Tenga en cuenta también que si desea crear un `.ipa` con un perfil de distribución, _debe_ compilar original `.ipa` con un perfil de distribución. De lo contrario el `Entitlements.xcent` será incorrecta.

Para dar un ejemplo concreto de cómo puede producirse este error, si ejecuta la siguiente `codesign --verify` comando en la ventana de Terminal después del paso 9, verá el error junto con el motivo exacto del error:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

Y el proceso de comprobación de App Store informará de un mensaje de error similar:

> ERROR ITMS-90035: "Firma no válida. Un recurso sealed es falta o no es válido. El archivo binario en la ruta de acceso [iPhoneApp1.app/iPhoneApp1] contiene una firma no válida. Asegúrese de que haya iniciado la aplicación con un certificado de distribución, no un certificado de ad hoc o un certificado de desarrollo. Compruebe que la configuración de firma de código en Xcode es correcta en el nivel de destino (que invalidan los valores en el nivel de proyecto). Además, asegúrese de que la agrupación que se va a cargar se creó mediante un destino de la versión de Xcode, no un destino del simulador. Si está seguro de que la configuración de firma de código es correcta, elija "Limpiar todo" en Xcode, elimine el directorio "compilación" en el Finder y volver a generar la versión de destino. Para obtener más información, consulte [ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
