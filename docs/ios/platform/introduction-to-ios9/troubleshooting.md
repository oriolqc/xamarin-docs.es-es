---
title: "Solución de problemas"
description: "Este artículo proporciona varias sugerencias de solución de problemas para trabajar con iOS 9 en aplicaciones de Xamarin.iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 6ea274ca198a4ca76a87198b02b6229e0e1c88b2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting"></a>Solución de problemas

_Este artículo proporciona varias sugerencias de solución de problemas para trabajar con iOS 9 en aplicaciones de Xamarin.iOS._

## <a name="there-was-a-problem-parsing-the-xml"></a>Se produjo un problema al analizar el XML

El Diseñador de iOS de Xamarin todavía no admite características de Xcode 7. Guiones gráficos no se cargará en el diseñador con _"Se produjo un problema al analizar el XML"_ al intentar usar el nuevo iOS 9 (Xcode 7) diseñadores elementos como StackView.

iOS compatibilidad con el Diseñador de características de Xcode 7 está pensado para la próxima versión de la característica de ciclo 6. La versión preliminar de ciclo 6 está disponible actualmente en el canal alfa y proporciona compatibilidad limitada para las nuevas características de Xcode 7.

Solución alternativa parcial para Visual Studio para Mac: haga clic en el guión gráfico y elija **abrir con** > **Xcode interfaz generador**.

## <a name="where-are-the-ios-8-simulators"></a>¿Dónde están los iOS 8 simuladores?

Si ha instalado Xcode 7 (o posterior) reemplazará automáticamente todos los simuladores de iOS 8 con simuladores de iOS 9 de forma predeterminada. Si desea ejecutar pruebas en iOS 8, se puede iniciar Xcode, a continuación, descargue e instale los simuladores de iOS 8.

En Xcode, seleccione la **Xcode** , a continuación, menú **preferencias...**   >  **Descarga**:

[ ![](troubleshooting-images/ios8.png "Descarga de simuladores de iOS 8")](troubleshooting-images/ios8.png)

Haga clic en el **comprobación e instalar ahora** botón para volver a instalar los simuladores de iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Restricción de diseño con errores de atributo izquierda/derecha

En iOS 8 (y versiones anteriores), los elementos de interfaz de usuario de guiones gráficos pudieron utilizar una combinación de ambos **derecha** & **izquierda** atributos (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) y  **A la izquierda** & **final** atributos (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) en el mismo diseño.

Si el mismo guión gráfico en el que se ejecuta en iOS 9, se producirá una excepción en el formato siguiente:

> Finaliza la aplicación debido a la excepción no detectada 'NSInvalidArgumentException', motivo: ' *** + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: no se puede realizar una restricción entre un delante/detrás atributo y un atributo de derecha a izquierda. Use delante/detrás de ambos o ninguno.'

iOS 9 exige diseños para que usen **derecha** & **izquierda** _o_ **iniciales**  &   **Finales** atributos pero *no* ambos. Para corregir este problema, cambie todas las restricciones de diseño para utilizar el mismo atributo establecido dentro de su archivo del guión gráfico.

