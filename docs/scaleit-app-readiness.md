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

## Grundsätzliches

Eine ScaleIT-App 

* MUSS Docker-basiert sein
* KANN dabei aus einem oder mehreren Docker-Containern bestehen.
* MUSS per Rancher-Template (Rancher 1.6) installierbar sein. 

## Der ScaleIT App-Name

Ein App-Name MUSS das Format ```<tld-unternehmen-appkurzname>``` haben.

* ```tld```: Top-Level-Domain, z.B. ```de```, ```com```
* ```unternehmen```: Unternehmensname: Kleinbuchstaben, ab 2.Stelle mit Zahlen, z.B. ```saegezahnmueller```
* ```appbezeichnung```: Kurzname der App, z.B. ```wartungsoptimierung```, ```pacman```

Beispiele gültiger App-Namen:

    de-ondics-pacman
    de-ondics-simulation2
    de-bigcompany1-app23

## Verzeichnisstruktur einer ScaleIT App

Folgende Verzeichnis-Struktur MUSS in der App bestehen:

    <app_name>
    ├── docker-compose.yml
    ├── README
    ├── DomainSoftware/
    | ├── <app-container-1>/
    |   ├── Dockerfile
    |   └── ...
    ├── Resources/
      ├── Documentation/
      ├── App-Pools/
        └── Rancher1.6/
          └── <app-name>/
            ├── README.md
            ├── config.yml
            └── 0
              ├── docker-compose.yml
              ├── rancher-compose.yml
              └── README.md

Wenn eine App Sidecars hat, MUSS folgendes Verzeichnis dazukommen:

    <app_name>
    └── Platform Sidecars/ 
    . ├── <sidecar-container-1>/
    . | ├── Dockerfile
    . | └── ...
    . └── SidecarContainer2/
    . . └── Dockerfile


## Benutzungsoberfläche / User Interface (UI) und Schnittstellen

Eine ScaleIT-App muss ein Web-UI haben. 

Es KANN mehrere haben (z.B. ein Sidecar-UI oder ein Dokumentations-UI)

## Rancher1.6 Integration

Eine ScaleIT-Apps MUSS in der Umgebung (Rancher 1.6)[https://rancher.com/docs/rancher/v1.6/en/] laufen.

### Rancher-Catalog

Eine ScaleIT-App MUSS ein Rancher-Template unter dem Namen der App anlegen. 

(Offizielle Dokumentation zu privaten Rancher-Catalogs)[https://rancher.com/docs/rancher/v1.6/en/catalog/private-catalog/]

## Networking einer App

# Zugriff von außerhalb der ScaleIT-Plattform auf Apps

Eine ScaleIT-App KANN Schnittstellen nach "außen" haben, z.B. ein oder mehrere Web-UIs, REST-APIs, etc.

In Produktion wird auf diese Schnittstellen normalerweise von außerhalb der ScaleIT-Plattform 
zugegriffen (z.B. per Web-Browsern von Arbeitsplätzen). 

ScaleIT Core Enterprise-Edition: Hierzu gibt es den https-Proxy, der nach außen alle Schnittstellen
über http/https (Port 80/443) anbietet und intern auf die Apps unter deren Rancher-Netzwerknamen zugreift.

## Networking innerhalb einer App (zwischen Container/Services)

Innerhalb einer App kann ein (Docker-Compose-)Service über seinen Namen aufgerufen werden.

Beispiel: aus dem Container/Service ```de-ondics-pacman-app``` kann auf den 
Container/Service  ```de-ondics-pacman-db``` zugegriffen werden:

    $ ping de-ondics-pacman
    
## Networking zwischen Apps

Innerhalb Rancher kann auf einen Service einer anderen App (eines anderen Rancher-Stacks) so zugegriffen werden:
```<service_name>.<appname>```:

    $ ping de-ondics-pacman-db.de-ondics-pacman

(Weitere Infos zum Rancher-Networking zwischen Stacks/ScaleIT-Apps)[https://rancher.com/docs/rancher/v1.6/en/cattle/internal-dns-service/]

## Networking zu Entwicklungszwecken

Die Web-UIs und Schnittstellen SOLLEN über TCP/IP-Ports in der App erreichbar sein.

Grund: Zu Entwicklungszwecken können so mehrere Web-Services parallel aufgerufen und getestet werden
(z.B. UI der App, UI des Sidecars, REST-API). Im Betrieb auf einer ScaleIT-Plattform wird die App
dann in der Regel über einen https-Proxy über Port 80 aufgerufen und die speziellen Ports werden 
nicht mehr benötigt. Das wird durch ein entsprechendes ```docker-compose.yml``` im Rancher-Template konfiguriert.

Falls ein Port auch in Produktion verwendet werden soll, MUSS dieser über alle ScaleIT App-Ökosysteme
hinweg eindeutig sein. 

(Liste von Ports, die App-Herstellern zugewiesen wurden)[scaleit-app-ports.md]



