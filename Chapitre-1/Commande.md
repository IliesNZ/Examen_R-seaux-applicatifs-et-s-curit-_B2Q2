# Chapitre 1 : Première connexion

### Slide 3/10 : Première fois... - user
* `pwgen` : Commande suggérée pour générer un mot de passe complexe.

### Slide 4/10 : Première fois... - Connexion ssh
* `ClientAliveInterval 300` : Fixe le timeout de la connexion.
* `ClientAliveCountMax 3` : Fixe le nombre de rappels avant déconnexion.
* `PermitRootLogin no` : N'autorise pas de connexion directe en root.
* `AllowUsers <your users>` : Précise le nom des comptes autorisés.

### Slide 5/10 : Première fois... - sudo
* `%sudo ALL=(ALL:ALL) ALL` : Ligne permettant d'ajouter l'utilisateur au groupe sudo.
* `Defaults timestamp_timeout=0` : Ajouter cette ligne pour gérer le timeout.

### Slide 6/10 : Première fois... - Déconnecter les utilisateurs inactifs
* Fichier cible : `/etc/profile.d/timeout.sh`.
* Lignes à y ajouter :
  * `echo "Idle users will be removed after 15 minutes"`.
  * `TMOUT=900`.
  * `readonly TMOUT`.
  * `export TMOUT`.

### Slide 7/10 : Première fois... - apt
* Fichier cible : `sources.list`. (Préciser le nom d'une version comme `trixie` plutôt que `stable` ).
* `ntp` : Exemple de paquet pour un serveur de temps.

### Slide 8/10 : Première fois... - Serveur de mails
* Fichier cible : `/etc/aliases`.
* Ligne à y ajouter : `root: me@example.org.`.

### Slide 9/10 : Première fois... - IDS / IPS
* `fail2ban` : Au minimum pour le système de détection d'intrusions.

<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../README.md">⬅️ Vers le readme</a> | 
<a href="../Chapitre-2/Commande.md">Chapitre suivant ➡️</a>
</p>
