# Chapitre 3 : Les emails (Commandes)

### Slide 12/71 : SMTP #1
* `netcat -C relay.proximus.be 25` : Méthode de connexion.

### Slide 15/71 : SMTP #2
* `openssl s_client -starttls smtp -connect <server name>:587` : Initialiser TLS.

### Slide 16/71 : SMTP #2
* `echo "\Ouser\Opassword" | base64` : Générer user/password en base 64.

### Slide 30/71 : SMTP - SPF
* `example.org IN TXT "v=spf1 ip4:192.0.2.1 -all"`.

### Slide 31/71 : SMTP - SPF
* `example.org IN TXT "v=spf1 -all"` : Aucun mail.
* `example.org IN TXT "v=spf1 +all"` : Autorise tout.
* `example.org IN TXT "v=spf1 ipv4:192.0.2.1 ipv6:2001:db8::1 -all"`.
* `example.org IN TXT "v=spf1 ip4:193.190.147.81 include:_spf.google.com ~all"`.

### Slide 36/71 : SMTP - DKIM
* `dig TXT iport._domainkey.cisco.com` : Ajout d'enregistrement DNS.

### Slide 37/71 : SMTP - DKIM
* `dig TXT google._domainkey.he2b.be` : Requête DNS une fois le sélecteur trouvé.

### Slide 47/71 : SMTP - DMARC
* `dig TXT _dmarc.example.org`.
* `$v=DMARC1; $p=none; rua=mailto:postmaster@example.org`.

### Slide 55/71 : Serveur mail, Postfix - Installation
* Fichiers de configurations : `/etc/postfix/main.cf` et `/etc/postfix/master.cf`.
* `postmap ssl_passwd` : Convertit en `ssl_passwd.db`.

### Slide 56/71 : Serveur mail, Postfix - Installation (suite)
* `myorigin = $mydoman`.
* `mydestination = localhost, localhost@localdomain`.

### Slide 57/71 : Serveur mail, Postfix - Installation (suite)
* `relayhost = `.
* `relayhost = smtp.example.org`.
* `relayhost = [smtp.example.org]`.
* `relayhost = [smtp.example.org]:587`.
* Les aliases : éditer `/etc/aliases` puis `newaliases`.

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
* Commande d'application : `postmap sasl_passwd`.

### Slide 61/71 : OpenDKIM - Installation
* `apt update`.
* `apt install opendkim opendkim-tools`.

### Slide 62/71 : OpenDKIM - Création de clés
* `opendkim-genkey -D /etc/dkimkeys -d example.org -s 2025`.

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
* `systemctl restart opendkim`.
* `systemctl restart postfix`.
* Enregistrement DNS ajouté dans `/etc/dkimkeys/2025.txt`.

### Slide 65/71 : OpenDKIM - Test et vérification
* `opendkim-testkey -vvv`.

### Slide 67/71 : OpenDMARC - Installation
* `apt install opendmarc`.

### Slide 68/71 : OpenDMARC - Configuration
* Dans `/etc/opendmarc.conf`:
  * `AuthservID example.org`.
  * `PidFile /var/run/opendmarc/opendmarc.pid`.
  * `Socket inet:8893@localhost`.
  * `Syslog true`.
  * `TrustedAuthservIDs example.org`.
* Créer un répertoire :
  * `mkdir /var/spool/opendmarc`.
  * `chown opendmarc:opendmarc /var/spool/opendmarc`.

### Slide 69/71 : OpenDMARC - Installation
* Dans `/etc/postfix/main.cf`:
  * `milter_default_action = accept`.
  * `milter_protocol = 6`.
  * `smtpd_milters = inet:localhost:8893`.
  * `non_smtpd_milters = inet:localhost:8893`.
* `systemctl restart postfix`.

### Slide 70/71 : OpenDMARC - Installation
* `systemctl restart opendmarc`.
<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../Chapitre-2/Commande.md">⬅️ Chapitre Précédent</a> | 
<a href="../Chapitre-4/Commande.md">Chapitre suivant ➡️</a>
</p>