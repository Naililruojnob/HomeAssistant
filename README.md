
### Source

https://www.home-assistant.io/installation/alternative
### Installation docker & docker compose

### **Installation de Docker et Docker Compose 

1. **Mettre à jour le système :**

```bash
sudo apt update && sudo apt upgrade -y
```

2. **Installer Docker et Docker Compose :**
    
```bash
sudo apt install -y docker docker-compose
```

### HomeAssistant

Le fichier docker-compose.yml pour Home Assistant et le suivant 

Pour le crée tu as juste a faire nano docker-compose.yml dans l'emplacement de ton choix

```yaml
services:
  homeassistant:
    container_name: homeassistant
    image: "ghcr.io/home-assistant/home-assistant:stable"
    volumes:
      - /PATH_TO_YOUR_CONFIG:/config # Modifier le chemin
      - /etc/localtime:/etc/localtime:ro
      - /run/dbus:/run/dbus:ro
    devices:
      - /dev/ttyUSB0:/dev/ttyUSB0 # Ajouter votre dongle Zigbee ici
    restart: unless-stopped
    privileged: true
    network_mode: host
```
#### **Explications**

1. **`services:`**  
    Définit les services à déployer.
    
2. **`homeassistant:`**  
    Service principal pour exécuter Home Assistant.
    
3. **`container_name:`**  
    Nom explicite du conteneur Docker.
    
4. **`image:`**  
    Image Docker officielle de Home Assistant.
    
5. **`volumes:`**
    - `/PATH_TO_YOUR_CONFIG:/config` : Stockage des configurations de Home Assistant.
    - `/etc/localtime:/etc/localtime:ro` : Synchronisation avec le fuseau horaire de l'hôte.
    - `/run/dbus:/run/dbus:ro` : Accès aux services DBus, par exemple pour Bluetooth.
7. **`devices:`**  
    Nécessaire pour exposer un dongle USB Zigbee ou autre périphérique à Home Assistant.
    
7. **`restart:`**  
    Assure le redémarrage du conteneur en cas d'arrêt.
    
8. **`privileged:`**  
    Permet des accès matériels spécifiques (par exemple, ports USB).
    
9. **`network_mode:`**  
    Permet à Home Assistant d'utiliser le réseau de l'hôte, utile pour les protocoles comme mDNS.

### **Configuration des Dongles USB**

#### Identifier le dongle Zigbee (par exemple, ConBee II)

1. **Lister les périphériques avant branchement :**
   
```bash
    ls /dev/tty*
```
    
2. **Brancher le dongle et relister :**
    
```bash
    ls /dev/tty*
```
    
    Un nouveau périphérique apparaît, typiquement `/dev/ttyUSB0` ou `/dev/ttyACM0`.
    
3. **Confirmer avec `dmesg` :**
    
```bash
    dmesg | grep tty
```
    
    La sortie mentionnera un nouveau périphérique attaché, par exemple :
    
```bash
    usb 1-1: FTDI USB Serial Device converter now attached to ttyUSB0
```
    
4. **Utiliser un chemin stable (`/dev/serial/by-id/`) :**
    
```bash
    ls -l /dev/serial/by-id/
```
    Exemple de sortie :
    
```bash
    usb-PhlipsHue_ConBee_II_123456-if00 -> ../../ttyUSB0
```
    
    Dans votre fichier `docker-compose.yml` :
    
```bash
    devices:   - /dev/serial/by-id/usb-PhlipsHue_ConBee_II_123456-if00:/dev/ttyUSB0
```


