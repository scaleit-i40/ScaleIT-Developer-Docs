# ScaleIT App-Development Cheat Sheet

Hier stehen die wichtigsten Vorkenntnisse und Befehle zur ScaleIT App-Entwicklung.

## Linux CLI (Kommandozeile)

    $ ls -l
    $ cd <verzeichnis>
    $ grep -Inri <string> .  # In diesem und allen enthaltenen Verzeichnissen nach <string> suchen

## Docker

    $ docker ps            # welche Container laufen?
    $ docker images        # welche Images sind lokal vorhanden?
    $ docker rmi <i-id>    # Löschen eines Images
    $ docker stop <c-id>   # Stoppen eines Container
    $ docker kill <c-id>   # Stoppen und Löschen eines Containes
    $ docker system prune  # Alle nicht verwendeten Container und Images löschen
    
Um ein Image in die Registry zu bringen [```docker push``` Doku](https://docs.docker.com/engine/reference/commandline/push/):

    $ docker login <registry>:5000   # Anmelden bei der registry
    $ docker push <i-id>             # Image in Registry hochladen. 
    $ docker logout                  # wieder Abmelden

## Docker-Compose

    $ docker-compose config     # prüfen, ob yml-Datei ok ist
    $ docker-compose up –d      # starten aller Services
    $ docker-compose down       # Stoppen aller Services
    $ docker-compose ps         # Anzeige laufender Services
    $ docker-compose restart    # Neustart bei Änderungen
    
## Git

    $ git clone <repo>        # entferntes GIT Repo lokal herkopieren
    $ git status              # Alle Änderungen anzeigen
    $ git init .              # Neues GIT-Repo beginnen
    $ git add …               # Dateien zum Repo hinzufügen
    $ git commit –m „…“ –a    # alle Dateien committen
    $ git push origin master  # lokale Änderungen in entferntes GIT Repo schieben
    $ git diff <datei>        # aktuelle Änderungen anzeigen

## Sonstige Entwicklungs-Tools

### Make

Das Makefile im Root einer App bietet folgende Befehle:

Hinweis: der gewünschte App-Pool muss im ```Makefile``` konfiguriert werden.

    $ make                            # Befehle anzeigen
    $ make build                      # Alle Images der App bauen
    $ make change-app-pool            # App-Pool in diversen docker-compose.yml umstellen
    $ make update-rancher-templates   # Rancher-Git aktualisieren
    $ make push                       # In den App-Pool übertragen
 
    
    
