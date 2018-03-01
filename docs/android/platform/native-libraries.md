---
title: Uso de bibliotecas nativas
ms.topic: article
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.openlocfilehash: 8d7e03582571939b8cd3ae89fc2deff3b5603d36
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="using-native-libraries"></a>Uso de bibliotecas nativas

Xamarin.Android admite el uso de bibliotecas nativas a través del mecanismo de PInvoke estándar. También puede agrupar las bibliotecas nativas adicionales que no forman parte del sistema operativo en su .apk.

Para implementar una biblioteca nativa con una aplicación de Xamarin.Android, agregue la biblioteca binaria para el proyecto y establezca su **acción de compilación** a **AndroidNativeLibrary**.

Para implementar una biblioteca nativa con un proyecto de biblioteca de Xamarin.Android, agregue la biblioteca binaria para el proyecto y establezca su **acción de compilación** a **EmbeddedNativeLibrary**.

Tenga en cuenta que puesto que Android admite varias Interfaces de binarios de aplicación (ABIs), Xamarin.Android debe saber qué ABI creada para la biblioteca nativa.
Hay dos maneras de ello, puede:

1.  Ruta de acceso "examen"
1.  Mediante el uso de un `AndroidNativeLibrary/Abi` elemento dentro del archivo de proyecto


Con el examen de ruta de acceso, el nombre del directorio principal de la biblioteca nativa se utiliza para especificar la ABI que los destinos de la biblioteca. Por lo tanto, si agrega `lib/armeabi/libfoo.so` al proyecto, a continuación, la ABI se se "examinan" como `armeabi`.

Como alternativa, puede editar el archivo de proyecto para especificar explícitamente la ABI para usar:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Para obtener más información sobre el uso de bibliotecas nativas, vea [interoperabilidad con bibliotecas nativas](http://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio-2015"></a>Depurar código nativo con Visual Studio 2015

Si usas *Visual Studio 2015*, no tiene que modificar los archivos de proyecto (tal y como se ha descrito anteriormente).
Puede compilar y depurar C++ dentro de la solución Xamarin.Android, agregando una referencia de proyecto de C++ **biblioteca dinámica compartida (Android)** proyecto.

Los desarrolladores de Visual Studio C++ pueden ver el [SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/) de ejemplo para probar la depuración de C++ en Visual Studio 2015 con Xamarin; y hacer referencia a nuestro [entrada de blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) para obtener más información.



## <a name="related-links"></a>Vínculos relacionados

- [SanAngeles_NativeDebug (ejemplo)](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
