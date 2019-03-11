---
title: 'Xamarin.iOS 9: solución de problemas'
description: En este artículo proporciona algunas sugerencias de solución de problemas para trabajar con iOS 9 en Xamarin.iOS. Sugerencias tratan sobre análisis de XML, simuladores, las restricciones de diseño, problemas de red y muchos otros temas.
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: f8fae79af654339b54a8df0d2ea32eef38f34adb
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668457"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin.iOS 9: solución de problemas

_En este artículo proporciona varias sugerencias de solución de problemas para trabajar con iOS 9 en aplicaciones de Xamarin.iOS._

## <a name="there-was-a-problem-parsing-the-xml"></a>Se ha producido un problema al analizar el XML

El Diseñador de iOS de Xamarin todavía no admite las características de Xcode 7. Guiones gráficos no se cargará en el diseñador con _"Se produjo un problema al analizar el XML"_ al intentar usar el nuevo iOS 9 (Xcode 7) como StackView los elementos del diseñador.

la próxima versión de la característica de ciclo 6 está destinado a las características de Xcode 7 con el Diseñador de iOS. La versión preliminar del ciclo de 6 actualmente está disponible en el canal alfa y tiene compatibilidad para las nuevas características de Xcode 7 limitada.

Solución parcial para Visual Studio para Mac: Haga clic en el guión gráfico y elija **abrir con** > **Interface Builder de Xcode**.

## <a name="where-are-the-ios-8-simulators"></a>¿Dónde están los iOS 8 simuladores?

Si ha instalado Xcode 7 (o superior) reemplazará automáticamente todos los simuladores de iOS 8 con simuladores de iOS 9 de forma predeterminada. Si desea probar en iOS 8, se puede iniciar Xcode, a continuación, descargue e instale los simuladores de iOS 8.

En Xcode, seleccione el **Xcode** , a continuación, menú **preferencias...**   >  **Descargas**:

