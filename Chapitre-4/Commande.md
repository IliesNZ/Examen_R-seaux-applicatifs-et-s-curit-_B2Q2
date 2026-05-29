# Chapitre 4 : HTTP[s] et Serveurs Web (Commandes)

### Slide 3/73 : HTTP
* `nc -C localhost 80` : Commande permettant d'initier une requête HTTP en clair sur le port 80 en local.
* `GET / HTTP/1.1` (suivi de `host: localhost`) : Syntaxe d'une requête HTTP basique pour demander la racine du serveur.

### Slide 5/73 : HTTP
* `curl -v example.org` : Commande permettant d'exécuter simplement une requête HTTP. L'argument `-v` (verbose) permet d'afficher les détails de la connexion, de la requête et des entêtes de réponse.

### Slide 14/73 : HTTP - Cache
* `Cache-Control: public, max-age=604800` : Entête HTTP de réponse indiquant que la ressource peut être mise en cache de façon publique et précisant sa durée de fraîcheur maximale en secondes.

### Slide 28/73 : HTTPS
* `openssl s_client -connect example.org:443` : Commande permettant de se connecter au serveur sur le port 443 pour initier et tester une connexion sécurisée (TLS/HTTPS).

### Slide 29/73 : HTTPS
* `curl -v https://example.org` : Exécute une requête HTTPS. Le mode verbose permet notamment de visualiser les détails du certificat serveur (sujet, validité, autorité de certification).

### Slide 30/73 : HTTPS - Obtention du certificat
* `openssl req -nodes -newkey rsa:2048 -sha256 -keyout myserver.key -out server.csr` : Génère une clé privée (`myserver.key`) et une requête de signature de certificat ou CSR (`server.csr`) à envoyer à une autorité de certification.

### Slide 34/73 : Apache2 - Installation et Configuration
* `apt install apache2 libapache2-mod-php` : Installation du serveur web Apache et du module PHP.
* Fichiers et dossiers de configuration clés : 
  * `apache2.conf` : Fichier principal.
  * `conf-[available|enabled]` : Dossiers de configuration globale.
  * `sites-[available|enabled]` : Dossiers de gestion des hôtes virtuels (vhosts).
  * `mods-[available|enabled]` : Dossiers de gestion des modules.
* `a2enconf`, `a2ensite`, `a2enmod` : Commandes pour activer (enable) respectivement une configuration, un site ou un module.
* `apache2ctl` : Outil de contrôle du serveur Apache.

### Slide 36/73 : Apache2 - Virtual host
* Directives d'un fichier de vhost :
  * `ServerName example.org` : Définit le nom de domaine principal de l'hôte virtuel.
  * `DocumentRoot /var/www/html/org.example` : Spécifie le répertoire racine où se trouvent les fichiers du site.
  * `ServerAdmin webmaster@example.org` : Adresse mail de l'administrateur.
* `a2ensite example.org` : Active l'hôte virtuel "example.org".

### Slide 40/73 : Apache2 - ACL
* `AccessFileName .htaccess` : Directive spécifiant le nom du fichier de configuration local (par défaut `.htaccess`) utilisé pour gérer les contrôles d'accès dans un répertoire.

### Slide 42/73 : Apache2 - ACL (Contrôle d'accès)
* `Require all denied` : (Nouvelle syntaxe) Interdit l'accès à tout le monde.
* `Require ip 127.0.0.1/8` : (Nouvelle syntaxe) Autorise l'accès uniquement à l'IP ou la plage d'IP spécifiée.

### Slide 43/73 : Apache2 - ACL (Authentification Basique)
* `AuthUserFile access/.htaccess_passwd` : Indique le fichier contenant les mots de passe.
* `AuthType Basic` : Définit le type d'authentification sur "Basic".
* `require valid-user` : Autorise l'accès à tout utilisateur s'étant authentifié avec succès.

### Slide 44/73 : Apache2 - Authentification par certificat
* `SSLCACertificateFile "conf/ssl.crt/company-ca.crt"` : Spécifie le certificat de l'autorité pour vérifier les certificats clients.
* `SSLVerifyClient optional` : Demande un certificat client de manière facultative.
* `SSLOptions +FakeBasicAuth +StrictRequire` : Options pour simuler une authentification basique avec le certificat.

### Slide 45/73 : Apache2 - Réécriture et forçage HTTPS
* `RewriteEngine on` : Active le moteur de réécriture d'URL.
* `RewriteCond "%{HTTPS}" "!=on"` : Condition vérifiant si la connexion n'est pas en HTTPS.
* `RewriteRule "^/?(.*)" "https://%{SERVER_NAME}/$1" [R,L]` : (Exemple typique) Règle pour forcer la redirection vers HTTPS.

