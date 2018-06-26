---
title: Usar equipo ciudad con Xamarin
description: Esta guía describe los pasos necesarios con el uso de TeamCity para compilar aplicaciones para dispositivos móviles y, a continuación, enviarlos a Xamarin Test Cloud.
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: e7279c03c730e95f211b555e5b832942c19ea8aa
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935420"
---
# <a name="using-team-city-with-xamarin"></a>Usar equipo ciudad con Xamarin

_Esta guía describe los pasos necesarios con el uso de TeamCity para compilar aplicaciones para dispositivos móviles y, a continuación, enviarlos a Xamarin Test Cloud._

Como se describe en el [Introducción a la integración continua](~/tools/ci/intro-to-ci.md) Guía de integración continua (CI) es una práctica útil al desarrollar aplicaciones móviles de calidad. Hay muchas opciones viables para software de servidor de integración continua; Esta guía se centrará en [TeamCity](http://www.jetbrains.com/teamcity/) de JetBrains.

Hay varias permutaciones diferentes de una instalación de TeamCity. La siguiente es una lista de algunos de ellos:

- **Servicio de Windows** : en este escenario, TeamCity comienza cuando se arranca Windows como un servicio de Windows. Se deben emparejar con un Host de compilación de Mac para compilar las aplicaciones de iOS.

- **Inicie el demonio en OS X** : conceptualmente, esto es muy similar a la que se ejecuta como un servicio de Windows se describe en el paso anterior. De forma predeterminada, las compilaciones se ejecutará con la cuenta raíz.

- **Cuenta de usuario en OS X** : es posible ejecutar TeamCity bajo una cuenta de usuario que se inicia cada vez que el usuario inicia sesión.

De los escenarios anteriores, la ejecución de TeamCity bajo una cuenta de usuario en OS X es la más sencilla y más fácil para el programa de instalación.

Hay varios pasos implicados con la configuración de TeamCity:

- **Instalar TeamCity** : la instalación de TeamCity no se trata en esta guía. Esta guía se da por supuesto que TeamCity esté instalado y ejecutándose en una cuenta de usuario. Las instrucciones en [instalar TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation) puede encontrarse en el [TeamCity 8 documentación](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) por JetBrains.

- **Preparar el servidor de compilación** : este paso incluye la instalación del software necesario, herramientas, y los certificados necesitan para crear aplicaciones móviles y prepararlos para la distribución.

- **Crear Script de compilación A** : este paso no es estrictamente necesario, pero un script de compilación es una herramienta útil para compilar aplicaciones desatendidas. Mediante un script de compilación le ayudará a resolver problemas de compilación que pueden surgir y proporciona una forma coherente y repetible de crear los archivos binarios para la distribución, incluso si no se ha practicado la integración continua.

- **Crear un proyecto de TeamCity** : una vez completados los tres pasos anteriores, debemos crear un proyecto de TeamCity que contendrá todos los metadatos necesarios para recuperar el código fuente, compile los proyectos y enviar las pruebas para Xamarin Test Cloud.

## <a name="requirements"></a>Requisitos

Experiencia con [Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud) es necesario.

Es necesaria la familiaridad con TeamCity 8.1. La instalación de TeamCity queda fuera del ámbito de este documento. Se supone que TeamCity está instalado en OS X Mavericks y se está ejecutando bajo una cuenta de usuario normal y no la cuenta raíz.

El servidor de compilación debe ser un equipo independiente, ejecute OS X, que se dedica a la integración continua. Idealmente, el servidor de compilación no será responsable de ningún otro rol, por ejemplo, un servidor de base de datos, el servidor web o la estación de trabajo de desarrollador.

> [!IMPORTANT]
> Esta guía no cubre una "equipo sin periféricos" instalación de Xamarin.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>Preparar el servidor de compilación

Es un paso fundamental para configurar un servidor de compilación instalar todas las herramientas necesarias, el software y los certificados para compilar las aplicaciones móviles. Es importante que el servidor de compilación pueda compilar la solución móvil y ejecutar las pruebas. Para minimizar los problemas de configuración, el software y las herramientas deben instalarse en la misma cuenta de usuario que está hospedando TeamCity. La siguiente es una lista de lo que se requiere:

1. **Visual Studio para Mac** : Esto incluye Xamarin.iOS y Xamarin.Android.
2. **Inicie sesión en el almacén de componentes de Xamarin** : este paso es opcional y solo es necesario si la aplicación usa componentes desde el almacén de componentes de Xamarin. Proactivamente iniciar sesión en el almacén de componentes en este momento evitará problemas cuando una compilación TeamCity intenta compilar la aplicación.
3. **Xcode** : Xcode es necesario para las aplicaciones de iOS de compilación y de inicio de sesión.
4. **Herramientas de línea de comandos de Xcode** : este procedimiento se describe en el paso 1 de la sección de instalación de la [actualizar Ruby con rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) guía.
5. **Firma de identidad y perfiles de aprovisionamiento** : importar los certificados y aprovisionamiento de perfil mediante XCode. Consulte la Guía de Apple en [Exportar identidades de firma y perfiles de aprovisionamiento](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) para obtener más detalles.
6. **Android keystore** : copiar el keystore Android necesario en un directorio que el usuario de TeamCity tiene acceso, es decir, `~/Documents/keystores/MyAndroidApp1`.
7. **Calabash** : este es un paso opcional si la aplicación tiene pruebas escritas con Calabash. Vea la [instalar Calabash en OS X Mavericks](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/) guía y la [actualizar Ruby con rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/) guía para obtener más información.

El siguiente diagrama muestra todos estos componentes:

![](teamcity-images/image1.png "Este diagrama muestra todos estos componentes")

Una vez que se ha instalado todo el software, inicie sesión en la cuenta de usuario y confirmar que todo el software está instalado correctamente y está trabajando. Esto debe incluir compilar la solución y el envío de la aplicación de prueba en la nube. Esto puede simplificar enormemente al ejecutar el script de compilación, como se describe en la sección siguiente.

## <a name="create-a-build-script"></a>Crear un Script de compilación

Aunque es posible completamente TeamCity controlar todos los aspectos de compilación y enviar las aplicaciones móviles a Test Cloud por sí mismo, se recomienda encarecidamente crear un script de compilación. Un script de compilación proporciona las siguientes ventajas:

1. **Documentación** : un script de compilación actúa como una forma de documentación sobre cómo se crea el software. Esto quita algunos de lo "mágica" que está asociado con la implementación de la aplicación y permite a los desarrolladores centrarse en las funciones.
1. **Repetibilidad** : un script de compilación garantiza que cada vez que la aplicación se compila y se implementa, se produce exactamente igual, independientemente de quién o qué hace el trabajo. Esta coherencia repetible quita los problemas o errores que pueden derivar en debido a una compilación ejecutada incorrectamente o los errores humanos.
1. **Control de versiones** : un script de compilación puede incluirse en el sistema de control de código fuente. Esto significa que los cambios en el script de compilación se pueden realiza un seguimiento, supervisar y corregir si se encuentran errores o imprecisiones.
1. **Preparar el entorno** : un script de compilación puede incluir una lógica para instalar cualquier necesario 3rd dependencias de entidad. Esto garantizará que las aplicaciones se compilan con los componentes adecuados.

El script de compilación puede ser tan simple como un archivo de Powershell (en Windows) o una secuencia de comandos de bash (en OS X). Al crear el script de compilación, hay varias opciones de lenguajes de scripting:

- [**Inclinación** ](https://github.com/jimweirich/rake) : se trata de un lenguaje específico de dominio (DSL) para compilar proyectos, tomando como base Ruby. Inclinación tiene la ventaja de popularidad y un ecosistema completo de bibliotecas.

- [**psake** ](https://github.com/psake/psake) : se trata de una biblioteca de Windows Powershell para la creación de software

- [**FALSIFICAR** ](http://fsharp.github.io/FAKE/) : se trata de un ADSL basado en F # que hace posible utilizar bibliotecas de .NET existentes si es necesario.

El lenguaje de scripting que se usa depende de sus preferencias y requisitos. El [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash) ejemplo contiene un ejemplo del uso de inclinación como una [Generar script](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile).

> [!NOTE]
> Es posible utilizar un sistema de compilación basado en XML como MSBuild o NAnt, pero estos falta la expresividad y el mantenimiento de un DSL que está dedicado a la creación de software.

### <a name="parameterizing-the-build-script"></a>Parametrizar la secuencia de comandos de compilación

El proceso de crear y probar el software necesita información que debe mantenerse en secreto. En particular creando un APK puede requerir una contraseña para el almacén de claves o el alias de la clave en el almacén de claves. Del mismo modo, Test Cloud requiere una clave de API que sea única a un desarrollador. Estos tipos de valores no deben ser difícil codificada en el script de compilación. En su lugar, se deben pasar como variables en el script de compilación.

Menos sensibles son valores como el Id. de dispositivo o el identificador de dispositivo Android que identificar qué dispositivos de prueba en la nube debe usar para probar de iOS se ejecuta. Estos no son valores que necesitan ser protegido, pero que pueden cambiar de una compilación a otra.

Almacenamiento de estos tipos de variables fuera de la secuencia de comandos de compilación también facilita compartir el script de compilación dentro de una organización con desarrolladores, por ejemplo. Los desarrolladores pueden usar el mismo script exacto que el servidor de compilación, pero pueden usar sus propias keystore y claves de API.

Hay dos opciones para almacenar estos valores confidenciales:

- **Un archivo de configuración** : para proteger la clave de API de prueba en la nube, este valor no debe estar activado en el control de versiones. El archivo se puede crear para cada máquina. Cómo se leen los valores de este archivo depende del lenguaje de scripting usado.

- **Las variables de entorno** : pueden configurarse fácilmente en una por equipo y se independiente del lenguaje de scripting subyacente.

Hay ventajas y desventajas de cada una de estas opciones. TeamCity funciona bien con variables de entorno, por lo que esta guía recomienda esta técnica al crear scripts de compilación.

### <a name="build-steps"></a>Pasos de compilación

El script de compilación debe llevar a cabo los pasos siguientes:

- **Compile la aplicación** : Esto incluye la firma de la aplicación con el perfil de aprovisionamiento correcto.

- **Enviar la aplicación Xamarin Test Cloud** : Esto incluye la firma y zip alinear el APK con el almacén de claves adecuado.

Estos dos pasos se explican con más detalle a continuación.

#### <a name="compiling-a-xamarinios-application"></a>Compilar una aplicación de Xamarin.iOS

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>Compilar una aplicación Xamarin.Android

Para compilar una aplicación Android, use **xbuild** (o **msbuild** en Windows):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

Tenga en cuenta que para compilar la aplicación de Xamarin para Android **xbuild** el proyecto lo que se utiliza para compilar la aplicación de iOS **xbuild** requiere que la solución.

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>Enviar Xamarin.UITests a prueba en la nube

UITests se envía mediante la `test-cloud.exe` aplicación, tal como se muestra en el fragmento siguiente:

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

Cuando se ejecuta la prueba, se devolverán los resultados de pruebas en la forma de archivo XML de estilo NUnit llama **report.xml**. TeamCity se mostrará la información en el registro de compilación.

Para obtener más información sobre cómo enviar UITests a prueba en la nube, consulte esta guía en [Xamarin.UITests preparar para la carga](/appcenter/test-cloud/preparing-for-upload/uitest/).

#### <a name="submitting-calabash-tests-to-test-cloud"></a>Enviar Calabash pruebas para probar en la nube

Pruebas de Calabash se envían con el `test-cloud` indicador, tal como se muestra en el fragmento siguiente:

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
Para enviar una aplicación Android para probar en la nube, es necesario volver a crear el servidor de prueba APK con calabash android:

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
Para obtener más información sobre cómo enviar pruebas Calabash, consulte la Guía de Xamarin en [enviar Calabash pruebas a Test Cloud](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/).

## <a name="creating-a-teamcity-project"></a>Crear un proyecto TeamCity

Una vez que se instala TeamCity y Visual Studio para Mac puede compilar el proyecto, es el momento de crear un proyecto en TeamCity para compilar el proyecto y enviarlo a la prueba en la nube.

1. Inicia sesión en TeamCity a través del explorador web. Navegue hasta el proyecto de raíz:

    ![Navegue hasta el proyecto raíz](teamcity-images/image2.png "navegue hasta el proyecto raíz") bajo el proyecto raíz, cree un nuevo proyecto secundario:

    ![Navegue a la raíz de proyecto debajo de la directiva el proyecto raíz, cree un nuevo proyecto secundario](teamcity-images/image3.png "navegar al proyecto debajo de la directiva la raíz de proyecto raíz, cree un nuevo proyecto secundario")
2. Una vez creado el subproyecto, agregue una nueva configuración de compilación:

    ![Una vez creado el subproyecto, agregue una nueva configuración de compilación](teamcity-images/image5.png "una vez que se ha creado el subproyecto, agregue una nueva configuración de compilación")
3. Asociar un proyecto de VC se a la configuración de compilación. Esto se realiza a través de la pantalla de configuración de Control de versión:

    ![Esto se realiza a través de la pantalla de configuración de Control de versión](teamcity-images/image6.png "Esto se realiza a través de la pantalla de configuración de Control de versión")

    Si no hay ningún proyecto de VC se crea, tiene la opción de crear uno a partir de la página nueva raíz VCS que se muestra a continuación:

    ![Si no hay ningún proyecto de VC se crea, tiene la opción de crear uno a partir de la página nueva raíz VCS](teamcity-images/image7.png "si no hay ningún proyecto de VC se crea, tiene la opción de crear uno a partir de la página nueva raíz de VCS")

    Una vez que se ha adjuntado la raíz de VCS, TeamCity will desproteger el proyecto e intenta automáticamente detectan pasos de compilación. Si está familiarizado con TeamCity, puede seleccionar uno de los pasos de compilación detectado. Es seguro pasar por alto los pasos de compilación detectado por ahora.

4. A continuación, configure un desencadenador de compilación. Esto se situarán en cola una compilación cuando se cumplen ciertas condiciones, por ejemplo, cuando un usuario confirma el código en el repositorio. Captura de pantalla siguiente muestra cómo agregar un desencadenador de la compilación:

    ![Esta captura de pantalla muestra cómo agregar un desencadenador de compilación](teamcity-images/image8.png "esta captura de pantalla muestra cómo agregar un desencadenador de compilación") se puede ver un ejemplo de cómo configurar un desencadenador de compilación en la captura de pantalla siguiente:

    ![Un ejemplo de cómo configurar un desencadenador de compilación puede verse en esta captura de pantalla](teamcity-images/image9.png "se puede ver un ejemplo de cómo configurar un desencadenador de compilación en esta captura de pantalla")

5. La sección anterior, la parametrización de la secuencia de comandos de compilación, recomienda almacenar algunos valores como variables de entorno. Estas variables se pueden agregar a la configuración de compilación a través de la pantalla de parámetros. Agregue las variables para la clave de API de prueba en la nube, el identificador de dispositivo de iOS y el identificador de dispositivo Android, como se muestra en la captura de pantalla siguiente:

    ![Agregue las variables para la clave de API de prueba en la nube, el identificador de dispositivo de iOS y el identificador de dispositivo Android](teamcity-images/image11.png "agregar las variables para la clave de API de prueba en la nube, el identificador de dispositivo de iOS y el identificador de dispositivo Android")

6. El último paso es agregar un paso de compilación que se va a invocar el script de compilación para compilar la aplicación y poner la aplicación en nube de prueba. Captura de pantalla siguiente es un ejemplo de un paso de compilación que utilice un Rakefile para compilar una aplicación:

    ![Esta captura de pantalla es un ejemplo de un paso de compilación que utilice un Rakefile para compilar una aplicación](teamcity-images/image12.png "esta captura de pantalla es un ejemplo de un paso de compilación que utilice un Rakefile para compilar una aplicación")

7. En este punto, la configuración de compilación está completa. Es una buena idea para desencadenar una compilación para confirmar que el proyecto se ha configurado correctamente. Una buena manera de hacerlo consiste en confirmar un cambio pequeño, insignificante en el repositorio. TeamCity debe detectar la confirmación e iniciar una compilación.

8. Una vez completada la compilación, inspeccionar el registro de compilación y ver si hay algún problema o las advertencias con la compilación que requieren atención.

## <a name="summary"></a>Resumen

Esta guía trata cómo usar TeamCity para compilar aplicaciones de Xamarin Mobile y, a continuación, enviarlos a prueba en la nube. Analizamos la creación de un script de compilación para automatizar el proceso de compilación. El script de compilación se encarga de compilar la aplicación, el envío a la nube de prueba y esperan los resultados

A continuación, hemos aprendido cómo crear un proyecto en TeamCity que se pondrán en cola una compilación cada vez que un desarrollador confirma código y llamará a la secuencia de comandos de compilación.

## <a name="related-links"></a>Vínculos relacionados

- [Preparar Xamarin.UITests fpr carga](/appcenter/test-cloud/preparing-for-upload/uitest/)
- [Instalar y configurar TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
