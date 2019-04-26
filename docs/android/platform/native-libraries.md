---
title: Uso de bibliotecas nativas
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 1b0771a0ccc2597ebd800468b82044e4020d9d94
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153321"
---
# <a name="using-native-libraries"></a>Uso de bibliotecas nativas

Xamarin.Android admite el uso de bibliotecas nativas a través del mecanismo estándar de PInvoke. También puede agrupar las bibliotecas nativas adicionales que no forman parte del sistema operativo en el archivo .apk.

Para implementar una biblioteca nativa con una aplicación de Xamarin.Android, agregue la biblioteca de binaria al proyecto y establezca su **acción de compilación** a **AndroidNativeLibrary**.

Para implementar una biblioteca nativa con un proyecto de biblioteca de Xamarin.Android, agregue la biblioteca de binaria al proyecto y establezca su **acción de compilación** a **EmbeddedNativeLibrary**.

Tenga en cuenta que puesto que Android admite varias Interfaces binarias de aplicación (ABI), Xamarin.Android debe saber para qué ABI se compila la biblioteca nativa de.
Hay dos maneras de hacerlo:

1.  Ruta de acceso "examen"
1.  Mediante el uso de un `AndroidNativeLibrary/Abi` elemento dentro del archivo de proyecto


Con el examen de la ruta de acceso, el nombre del directorio principal de la biblioteca nativa se utiliza para especificar la ABI a la que se dirige la biblioteca. Por lo tanto, si agrega `lib/armeabi/libfoo.so` al proyecto, a continuación, la ABI se se "examinan" como `armeabi`.

Como alternativa, puede editar el archivo de proyecto para especificar explícitamente la ABI para usar:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Para obtener más información sobre el uso de bibliotecas nativas, consulte [interoperabilidad con las bibliotecas nativas](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Depurar código nativo con Visual Studio

Si usas *2019 de Visual Studio* o *Visual Studio 2017*, no tendrá que modificar los archivos de proyecto como se describió anteriormente.
Puede compilar y depurar C++ dentro de la solución de Xamarin.Android mediante la adición de una referencia de proyecto en C++ **biblioteca dinámica compartida (Android)** proyecto.

Para depurar código nativo de C++ en el proyecto, siga estos pasos:

1. Haga doble clic en el proyecto **propiedades** y seleccione el **opciones de Android** página.
2. Desplácese hacia abajo hasta **opciones de depuración**.
3. En el **depurador** menú desplegable, seleccione **C++** (en lugar del predeterminado **.Net (Xamarin)**).

Visual Studio C++ los desarrolladores pueden ver el [SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/) ejemplo intentar depurar C++ de 2019 de Visual Studio o Visual Studio 2017 con Xamarin; y hacer referencia a nuestro [entrada de blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) para obtener más información información.



## <a name="related-links"></a>Vínculos relacionados

- [SanAngeles_NativeDebug (sample)](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
- [Desarrollo de aplicaciones nativas de Xamarin Android](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
