# Media Server Automatizado

Este repositorio describe una arquitectura automatizada para la gestión, descarga y visualización de contenido multimedia (películas y series) utilizando diversas herramientas de código abierto.

## Arquitectura del Sistema

### Flujo de Solicitud de Contenido

1. **Solicitud del usuario**  
   - [**Jellyseerr**](https://github.com/Fallenbagel/jellyseerr): los usuarios pueden solicitar películas o series.

2. **Procesamiento de la solicitud**  
   Dependiendo del tipo de contenido, se envía a:
   - [**Radarr**](https://radarr.video/) (películas)  
   - [**Sonarr**](https://sonarr.tv/) (series)

3. **Búsqueda y descarga del contenido**  
   - Radarr y Sonarr utilizan **indexadores** gestionados por [**Prowlarr**](https://wiki.servarr.com/prowlarr) para buscar torrents del contenido solicitado.  
   - Si se encuentra, se envía al cliente de torrent [**qBittorrent**](https://www.qbittorrent.org/) para su descarga.  
   - El contenido descargado se almacena en una carpeta de *seeding* para compartirlo (si aplica).

4. **Organización del contenido**  
   - Radarr y Sonarr mueven/copiar el contenido descargado a sus respectivas bibliotecas.  
   - Estas bibliotecas son leídas por:
     - [**Jellyfin**](https://jellyfin.org/): media server libre y autoalojado.

5. **Subtítulos**  
   - [**Bazarr**](https://www.bazarr.media/): busca e incrusta subtítulos automáticamente en el contenido gestionado por Radarr y Sonarr.

6. **Gestión de indexadores**  
   - **Prowlarr** centraliza y administra los indexadores de torrents.  
   - Se sincroniza automáticamente con Radarr y Sonarr.

## Diagrama del Flujo

```mermaid
graph TD;
    Usuario-->|Solicita|Jellyseerr
    Jellyseerr-->|Películas|Radarr
    Jellyseerr-->|Series|Sonarr
    Radarr-->|Busca en indexadores|Prowlarr
    Sonarr-->|Busca en indexadores|Prowlarr
    Prowlarr-->|Indexadores|Internet
    Radarr-->|Torrent|qBittorrent
    Sonarr-->|Torrent|qBittorrent
    qBittorrent-->|Descarga|CarpetaSeed
    qBittorrent-->|Seeding|Tracker
    Radarr-->|Copia a|BibliotecaPelículas
    Sonarr-->|Copia a|BibliotecaSeries
    BibliotecaPelículas-->|Visualiza|Jellyfin
    BibliotecaSeries-->|Visualiza|Jellyfin
    Bazarr-->|Subtítulos|Radarr
    Bazarr-->|Subtítulos|Sonarr
