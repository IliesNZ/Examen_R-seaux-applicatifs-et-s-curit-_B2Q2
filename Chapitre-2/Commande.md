
# Chapitre 2 : IDS|IPS

### Slide 8/26 : Surveillance des logs
* `^\w{3} [ :0-9]{11} [.[:alnum:]-]+ SSMTP\[[0-9]+\]: Sent mail\ for . $*\backslash([\Theta-9]+[\Theta-9.]+$ Bye\) uid=[0-9]+ username=[\._[:alnum:]\ -]+ outbytes=[0-9]+\$` : Expression régulière d'exemple.

### Slide 10/26 : fail2ban
* Fichiers de configuration globaux : `/etc/fail2ban/{fail2ban.conf,jail.conf,filter.d,action.d}`.
* Fichier de configuration locale : `/etc/fail2ban/jail.local`.

### Slide 11/26 : fail2ban - Configuration
* Exemple de configuration:
    [DEFAULT]
    bantime = <long enough>
    findtime = <time during retry are counted>
    maxretry = <n>
    action = %(action_mw)s
    [<service>]
    enabled = true

### Slide 12/26 : fail2ban - Client
* `fail2ban-client status` : Affiche le statut général.
* `fail2ban-client status <JAIL>` : Affiche le statut d'une prison.
* `fail2ban-client set loglevel <LEVEL>` : Modifie le niveau de log.
* `fail2ban-client get loglevel` : Affiche le niveau de log.
* `fail2ban-client set <JAIL> [un]banip <IP>` : Banni / débanni une IP.
* `fail2ban-client set <JAIL> <add|del>ignoreip <IP>` : Gère l'ignorance d'IP.
* `iptables -L` : Montre toutes les règles du firewall.

### Slide 14/26 : tripwire - Introduction
* `$sha512sum /etc/hosts` : Comparaison de hash.

### Slide 15/26 : tripwire - Clés
* `twadmin --generate-keys -L /etc/tripwire/<host>-local.key`.
* `twadmin --generate-keys -L /etc/tripwire/site.key`.

### Slide 16/26 : tripwire - Configuration
* Fichier source pour configuration : `/etc/tripwire/twcfg.txt`.
* Fichier source pour les règles : `/etc/tripwire/twpol.txt`.

### Slide 17/26 : tripwire - Création des fichiers
* `twadmin --create-cfgfile -S /etc/tripwire/site.key \ /etc/tripwire/twcfg.txt`.
* `twadmin --create-polfile -S /etc/tripwire/site.key \ /etc/tripwire/twpol.txt`.

### Slide 18/26 : tripwire - Politique
* Règles dans `/etc/tripwire/twpol.txt`:
    /etc -> \$(SEC_BIN);
    /etc/passwd -> \$(SEC_CONFIG);
    /etc/shadow -> \$(SEC_CONFIG);

### Slide 19/26 : tripwire - Initialisation
* `tripwire --init` : Lance l'initialisation.
* Emplacement BD : `/var/lib/tripwire/<host>.twd`.

### Slide 20/26 : tripwire - Vérification
* `tripwire --check` : Lancer la vérification.
* Fichier de rapport : `/var/lib/tripwire/report/<host>-<date>.twr`.
* `twprint -m r --twrfile /var/lib/tripwire/report/<host>-<date>.twr`.

### Slide 25/26 : portsentry - Configuration
* Dans `/etc/portsentry/portsentry.conf`:
  * `BLOCK_UDP="1"`.
  * `BLOCK_TCP="1"`.
  * `KILL_RUN_CMD=/sbin/iptables`.
* Dans `/etc/default/portsentry`:
  * `TCP_MODE="atcp"`.
  * `UDP_MODE="audp"`.

<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../Chapitre-1/Commande.md">⬅️ Chapitre Précédent</a> | 
<a href="../Chapitre-3/Commande.md">Chapitre suivant ➡️</a>
</p>