### Slide 48/73 : Apache2 - HTTPS (Configuration Vhost)
* `<VirtualHost _default_:443>` : Déclare un hôte virtuel écoutant sur le port sécurisé 443.
* `SSLEngine on` : Active le moteur SSL/TLS pour ce vhost.
* `SSLCertificateFile /elsewhere/cert.pem` : Chemin vers le certificat public du serveur.
* `SSLCertificateKeyFile /elsewhere/key.key` : Chemin vers la clé privée du serveur.

### Slide 50/73 : Apache2 - HTTPS (Let's Encrypt)
* `apt install dehydrated dehydrated-apache2` : Installation de l'outil Dehydrated pour automatiser Let's Encrypt.
* `/etc/dehydrated/domain.txt` et `/etc/dehydrated/config` : Fichiers pour configurer les domaines à certifier.
* `dehydrated -c` : Commande pour lancer la création ou le renouvellement des certificats.

### Slide 52/73 : Apache2 - Reverse Proxy
* `a2enmod proxy proxy_http` : Active les modules nécessaires pour qu'Apache agisse comme un reverse proxy.
* Directives Vhost pour le reverse proxy :
  * `ProxyPreserveHost On` : Transmet l'entête Host original au serveur cible.
  * `ProxyPass / http://127.0.0.1:8080/` : Redirige les requêtes entrantes vers le serveur interne.
  * `ProxyPassReverse / http://app.example.org` : Réécrit les entêtes de réponse du serveur interne pour le client.

### Slide 54/73 : Apache2 - Load balancer
* `a2enmod proxy proxy_balancer proxy_http lbmethod_byrequests` : Active les modules de répartition de charge par requêtes.

### Slide 55/73 : Apache2 - Load balancer (Configuration)
* `<Proxy "balancer://mycluster">` : Définit un groupe de serveurs pour l'équilibrage de charge.
* `BalancerMember http://192.168.1.11:8080 loadfactor=1` : Ajoute un serveur au cluster avec un poids de 1.
* `ProxySet lbmethod=byrequests` : Applique la méthode de répartition.
* `ProxyPass "/" "balancer://mycluster/"` : Dirige le trafic vers le cluster.

### Slide 57/73 : Nginx - Installation et Configuration
* `apt install nginx` : Installation du serveur Nginx.
* Fichiers et dossiers clés :
  * `nginx.conf` : Fichier de configuration principal.
  * `sites-[available|enabled]` : Dossiers pour les "server blocks" (équivalent des vhosts).

### Slide 58/73 : Nginx - Server blocks
* Structure de base :
  `server { root /data/www; }` : Définit le répertoire racine du serveur block.

### Slide 59/73 : Nginx - Locations
* `location / { ... }` : Bloc permettant de définir des règles spécifiques pour une URI donnée (ici la racine).

### Slide 60/73 : Nginx - Server blocks (Suite)
* `listen 80;` : Indique au serveur d'écouter sur le port 80.
* `server_name example.org;` : Définit le nom de domaine auquel ce bloc répond.

### Slide 61/73 : Nginx - Server blocks (Catch-all)
* `server_name _;` : Syntaxe agissant comme un "catch-all" pour intercepter toutes les requêtes ne correspondant pas aux autres `server_name`.

### Slide 62/73 : Nginx - Proxy
* `proxy_pass http://localhost:8080;` : Directive dans un bloc `location` pour transmettre la requête à un autre serveur (Reverse Proxy).

### Slide 66/73 : Caddy server
* `/etc/caddy/CaddyFile` : Emplacement du fichier de configuration principal.
* `curl localhost:2019/config/` : Requête pour afficher la configuration active via l'API de Caddy.
* Outils de formatage JSON : `| jq` ou `| python3 json.tools`.

### Slide 67-68/73 : CaddyFile exemple
* Syntaxe pour un site statique : 
  `example1.com { root * /www/example.com file_server }`
* Syntaxe pour un reverse proxy :
  `example2.com { reverse_proxy localhost:9000 }`

### Slide 73/73 : TODO - Requête POST manuelle
* Exemple de structure pour soumettre un formulaire via `nc` :
  ```http
  POST /login.php HTTP/1.1
  Host: example.org
  Content-Type: application/x-www-form-urlencoded
  Content-Length: 29
  
  username=testuser&password=1234

<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../Chapitre-3/Commande.md">⬅️ Chapitre précédent</a> | 
<a href="../Chapitre-5/Commande.md">Chapitre suivant ➡️</a>
</p>