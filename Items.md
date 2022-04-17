# Curso de Zabbix

## Continuación Zabbix Proxy

Comprobar que la instalación este actualizada

```
# apt update
# apt upgrade
```

Para conectar nuestro agente existe la posibilidad, de hacerlo sin encriptación, con certificados, o con claves precompartidas (PSK), para proteger la conexion entre servidor y agente, generar una PSK:

```
# sh -c "openssl rand -hex 32 > /etc/zabbix/zabbix_agentd.psk"
# cat /etc/zabbix/zabbix_agentd.psk
b7ebd85bd76aadee22f8a3c51e8bb37e981acb0d48aa0d346c55001f1f2ff1d8
```

Instalar el agente de Zabbix en el Proxy
Editar el archivo de configuracion: /etc/zabbix/zabbix_agentd.conf

```
PidFile=/run/zabbix/zabbix_agentd.pid
LogFile=/var/log/zabbix/zabbix_agentd.log
LogFileSize=3
Server=127.0.0.1 
ServerActive=127.0.0.1
Hostname=Zabbix Proxy 01
Include=/etc/zabbix/zabbix_agentd.d/*.conf
TLSConnect=psk
TLSAccept=psk
TLSPSKIdentity=PSK 002
TLSPSKFile=/etc/zabbix/zabbix_agentd.psk
```

Reiniciar los servicios, comprobar sus estados y habilitarlos

```
# systemctl restart zabbix-agent 
# systemctl status zabbix-agent
● zabbix-agent.service - Zabbix Agent
     Loaded: loaded (/lib/systemd/system/zabbix-agent.service; disabled; vendor preset: enabled)
     Active: active (running) since Tue 2022-04-05 14:43:02 UTC; 16min ago
   Main PID: 646 (zabbix_agentd)
      Tasks: 6 (limit: 1066)
     Memory: 11.2M
     CGroup: /system.slice/zabbix-agent.service
             ├─646 /usr/sbin/zabbix_agentd -c /etc/zabbix/zabbix_agentd.conf
             ├─651 /usr/sbin/zabbix_agentd: collector [idle 1 sec]
             ├─652 /usr/sbin/zabbix_agentd: listener #1 [waiting for connection]
             ├─653 /usr/sbin/zabbix_agentd: listener #2 [waiting for connection]
             ├─654 /usr/sbin/zabbix_agentd: listener #3 [waiting for connection]
             └─655 /usr/sbin/zabbix_agentd: active checks #1 [idle 1 sec]

Apr 05 14:43:02 manual systemd[1]: Starting Zabbix Agent...
Apr 05 14:43:02 manual systemd[1]: Started Zabbix Agent.

systemctl enable zabbix-agent
```

![](img/diez.png)

Ir a la opcion de <span style="color:#2471A3">Configuration/Hosts/Create host</span> 

Configurar:

 <span style="color:#2471A3">Host name</span>

<span style="color:#2471A3">Groups</span>

<span style="color:#2471A3">Interfaces</span>
agregar del tipo <span style="color:#138D75">Agent</span> con la <span style="color:#2471A3">IP address</span> y <span style="color:#2471A3">Port</span> correspondientes

![](img/once.png)

Ir a la pestaña <span style="color:#2471A3">Encryption</span> seleccionar <span style="color:#138D75">PSK</span>, y agregar el valor de <span style="color:#2471A3">PSK Identity</span> y <span style="color:#2471A3">PSK</span>

![](img/doce.png)

Ir a la opcion de <span style="color:#2471A3">Monitoring/Hosts</span> y verficar que el host agregado (192.168.56.144) tenga la disponibilidad en verde y no es asi y esta en rojo, verifique los datos y/o eventuales Firewall

