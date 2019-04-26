---
title: Orientación de la autenticación de huella digital
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 535eabe07cb4f4d36e6a6f918b5717efcc99185d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023553"
---
# <a name="fingerprint-authentication-guidance"></a>Orientación de la autenticación de huella digital

## <a name="fingerprint-authentication-guidance"></a>Orientación de la autenticación de huella digital

Ahora que hemos visto los conceptos y la autenticación de huella digital de las API que rodean a Android 6.0, vamos a tratar algunos consejos generales para el uso de la API de huellas digitales.

1. **Usar las API de compatibilidad de biblioteca de compatibilidad de Android v4** &ndash; Esto simplifica el código de aplicación desactivando la casilla de la API desde el código y permitir que una aplicación para el máximo posible de los dispositivos de destino.
2. **Proporcionan alternativas para la autenticación con huella digital** &ndash; autenticación con huella digital es una manera excelente y rápida para una aplicación autenticar un usuario, sin embargo, no se puede suponer que siempre funcionan o estar disponible. Es posible que el escáner de huella digital puede producir un error, la lente quizás ser sucio, el usuario no ha configurado el dispositivo para usar la autenticación con huella digital o las huellas digitales ya que han desaparecido. También es posible que el usuario no puede utilizar la autenticación con huella digital con la aplicación. Por estas razones, una aplicación Android debe proporcionar un proceso de autenticación alternativo, como el nombre de usuario y contraseña.
3. **Utilice el icono de huella digital de Google** &ndash; todas las aplicaciones deben usar el mismo icono de huella digital proporcionado por Google. El uso de un icono estándar facilita a los usuarios de Android reconocer dónde se usa autenticación con huella digital en las aplicaciones: 
    
    ![Icono de huella digital de Android](summary-images/ic-fp-40px.png)
    
4. **Notificar al usuario** &ndash; una aplicación debe mostrar algún tipo de notificación al usuario que el escáner de huella digital está activo y en espera de un toque o pasar el dedo. 

## <a name="summary"></a>Resumen

Autenticación con huella digital es una excelente manera de permitir que una aplicación de Xamarin.Android comprobar rápidamente a los usuarios, lo que facilita a los usuarios interactuar con características confidenciales, como compras en la aplicación. Esta guía describe los conceptos y el código que es necesario para incorporar la huella digital de Android 6.0 de la API en su aplicación de Xamarin.Android.

Primero analizamos la huella digital de la API por sí mismos, `FingerprintManager` (y `FingerprintManagerCompat`). Hemos visto cómo la `FingerprintManager.AuthenticationCallbacks` clase abstracta debe ser extendida por una aplicación y sirve de intermediario entre el hardware de huella digital y la propia aplicación. A continuación, hemos visto cómo comprobar la integridad de los resultados de escáner de huellas digitales con un Java `Cipher` objeto. Por último, analizamos un poco en las pruebas que se describe cómo inscribir una huella digital en un dispositivo y utilizando **adb** para simular un deslizamiento de huellas digitales en un emulador. 

Si aún no lo ha hecho, debe mirar el [aplicación de ejemplo](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) que acompaña a esta guía. El [ejemplo de cuadro de diálogo de huellas digitales](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/) se ha trasladado desde Java xamarin.Android y proporciona otro ejemplo sobre cómo agregar autenticación con huella digital a una aplicación Android.



## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de guía de huellas digitales](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Ejemplo de cuadro de diálogo de huellas digitales](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Icono de huella digital](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
