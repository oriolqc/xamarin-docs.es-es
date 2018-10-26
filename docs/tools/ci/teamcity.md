---
title: Uso de ciudad del equipo con Xamarin
description: Esta guía explican los pasos implicados en el uso de TeamCity para compilar aplicaciones móviles y, a continuación, volver a enviarlas a Xamarin Test Cloud.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: a3cb79f33b64d933aa8ab4d3555479cc16238992
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118271"
---
# <a name="using-team-city-with-xamarin"></a>Uso de ciudad del equipo con Xamarin

_Esta guía explican los pasos implicados en el uso de TeamCity para compilar aplicaciones móviles y, a continuación, volver a enviarlas a Xamarin Test Cloud._

Como se describe en el [Introducción a la integración continua](~/tools/ci/intro-to-ci.md) , Guía de integración continua (CI) es una práctica útil al desarrollar aplicaciones móviles de calidad. Hay muchas opciones viables para software de servidor de integración continua; Esta guía se centrará en [TeamCity](http://www.jetbrains.com/teamcity/) de JetBrains.

Hay varias permutaciones diferentes de una instalación de TeamCity. La siguiente es una lista de algunos de ellos:

- **Servicio de Windows** : en este escenario, TeamCity se inicia cuando se arranca Windows como un servicio de Windows. Debe ir acompañada de un Host de compilación de Mac para compilar las aplicaciones de iOS.

- **Inicie el demonio en OS X** : conceptualmente, esto es muy similar a la que se ejecuta como un servicio de Windows que se describe en el paso anterior. De forma predeterminada, las compilaciones se ejecutará con la cuenta raíz.

- **Cuenta de usuario en OS X** : es posible ejecutar TeamCity con una cuenta de usuario que se inicia cada vez que el usuario inicia sesión.

De los escenarios anteriores, ejecuta TeamCity con una cuenta de usuario en OS X es el más simple y sencilla al programa de instalación.

Hay varios pasos implicados con la configuración de TeamCity:

- **Instalación de TeamCity** : la instalación de TeamCity no se trata en esta guía. Esta guía se da por supuesto que TeamCity está instalado y ejecutándose en una cuenta de usuario. Instrucciones sobre [instalar TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation) puede encontrarse en el [TeamCity 8 documentación](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) de JetBrains.

- **Preparar el servidor de compilación** : este paso consiste en instalar el software necesario, herramientas, y los certificados necesitan para crear aplicaciones móviles y prepararlos para la distribución.

- **Crear Script de compilación A** : este paso no es estrictamente necesario, pero un script de compilación es una ayuda útil para crear aplicaciones desatendidas. Mediante un script de compilación ayudará a solucionar los problemas de compilación que pueden surgir y proporciona una manera coherente y repetible para crear los archivos binarios para la distribución, incluso si no se usa la integración continua.

- **Crear un proyecto de TeamCity** : una vez completados los tres pasos anteriores, debemos crear un proyecto de TeamCity que contendrá todos los metadatos necesarios para recuperar el código fuente, compile los proyectos y enviar las pruebas a Xamarin Test Cloud.

## <a name="requirements"></a>Requisitos

Experimentar con [Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud) es necesario.

Es necesario estar familiarizado con TeamCity 8.1. La instalación de TeamCity está fuera del ámbito de este documento. Se supone que TeamCity está instalado en OS X Mavericks y se ejecuta con una cuenta de usuario normal y no la cuenta raíz.

El servidor de compilación debe ser un equipo independiente, ejecutándose en OS X, que se dedica a la integración continua. Idealmente, el servidor de compilación no será responsable de ningún otro rol, por ejemplo, un servidor de base de datos, un servidor web o una estación de trabajo de desarrollador.

> [!IMPORTANT]
> Esta guía no trata una instalación de Xamarin "sin periféricos".

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparar el servidor de compilación

Es un paso fundamental en la configuración de un servidor de compilación instalar todas las herramientas necesarias, el software y los certificados para compilar las aplicaciones móviles. Es importante que el servidor de compilación pueda compilar la solución móvil y ejecutar las pruebas. Para minimizar los problemas de configuración, las herramientas y software deben instalarse en la misma cuenta de usuario que está hospedando TeamCity. La siguiente es una lista de los recursos necesarios:

1. **Visual Studio para Mac** : Esto incluye Xamarin.iOS y Xamarin.Android.
2. **Inicie sesión en el Store del componente Xamarin** : este paso es opcional y solo es necesario si la aplicación usa componentes desde el almacén de componentes de Xamarin. Proactivamente iniciando sesión en el almacén de componentes en este momento impedirá que los problemas cuando una compilación de TeamCity intenta compilar la aplicación.
3. **Xcode** : Xcode se requiere para las aplicaciones de iOS de compilación e inicie sesión.
4. **Herramientas de línea de comandos de Xcode** : Esto se describe en el paso 1 de la sección de instalación de la [actualizar Ruby con rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) guía.
5. **Firma de identidad y perfiles de aprovisionamiento** : importar los certificados y aprovisionamiento de perfil a través de XCode. Consulte la Guía de Apple en [Exportar identidades de firma y perfiles de aprovisionamiento](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) para obtener más detalles.
6. **Android keystore** : copiar el keystore Android necesario en un directorio que el usuario de TeamCity tiene acceso, es decir, `~/Documents/keystores/MyAndroidApp1`.
7. **Calabash** : este es un paso opcional, si la aplicación tiene pruebas escritas con Calabash. Consulte la [Calabash instalación en OS X Mavericks](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/) guía y la [actualizar Ruby con rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) guía para obtener más información.

El siguiente diagrama ilustra todos estos componentes:

![](teamcity-images/image1.png "Este diagrama ilustra todos estos componentes")

Una vez que se ha instalado todo el software, inicie sesión en la cuenta de usuario y confirmar que todo el software se ha instalado correctamente y funciona. Esto debe incluir la compilación de la solución y el envío de la aplicación a Test Cloud. Esto puede simplificar enormemente al ejecutar el script de compilación, como se describe en la sección siguiente.

## <a name="create-a-build-script"></a>Crear un Script de compilación

Aunque es perfectamente posible que TeamCity controlar todos los aspectos de la compilación y envío de las aplicaciones móviles en Test Cloud por sí mismo, se recomienda crear un script de compilación. Un script de compilación proporciona las siguientes ventajas:

1. **Documentación** : un script de compilación actúa como una forma de documentación sobre cómo se crea el software. Esto elimina algunas de la "magia" que está asociado con la implementación de la aplicación y permite a los desarrolladores centrarse en las funciones.
1. **Repetibilidad** : un script de compilación garantiza que cada vez que la aplicación se compila e implementa, ocurre exactamente igual, independientemente de quién o qué hace el trabajo. Esta coherencia repetible quita los problemas o errores que podrían cuelen en debido a una compilación ejecutada de forma incorrecta o errores humanos.
1. **Control de versiones** : un script de compilación puede incluirse en el sistema de control de código fuente. Esto significa que los cambios realizados en el script de compilación pueden se realiza un seguimiento, supervisar y corregir si se encuentran errores o imprecisiones.
1. **Preparar el entorno** : un script de compilación puede incluir una lógica para instalar cualquier requiere 3rd dependencias de terceros. Esto garantizará que las aplicaciones se crean con los componentes correctos.

El script de compilación puede ser tan simple como un archivo de Powershell (en Windows) o un script de bash (en OS X). Al crear el script de compilación, hay varias opciones para lenguajes de scripting:

- [**Rake** ](https://github.com/jimweirich/rake) : se trata de un lenguaje específico de dominio (DSL) para la creación de proyectos, basado en Ruby. Rastrillo tiene la ventaja de la popularidad y un rico ecosistema de bibliotecas.

- [**psake** ](https://github.com/psake/psake) : se trata de una biblioteca de Windows Powershell para la creación de software

- [**FALSIFICAR** ](http://fsharp.github.io/FAKE/) : se trata de un DSL basándose en F# lo que hace posible utilizar las bibliotecas de .NET existentes, si es necesario.

Se utiliza el lenguaje de scripting depende de los requisitos y preferencias. El [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash) ejemplo contiene un ejemplo del uso de Rake como un [script de compilación](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile).

> [!NOTE]
> Es posible utilizar un sistema de compilación basado en XML, como MSBuild o NAnt, pero estos falta la expresividad y el mantenimiento de un DSL que se dedica a la creación de software.

### <a name="parameterizing-the-build-script"></a>Parametrizar el Script de compilación

El proceso de compilación y pruebas de software requiere información que debe mantenerse en secreto. En particular creando un APK puede requerir una contraseña para el almacén de claves o el alias de clave en el almacén de claves. Del mismo modo, en la nube de prueba requiere una clave de API que es única para un desarrollador. Estos tipos de valores no deben ser difíciles codificadas en el script de compilación. En su lugar se deben pasar como variables en el script de compilación.

Menos sensibles son valores, como el Id. de dispositivo o el identificador de dispositivo Android que identificar qué tipo de prueba debe usar Test Cloud para los dispositivos de iOS se ejecuta. Estos no son valores que deben protegerse, pero que pueden cambiar de una compilación a otra.

Almacenamiento de estos tipos de variables fuera de la secuencia de comandos de compilación también facilita compartir el script de compilación de una organización, con desarrolladores, por ejemplo. Los desarrolladores pueden usar el mismo script exacto que el servidor de compilación, pero pueden usar sus propias keystore y claves de API.

Hay dos opciones para almacenar estos valores confidenciales:

- **Un archivo de configuración** : para proteger la clave de API de prueba en la nube, este valor no debe estar activado en el control de versiones. El archivo se puede crear para cada máquina. Cómo se leen los valores de este archivo depende de usar el lenguaje de scripting.

- **Las variables de entorno** : pueden configurarse fácilmente en una por equipo y Bor independiente del lenguaje de scripting subyacente.

Existen ventajas y desventajas en cada una de estas opciones. TeamCity funciona bien con las variables de entorno, por lo que esta guía le recomendará esta técnica al crear scripts de compilación.

### <a name="build-steps"></a>Pasos de compilación

El script de compilación debe ser capaz de realizar los pasos siguientes:

- **Compile la aplicación** : Esto incluye la firma de la aplicación con el perfil de aprovisionamiento correcto.

- **Envíe la aplicación a Xamarin Test Cloud** : Esto incluye la firma y zip alinear el APK con el almacén de claves adecuado.

Estos dos pasos se explicará más detalladamente a continuación.

#### <a name="compiling-a-xamarinios-application"></a>Compilar una aplicación de Xamarin.iOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilar una aplicación de Xamarin.Android

Para compilar una aplicación de Android, use **xbuild** (o **msbuild** en Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

Tenga en cuenta que para compilar la aplicación Xamarin Android **xbuild** el proyecto y que se utiliza para compilar la aplicación de iOS **xbuild** requiere que la solución.

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>Enviar Xamarin.UITests en Test Cloud

UITests se envían usando el `test-cloud.exe` aplicación, tal y como se muestra en el fragmento de código siguiente:

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

Cuando se ejecuta la prueba, se devolverá los resultados de pruebas en la forma de archivo XML de estilo NUnit denominado **report.xml**. TeamCity mostrará la información en el registro de compilación.

Para obtener más información sobre cómo enviar UITests a Test Cloud, consulte esta guía en [Xamarin.UITests preparar para la carga](/appcenter/test-cloud/preparing-for-upload/uitest/).

#### <a name="submitting-calabash-tests-to-test-cloud"></a>Envío de Calabash pruebas para probar en la nube

Pruebas de Calabash se envían usando el `test-cloud` GEM; para ello, como se muestra en el fragmento de código siguiente:

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
Para enviar una aplicación de Android en Test Cloud, es necesario volver a crear el servidor de prueba APK con calabash android:

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
Para obtener más información sobre el envío de las pruebas de Calabash, consulte la Guía de Xamarin en [enviando las pruebas de Calabash en Test Cloud](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/).

## <a name="creating-a-teamcity-project"></a>Creación de un proyecto de TeamCity

Una vez que se instala TeamCity y Visual Studio para Mac puede compilar el proyecto, es hora de crear un proyecto de TeamCity para compilar el proyecto y enviarla a Test Cloud.

1. Iniciado, inicie sesión en TeamCity a través del explorador web. Desplácese hasta el proyecto raíz:

    ![Navegue hasta el proyecto raíz](teamcity-images/image2.png "navegue hasta el proyecto raíz") bajo el proyecto raíz, cree un nuevo proyecto secundario:

    ![Vaya a la raíz de proyecto debajo de la directiva el proyecto raíz, cree un nuevo proyecto secundario](teamcity-images/image3.png "navegar a la raíz de proyecto debajo de la directiva el proyecto raíz, cree un nuevo proyecto secundario")
2. Una vez creado el subproyecto, agregue una nueva configuración de compilación:

    ![Una vez creado el subproyecto, agregue una nueva configuración de compilación](teamcity-images/image5.png "una vez creado el subproyecto, agregue una nueva configuración de compilación")
3. Asociar un proyecto de VC a la configuración de compilación. Esto se realiza a través de la pantalla de configuración de Control de versión:

    ![Esto se realiza a través de la pantalla de configuración de Control de versión](teamcity-images/image6.png "Esto se realiza a través de la pantalla de configuración de Control de versión")

    Si no hay ningún proyecto de VCS creado, tiene la opción de crear uno desde la página nueva raíz de VCS que se muestra a continuación:

    ![Si no hay ningún proyecto de VCS creado, tiene la opción de crear uno desde la página nueva raíz de VCS](teamcity-images/image7.png "si no hay ningún proyecto de VCS creado, tiene la opción de crear uno desde la página nueva raíz de VCS")

    Una vez que se ha adjuntado la raíz de VCS, TeamCity will desproteger el proyecto e intenta automáticamente detectan pasos de compilación. Si está familiarizado con TeamCity, puede seleccionar uno de los pasos de compilación detectados. Es seguro omitir los pasos de compilación detectados por ahora.

4. A continuación, configure un desencadenador de compilación. Esto se pondrán en cola una compilación cuando se cumplen ciertas condiciones, por ejemplo, cuando un usuario guarde el código en el repositorio. Captura de pantalla siguiente muestra cómo agregar un desencadenador de compilación:

    ![Esta captura de pantalla muestra cómo agregar un desencadenador de compilación](teamcity-images/image8.png "esta captura de pantalla muestra cómo agregar un desencadenador de compilación") puede verse un ejemplo de cómo configurar un desencadenador de compilación en la captura de pantalla siguiente:

    ![Un ejemplo de cómo configurar un desencadenador de compilación puede verse en esta captura de pantalla](teamcity-images/image9.png "puede verse un ejemplo de cómo configurar un desencadenador de compilación en esta captura de pantalla")

5. La sección anterior, parametrizar el Script de compilación, recomienda almacenar algunos valores como variables de entorno. Estas variables se pueden agregar a la configuración de compilación a través de la pantalla de parámetros. Agregue las variables para la clave de API de prueba en la nube, el identificador del dispositivo iOS y el identificador de dispositivo Android, como se muestra en la captura de pantalla siguiente:

    ![Agregue las variables para la clave de API de prueba en la nube, el identificador del dispositivo iOS y el identificador de dispositivo Android](teamcity-images/image11.png "agregue las variables para la clave de API de prueba en la nube, el identificador del dispositivo iOS y el identificador de dispositivo Android")

6. El último paso es agregar un paso de compilación que invocará el script de compilación para compilar la aplicación y poner en cola la aplicación a Test Cloud. Captura de pantalla siguiente es un ejemplo de un paso de compilación que usa un Rakefile para compilar una aplicación:

    ![Esta captura de pantalla es un ejemplo de un paso de compilación que usa un Rakefile para compilar una aplicación](teamcity-images/image12.png "esta captura de pantalla es un ejemplo de un paso de compilación que usa un Rakefile para compilar una aplicación")

7. En este momento, la configuración de compilación completada. Es una buena idea para desencadenar una compilación para confirmar que el proyecto está configurado correctamente. Una buena forma de hacerlo es confirmar un cambio pequeño, insignificante en el repositorio. TeamCity debe detectar la confirmación e iniciar una compilación.

8. Una vez completada la compilación, inspeccione el registro de compilación y compruebe si hay algún problema o las advertencias con la compilación que requieren atención.

## <a name="summary"></a>Resumen

Esta guía trata sobre cómo usar TeamCity para crear aplicaciones de Xamarin Mobile y, a continuación, enviarlos a Test Cloud. Hemos tratado la creación de un script de compilación para automatizar el proceso de compilación. El script de compilación se encarga de compilar la aplicación, el envío a Test Cloud y esperar los resultados

A continuación, hemos tratado la creación de un proyecto de TeamCity que se pondrán en cola una compilación cada vez que un desarrollador confirma el código y llamará al script de compilación.

## <a name="related-links"></a>Vínculos relacionados

- [Preparando Xamarin.UITests fpr carga](/appcenter/test-cloud/preparing-for-upload/uitest/)
- [Instalación y configuración de TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
