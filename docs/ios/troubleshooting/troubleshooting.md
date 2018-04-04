---
title: Solución de problemas
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/0201
ms.openlocfilehash: 6a179c1d63e9b5a7b8a42705d5c112a7b71a4906
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting"></a>Solución de problemas

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin.iOS no se puede resolver System.ValueTuple

Este error se produce debido a una incompatibilidad con Visual Studio.

- **Visual Studio 2017 Update 1** (versión 15.1 o anterior) sólo es compatible con **System.ValueTuple NuGet 4.3.0** (o anterior).

- **Actualización 2 de Visual Studio de 2017** (versión más reciente o 15.2) solo es compatible con la **System.ValueTuple NuGet 4.3.1** u otra más reciente.

Elija System.ValueTuple NuGet correcta que se corresponde con la instalación de Visual Studio de 2017.


## <a name="receiving-error-retrieving-update-information-error-message"></a>Recibir el mensaje de Error 'Error al recuperar información de actualización'

Al intentar actualizar el software y este mensaje de error aparece, intente reiniciar el IDE y cerrar la sesión y, a continuación, vuelva a registrarlo a su cuenta en el IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>¿Cómo creo tomas o acciones con el generador de interfaz?

Con la introducción del Diseñador de Xamarin para iOS en Visual Studio para Mac y Visual studio, los desarrolladores de Xamarin.iOS ahora pueden aprovechar las de creación de una interfaz de usuario a través de guiones gráficos y .xibs. Hacer referencia a la [Hello, iOS](~/ios/get-started/hello-ios/index.md) guías para obtener más información sobre cómo utilizar el diseñador.

También puede hacer referencia a de Apple [toma](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) y [acciones](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) guías para obtener más información sobre el uso de salidas y las acciones en IB.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding produce NotSupportedException

Puede que esté usando una codificación que no se agrega de forma predeterminada. Compruebe el [internacionalización](~/ios/app-fundamentals/localization/index.md) página para obtener información sobre cómo agregar compatibilidad con la codificación más.

## <a name="systemmissingmethodexception-anything-else"></a>System.MissingMethodException (nada)

El miembro es probable que se quitó el vinculador y, por tanto, no existe en el ensamblado en tiempo de ejecución.  Existen varias soluciones a este:

-  Agregar el [[conservar]](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) al miembro de atributo.  Esto impedirá que el vinculador quitarla.
-  Al invocar [mtouch](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29) , use la **- nolink** o **- linksdkonly** opciones. -    **-nolink** deshabilita todos los vínculos.
-    **-linksdkonly** se vinculará siempre Xamarin.iOS ensamblados, como *monotouch.dll* o xamarin.ios.dll.

Tenga en cuenta que los ensamblados se vinculan para que el archivo ejecutable resultante es menor; por lo tanto, al deshabilitar la vinculación puede producir un archivo ejecutable mayor que es deseable.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Obtiene un ModelNotImplementedException

Si va a obtener esta excepción, esto significa que se está llamando a base. Método () en una clase que reemplaza un modelo. No es necesario llamar al método base en una clase para los modelos (estas son clases que se marcan con el atributo [modelo]).

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

Además, los tipos que contiene el código anterior deben ser una subclase de [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/).  Si el tipo contenedor es dentro de un espacio de nombres, también debe tener un [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo que proporciona un nombre de tipo sin un espacio de nombres (como el generador de interfaz no admite espacios de nombres de tipos):

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Clase desconocida XXXX en archivo de generador de interfaz

Este error se genera si se define una clase en los archivos del generador de interfaz, pero no proporciona una implementación real para él en el código de C#.

Debe agregar código similar al siguiente:

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System.MissingMethodException: No hay ningún constructor encuentra para Foo.Bar::ctor(System.IntPtr)

Este error se produce en tiempo de ejecución cuando el código intenta crear una instancia de las clases que hace referencia desde el archivo de generador de interfaz. Esto significa que olvidó agregar un constructor que toma un único IntPtr como un parámetro.

Se utiliza el constructor con un identificador IntPtr para enlazar los objetos administrados con sus representaciones no administradas.

Para solucionar este problema, agregue la siguiente línea de código a la clase Foo.Bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>Tipo {Foo} no contiene una definición para `GetNativeField' and no extension method `GetNativeField' de tipo se encontró {Foo}

Si obtiene este error en los archivos generados diseñadores (*. xib.designer.cs), significa que una de estas dos cosas:

 **(1) falta clase parcial o una clase base**

Las clases parciales generado por el diseñador deben tener clases parciales correspondientes en el código de usuario que heredan de alguna subclase de `NSObject`, a menudo `UIViewController`. Asegúrese de que tiene esta clase para el tipo que se lo ofrece el error.

 **2) espacios de nombres de predeterminado cambiado**

