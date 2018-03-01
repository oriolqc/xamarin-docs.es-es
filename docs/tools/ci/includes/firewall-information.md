## <a name="firewall-configuration"></a>Configuración del Firewall

En orden para las pruebas que se enviarán a Xamarin Test Cloud, el equipo que lo envía las pruebas debe ser capaz de comunicarse con los servidores de prueba en la nube. Los servidores de seguridad deben configurarse para permitir el tráfico de red hacia y desde los servidores ubicados en **testcloud.xamarin.com** en los puertos 80 y 443. Este punto de conexión está administrado por DNS y la dirección IP está sujeta a cambios. 

En algunas situaciones, una prueba (o un dispositivo que ejecuta la prueba) debe comunicarse con servidores web protegidos por un firewall. En este escenario se debe configurar el firewall para permitir el tráfico desde las siguientes direcciones IP:

* **195.249.159.238**
* **195.249.159.239**
