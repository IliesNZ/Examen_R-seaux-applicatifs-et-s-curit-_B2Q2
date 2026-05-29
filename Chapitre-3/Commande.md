# Chapitre 3 : Les emails (Commandes)

### Slide 12/71 : SMTP #1
* `netcat -C relay.proximus.be 25` : Permet d'initier une connexion SMTP en clair sur le port 25. Le cours précise que cette méthode ne fonctionne plus car tous les serveurs demandent désormais d'être authentifiés.  

### Slide 15/71 : SMTP #2
* `openssl s_client -starttls smtp -connect <server name>:587` : Permet de se connecter au serveur et d'initialiser une connexion chiffrée TLS sur le port 587.  

### Slide 16/71 : SMTP #2
* `echo "\Ouser\Opassword" | base64` : Sert à générer l'utilisateur et le mot de passe encodés en base 64 pour pouvoir passer la commande d'authentification (AUTH PLAIN) au serveur.  

### Slide 30/71 : SMTP - SPF
* `example.org IN TXT "v=spf1 ip4:192.0.2.1 -all"` : Exemple de champ TXT dans le DNS pour signaler un serveur autorisé.

### Slide 31/71 : SMTP - SPF
* `example.org IN TXT "v=spf1 -all"` : N'autorise aucun mail.
* `example.org IN TXT "v=spf1 +all"` : Autorise tout.
* `example.org IN TXT "v=spf1 ipv4:192.0.2.1 ipv6:2001:db8::1 -all"` : Autorise spécifiquement une ou des IP.
* `example.org IN TXT "v=spf1 ip4:193.190.147.81 include:_spf.google.com ~all"` : Utilise une configuration définie ailleurs (include).

### Slide 36/71 : SMTP - DKIM
* `dig TXT iport._domainkey.cisco.com` : C'est une requête DNS. Elle permet d'interroger et de récupérer les enregistrements TXT, par exemple pour trouver la clé publique DKIM.

### Slide 37/71 : SMTP - DKIM
* `dig TXT google._domainkey.he2b.be` : Requête DNS une fois le sélecteur trouvé (ici "google").

### Slide 47/71 : SMTP - DMARC
* `dig TXT _dmarc.example.org` : Requête DNS pour vérifier l'enregistrement DMARC.
* `$v=DMARC1; $p=none; rua=mailto:postmaster@example.org` : Exemple d'enregistrement DMARC.

### Slide 55/71 : Serveur mail, Postfix - Installation
* Fichiers de configurations : `/etc/postfix/main.cf` et `/etc/postfix/master.cf`.
* `postmap ssl_passwd` : Convertit le fichier texte contenant les mots de passe en un fichier "base de données" (`.db`) utilisable par Postfix.

### Slide 56/71 : Serveur mail, Postfix - Installation (suite)
* `myorigin = $mydoman` : Ajouté aux mails non "fully qualified".
* `mydestination = localhost, localhost@localdomain` : Domaines pour lesquels la machine délivre localement les mails.

### Slide 57/71 : Serveur mail, Postfix - Installation (suite)
* `relayhost = ` : Vide, indique que postfix envoie les mails.
* `relayhost = smtp.example.org` : Indique le serveur relais.
* `relayhost = [smtp.example.org]` : Indique le serveur relais via requête A, AAAA, CNAME.
* `relayhost = [smtp.example.org]:587` : Indique le serveur relais sur un port spécifique.
* Les aliases : éditer `/etc/aliases` puis exécuter `newaliases`. `newaliases` est une commande à lancer obligatoirement après avoir édité le fichier `/etc/aliases` pour mettre à jour les alias du serveur de mail.

### Slide 58/71 : Serveur mail, Postfix - Installation (suite)
* Mails d'un serveur inexistant vers domaine existant dans `/etc/postfix/generic`.

### Slide 59/71 : Serveur mail, Postfix - Installation (suite)
* Lignes à ajouter dans la configuration :
  * `smtp_sasl_auth_enable = yes`.
  * `smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd`.
  * `smtp_sasl_security_options = noanonymous`.
  * `smtp_use_tls # parfois`.
* Ligne à ajouter dans le fichier `sasl_passwd` :
  * `[smtp.example.org] me@example.org:mysecret`.
* Commande d'application : `postmap sasl_passwd`. Convertit le fichier texte contenant les mots de passe en un fichier "base de données" (`.db`) utilisable par Postfix.

### Slide 61/71 : OpenDKIM - Installation
* `apt update` : Commande d'administration pour mettre à jour les sources.
* `apt install opendkim opendkim-tools` : Commande d'administration pour installer les paquets nécessaires.

### Slide 62/71 : OpenDKIM - Création de clés
* `opendkim-genkey -D /etc/dkimkeys -d example.org -s 2025` : Commande de création de la clé. L'argument `-d` spécifie que la clé est créée pour le domaine (ici example.org) et l'argument `-s` spécifie le sélecteur choisi (ici 2025).

### Slide 63/71 : OpenDKIM - Fichier de configuration
* Dans `/etc/opendkim.conf`:
  * `Domain example.org`.
  * `Selector 2025`.
  * `KeyFile /etc/dkimkeys/2025.private`.
  * `Socket inet:8891@localhost`.
  * `Socket local:/var/spool/postfix/opendkim/opendkim.sock`.

### Slide 64/71 : OpenDKIM - Intégration avec Postfix
* Dans `/etc/postfix/main.cf`:
  * `smtpd_milters = inet:localhost:8891`.
  * `non_smtpd_milters = $smtpd_milters`.
* `systemctl restart opendkim` : Redémarre le service pour qu'il prenne en compte les nouvelles configurations.
* `systemctl restart postfix` : Redémarre le service Postfix.
* Enregistrement DNS ajouté dans `/etc/dkimkeys/2025.txt`.

### Slide 65/71 : OpenDKIM - Test et vérification
* `opendkim-testkey -vvv` : Sert spécifiquement à la vérification de la configuration OpenDKIM.

### Slide 67/71 : OpenDMARC - Installation
* `apt install opendmarc` : Commande d'administration pour installer le paquet.

### Slide 68/71 : OpenDMARC - Configuration
* Dans `/etc/opendmarc.conf`:
  * `AuthservID example.org`.
  * `PidFile /var/run/opendmarc/opendmarc.pid`.
  * `Socket inet:8893@localhost`.
  * `Syslog true`.
  * `TrustedAuthservIDs example.org`.
* Créer un répertoire :
  * `mkdir /var/spool/opendmarc` : Crée un répertoire spécifique pour stocker les rapports DMARC.
  * `chown opendmarc:opendmarc /var/spool/opendmarc` : Commande d'administration Linux qui attribue les droits de propriétaire et de groupe à l'utilisateur `opendmarc` sur le répertoire des rapports.

### Slide 69/71 : OpenDMARC - Installation
* Dans `/etc/postfix/main.cf`:
  * `milter_default_action = accept`.
  * `milter_protocol = 6`.
  * `smtpd_milters = inet:localhost:8893`.
  * `non_smtpd_milters = inet:localhost:8893`.
* `systemctl restart postfix` : Redémarre le service pour qu'il prenne en compte les nouvelles configurations.

### Slide 70/71 : OpenDMARC - Installation
* `systemctl restart opendmarc` : Redémarre le service après ajout de l'enregistrement DMARC dans le DNS.
<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../Chapitre-2/Commande.md">⬅️ Chapitre Précédent</a> | 
<a href="../Chapitre-4/Commande.md">Chapitre suivant ➡️</a>
</p>