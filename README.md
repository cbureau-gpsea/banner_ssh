# Banner SSH

This script display a banner when a user connects to the Debian machine using ssh.

## Installation

To display the next banner, replace the text by this one in (*/etc/motd*) :
```plaintext
###########################################################################################

 ______                                                                             ______
|  ____| __     ______  _    _  ______            _   _      _      __  __  _____  |____  |
| |      \ \   / / __ \| |  | ||  __  \          | \ | |    / \    |  \/  || ____|      | |
| |       \ \_/ / |  | | |  | || |__) |          |  \| |   / _ \   | \  / || |___       | |
| |        \   /| |  | | |  | ||  _  /           | . ` |  / /_\ \  | |\/| ||  _|        | |
| |         | | | |__| | |__| || | \ \   _______ | |\  | / _____ \ | |  | || |___       | |
| |____     |_|  \____/ \____/ |_|  \_\ |_______||_| \_|/_/     \_\|_|  |_||_____|  ____| |
|______|                                                                           |______|


###########################################################################################

          Toutes les connexions sont surveillées et enregistrées
          L'accès non autorisé à ce serveur est interdit
          Toute tentative d'intrusion sera signalée aux autorités compétentes
          Déconnectez-vous MAINTENANT si vous n'êtes pas un utilisateur autorisé !

###########################################################################################

          All connections are monitored and recorded
          Unauthorized access to this server is prohibited
          Any intrusion attemps will be reported to all law Enforcement Agencies
          Disconnect NOW if you're not an authorized user !

###########################################################################################
```

And to display the next informations on your computer, like this :

```plaintext

 System information as of Wed Oct  9 15:57:41 CEST 2024

  System load:  0.08               Processes:             249
  Usage of /:   85.4% of 23.95GB   Users logged in:       0
  Memory usage: 69%                IPv4 address for ens3: 192.168.1.6
  Swap usage:   10%

  => / is using 85.4% of 23.95GB
```

Add the next script in (*/etc/update-motd.d/10-uname*) :

```bash
#!/bin/sh

echo ""

# Retrieve system values ​​dynamically
system_load=$(uptime | awk -F 'load average:' '{ print $2 }' | awk '{ print $1 }' | tr -d ','| tr -d ',')
processes=$(ps aux | wc -l)
disk_usage=$(df -h / | grep / | awk '{print $5 " of " $2 "B"}')
users_logged_in=$(who | wc -l)
memory_usage=$(free -m | awk '/Mem:/ { printf("%.2f%%", $3/$2 * 100.0) }')
ipv4_address=$(hostname -I | awk '{print $1}')
swap_usage=$(free -m | awk '/Swap:/ { printf("%.2f%%", $3/$2 * 100.0) }')

# Function to add spaces for alignment
align_text() {
    printf "%-15s %-18s %-23s %s\n" "$1" "$2" "$3" "$4"
}

# Show header
echo " System information as of $(date)"
echo ""

# Display system information with desired layout
align_text "  System load:" "$system_load" "Processes:" "$processes"

align_text "  Usage of /:" "$disk_usage" "Users logged in:" "$users_logged_in"

align_text "  Memory usage:" "$memory_usage" "IPv4 address for ens3:" "$ipv4_address"

align_text "  Swap usage:" "$swap_usage"

# Add the ending message
echo ""
echo "  => / is using $disk_usage"
```
