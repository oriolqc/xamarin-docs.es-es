---
title: Guía de la autenticación de huellas digitales
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 2b66c3660f6d8af9217089a7615784957fcc6ed7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="fingerprint-authentication-guidance"></a>Guía de la autenticación de huellas digitales

## <a name="fingerprint-authentication-guidance"></a>Guía de la autenticación de huellas digitales

Ahora que hemos visto los conceptos y la autenticación de huellas digitales de las API que rodean Android 6.0, veamos algunos consejos generales para el uso de las API de huellas digitales.

1. **Usar las API de compatibilidad de la biblioteca de compatibilidad con Android v4** &ndash; Esto simplifica el código de aplicación mediante la eliminación de la comprobación de la API desde el código y permitir que una aplicación para el máximo posible de dispositivos de destino.
2. **Proporcionan alternativas para la autenticación de huellas dactilares** &ndash; autenticación de huellas dactilares es una manera estupenda y rápida de una aplicación autenticar un usuario, sin embargo, no se supone que siempre funcionan o estar disponible. Es posible que el escáner de huellas digitales puede producir un error, la lente quizás desfasadas, el usuario no ha configurado el dispositivo para utilizar la autenticación de huellas digitales, o las huellas digitales desde entonces han desaparecido. También es posible que el usuario que no desee usar la autenticación de huellas digitales con la aplicación. Por estos motivos, una aplicación de Android debe proporcionar un proceso de autenticación alternativo como nombre de usuario y contraseña.
3. **Utilice el icono de huella digital de Google** &ndash; todas las aplicaciones deben usar el mismo icono de huella digital proporcionado por Google. El uso de un icono estándar facilita a los usuarios de Android para que reconozca donde se utiliza la autenticación mediante huellas digitales en las aplicaciones: 
    
    ![Icono de huella digital de Android](summary-images/ic-fp-40px.png)
    
4. **Notificar al usuario** &ndash; una aplicación debe mostrar algún tipo de notificación al usuario que el escáner de huellas digitales está activo y esperando una entrada táctil o Deslizar rápidamente. 

## <a name="summary"></a>Resumen

Autenticación de huellas dactilares es una excelente manera de permitir que una aplicación Xamarin.Android comprobar rápidamente si los usuarios, lo que facilita a los usuarios interactuar con características confidenciales, como compras en la aplicación. Esta guía describe los conceptos y el código necesario para incorporar la huella digital de Android 6.0 de la API de la aplicación Xamarin.Android.

En primer lugar se explicó la huella digital de la API por sí mismos, `FingerprintManager` (y `FingerprintManagerCompat`). Se examina cómo `FingerprintManager.AuthenticationCallbacks` clase abstracta debe extender una aplicación y se utiliza como un intermediario entre el hardware de huellas digitales y la propia aplicación. A continuación, se examina cómo comprobar la integridad de los resultados de escáner de huellas digitales con un Java `Cipher` objeto. Por último, analizamos un poco en las pruebas de forma que se describe cómo inscribir una huella digital en un dispositivo y utilizar **adb** para simular un deslice el dedo huellas digitales en un emulador. 

Si aún no lo ha hecho, debe mirar la [aplicación de ejemplo](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) que muestra en esta guía. El [ejemplo de cuadro de diálogo de huellas digitales](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/) ha sido trasladada de Java a Xamarin.Android y proporciona otro ejemplo sobre cómo agregar la autenticación de huellas digitales para una aplicación Android.



## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de guía de huellas digitales](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Ejemplo de cuadro de diálogo de huellas digitales](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Icono de huellas digitales](https://developer.android.comhttps://developer.xamarin.com/samples/FingerprintDialog/res/drawable-hdpi/ic_fp_40px.html)
