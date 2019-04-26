---
title: ¿Es posible crear un archivo .xcarchive desde Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 952777b3178b82657c8f64d5882c532c188c7a79
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422027"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>¿Es posible crear un archivo .xcarchive desde Visual Studio?

## <a name="for-xamarin-4"></a>Para Xamarin 4

A partir de Xamarin 4.x, ahora es posible crear un `.xcarchive` desde Windows estableciendo el `ArchiveOnBuild` propiedad `true`. Por ejemplo, mediante `MSBuild` en la línea de comandos:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

El `.xcarchive` se colocarán en el `$HOME/Library/Developer/Xcode/Archives` directorio en el host de compilación de Mac Xcode y Xamarin Studio Buscar archivos de presentación compilado previamente.

Vea este [registrar los foros de Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) para algunos brief notas adicionales sobre el `ArchiveOnBuild` propiedad. Consulte la documentación sobre [compilaciones de línea de comandos de Xamarin.iOS en Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para obtener más detalles sobre la `ServerAddress` y `ServerUser` propiedades.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Para Xamarin 3 y versiones anteriores

La extensión de Visual Studio 3.x de Xamarin no proporciona un mecanismo para generar `.xcarchive` archiva. Dicho esto, la lógica utilizada para crear `.xcarchive` archiva en Xamarin Studio en Mac [se describe aquí](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), por lo que probablemente podría crear su propio `.xcarchive` manualmente si se deseaba.

Pero merece la pena tener en cuenta que no es necesario un `.xcarchive` para enviarlo a Store de la aplicación. Puede enviar un archivo IPA, siempre que se firmó con un perfil de distribución de App Store (no un perfil de distribución Ad Hoc).

De hecho, puede incluso comprima el `.app` agrupación (que está firmado con un perfil de distribución de App Store) y enviar esa `.zip` archivo a la tienda de aplicaciones.

En cualquier caso, puede usar la aplicación Application Loader para enviar la aplicación (en lugar de Xcode).

