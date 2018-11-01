---
title: Sugerencias para solucionar problemas de Xamarin.iOS
description: Este documento proporciona algunas sugerencias útiles para solucionar problemas durante el desarrollo de aplicaciones de Xamarin.iOS. Describe los mensajes de error específicos, así como otros posibles problemas.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2018
ms.openlocfilehash: 80f37e1753362ec0503b70e49a2206684c0fedb5
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675333"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Sugerencias para solucionar problemas de Xamarin.iOS 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin.iOS no se puede resolver System.ValueTuple

Este error se produce debido a una incompatibilidad con Visual Studio.

- **Visual Studio 2017 Update 1** (versión 15.1 o versiones anterior) sólo es compatible con **System.ValueTuple NuGet 4.3.0** (o anterior).

- **Visual Studio 2017 Update 2** (versión 15.2 o posterior) solo es compatible con la **System.ValueTuple NuGet 4.3.1** o versiones más recientes.

Elija el paquete System.ValueTuple NuGet correcto que se corresponde con la instalación de Visual Studio 2017.


## <a name="receiving-error-retrieving-update-information-error-message"></a>Recibir el mensaje de Error 'Error al recuperar información de actualización'

Al intentar actualizar el software y este mensaje de error aparece, vuelva a intentar reiniciar el IDE y el cierre de sesión y, de nuevo, en su cuenta en el IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>¿Cómo creo acciones o las salidas con Interface Builder?

Con la introducción del Diseñador de Xamarin para iOS en Visual Studio para Mac y Visual studio, Xamarin.iOS a los desarrolladores ahora pueden aprovechar las ventajas de la creación de una interfaz de usuario a través de los guiones gráficos y .xibs. Hacer referencia a la [Hello, iOS](~/ios/get-started/hello-ios/index.md) guías para obtener más información acerca de cómo utilizar el diseñador.

También puede hacer referencia a Apple [toma](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) y [acciones](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) guías para obtener más información sobre el uso de salidas y acciones en IB.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding produce una excepción NotSupportedException

Puede que esté usando una codificación no se agrega de forma predeterminada. Compruebe el [internacionalización](~/ios/app-fundamentals/localization/index.md) página para obtener información sobre cómo agregar compatibilidad con la codificación más.

## <a name="systemmissingmethodexception-anything-else"></a>System.MissingMethodException (cualquier otra cosa)

El miembro es probable que se ha quitado el vinculador y, por tanto, no existe en el ensamblado en tiempo de ejecución.  Existen varias soluciones a este:

-  Agregar el [[conservar]](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) al miembro de atributo.  Esto impedirá que el vinculador quitarlo.
-  Al invocar [mtouch](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29) , utilice el **- nolink** o **- linksdkonly** opciones. -    **-nolink** deshabilita todos los vínculos.
-    **-linksdkonly** vinculará solo los ensamblados siempre Xamarin.iOS, como *monotouch.dll* o xamarin.ios.dll.

Tenga en cuenta que los ensamblados están vinculados para que el archivo ejecutable resultante es más pequeño; por lo tanto, al deshabilitar la vinculación puede producir un archivo ejecutable mayor que es deseable.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Obtiene un ModelNotImplementedException

Si recibe esta excepción, esto significa que está llamando a base. Método () en una clase que invalida un modelo. No es necesario llamar al método base en una clase para los modelos (son clases que se marcan con el atributo [modelo]).

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>Esta clase no es un valor de clave de codificación compatible para la clave XXXX

Si recibe este error al cargar un archivo NIB significa que el valor que XXXX no se encontró en la clase administrada. Esto significa que falta una declaración similar al siguiente:

```csharp
[Connect]
TypeName XXXX {
   get {
       return (TypeName) GetNativeField ("XXXX");
   }
   set {
       SetNativeField ("XXXX", value);
   }
}
```

La definición anterior se genera automáticamente por Visual Studio para Mac para los archivos XIB que se agregan a Visual Studio para Mac en el `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` archivo.

