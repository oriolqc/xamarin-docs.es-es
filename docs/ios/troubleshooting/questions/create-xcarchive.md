---
title: ¿Es posible crear un archivo .xcarchive desde Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 23af3bf277ab68ffe93df2e1ee8ee64afc01828d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>¿Es posible crear un archivo .xcarchive desde Visual Studio?

## <a name="for-xamarin-4"></a>Para Xamarin 4

A partir de Xamarin 4.x, ahora es posible crear un `.xcarchive` desde Windows estableciendo la `ArchiveOnBuild` propiedad `true`. Por ejemplo, mediante `MSBuild` en la línea de comandos:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

El `.xcarchive` se colocarán en el `$HOME/Library/Developer/Xcode/Archives` directorio en el host de compilación de Mac Xcode y Xamarin Studio Buscar en archivos de presentación compilado previamente.

Consulte esta [registrar foros Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) para algunos breve notas adicionales sobre la `ArchiveOnBuild` propiedad. Consulte la documentación sobre [compilaciones de línea de comandos de Xamarin.iOS en Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para obtener más detalles sobre la `ServerAddress` y `ServerUser` propiedades.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Para Xamarin 3 y versiones anteriores

La extensión de Visual Studio Xamarin 3.x no proporciona un mecanismo para generar `.xcarchive` archiva. Es decir, la lógica utilizada para crear `.xcarchive` archiva en Xamarin Studio en Mac [se describe aquí](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), por lo que probablemente podría crear su propio `.xcarchive` manualmente si se deseaba.

Pero cabe mencionar que no es necesario un `.xcarchive` y enviarlo a la tienda de aplicaciones. Puede enviar un archivo IPA mientras está firmado con un perfil de distribución de almacén de aplicación (no un perfil de distribución Ad Hoc).

De hecho, aunque solo código postal la `.app` agrupación (que está firmado con un perfil de distribución de almacén de aplicación) y enviar esa `.zip` archivo a la tienda de aplicaciones.

En cualquier caso, puede usar la aplicación de cargador de la aplicación para enviar la aplicación (en lugar de Xcode).

