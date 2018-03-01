
Al compilar aplicaciones para iOS en Visual Studio y el agente de compilación para Mac, el lote .app no se vuelve a copiar en la máquina de Windows. Las herramientas de Xamarin para Visual Studio 7.4 agregan una nueva propiedad `CopyAppBundle` que permite a las compilaciones de integración continua copiar lotes .app en Windows.

Para utilizar esta funcionalidad, agregue la propiedad `CopyAppBundle` al elemento .csproj, en el grupo de propiedades en el que quiera aplicarla. En el ejemplo siguiente se muestra cómo copiar el lote .app en el equipo de Windows para una compilación **Depuración** que tiene como objetivo **iPhoneSimulator**:

    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">
        <CopyAppBundle>true</CopyAppBundle>
    </PropertyGroup>