Además, los tipos que contiene el código anterior deben ser una subclase de [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/).  Si el tipo contenedor es dentro de un espacio de nombres, también debe tener un [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo que proporciona un nombre de tipo sin un espacio de nombres (como el generador de interfaz no es compatible con espacios de nombres de tipos):

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Clase desconocida XXXX en el archivo de Interface Builder

Este error se genera si se define una clase en los archivos del generador de interfaz, pero no proporciona la implementación real para él en su C# código.

Es preciso agregar código similar al siguiente:

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System.MissingMethodException: Encontró ningún constructor para Foo.Bar::ctor(System.IntPtr)

Este error se produce en tiempo de ejecución cuando el código intenta crear una instancia de las clases que hace referencia desde el archivo de Interface Builder. Esto significa que olvidó agregar un constructor que toma un IntPtr único como parámetro.

El constructor con un identificador IntPtr se usa para enlazar los objetos administrados por sus representaciones no administradas.

Para solucionar este problema, agregue la siguiente línea de código a la clase Foo.Bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>El tipo {Foo} no contiene una definición para `GetNativeField' and no extension method `GetNativeField' de tipo se encontró {Foo}

Si recibe este error en los archivos generados diseñadores (*. xib.designer.cs), significa que uno de dos cosas:

 **(1) falta una clase parcial o clase base**

Las clases parciales generado por el diseñador deben tener clases parciales correspondientes en el código de usuario que heredan de alguna subclase de `NSObject`, a menudo `UIViewController`. Asegúrese de que tiene esta clase para el tipo que es dar el error.

 **2) los espacios de nombres predeterminado de cambiado**

Los archivos de diseñador se generan mediante la configuración de espacio de nombres predeterminado del proyecto. Si ha cambiado esta configuración, o cambiar el nombre del proyecto, las clases parciales generadas ya no pueden estar en el mismo espacio de nombres que sus equivalentes de código de usuario.

Configuración de Namespace puede encontrarse en el cuadro de diálogo Opciones del proyecto. El espacio de nombres predeterminado se encuentra en la **General -> configuración de Main** sección. Si está en blanco, se usa el nombre del proyecto como el valor predeterminado. Configuración de espacio de nombres más avanzada puede encontrarse en el **código fuente -> directivas de nomenclatura .NET** sección.

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>Para las acciones de advertencia: el método privado 'Foo' nunca se utiliza. (CS0169)

Acciones para los archivos del generador de interfaz se conectan a los widgets mediante la reflexión en tiempo de ejecución, por lo que se espera que esta advertencia.

Puede usar "advertencia #pragma deshabilita 0169" "Habilitar la advertencia #pragma 0169" alrededor de las acciones si desea suprimir esta advertencia solo para estos métodos, o agregar 0169 al campo "Ignorar advertencias" en las opciones del compilador si desea deshabilitarlo para todo el proyecto (no se recomienda).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>no se pudo mtouch con el siguiente mensaje: no se puede abrir el ensamblado ' / path/to/yourproject.exe'

Si ve este mensaje de error, por lo general el problema es que la ruta de acceso absoluta al proyecto contiene un espacio. Esto se solucionará en una versión futura de Xamarin.iOS, pero puede solucionar el problema, mueva el proyecto a una carpeta sin espacios en blanco.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>La versión de sqlite3 es antigua: actualice al menos v3.5.0!

Esto ocurre cuando realiza todos los elementos siguientes:

1.  Usar Mono.Data.Sqlite
1.  Usar Mac OS X Leopard (10.5)
1.  Ejecute la aplicación en el simulador.


El problema es que Mono recoge el OS X `libsqlite3.dylib`, del no el iPhoneSimulator `libsqlite3.dylib` archivo. La aplicación *le* profesional en el dispositivo, pero no el simulador.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>Implementar en el dispositivo se produce un error con la System.Exception: AMDeviceInstallApplication devuelve 3892346901

Este error significa que la configuración de firma de código, su certificado/identificador de paquete no coincide con el perfil de aprovisionamiento instalado en el dispositivo.  Confirmar tiene el certificado adecuado seleccionado en Opciones de proyecto -> firma de lote de iPhone y el identificador de paquete correcta especificado en las opciones de proyecto -> aplicación de iPhone

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>Finalización de código no funciona en Visual Studio para Mac

