# ScaleIT App-Readiness

Die ScaleIT App-Readiness beschreibt, welche Anforderungen eine App erfüllen muss, 
damit sie eine ScaleIT-App wird. 

App-Ökosystembetreiber achten darauf, dass nur ScaleIT-Apps  in deren App-Pools stehen.
Nur so ist sichergestellt, dass Apps auf allen ScaleIT-Plattform ausgeführt werden 
und Betriebsprozesse (Installation, Upgrade, Löschen, ...) funktionieren können.

Bei der ScaleIT App-Readiness wird unterschieden in 

* MUSS: Zwingende Anforderungen
* SOLL: Empfehlungen
* KANN: Erläuternde Hinweise zum App-Bau 

# Architektur

## Betriebsumgbungen = ScaleIT App-Runtime-Environments (SARE)

Die Betriebsumgebungen einer Apps werden als ScaleIT App-Runtime-Environments (SARE) bezeichnet.
SAREs sind immer Docker-basiert. 

Folgende Betriebsumgebungen sind definiert:

* ScaleIT Rancher 1.6
* ScaleIT Docker-Compose
* ScaleIT Kubernetes

Eine ScaleIT-App 

* MUSS die AEE ScaleIT-Rancher 1.6 unterstützen
* SOLL die AEE ScaleIT-Docker-Compose unterstützen
* KANN die AEE ScaleIT-Kubernetes unterstützen

## Anforderungen an eine ScaleIT-App?

Eine ScaleIT-App 

* MUSS Docker-basiert sein
* KANN aus einem oder mehreren Docker-Containern bestehen.

Die App SOLL self-contained sein, d.h. sie sollte nach der Installation
isoliert lauffähig sein. Wenn sie externe Ressourcen benötigt (z.B. eine
Datenbank), soll diese in der App enthalten sein. Die Datenbank soll
bei der Installation konfigurierbar sein, so dass eine andere Datenbank 
(z.B. aus Datensicherheitsgründen) verwendet werden kann.

In jeder ScaleIT-Plattform sind folgende Dienste verfügbar, die eine
App nicht selber enthalten muss:

* MQTT-Broker (Ports 1883/1884)
* Verzeichnisdienst ETCD (Port 49501)

Der MQTT-Broker und der Verzeichnisdienst müssen bei der Installation 
konfigurierbar sein (IP-Adresse/Hostname, Port).

Für die Nutzung dieser Services in einer App gilt (MUSS):

* [ScaleIT Messaging mit Mqtt](scaleit-messaging.md)
* [ScaleIT Directory-Services mit ETCD](scaleit-etcd.md)


## Der ScaleIT App-Name

Ein App-Name MUSS das Format ```<tld-unternehmen-appkurzname>``` haben.

* ```tld```: Top-Level-Domain, z.B. ```de```, ```com```
* ```unternehmen```: Unternehmensname: Kleinbuchstaben, ab 2.Stelle mit Zahlen, z.B. ```saegezahnmueller```
* ```appbezeichnung```: Kurzname der App, z.B. ```wartungsoptimierung```, ```pacman```

Beispiele gültiger App-Namen:

    de-ondics-pacman
    de-ondics-simulation2
    de-bigcompany1-app23
    de-companyx-maschine1-sps

## Verzeichnisstruktur einer ScaleIT App

Folgende Verzeichnis-Struktur MUSS in der App bestehen:

    01      <app_name>
    02      ├── docker-compose.yml
    03      ├── README.md
    04      ├── DomainSoftware/
    05      | ├── <app-container-1>/
    06      |   ├── Dockerfile
    07      |   └── ...
    08      ├── Resources/
    09      ├── Documentation/
    10        ├── AppRuntimeEnvironments/
    11          └── ScaleITRancher1.6/
    12            └── <app-name>/
    13              ├── README.md
    14              ├── config.yml
    15              └── 0
    16                ├── docker-compose.yml
    17                ├── rancher-compose.yml
    18                └── README.md

Wenn eine App das SARE ScaleIT-Docker-Compose unterstützt, MUSS folgendes Verzeichnis aufgenommen werden

    50          └── ScaleIT-Docker-Compose/
    51            └── docker-compose.yml


Wenn eine App Sidecars hat, MUSS folgendes Verzeichnis dazukommen:

    61      <app_name>
    62      └── Platform Sidecars/ 
    63      . ├── <sidecar-container-1>/
    64      . | ├── Dockerfile
    65      . | └── ...
    66      . └── SidecarContainer2/
    67      . . └── Dockerfile


## Benutzungsoberfläche / User Interface (UI) und Schnittstellen

Eine ScaleIT-App MUSS ein Web-UI haben. 

Es KANN mehrere Web-UI's haben (z.B. ein Sidecar-UI oder ein Dokumentations-UI)

## Rancher1.6 Integration

Eine ScaleIT-Apps MUSS in der Umgebung [Rancher 1.6](https://rancher.com/docs/rancher/v1.6/en/) laufen.

### Rancher-Catalog

Eine ScaleIT-App MUSS ein Rancher-Template unter dem Namen der App anlegen. 

[Offizielle Dokumentation zu privaten Rancher-Catalogs](https://rancher.com/docs/rancher/v1.6/en/catalog/private-catalog/)

## Networking einer App

### Zugriff von außerhalb der ScaleIT-Plattform auf Apps

Eine ScaleIT-App KANN Schnittstellen nach "außen" haben, z.B. ein oder mehrere Web-UIs, REST-APIs, etc.

In Produktion wird auf diese Schnittstellen normalerweise von außerhalb der ScaleIT-Plattform 
zugegriffen (z.B. per Web-Browsern von Arbeitsplätzen). 

ScaleIT Core Enterprise-Edition: Hierzu gibt es den https-Proxy, der nach außen alle Schnittstellen
über http/https (Port 80/443) anbietet und intern auf die Apps unter deren Rancher-Netzwerknamen zugreift.

### Networking innerhalb einer App (zwischen Container/Services)

Innerhalb einer App kann ein (Docker-Compose-)Service über seinen Namen aufgerufen werden.

Beispiel: aus dem Container/Service ```de-ondics-pacman-app``` kann auf den 
Container/Service  ```de-ondics-pacman-db``` zugegriffen werden:

    $ ping de-ondics-pacman
    
### Networking zwischen Apps

Innerhalb Rancher kann auf einen Service einer anderen App (eines anderen Rancher-Stacks) so zugegriffen werden:
```<service_name>.<appname>```:

    $ ping de-ondics-pacman-db.de-ondics-pacman

[Weitere Infos zum Rancher-Networking zwischen Stacks/ScaleIT-Apps](https://rancher.com/docs/rancher/v1.6/en/cattle/internal-dns-service/)

### Networking zu Entwicklungszwecken

Die Web-UIs und Schnittstellen SOLLEN über TCP/IP-Ports in der App erreichbar sein.

Grund: Zu Entwicklungszwecken können so mehrere Web-Services parallel aufgerufen und getestet werden
(z.B. UI der App, UI des Sidecars, REST-API). Im Betrieb auf einer ScaleIT-Plattform wird die App
dann in der Regel über einen https-Proxy über Port 80 aufgerufen und die speziellen Ports werden 
nicht mehr benötigt. Das wird durch ein entsprechendes ```docker-compose.yml``` im Rancher-Template konfiguriert.

Falls ein Port auch in Produktion verwendet werden soll, MUSS dieser über alle ScaleIT App-Ökosysteme
hinweg eindeutig sein. 

[Liste von Ports, die App-Herstellern zugewiesen wurden](scaleit-app-ports.md)