Los archivos de diseñador se generan mediante la configuración de espacio de nombres predeterminado del proyecto. Si ha cambiado esta configuración, o cambiar el nombre del proyecto, las clases parciales generadas ya no pueden estar en el mismo espacio de nombres que sus equivalentes de código de usuario.

Configuración de Namespace puede encontrarse en el cuadro de diálogo Opciones del proyecto. El espacio de nombres predeterminado se encuentra en la **General -> configuración de Main** sección. Si está en blanco, el nombre del proyecto se utiliza como el valor predeterminado. Espacio de nombres de configuración más avanzadas puede encontrarse en el **código fuente -> .NET Naming Policies** sección.

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>Para las acciones de advertencia: el método privado 'Foo' nunca se utiliza. (CS0169)

Acciones para los archivos del generador de interfaz están conectadas con los widgets mediante reflexión en tiempo de ejecución, por lo que se espera que esta advertencia.

Puede usar "Advertencia de #pragma deshabilitar 0169" "Advertencia de #pragma: habilite 0169" alrededor de las acciones si desea suprimir esta advertencia solo para estos métodos, o agregar 0169 al campo "Omitir advertencias" en Opciones del compilador si desea deshabilitarlo para todo el proyecto (not se recomienda).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>no se pudo mtouch con el siguiente mensaje: no se puede abrir el ensamblado ' / path/to/yourproject.exe'

Si ve este mensaje de error, por lo general el problema es que la ruta de acceso absoluta del proyecto contiene un espacio. Este problema se solucionará en una versión futura de Xamarin.iOS, pero se puede solucionar el problema, mueva el proyecto a una carpeta sin espacios en blanco.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>La versión de sqlite3 es antigua: actualice al menos v3.5.0!

Esto sucede si hace que todos los elementos siguientes:

1.  Usar Mono.Data.Sqlite
1.  Usar Mac OS X Leopard (10.5)
1.  Ejecute la aplicación en el simulador.


El problema es que se infecta Mono OS X `libsqlite3.dylib`, del no el iPhoneSimulator `libsqlite3.dylib` archivo. La aplicación *le* en el dispositivo, pero no el simulador.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>Implementar en el dispositivo se produce un error con System.Exception: AMDeviceInstallApplication devolvió 3892346901

Este error significa que la configuración de firma de código para el Id. del certificado/lote no coincide con el perfil de aprovisionamiento instalado en el dispositivo.  Confirme que tiene el certificado apropiado seleccionado en Opciones de proyecto -> iPhone firma de paquete y el identificador de paquete correcto especificado en las opciones de proyecto -> aplicación de iPhone

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>Completar código no funciona en Visual Studio para Mac

Asegúrese de que está utilizando la versión más reciente de Visual Studio para Mac y Xamarin.iOS