Asegúrese de que utiliza la versión más reciente de Visual Studio para Mac y Xamarin.iOS

Si el problema persiste, póngase [archivar un error](http://monodevelop.com/Developers#Reporting_Bugs), adjuntar el **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP} .log**, y **componentes-{TIMESTAMP} .log** los archivos de registro.

Si todo lo demás falla, puede intentar quitar la caché de la finalización de código para que se vuelve a generar:

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Tenga cuidado de que se escribe este comando correctamente o que accidentalmente podría quitar los archivos importantes.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>Visual Studio para Mac se bloquea al copiar texto

Las utilidades de Mac populares QuickSilver, la barra de herramientas de Google y la barra de inicio tienen características de Portapapeles que Visual Studio con daños en la memoria del equipo Mac. En las opciones, puede enumerar Visual Studio para Mac como un proceso que no deben interferir con.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio para Mac se queja sobre 2.4 Mono necesarios

Si ha actualizado Visual Studio para Mac debido a una actualización reciente y, al intentar iniciarlo nuevo se queja sobre 2.4 Mono no está presente, lo único que debe hacer es [actualizar la instalación de Mono 2.4](http://www.go-mono.com/mono-downloads/download.html).  

Mono 2.4.2.3_6 corrige algunos problemas importantes que impidieron que Visual Studio para Mac se ejecuten de forma confiable, a veces está bloqueada de Visual Studio para Mac durante el inicio o impide que la base de datos de finalización de código que se está generando.

Una vez que instale el nuevo Mono, Visual Studio para Mac iniciará según lo previsto.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Aserción en... /.. /.. /.. /mono/Metadata/Generic-Sharing.c:704, condición 'oti' no cumplido

Si recibe el seguimiento de la pila siguiente:

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Esto significa que está vinculando una biblioteca estática que se compilan con código thumb en el proyecto. A partir de la versión del SDK de iPhone 3.1 (o superior en el momento de redactar este artículo) Apple introdujo un error en el vinculador al vincular código no Thumb (Xamarin.iOS) con código Thumb (la biblioteca estática). Debe vincular con una versión de control de posición que no son de la biblioteca estática para mitigar este problema.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System.ExecutionEngineException: Intentando () Foo[]:System.Collections.Generic.ICollection'1.get_Count de método (el contenedor administrado a administrado) de compilación JIT

El sufijo [] indica que usted o la biblioteca de clases llama a un método en una matriz a través de una colección genérica, como IEnumerable <>, ICollection <> o <> IList. Como alternativa, puede forzar explícitamente el compilador AOT para incluir este tipo de método mediante una llamada al método y asegurándose de se ejecuta este código antes de la llamada que desencadenó la excepción. En este caso, podría escribir:

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Que forzará al compilador AOT que incluya el método get_Count.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio para el editor de código fuente de Mac es muy lento

A veces, el editor Visual Studio para Mac origen pasa a ser muy lento, aparecen deje de responder durante varios segundos entre escribir caracteres.

Este problema es muy poco frecuente y muy difíciles de reproducir, normalmente no se puede reproducir en el mismo equipo después de reiniciar Visual Studio para Mac. Por este motivo, agradecemos si pudiera realizar varios pasos de depuración antes de reiniciar Visual Studio para Mac y nos envía los resultados.

1.  Intente cerrar la pestaña del editor y vuelva a abrirlo. ¿Se tarda un poco de edición o mover el símbolo de intercalación hasta la reducción ocurre de nuevo?
1.  Deshabilitar la "sincronización de carretera de mediante la herramienta de desarrollador"Quartz Debug"(que puede encontrar mediante Spotlight) y compruebe si el rendimiento del editor de origen se restaura al estado normal.
1.  Intente repetir el paso (1) con la sincronización de carretera sigue deshabilitada.
1.  Si el editor se bloquea durante más de unos segundos, intente ejecutar "killall-salir [Visual Studio para Mac]" en un terminal, mientras que se ha bloqueado. Puede ser difícil para el comando kill ocurrir mientras se ha bloqueado el editor, pero es esencial para hacerlo, porque el comando obliga a Mono para escribir los seguimientos de pila de todos los subprocesos en el registro de MD, que podemos usar para detectar qué estado de los subprocesos están en mientras se ha bloqueado la XS de tiempo.



Adjunte los registros XS, **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP} .log**, y **componentes-{TIMESTAMP} .log**(en versiones anteriores de MonoDevelop/XS, simplemente enviar **~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**).

 **Nota: Se ha corregido el problema anterior en XS 2.2 Final**

## <a name="compiled-application-is-very-large"></a>Aplicación compilada es muy grande

Para admitir la depuración, las compilaciones de depuración contienen código adicional. Los proyectos compilados en modo de lanzamiento son una fracción del tamaño.

A partir de Xamarin.iOS 1.3 debug compilaciones incluyen compatibilidad de depuración para cada componente de Mono (todos los métodos de todas las clases de los marcos de trabajo).  

Con Xamarin.iOS 1.4 se presentará un método más específico para la depuración, el valor predeterminado será solo proporciona instrumentación de depuración para el código y las bibliotecas y no lo hace para todos los [ensamblados Mono](~/cross-platform/internals/available-assemblies.md) (Esto sigue es posible, pero tendrá que participar en los ensamblados de depuración).

## <a name="installation-hangs"></a>Se bloquea la instalación

Si tiene la ejecución del simulador de iPhone de bloqueo instaladores Mono y Xamarin.iOS. Este problema no se limita a Mono o Xamarin.iOS, se trata de un problema de coherencia a través de cualquier software que intenta instalar software en MacOS nieve Leopard si iPhone simulador se está ejecutando en tiempo de instalación.

Asegúrese de que salga del simulador de iPhone y vuelva a intentar la instalación.

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>Se ha quedado sin camas elásticas de tipo 0

Si recibe este mensaje mientras se ejecuta el dispositivo, puede crear más de tipo 0 camas elásticas (tipo específico) mediante la modificación de la sección "iPhone compilación" de opciones de proyecto.  Desea agregar los destinos de generación de los argumentos adicionales para el dispositivo:

 `-aot "ntrampolines=2048"`

El número de camas elásticas predeterminado es 1024.  Pruebe a aumentar este número hasta que haya suficiente para la aplicación.

## <a name="ran-out-of-trampolines-of-type-1"></a>Se ha quedado sin camas elásticas de tipo 1

Si realiza un uso intensivo de los genéricos recursiva, puede aparecer este mensaje en el dispositivo.  Puede crear un tipo más camas elásticas 1 (tipo RGCTX) mediante la modificación de la sección "iPhone compilación" de opciones de proyecto.  Desea agregar los destinos de generación de los argumentos adicionales para el dispositivo:

 `-aot "nrgctx-trampolines=2048"`

El número de camas elásticas predeterminado es 1024.  Pruebe a aumentar este número hasta que haya suficiente para el uso de genéricos.

## <a name="ran-out-of-trampolines-of-type-2"></a>Se ha quedado sin camas elásticas de tipo 2

Si hace uso intensivo de las interfaces, puede aparecer este mensaje en el dispositivo.
Puede crear más de tipo 2 camas elásticas (tipo de código thunk IMT) mediante la modificación de la sección "iPhone compilación" de opciones de proyecto.  Desea agregar los destinos de generación de los argumentos adicionales para el dispositivo:

 `-aot "nimt-trampolines=512"`

El número predeterminado de camas elásticas IMT Thunk es 128.  Pruebe a aumentar este número hasta que haya suficiente para el uso de interfaces.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>Depurador no puede conectar con el dispositivo

Al iniciar la depuración de una configuración de dispositivo, verá el depurador muestra un cuadro de diálogo que indica que está intentando conectarse a la aplicación. Hay varias razones, que el depurador no puede ser capaz de conectarse a la aplicación, según el modo usa para conectarse (USB o Wi-Fi).

 **Si el dispositivo y el host del depurador están en distintas redes**, un firewall o una red privada puede estar impidiendo la conexión para el host del depurador en modo de Wi-Fi de la aplicación.

 **Visual Studio para Mac no podrá consultar la dirección IP correcta del host**. Wi-Fi en el modo de Visual Studio para Mac ofrece a la aplicación todas las direcciones IP puede encontrar del host y la aplicación trata de todas ellas para ver si puede usar cualquiera de ellos para conectarse a Visual Studio para Mac.

 **Otro dispositivo está conectado a un puerto USB en el host.** En algunos casos, otros dispositivos conectados al puerto USB puertos en el host se sabe que algún modo interferir con la depuración en modo USB.

Si el modo de Wi-Fi o USB no funciona, puede probar fácilmente la otra: en Visual Studio para Mac, abra las preferencias, vaya a la página de preferencias, depurador/iPhone depurador y activar o desactivar la casilla de verificación "Depurar dispositivos iOS a través de Wi-Fi en lugar de a través de USB".   Si no funciona, puede ver más información sobre el error en la consola del dispositivo en modo detallado (que se habilita agregando "-v - v - v" a los argumentos mtouch adicionales en las opciones del proyecto).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Error 134: no se pudo mtouch con el siguiente mensaje:

Este error puede generarse si intenta compilar con - nolink en el estilo de Xamarin.iOS 1.4 de las versiones. Puede solucionar este error mediante la especificación de argumentos adicionales en la configuración del proyecto de monodevelop.

Agregue el argumento

 `-nosymbolstrip`

y se debe resolver el problema.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>Identidad de distribución no se muestra en Visual Studio para el proyecto de opciones de firma de Mac

Visual Studio para Mac 2.2 tiene un error que hace que no vuelva a detectar los certificados de distribución que contienen una coma. Actualice a Visual Studio para Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Error "AFCFileRefWrite devuelto: 1" durante la carga

Al cargar una aplicación en el dispositivo puede recibir un Error "AFCFileRefWrite devuelto: 1". Esto puede ocurrir si tiene un archivo de longitud cero.

## <a name="error-mtouch-failed-with-no-output"></a>Error "se produjo ninguna salida mtouch"

La versión actual de Xamarin.iOS y Visual Studio para Mac se producirá un error cuando el nombre del proyecto o el directorio donde se almacenan la solución o proyecto contiene espacios.
Para corregir este problema:


-  Asegúrese de que ni el proyecto o el directorio donde está almacenado contiene un espacio.
-  En el proyecto "Configuración de Main", asegúrese de que el nombre del proyecto no contiene espacios en blanco.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Error "el binario cargado no es válido. Se usó una versión beta de la versión preliminar del SDK para compilar la aplicación"

Este error ocurre normalmente con un proyecto que se inició en el desarrollo de iPad antes de que se lanzó Xamarin.iOS 2.0.0, es probable que tenga algunas claves en el archivo Info.plist, como:

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Este par de claves debe quitarse como Visual Studio para Mac gestiona por usted automáticamente.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Error "se ha utilizado una versión beta de la versión preliminar del SDK para compilar la aplicación"

(Aportación Ed Anuff)

Siga estos pasos:

-  Cambio de la versión del SDK en iPhone compilación 3.2 o iTunes connect la rechazará con la carga porque lo está viendo una aplicación compatible para iPad creada con una versión SDK menor 3.2
-  Crear un archivo Info.plist personalizado para el proyecto y establecer explícitamente MinimumOSVersion a 3.0 en ella.   Esto reemplazará el valor de MinimumOSVersion 3.2 establecido por Xamarin.iOS.   Si no lo hace, la aplicación no podrá ejecutar en un iPhone.
-  Recompilación, código postal y carga a iTunes conectan.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Excepción no controlada: System.Exception: no se pudo encontrar someSelector selector: en {type}

Esta excepción se debe a uno de tres cosas:


1.  Ha proporcionado un Selector para el tiempo de ejecución Objective-C sin aplicar el atributo [Exportar] correspondiente a un método
1.  Se ha habilitado la vinculación completa y no se aplica el atributo [Preserve] al método ed [Exportar].
1.  El atributo [Exportar] se ha aplicado a un método privado en un tipo heredado.


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>No se actualiza el archivo MainWindow.xib.designer.cs

Se produjo un error en Xamarin Studio 2.4 que ha provocado que no agrupar el archivo MainWindow.xib con el archivo MainWindow.xib.designer en proyectos nuevos. Esto significaba que no se actualizaría el código del diseñador para ese archivo en particular.

Este problema se ha corregido en la versión de Visual Studio para Mac que está disponible en su actualizador integrada, por lo que asegúrese de usar la versión más reciente.

Puede corregir los proyectos existentes mediante la eliminación (no eliminar) el xib y su archivo de diseñador, a continuación, vuelve a agregarlo. Volver a esto debería agrupar los archivos correctamente.

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView o UIActionSheet desaparecer después de haberse creado

Si tiene algún código similar al siguiente:

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

el objeto "como actionSheet" actúa como una variable temporal en la función y tan pronto como finaliza la función, el objeto es elegible para la recolección de elementos no utilizados, por lo que termina siendo recolectado.

Para corregir este problema, debe mantener una referencia "como actionsheet" fuera de su método, en algún lugar que durará más allá de su método.

## <a name="project-always-runs-in-the-ipad-simulator"></a>Proyecto siempre se ejecuta en el simulador de iPad

El instalador del SDK 4.0 iPhone instala 2 SDK - el SDK de 3,2, para la creación de aplicaciones solo iPad y el SDK 4.0, se usa para iPhone compilen y aplicaciones universales. También instala un simulador 3.2, que simula sólo un iPad, y un simulador 4.0 que simula iPhone o iPhone 4. Se quitan todos los SDK y simuladores más antiguos.

Visual Studio para las opciones de compilación de Mac iPhone proyecto incluye una configuración de la versión SDK que se usará en la creación de la aplicación. Esta configuración puede encontrarse en **opciones de proyecto -> compilación -> compilación iPhone**.

Nuevos proyectos en Visual Studio para Mac usan el SDK instalado más antiguo como su configuración predeterminada del SDK, y si el SDK especificado no existe, se usará la más cercana para compilar la aplicación puede encontrar Visual Studio para Mac. Esto se hace para que los proyectos no siempre sería requre el SDK más reciente. Sin embargo, actualmente esto provoca la 3.2 SDK que se va a usar - que da como resultado que se va a usar el simulador de iPad.

Para solucionar este problema mediante el SDK 4.0, vaya a **opciones de proyecto -> compilación -> compilación iPhone**> y cambie el valor del SDK en "4.0" mediante el cuadro de lista desplegable. Debe hacerlo para cada combinación de plataforma, tener acceso mediante las listas desplegables en la parte superior del panel y la configuración.

La versión del SDK no debe confundirse con la configuración de "Versión mínima del sistema operativo".
Este valor no tiene que coincidir con el valor de la versión SDK: afecta a la versión mínima del sistema operativo se instalará la aplicación, que puede ser anterior a la SDK, siempre use sólo las API que existen en el sistema operativo anterior o protección el uso de las características más recientes mediante compro de versión del sistema operativo en tiempo de ejecución KS. Se debe establecer para la versión del sistema operativo más antigua en el que probar la aplicación.

Tenga en cuenta también que la **proyecto -> destino del simulador de iPhone**> se puede usar el menú para elegir el simulador que se utiliza de forma predeterminada al ejecutar y depurar un proyecto. Además, el **ejecución -> ejecutar con**> se puede usar el menú para elegir un simulador específico con el que se va a ejecutar

## <a name="ibtool-returns-error-133"></a>ibtool devuelve el error 133

Esto significa que tiene instalado el 4 de XCode.   En XCode 4, se ha quitado la herramienta ibtool, ya no es posible editar los archivos XIB con una herramienta independiente.

Si desea usar Interface Builder, instale [XCode serie 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792), disponible en el sitio web de Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>"No se puede crear el enlace de presentación para el tipo mime: application/vnd.apple -<wbr/>generador de interfaz"

Este error se produce si intenta crear un interfaz de usuario de iPhone desde un proyecto que no sean iPhone. Asegúrese de que comience con una solución de iPhone/iPad, no es posible agregar elementos de interfaz de usuario de iPhone a un proyecto iPhone/iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Bloqueo de inicio cuando se ejecuta en el simulador de iOS

Si se produce un bloqueo en tiempo de ejecución (SIGSEGV) en el simulador junto con un seguimiento de pila que tiene este aspecto:

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
.. .y probablemente tenga ensamblado obsoleto uno (o más) en el directorio de aplicación del simulador. Es posible que existe dichos ensamblados, ya que Apple iOS simulator agrega y actualiza los archivos, pero nunca elimina. Si esto ocurre, a continuación, la solución más sencilla consiste en seleccionar "Restablecer y contenido y configuración de..." en el menú del simulador.   

**Advertencia:** Esto quitará todos los archivos, aplicaciones y datos desde el simulador.   Próxima vez que ejecute la aplicación, Visual Studio para Mac se implementará en el simulador y no habrá ningún ensamblado antiguo y obsoleto que provoca el bloqueo.

## <a name="simulator-hangs-during-application-installation"></a>Simulator se bloquea durante la instalación de la aplicación

Esto puede ocurrir cuando los nombres de aplicación incluyen un '.' (punto) en su nombre.
Esto se puede utilizar como el nombre del archivo ejecutable en CFBundleExecutable - incluso si es posible que funciona en muchos otros casos (como dispositivos).

 * "El valor no debe incluir cualquier extensión de nombre de". - [http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Error: "tipo de atributo personalizado 0 x 43 no se admite" al hacer doble clic en archivos .xib

Esto se debe al intentar abrir archivos .xib cuando las variables de entorno se establecen incorrectamente. Esto no debería ocurrir con el uso normal de Visual Studio para Mac/Xamarin.iOS y vuelve a abrir Visual Studio para Mac desde /Applications debería corregir el problema.

Al intentar actualizar el software y este mensaje de error aparece, por favor, correo electrónico *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>Aplicación se ejecuta en el simulador, pero se produce un error en el dispositivo

Este problema, se puede producir de varias formas y no siempre produce un error de coherencia. Si la aplicación contiene un .xib, compruebe para asegurarse de que el **acción de compilación** en el .xib se establece en **interfaz**. Se trata de la acción de compilación predeterminada para .xibs.

Para comprobar la acción de compilación, haga clic con el botón derecho en el archivo .xib y elija **acción de compilación**.


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException: No hay datos disponibles para la codificación 437

Al incluir 3rd bibliotecas de terceros en la aplicación de Xamarin.iOS, podría obtener un error en el formulario "System.NotSupportedException: No hay datos disponibles para la codificación 437" al intentar compilar y ejecutar la aplicación. Por ejemplo, bibliotecas, como `Ionic.Zip.ZipFile`, puede producir esta excepción durante la operación.

Esto puede solucionarse, abra las opciones para el proyecto de Xamarin.iOS, que se va a **compilación de iOS** > **internacionalización** y comprobando la **West** internacionalización.



<a name="Can't_upgrade_to_Indie/Business_from_Trial_Account" />


## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>No se puede actualizar a Indie/Business de cuenta de prueba

Si ha adquirido recientemente Xamarin.iOS e inició previamente una versión de prueba de Xamarin.iOS, deberá completar los pasos siguientes para obtener este cambio de licencia seleccionado por Visual Studio para Mac o Visual Studio.

-  Cierre Visual Studio para Mac o Visual Studio
-  Quitar todos los archivos de ~/Library/MonoTouch en Mac o %PROGRAMDATA%\MonoTouch\License\ para Windows
-  Vuelva a abrir Visual Studio para Mac o Visual Studio y compilar un proyecto de Xamarin.iOS


## <a name="receiving-activation-incomplete-error-message"></a>Recibir el mensaje de Error 'Incompleta de activación'

Esto puede ocurrir cuando se usa Xamarin.iOS para Visual Studio. Para resolver este problema, envíe los registros desde la ubicación siguiente a [ contact@xamarin.com ](mailto:contact@xamarin.com).

-  Ubicación del registro: %LocalAppData%/Xamarin/Logs
