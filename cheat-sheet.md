# ScaleIT App Development Cheat Sheet

oder: alles wichtige, in Kurzform.

## Docker

Mit Docker werden Container gemanaged.
Die Spezifikation eines Containers/Images steht im ```Dockerfile```.  

    docker ps       # alle laufenden Docker container anzeigen
    docekr images   # welche Docker Images sind lokal verfügbar?
    sudo docker service restart     # Docker komplett neu starten
    docker info     # Version etc.
    
 # Docker-Compose
 
 Docker-Compose fasst viele Docker-befehle zusammen und macht Container-Gruppen leichter handhabbar.
 Die Spezifikation eines Docker-Compose steht in ```docker-compose.yml```.  

    docker-compose config    # Prüfen, ob ein docker-compose.yml korrekt ist
    docker-compose up -d     # Startet alle Container im Hintergrund
    dockeer-compose up       # Startet alle Container im Vordergrund (gut für Debugging mit Log-Anzeige)
    
 