Para obtener más información, consulte el [error de restricción de iOS 9](http://stackoverflow.com/questions/32692841/ios-9-constraint-error) discusión de desbordamiento de la pila.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>90535-ITMS de ERROR: Clave de CFBundleExecutable inesperada

Después de cambiar a iOS 9, desde una aplicación usa componentes de entidad 3rd (específicamente nuestro componente existente de Google Maps) que se compilan y se ejecutaban en iOS 8 (o anterior), al intentar enviar la nueva compilación en iTunes Connect puede obtener un error en el formulario:

> ERROR ITMS-90535: Clave de CFBundleExecutable inesperada. La agrupación en 'Payload/app-name.app/component.bundle' no contiene un ejecutable del paquete...

Este problemas normalmente se pueden resolver mediante la búsqueda de la agrupación con nombre en el proyecto y - igual que el mensaje de error sugiere - editar el `Info.plist` que se encuentra en la agrupación mediante la eliminación de la `CFBundleExecutable` clave. El `CFBundlePackageType` clave debe establecerse en `BNDL` así.

Después de realizar estos cambios, realice una limpieza y volver a generar todo el proyecto. Debe ser capaz de enviar a iTunes Connect sin ningún problema después de realizar estos cambios.

Para obtener más información, consulte la [desbordamiento de la pila](http://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) discusión.

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>Error en CFNetwork SSLHandshake (-9824)

Al intentar conectarse a internet, ya sea directamente o de una vista web de iOS 9, podría aparecer un error en el formulario:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

O bien, en el formulario:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

En iOS9, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación. Además, ATS requiere comunicación utilizando la `HTTPS` protocolo y comunicación de alto nivel API cifrarse con la versión 1.2 de TLS confidencialidad directa.

Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9 y OS X 10.11 (El capitán), todas las conexiones con `NSURLConnection`, `CFURL` o `NSURLSession` estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Vea la [Opting horizontal de ATS](~/ios/app-fundamentals/ats.md) sección de nuestro [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md) guía para obtener información sobre cómo resolver este problema.

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Mis aplicaciones existentes no se ejecutan en iOS 9

Consulte nuestro [información de compatibilidad de iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) para obtener instrucciones sobre volver a compilar y volver a implementar las aplicaciones existentes para que se ejecute en iOS 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView es Null en constructores

**Motivo:** en iOS 9 la `initWithFrame:` constructor está ahora necesario debido a cambios de comportamiento en iOS 9 como el [Estados de documentación de UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Si ha registrado una clase para el identificador especificado y se debe crear una nueva celda, la celda se ha inicializado mediante una llamada a su `initWithFrame:` método.

**Corrección:** agregar el `initWithFrame:` constructor similar al siguiente:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Relacionadas con ejemplos: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView produce un error Init con codificador al cargar una vista desde un Xib/Nib

**Motivo:** el `initWithCoder:` constructor es el que se llama cuando se carga una vista desde un archivo de interfaz generador Xib. Si este constructor no se exporta a código no administrado no puede llamar a nuestra versión administrada del mismo. Anteriormente (p. ej. en iOS 8) el `IntPtr` se invoca el constructor para inicializar la vista.

**Corrección:** crear y exportar el `initWithCoder:` constructor similar al siguiente:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Ejemplo relacionado: [Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Mensaje de Dyld: Ninguna imagen de caché con nombre...

Podría experimentar un bloqueo con la siguiente información en el registro:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** se trata de un error en el vinculador nativo de Apple, lo que sucede cuando realicen un marco de trabajo privada pública (JavaScriptCore se hizo público en iOS 7, antes que es un marco de trabajo privado), y el destino de implementación de la aplicación es para una versión de iOS cuando el Framework estaba privada. En este caso se vinculará el vinculador de Apple con la versión de framework en lugar de la versión pública privada.

**Corrección:** este problema se solucionará IOS 9, pero no hay una solución sencilla que se puede aplicar a usted mismo entretanto: simplemente como destino una versión posterior de iOS en su proyecto (puede intentar iOS 7 en este caso). Otros marcos de trabajo podrían tener problemas similares, por ejemplo el marco de trabajo de WebKit se hizo público en iOS 8 (y por lo que los destinatarios de iOS 7 se producirá este error; se debe tener como destino iOS 8 para usar WebKit en la aplicación).

## <a name="untrusted-enterprise-developer"></a>Desarrollador de la empresa no es de confianza

Al intentar ejecutar la versión de iOS 9 de la aplicación Xamarin.iOS en hardware real iOS, podría obtener un mensaje que indica que no se ha confiado en la cuenta de desarrollador en el dispositivo. Por ejemplo:

[ ![](troubleshooting-images/untrusted01.png "Alerta de desarrollador de la empresa no es de confianza")](troubleshooting-images/untrusted01.png)

Para solucionar este problema, realice lo siguiente:

1. Iniciar Xcode (la versión beta más reciente) en el desarrollo de Mac.
2. Seleccione **dispositivos** desde el **ventana** menú para abrir la ventana de dispositivos: 

    [ ![](troubleshooting-images/untrusted02.png "La ventana de dispositivos")](troubleshooting-images/untrusted02.png)
3. En el **dispositivos** del lado del panel, seleccione el dispositivo, el menú contextual y seleccione **Mostrar perfiles de aprovisionamiento...** : 

    [ ![](troubleshooting-images/untrusted03.png "Perfiles de aprovisionamiento de SShow")](troubleshooting-images/untrusted03.png)
4. Seleccione cada perfil de aprovisionamiento actualmente en el dispositivo y haga clic en el  **-**  botón para eliminarlo: 

    [ ![](troubleshooting-images/untrusted04.png "Eliminar un perfil de aprovisionamiento")](troubleshooting-images/untrusted04.png)
5. Desde el **Xcode** menú, seleccione **preferencias...**  y **cuentas**: 

    [ ![](troubleshooting-images/untrusted05.png "Preferencias de la cuenta de Xcode")](troubleshooting-images/untrusted05.png)
6. Haga clic en el **ver detalles...**  , a continuación, haga clic en el **todos los descargar** botón: 

    [ ![](troubleshooting-images/untrusted06.png "Descargar todos los perfiles")](troubleshooting-images/untrusted06.png)
7. Cuando ha terminado de actualizar la lista, haga clic en el **realiza** botón y cerrar la ventana de preferencias.
8. Quitar la versión existente de la aplicación Xamarin.iOS que estaba intentando probar desde el dispositivo iOS.
9. Vuelva a Visual Studio para Mac, realice una compilación limpia e intente volver a ejecutar la aplicación en el dispositivo.

Es posible que deba detener y reiniciar Visual Studio para Mac antes de que se ven los nuevos perfiles de aprovisionamiento cargados por Xcode. Es posible que también deba ajustar el **agrupación de firma de iOS** opciones para la aplicación de Xamarin.iOS seleccionar los nuevos perfiles de aprovisionamiento.

## <a name="launch-screen-issues"></a>Problemas de la pantalla de inicio

iOS 9 ahora cumple los requisitos de la pantalla de inicio para que ya no se puede reutilizar la misma imagen de inicio para admitir las orientaciones de la interfaz diferente. Vea de Apple [UILanchImage referencia](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) para obtener más información.

Si lo desea, puede usar un archivo de guión gráfico para presentar la pantalla de inicio de la aplicación en lugar de utilizar un conjunto de **.png** archivos de imagen. Esto es ahora Apple preferido hasta presentar pantallas de inicio. Visite nuestro [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) guía para obtener más información.

Por último, la aplicación debe utilizar un archivo de guión gráfico para su pantalla de inicio y admite todas las orientaciones de la interfaz cuatro (vertical, boca abajo vertical, horizontal derecho e izquierdo horizontal) para tener en cuenta para que se ejecuta en un panel de la diapositiva sobre o en modo de vista en dos paneles. Para obtener más información acerca de las nuevas capacidades de multitarea de iOS 9, consulte nuestro [multitarea para iPad](~/ios/platform/multitasking.md) guía.

## <a name="nsinternalinconsistencyexception-exception"></a>NSInternalInconsistencyException (excepción)

Al compilar y ejecutar una aplicación de Xamarin.iOS existente para iOS 9 podría aparecer un error en el formulario:

> Excepción de C de objetivo.  Nombre: NSInternalInconsistencyException razón: ventanas de la aplicación debe tener un controlador de vista de raíz al final de iniciar la aplicación

Se trata de error se produce porque Windows de la aplicación se esperan que tengan un controlador de vista de raíz al final de iniciar la aplicación y no de la aplicación existente.

Hay al menos dos posibles soluciones para este problema:

1. Aplicación de actualización para el uso de archivo del guión gráfico en lugar de `xib` archivos para definir la interfaz de usuario. Éste requiere una gran cantidad de tiempo según el tamaño de la aplicación y conocimientos sobre cómo usar el diseñador (o el generador de interfaz de Xcode) de iOS a guiones gráficos de diseño. Para obtener más información, consulte nuestro [Introducción a guiones gráficos unificado](~/ios/user-interface/storyboards/unified-storyboards.md) documentación.
2. El programa de instalación `RootViewController` propiedad de ventana de la aplicación en `FinishedLaunching` método `AppDelegate` clase para que señale a un controlador de vista en la interfaz de usuario de la aplicación.

## <a name="when-to-initialize-views-and-view-controllers"></a>Cuándo inicializar vistas y controladores de la vista

Con Xamarin.iOS es posible realizar la inicialización de la vista o View Controller dentro de los constructores que se llama cuando algo se expone al código administrado, pero divide el diseño de iOS.

En general debe no inicializar todo lo que puede devolver la llamada código Objective-C desde el constructor porque no puede estar seguro de cuándo se llamará. Que también significa que hay un mejores sitios (otros .ctor) o llamadas a invalidar (tal y como Objective-C no tiene ningún evento) donde se debe realizar esta inicialización.



## <a name="related-links"></a>Vínculos relacionados

- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [What's New en iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Actualizar las aplicaciones de Xamarin.iOS a iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