Si el problema persiste, vuelva [registre un error](http://monodevelop.com/Developers#Reporting_Bugs), adjuntar la **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP} .log**, y **componentes-{TIMESTAMP} .log** archivos de registro.

Si se produce un error en todo lo demás, puede intentar quitar la caché de finalización de código para que se vuelve a generar:

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Asegúrese de que se escribe este comando correctamente o puede quitar accidentalmente archivos importantes.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>Visual Studio para Mac se bloquea al copiar texto

Las utilidades de Mac populares QuickSilver, la barra de herramientas de Google y la barra de inicio tienen características de Portapapeles que Visual Studio con daños en la memoria de Mac. En sus opciones, puede enumerar Visual Studio para Mac como un proceso que no deben interferir con.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio para Mac se queja sobre 2.4 Mono necesarios

Si actualiza Visual Studio para Mac debido a una actualización reciente y al intentar iniciarlo nuevo queja sobre 2.4 Mono no está presente, todo lo que tiene que hacer es [actualizar la instalación de Mono 2.4](http://www.go-mono.com/mono-downloads/download.html).  

2.4.2.3_6 mono corrige algunos problemas importantes que impidieron que Visual Studio para Mac ejecutan de forma confiable, a veces bloqueado Visual Studio para Mac durante el inicio o impidieron que la base de datos de finalización de código que se está generando.

Una vez que instale el nuevo Mono, Visual Studio para Mac se iniciará según lo previsto.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Aserción de... /.. /.. /.. /mono/Metadata/Generic-Sharing.c:704, condición 'oti' no se cumplen

Si recibe el seguimiento de la pila siguiente:

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Significa que está vinculando una biblioteca estática que se compilan con código thumb en el proyecto. A partir de la versión del SDK iPhone 3.1 (o superior en el momento de redactar este artículo) Apple introducido un error en el vinculador al vincular código Thumb no (Xamarin.iOS) con código Thumb (la biblioteca estática). Debe vincularse a una versión no general de la biblioteca estática para mitigar este problema.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System.ExecutionEngineException: Intentando JIT compilación () de Foo[]:System.Collections.Generic.ICollection'1.get_Count método (contenedor administrado a administrado)

El sufijo [] indica que usted o la biblioteca de clases se llama a un método en una matriz a través de una colección genérica, como IEnumerable <>, ICollection <> o <> de IList. Como alternativa, puede forzar explícitamente el compilador AOT para incluir el método de este tipo llamando al método usted mismo y asegurándose de se ejecute este código antes de la llamada que desencadenó la excepción. En este caso, podría escribir:

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Que forzará al compilador AOT que incluya el método get_Count.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio para el editor de código fuente de Mac es muy lento

A veces Visual Studio para el editor de código fuente de Mac pasa a ser muy lento, que aparecen que deja de responder durante varios segundos entre caracteres de escritura.

Este problema es muy poco frecuente y muy difíciles de reproducir, normalmente no se puede reproducir en el mismo equipo después de reiniciar Visual Studio para Mac. Por esta razón le agradeceríamos se puede realizar varios pasos de depuración antes de reiniciar Visual Studio para Mac y enviar los resultados con nosotros.

1.  Intente cerrar la pestaña del editor y volver a abrirlo. ¿Se tarda un poco de edición o mover el carácter de intercalación hasta que la degradación ocurre de nuevo?
1.  Deshabilitar la "sincronización de carretera de con la herramienta de desarrollador"Quartz Debug"(que puede encontrar con Spotlight) y compruebe si el rendimiento del editor de código fuente se restaura a su estado normal.
1.  Intente repetir el paso (1) con la sincronización de carretera sigue deshabilitada.
1.  Si el editor se bloquea durante más de unos segundos, pruebe a ejecutar "killall-salir [Visual Studio para Mac]" en un terminal mientras se está bloqueado. Puede resultar difícil para el comando kill que se produzca mientras se ha producido un error en el editor, pero es fundamental a tal efecto, ya que el comando fuerza Mono para escribir seguimientos de pila de todos los subprocesos en el registro MD, que podemos usar para detectar el estado de los subprocesos están en mientras el extra se ha producido un error de tiempo.



Adjunte los registros extra, **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP} .log**, y **componentes-{TIMESTAMP} .log**(en versiones anteriores de extra/MonoDevelop, envíelos **~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**).

 **Nota: Se ha corregido el problema anterior en extra 2.2 Final**

## <a name="compiled-application-is-very-large"></a>Aplicación de compilación es muy grande

Para admitir la depuración, compilaciones de depuración contienen código adicional. Los proyectos compilados en modo de lanzamiento son una fracción del tamaño.

A partir de Xamarin.iOS 1.3 la depuración compilaciones incluyen compatibilidad de depuración para cada componente único de Mono (todos los métodos de todas las clases de los marcos de trabajo).  

Con Xamarin.iOS 1.4 se presentará un método más preciso para la depuración, el valor predeterminado será solo proporcionan instrumentación de depuración para el código y las bibliotecas y no hacer esto para todos los [ensamblados Mono](~/cross-platform/internals/available-assemblies.md) (Esto sigue sea posible, pero se deben participar en los ensamblados de depuración).

## <a name="installation-hangs"></a>Se bloquea la instalación

Instaladores de Mono y Xamarin.iOS de bloqueo si tiene la ejecución de simulador de iPhone. Este problema no se limita a Mono o Xamarin.iOS, se trata de un problema de coherencia a través de cualquier software que intenta instalar software en MacOS nieve Leopard si iPhone simulador se ejecuta durante la instalación.

Asegúrese de que salga el simulador de iPhone y vuelva a intentar la instalación.

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>Se ha quedado sin camas elásticas de tipo 0

Si recibe este mensaje mientras se ejecuta el dispositivo, puede crear más escriba 0 camas elásticas (tipo específico) mediante la modificación de la sección "iPhone compilación" de opciones de proyecto.  Va a agregar destinos de compilación de los argumentos adicionales para el dispositivo:

 `-aot "ntrampolines=2048"`

El número predeterminado de camas elásticas es 1024.  Pruebe a aumentar este número hasta que haya suficiente para la aplicación.

## <a name="ran-out-of-trampolines-of-type-1"></a>Se ha quedado sin camas elásticas de tipo 1

Si realiza un uso intensivo de los genéricos recursiva, puede obtener este mensaje en el dispositivo.  Puede crear más escriba camas elásticas 1 (tipo RGCTX) mediante la modificación de la sección "iPhone compilación" de opciones de proyecto.  Va a agregar destinos de compilación de los argumentos adicionales para el dispositivo:

 `-aot "nrgctx-trampolines=2048"`

El número predeterminado de camas elásticas es 1024.  Pruebe a aumentar este número hasta que haya suficiente para el uso de genéricos.

## <a name="ran-out-of-trampolines-of-type-2"></a>Se ha quedado sin camas elásticas de tipo 2

Si realiza uso intensivo de interfaces, puede obtener este mensaje en el dispositivo.
Puede crear más de tipo 2 camas elásticas (tipo de fragmentos de código thunk IMT) mediante la modificación de la sección "iPhone compilación" de opciones de proyecto.  Va a agregar destinos de compilación de los argumentos adicionales para el dispositivo:

 `-aot "nimt-trampolines=512"`

El número predeterminado de camas elásticas de código Thunk de IMT es 128.  Pruebe a aumentar este número hasta que haya suficiente para el uso de interfaces.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>Depurador es no se puede conectar con el dispositivo

Al iniciar la depuración de una configuración de dispositivo, verá el depurador muestra un cuadro de diálogo que indica que está intentando conectarse a la aplicación. Hay varias razones, es posible que el depurador no pueda conectarse a la aplicación, dependiendo del modo que está utilizando para conectarse (USB o Wi-Fi).

 **Si el dispositivo y el host del depurador que se encuentran en distintas redes**, un firewall o una red privada puede estar impidiendo la conexión para el host del depurador en modo de Wi-Fi de la aplicación.

 **Visual Studio para Mac no pueda consultar la dirección IP correcta del host**. Wi-Fi modo Visual Studio para Mac ofrece a la aplicación de todas las direcciones IP que puede encontrar del host y la aplicación intenta todas ellas para ver si puede usar cualquiera de ellos para conectarse a Visual Studio para Mac.

 **Otro dispositivo está conectado a un puerto USB en el host.** En algunos casos, otros dispositivos conectados al puerto USB famosos puertos en el host de algún modo interferir con la depuración en modo USB.

Si el modo de Wi-Fi o USB no funciona, puede probar fácilmente las demás: en Visual Studio para Mac, abra las preferencias, vaya a la página de preferencias/depurador/iPhone depurador y activar o desactivar la casilla de verificación "Depurar dispositivos iOS a través de Wi-Fi en lugar de a través de USB".   Si no funciona, puede ver más información sobre el error en la consola del dispositivo en modo detallado (que está habilitado mediante la adición de "-v - v - v" a los argumentos de mtouch adicionales en las opciones del proyecto).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Error 134: no se pudo mtouch con el siguiente mensaje:

Este error se generará si está intentando crear con - nolink el estilo de Xamarin.iOS 1.4 de versiones. Este error se puede solucionar mediante la especificación de argumentos adicionales en la configuración del proyecto monodevelop.

Agregue el argumento

 `-nosymbolstrip`

y se debe resolver el problema.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>Identidad de distribución no se muestra en Visual Studio para proyectos de Mac opciones de firma

Visual Studio para Mac 2.2 tiene un error que hace que no detecta los certificados de distribución que contienen una coma. Actualice a Visual Studio para Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Error "AFCFileRefWrite devuelto: 1" durante la carga

Al cargar una aplicación en el dispositivo puede recibir un Error "AFCFileRefWrite devuelto: 1". Esto puede ocurrir si tiene un archivo de longitud cero.

## <a name="error-mtouch-failed-with-no-output"></a>Error "no se pudo sin resultados de mtouch"

La versión actual de Xamarin.iOS y Visual Studio para Mac producirá un error cuando el nombre del proyecto o el directorio donde se almacenan la solución o proyecto contener espacios.
Para solucionar este problema:


-  Asegúrese de que ni el proyecto o el directorio donde está almacenado contiene un espacio.
-  La configuración del proyecto "Main" Asegúrese de que el nombre del proyecto no contiene espacios en blanco.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Error "el archivo binario que se ha cargado no es válido. Se utilizó una versión beta de la versión preliminar del SDK para compilar la aplicación"

Este error puede deberse a un proyecto que se inició en el desarrollo de iPad antes del lanzamiento de Xamarin.iOS 2.0.0, es probable que tenga algunas claves en su Info.plist como:

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Este par de claves debe quitarse como Visual Studio para Mac se encarga de automáticamente.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Error "se ha utilizado una versión beta de la versión preliminar del SDK para compilar la aplicación"

(Aportados por Ed Anuff)

Siga estos pasos:

-  Cambiar la versión del SDK en iPhone compilación 3.2 o iTunes conectarse no admitirán en carga porque está viendo una aplicación compatible iPad creada con una versión SDK inferior 3.2
-  Crear un Info.plist personalizado para el proyecto y establecer explícitamente MinimumOSVersion a 3.0 en ella.   Esto anulará el valor de MinimumOSVersion 3.2 establecido por Xamarin.iOS.   Si no lo hace, la aplicación no pueda ejecutar en un iPhone.
-  Volver a generar, zip y carga a iTunes conectar.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Excepción no controlada: System.Exception: no se pudo encontrar el selector someSelector: {tipo}

Esta excepción se debe a una de estas tres cosas:


1.  Ha proporcionado un Selector para el tiempo de ejecución de C objetivo sin aplicar el atributo [Exportar] correspondiente a un método
1.  Se ha habilitado la vinculación completa y no se aplica el atributo [Preserve] al método ed [Exportar].
1.  Ha aplicado el atributo [Exportar] para un método privado en un tipo heredado.


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>No se actualiza el archivo MainWindow.xib.designer.cs

Se produjo un error en Xamarin Studio 2.4 que ha provocado que no agrupar el archivo MainWindow.xib con el archivo MainWindow.xib.designer en proyectos nuevos. Esto significaba que no se actualizaría el código del diseñador para ese archivo en particular.

Este problema se corregirá en la versión de Visual Studio para Mac que está disponible en su actualizador integrado, por lo que, asegúrese de usar la versión más reciente.

Puede corregir los proyectos existentes mediante la eliminación de (no eliminar) la xib y su archivo del diseñador, a continuación, se vuelve a agregar. Esto que debe volver a agrupar los archivos correctamente.

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView o UIActionSheet desaparecer después de que se está creando

Si tiene algún código similar al siguiente:

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

el objeto "actionSheet" reside como una variable temporal en la función y tan pronto como finaliza la función, el objeto es apto para la recolección de elementos no utilizados, por lo que termina están recolectando.

Para solucionar este problema, debe mantener una referencia a "actionSheet" fuera del método, en algún lugar que durará más allá de su método.

## <a name="project-always-runs-in-the-ipad-simulator"></a>Proyecto siempre se ejecuta en el simulador de iPad

El instalador de iPhone SDK 4.0 instala 2 SDK - el SDK 3,2, para la creación de aplicaciones solo iPad y el SDK 4.0, usada para iPhone de creación y aplicaciones universales. También instala un simulador 3,2, lo que simula solo un iPad, y un simulador 4.0 que simula iPhone o iPhone 4. Se quitan todos los SDK anteriores y los simuladores.

Visual Studio para opciones de compilación del proyecto de Mac iPhone incluyen una opción para la versión del SDK que se utilizará en la creación de la aplicación. Esta configuración puede encontrarse en **opciones de proyecto -> compilación -> iPhone compilación**.

Los nuevos proyectos en Visual Studio para Mac usan el SDK instalado más antiguo como su configuración predeterminada de SDK, y si el SDK especificado no existe, se utilizará el más cercano que puede encontrar al compilar la aplicación de Visual Studio para Mac. Esto se hace para que los proyectos no siempre debería requre el SDK más reciente. Sin embargo, actualmente esto provoca la 3,2 SDK que se va a usa - que da como resultado el simulador iPad usándola.

Para solucionar este problema mediante el SDK 4.0, vaya a **opciones de proyecto -> compilación -> iPhone compilación**> y cambie el valor SDK "4.0" mediante el cuadro de lista desplegable. Debe hacerlo para cada combinación de plataforma, acceso mediante las listas desplegables en la parte superior del panel y la configuración.

La versión del SDK no debe confundirse con la configuración de "Versión de SO mínimo".
Este valor no tiene que coincidir con el valor de la versión SDK, afecta a la versión mínima del sistema operativo se instalará la aplicación, que puede ser anterior a la SDK, siempre y cuando utilice solo las API que existen en el sistema operativo anterior, o protección de uso de características más recientes mediante compro de versión de sistema operativo en tiempo de ejecución KS. Se debe establecer para la versión más antigua del SO en el que probar la aplicación.

Tenga en cuenta también que la **proyecto -> iPhone destino del simulador**> menú puede utilizarse para seleccionar el simulador que se usa de forma predeterminada al ejecutar/depurar un proyecto. Además, el **ejecución -> ejecutar con**> menú puede utilizarse para elegir un simulador específico con el que se va a ejecutar

## <a name="ibtool-returns-error-133"></a>ibtool devuelve el error 133

Esto significa que tiene instalado el 4 de XCode.   En XCode 4, se ha quitado la herramienta ibtool, ya no es posible editar los archivos XIB con una herramienta independiente.

Si desea utilizar el generador de interfaz, instale [XCode serie 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792), disponible en el sitio web de Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>"No se puede crear el enlace de presentación para el tipo mime: application/vnd.apple -<wbr/>interfaz generador"

Este error se produce si intenta crear un interfaz de usuario de iPhone desde un proyecto no iPhone. Asegúrese de que comience con una solución de iPhone/iPad, no es posible agregar elementos de interfaz de usuario de iPhone a un proyecto no iPhone/iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Bloqueo de inicio cuando se ejecuta en el simulador de iOS

Si se produce un bloqueo en tiempo de ejecución (SIGSEGV) en el simulador junto con un seguimiento de pila que el siguiente aspecto:

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
.. Then es probable que tenga una (o más) ensamblado obsoleto en el directorio de la aplicación del simulador. Estos ensamblados pueden existe porque Apple iOS simulator agrega y actualiza los archivos, pero nunca elimina. Si esto ocurre, a continuación, la solución más sencilla consiste en seleccionar "Restablecer y contenido y configuración..." en el menú del simulador.   

**Advertencia:** Esto quitará todos los archivos, aplicaciones y datos desde el simulador.   Próxima vez que ejecute la aplicación, Visual Studio para Mac se implementará en el simulador y no habrá ningún ensamblado antiguo, obsoleto que provoca el bloqueo.

## <a name="simulator-hangs-during-application-installation"></a>Simulator se bloquea durante la instalación de la aplicación

Esto puede ocurrir cuando los nombres de aplicación incluyen un '.' (punto) en su nombre.
Esto se prohíbe como el nombre del archivo ejecutable en CFBundleExecutable - incluso si es posible funciona en muchos otros casos (como dispositivos).

 * "El valor no debe incluir cualquier extensión en el nombre." - [http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Error: "tipo de atributo personalizado 0x43 no es compatible" al hacer doble clic en archivos de .xib

Esto se debe al intentar abrir los archivos de .xib cuando las variables de entorno se establecen incorrectamente. Esto no debería suceder con el uso normal de Visual Studio para Mac/Xamarin.iOS y volver a abrir Visual Studio para Mac desde /Applications debería solucionar el problema.

Al intentar actualizar el software y este mensaje de error aparece, por favor, correo electrónico *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>Aplicación se ejecuta en el simulador, pero se produce un error en el dispositivo

Este problema puede manifestarse de varias formas y siempre no genera un error de coherencia. Si la aplicación contiene un .xib, compruebe para asegurarse de que el **acción de compilación** en el .xib se establece en **interfaz**. Se trata de la acción de compilación predeterminada para .xibs.

Para comprobar la acción de compilación, haga clic con el botón secundario en el archivo .xib y elija **acción de compilación**.


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException: No hay datos disponibles para la codificación 437

Cuando se incluyen 3rd bibliotecas de entidad en la aplicación de Xamarin.iOS, podría obtener un error con el formato "System.NotSupportedException: No hay datos disponibles para la codificación 437" al intentar compilar y ejecutar la aplicación. Por ejemplo, bibliotecas, como `Ionic.Zip.ZipFile`, puede producir esta excepción durante la operación.

Esto se puede resolver, abra las opciones para el proyecto Xamarin.iOS, vaya a **iOS compilación** > **internacionalización** y comprobando la **oeste** internacionalización.



<a name="Can't_upgrade_to_Indie/Business_from_Trial_Account" />


## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>No se puede actualizar a Indie/Business de cuenta de prueba

Si ha adquirido recientemente Xamarin.iOS e inició previamente una versión de prueba de Xamarin.iOS, debe completar los pasos siguientes para obtener este cambio de licencia detectado por Visual Studio para Mac o en Visual Studio.

-  Cierre Visual Studio para Mac/Visual Studio
-  Quitar todos los archivos de ~/Library/MonoTouch en Mac o %PROGRAMDATA%\MonoTouch\License\ para Windows
-  Vuelva a abrir Visual Studio para Mac/Visual Studio y crear un proyecto de Xamarin.iOS


## <a name="receiving-activation-incomplete-error-message"></a>Recibir el mensaje de Error 'Incompleta de activación'

Este problema puede producirse al usar Xamarin.iOS para Visual Studio. Para resolver este problema, envíe los registros de la siguiente ubicación para [ contact@xamarin.com ](mailto:contact@xamarin.com).

-  Ubicación del registro: %LocalAppData%/Xamarin/Logs
