# Chapitre 2 : IDS|IPS (Commandes)

## Analyse des logs (Slide 8) :

### Expression régulière (pattern) d'exemple pour l'analyse des logs :

    ^\w{3} [ :0-9]{11} [.[:alnum:]-]+ SSMTP\[[0-9]+\]: Sent mail\ for . $*\backslash([\Theta-9]+[\Theta-9.]+$ Bye\) uid=[0-9]+ username=[\._[:alnum:]\ -]+ outbytes=[0-9]+\$   

## Fail2ban (Slide 10-12)

### Fichier de configuration :

- Configurations globales : /etc/fail2ban/{fail2ban.conf,jail.conf,filter.d,action.d}   
- Configuration locale (à privilégier pour l'écriture) : /etc/fail2ban/jail.local   

### Exemple de bloc de configuration :

    Ini, TOML

    [DEFAULT] [cite: 162]
    bantime = &lt;long enough&gt; [cite: 163]
    findtime = &lt;time during retry are counted&gt; [cite: 164]
    maxretry = &lt;n&gt; [cite: 165]
    action = %(action_mw)s [cite: 166]

    [&lt;service&gt;] [cite: 167]
    enabled = true [cite: 168]

### Commandes client & Firewall

- fail2ban-client status : Affiche le statut général.  
- fail2ban-client status &lt;JAIL&gt; : Affiche le statut d'une prison spécifique.  
- fail2ban-client set loglevel &lt;LEVEL&gt; : Modifie le niveau de log.  
- fail2ban-client get loglevel : Affiche le niveau de log actuel.  
- fail2ban-client set &lt;JAIL&gt; [un]banip &lt;IP&gt; : Bannit ou débannit manuellement une IP dans une prison. 
- fail2ban-client set &lt;JAIL&gt; &lt;add|del&gt;ignoreip &lt;IP&gt; : Ajoute ou retire une IP de la liste d'ignorance d'une prison.  
- iptables -L : Montre toutes les règles du firewall en cours (utile pour vérifier les actions de fail2ban).  

## Tripwire (Slide 14-20) :

- $sha512sum /etc/hosts : Commande d'exemple pour calculer le hash d'un fichier.  
- twadmin --generate-keys -L /etc/tripwire/&lt;host&gt;-local.key : Génère la clé locale.  
- twadmin --generate-keys -L /etc/tripwire/site.key : Génère la clé de site.  
- /etc/tripwire/twcfg.txt : Fichier source pour générer la BD chiffrée de configuration (tw.cfg).  
- /etc/tripwire/twpol.txt : Fichier source pour générer la BD chiffrée des règles (tw.pol).  
- twadmin --create-cfgfile -S /etc/tripwire/site.key \ /etc/tripwire/twcfg.txt : Crée le fichier de configuration chiffré.  
- twadmin --create-polfile -S /etc/tripwire/site.key \ /etc/tripwire/twpol.txt : Crée le fichier de politique chiffré.  

### Configuration des règles à surveiller :

    Plaintext
    /etc -&gt; $(SEC_BIN); [cite: 224]
    /etc/passwd -&gt; $(SEC_CONFIG); [cite: 225]
    /etc/shadow -&gt; $(SEC_CONFIG); [cite: 226]

- tripwire --init : Initialise la base de données des fichiers surveillés.  
- /var/lib/tripwire/&lt;host&gt;.twd : Emplacement où le fichier de base de données est créé.  
- tripwire --check : Lance une vérification d'intégrité. 
- /var/lib/tripwire/report : Dossier contenant les rapports générés au format .twr.  
- twprint -m r --twrfile /var/lib/tripwire/report/&lt;host&gt;-&lt;date&gt;.twr : Commande pour lire le rapport généré en clair.  


<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../Chapitre-1/Commande.md">⬅️ Chapitre Précédent</a> | 
<a href="../Chapitre-3/Commande.md">Chapitre suivant ➡️</a>
</p>