[![](troubleshooting-images/ios8.png "Descargas de simuladores de iOS 8")](troubleshooting-images/ios8.png#lightbox)

Haga clic en el **comprobación e instalar ahora** botón para volver a instalar los simuladores de iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Restricción de diseño con errores de atributos de la izquierda y derecha

En iOS 8 (y anterior), los elementos de interfaz de usuario en los guiones gráficos podrían utilizar una combinación de ambas **derecha** & **izquierda** atributos (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) y  **Iniciales** & **final** atributos (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) en el mismo diseño.

Si el mismo guión gráfico en que se ejecuta en iOS 9, se producirá una excepción en el formato siguiente:

> Finaliza la aplicación debido a una excepción no detectada 'NSInvalidArgumentException', motivo: ' *** + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: No se puede realizar una restricción entre un atributo iniciales y finales y un atributo de derecha a izquierda. Use iniciales y finales para ambos o ninguno. "

iOS 9 impone los diseños para que usen **derecha** & **izquierda** _o_ **iniciales**  &   **Finales** atributos pero *no* ambos. Para corregir este problema, cambie todas las restricciones de diseño para usar el mismo atributo establecido en el archivo de guión gráfico.

Para obtener más información, consulte el [error de restricción de iOS 9](https://stackoverflow.com/questions/32692841/ios-9-constraint-error) debate de Stack Overflow.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>ERROR ITMS-90535: Clave CFBundleExecutable inesperada

Después de cambiar a iOS 9, desde una aplicación usa componentes de terceros 3rd (específicamente el componente existente de Google Maps) que compilan y ejecutan en iOS 8 (o anterior), al intentar enviar la nueva compilación en iTunes Connect puede aparecer un error en el formulario:

> ERROR ITMS-90535: Clave CFBundleExecutable inesperada. La agrupación en 'Payload/app-name.app/component.bundle' no contiene un ejecutable del paquete...

Este problemas normalmente se puede resolver mediante la búsqueda de la agrupación con nombre en el proyecto y - tal como sugiere el mensaje de error: puede editar el `Info.plist` que se encuentra en la agrupación mediante la eliminación de la `CFBundleExecutable` clave. El `CFBundlePackageType` clave debe establecerse en `BNDL` también.

Después de realizar estos cambios, realice una limpieza y volver a generar todo el proyecto. Debe ser capaz de enviar a iTunes Connect sin ningún problema después de realizar estos cambios.

Para obtener más información, consulte este [Stack Overflow](https://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) discusión.

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>Error en CFNetwork SSLHandshake (-9824)

Al intentar conectarse a internet, ya sea directamente o desde una vista web en iOS 9, podría obtener un error en el formulario:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

O en el formulario:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

En IOS 9, App Transport Security (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación. Además, ATS requiere comunicación utilizando la `HTTPS` protocolo y comunicación de alto nivel API a cifrarse con la versión 1.2 de TLS con confidencialidad directa.

Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10.11 (El capitán), todas las conexiones mediante `NSURLConnection`, `CFURL` o `NSURLSession` estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Consulte la [Opting horizontal de ATS](~/ios/app-fundamentals/ats.md) sección de nuestra [App Transport Security](~/ios/app-fundamentals/ats.md) guía para obtener información sobre cómo resolver este problema.

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Mis aplicaciones existentes no se ejecutan en iOS 9

Consulte nuestra [información de compatibilidad de iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) para obtener instrucciones sobre volver a compilar y volver a implementar las aplicaciones existentes ejecutar en iOS 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView es Null en constructores

**Motivo:** En iOS 9 el `initWithFrame:` constructor ahora es necesario debido a cambios de comportamiento en iOS 9 como el [UICollectionView Estados de documentación](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Si ha registrado una clase para el identificador especificado y se debe crear una nueva celda, la celda se ha inicializado mediante una llamada a su `initWithFrame:` método.

**Fix:** Agregar el `initWithFrame:` constructor como este:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Muestras relacionadas: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView produce un error Init con el codificador al cargar una vista desde un Xib/Nib

**Motivo:** El `initWithCoder:` constructor es lo que se llama cuando la carga de una vista desde un archivo Xib de generador de interfaz. Si este constructor no se exporta a código no administrado no puede llamar a nuestra versión administrada del mismo. Anteriormente (p ej. en iOS 8) el `IntPtr` se invoca el constructor para inicializar la vista.

**Fix:** Crear y exportar el `initWithCoder:` constructor como este:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Ejemplo relacionado: [Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Mensaje Dyld: Ninguna imagen de la memoria caché con nombre...

Puede experimentar un bloqueo con la siguiente información en el registro:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** Se trata de un error en el enlazador nativo de Apple, lo que sucede cuando los gobiernos realizan un marco de trabajo privada a pública (JavaScriptCore se hizo público en iOS 7, antes que era un marco de trabajo privado), y el destino de implementación de la aplicación es para una versión de iOS cuando el marco de trabajo era privado. En este caso se vinculará el vinculador de Apple con la versión de framework en lugar de la versión pública privada.

**Fix:** Esto se solucionará para iOS 9, pero no hay una solución sencilla que se puede aplicar a usted mismo mientras tanto: simplemente tener como destino una versión posterior de iOS en el proyecto (puede intentar iOS 7 en este caso). Otros marcos de trabajo pueden presentar problemas similares, por ejemplo el marco de trabajo de WebKit se hizo público en iOS 8 (y por lo que como destino iOS 7 se producirá este error; se debe tener como destino iOS 8 para usar WebKit en la aplicación).

## <a name="untrusted-enterprise-developer"></a>Desarrollador empresarial de confianza

Cuando se intenta ejecutar la versión de iOS 9 de la aplicación de Xamarin.iOS en hardware de iOS real, podría obtener un mensaje que indica que la cuenta de desarrollador no es de confianza en el dispositivo. Por ejemplo:

[![](troubleshooting-images/untrusted01.png "Alerta de desarrollador empresarial de confianza")](troubleshooting-images/untrusted01.png#lightbox)

Para solucionar este problema, realice lo siguiente:

1. Iniciar Xcode (la versión beta más reciente) en el desarrollo de Mac.
2. Seleccione **dispositivos** desde el **ventana** menú para abrir la ventana de dispositivos: 

    [![](troubleshooting-images/untrusted02.png "La ventana de dispositivos")](troubleshooting-images/untrusted02.png#lightbox)
3. En el **dispositivos** cara panel, seleccione el dispositivo, con el botón secundario y seleccione **Mostrar perfiles de aprovisionamiento...** : 

    [![](troubleshooting-images/untrusted03.png "Perfiles de aprovisionamiento SShow")](troubleshooting-images/untrusted03.png#lightbox)
4. Seleccione cada perfil de aprovisionamiento actualmente en el dispositivo y haga clic en el **-** botón para eliminarlo: 

    [![](troubleshooting-images/untrusted04.png "Eliminar un perfil de aprovisionamiento")](troubleshooting-images/untrusted04.png#lightbox)
5. Desde el **Xcode** menú, seleccione **preferencias...**  y **cuentas**: 

    [![](troubleshooting-images/untrusted05.png "Preferencias de cuenta de Xcode")](troubleshooting-images/untrusted05.png#lightbox)
6. Haga clic en el **ver detalles...**  botón y, después, haga clic en el **todos los descargar** botón: 

    [![](troubleshooting-images/untrusted06.png "Descargar todos los perfiles")](troubleshooting-images/untrusted06.png#lightbox)
7. Cuando ha terminado de actualizar la lista, haga clic en el **realiza** botón y cerrar la ventana de preferencias.
8. Quitar la versión existente de la aplicación de Xamarin.iOS que estaba intentando probar desde el dispositivo iOS.
9. Vuelva a Visual Studio para Mac, realice una compilación limpia e intente volver a ejecutar la aplicación en el dispositivo.

Es posible que deba detener y reiniciar Visual Studio para Mac antes de que se ven los nuevos perfiles de aprovisionamiento cargados por Xcode. También es posible que deba ajustar el **firma de lote de iOS** opciones para la aplicación de Xamarin.iOS seleccionar los perfiles de aprovisionamiento de nuevo.

## <a name="launch-screen-issues"></a>Problemas de la pantalla de inicio

iOS 9 ahora aplica los requisitos de la pantalla de inicio para que ya no se puede reutilizar la misma imagen de inicio para admitir las orientaciones de interfaz diferente. Consulte Apple [UILanchImage referencia](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) para obtener más información.

Si lo desea, puede usar un archivo de guión gráfico para presentar la pantalla de inicio de la aplicación en lugar de usar un conjunto de **.png** archivos de imagen. Esto es ahora Apple preferida del modo presentar las pantallas de inicio. Consulte nuestra [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) guía para obtener más información.

Por último, la aplicación debe usar un archivo de guión gráfico para su pantalla de inicio y admite todas las orientaciones de interfaz cuatro (vertical, al revés vertical, horizontal derecho e izquierdo horizontal) para considerarse para la que se ejecuta en un panel de diapositivas en o en modo de vista en dos paneles. Para obtener más información acerca de las nuevas capacidades de multitarea de iOS 9, consulte nuestra [multitarea para iPad](~/ios/platform/multitasking.md) guía.

## <a name="nsinternalinconsistencyexception-exception"></a>Excepción NSInternalInconsistencyException

Al compilar y ejecutar una aplicación existente de Xamarin.iOS para iOS 9 podría obtener un error en el formulario:

> Excepción de C de objetivo.  Nombre: NSInternalInconsistencyException motivo: Ventanas de la aplicación se esperan que tengan un controlador de vista raíz al final de la aplicación de inicio

Se trata de error se va a generar porque la aplicación de Windows se esperan que tengan un controlador de vista raíz al final del inicio de la aplicación y no de la aplicación existente.

Hay al menos dos posibles soluciones para este problema:

1. Actualizar la aplicación para usar el archivo de guión gráfico en lugar de `xib` archivos para definir su interfaz de usuario. Esto requiere una gran cantidad de tiempo según el tamaño de la aplicación y conocimientos sobre cómo usar el diseñador (o Interface Builder de Xcode) de iOS a guiones gráficos de diseño. Para obtener más información, consulte nuestra [Introducción a guiones gráficos unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentación.
2. El programa de instalación `RootViewController` propiedad de la ventana de la aplicación en `FinishedLaunching` método `AppDelegate` clase para que apunte a un controlador de vista en la interfaz de usuario de la aplicación.

## <a name="when-to-initialize-views-and-view-controllers"></a>Cuándo se debe inicializar vistas y controladores de vista

Es posible realizar la inicialización de la vista o controlador de vista dentro de los constructores que se llama cuando algo se expone al código administrado, pero interrumpe el diseño de iOS con Xamarin.iOS.

En general debería no inicializar todo lo que puede devolver la llamada código Objective-C desde el constructor ya que no puede estar seguro de cuándo se llamará. También significa que hay una mejor lugares (otros .ctor) o las llamadas a invalidar (tal y como Objective-C no tiene ningún evento) donde se debe realizar esta inicialización.

## <a name="related-links"></a>Vínculos relacionados

- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Actualizar las aplicaciones de Xamarin.iOS para IOS 9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
