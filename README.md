# Media Server Automatizado

Este repositorio describe una arquitectura automatizada para la gestión, descarga y visualización de contenido multimedia (películas, series y música) utilizando diversas herramientas de código abierto.

## Arquitectura del Sistema

### Flujo de Solicitud de Contenido

1. **Solicitud del usuario**  
   - **[Jellyseerr](https://github.com/Fallenbagel/jellyseerr)**: los usuarios pueden solicitar películas o series.
   - **[Ombi](https://github.com/Ombi-app/Ombi)**: permite solicitar películas, series y **música**.

2. **Procesamiento de la solicitud**  
   Dependiendo del tipo de contenido, se envía a:
   - **[Radarr](https://radarr.video/)** (películas)
   - **[Sonarr](https://sonarr.tv/)** (series)
   - **[Lidarr](https://lidarr.audio/)** (música)

3. **Búsqueda y descarga del contenido**  
   - Radarr, Sonarr o Lidarr utilizan **indexadores** para buscar torrents del contenido solicitado.
   - Si se encuentra, se envía al cliente de torrent **[qBittorrent](https://www.qbittorrent.org/)** para descargarlo.
   - El contenido descargado se almacena en una carpeta de **seeding** para compartirlo y mejorar el ratio en el tracker.

4. **Organización del contenido**  
   - Radarr, Sonarr y Lidarr mueven/copian el contenido descargado a sus respectivas bibliotecas de medios.
   - Estas bibliotecas son leídas por:
     - **[Jellyfin](https://jellyfin.org/)** (media server libre)
     - **[Plex](https://www.plex.tv/)** (media server con funciones premium)

5. **Subtítulos**  
   - **[Bazarr](https://www.bazarr.media/)** se encarga de buscar e incrustar subtítulos automáticamente en el contenido gestionado por Radarr y Sonarr.

6. **Gestión de indexadores**  
   - **[Prowlarr](https://wiki.servarr.com/prowlarr)** centraliza y administra los indexadores de torrents.
   - Se sincroniza automáticamente con Radarr, Sonarr y Lidarr.

## Diagrama del Flujo

```mermaid
graph TD;
    Usuario-->|Solicita|Jellyseerr
    Usuario-->|Solicita|Ombi
    Jellyseerr-->|Películas|Radarr
    Jellyseerr-->|Series|Sonarr
    Ombi-->|Música|Lidarr
    Radarr-->|Busca en indexadores|Prowlarr
    Sonarr-->|Busca en indexadores|Prowlarr
    Lidarr-->|Busca en indexadores|Prowlarr
    Prowlarr-->|Indexadores|Internet
    Radarr-->|Torrent|qBittorrent
    Sonarr-->|Torrent|qBittorrent
    Lidarr-->|Torrent|qBittorrent
    qBittorrent-->|Descarga|CarpetaSeed
    qBittorrent-->|Seeding|Tracker
    Radarr-->|Copia a|BibliotecaPelículas
    Sonarr-->|Copia a|BibliotecaSeries
    Lidarr-->|Copia a|BibliotecaMúsica
    BibliotecaPelículas-->|Visualiza|Jellyfin
    BibliotecaSeries-->|Visualiza|Jellyfin
    BibliotecaMúsica-->|Visualiza|Jellyfin
    BibliotecaPelículas-->|Visualiza|Plex
    BibliotecaSeries-->|Visualiza|Plex
    BibliotecaMúsica-->|Visualiza|Plex
    Bazarr-->|Subtítulos|Radarr
    Bazarr-->|Subtítulos|Sonarr