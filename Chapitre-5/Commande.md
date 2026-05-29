# Chapitre 5 : LDAP (Commandes)

### Slide 9/33 : Modèle de données - objectClass inetOrgPerson
* `/etc/ldap/schema/inetorgperson.schema` : Fichier contenant le schéma `inetOrgPerson`.

### Slide 10/33 : Modèle de données - objectClass person
* `/etc/ldap/schema/core.schema` : Fichier contenant le schéma `person`.

### Slide 18/33 : Modèle fonctionnel - Opérations
* Les commandes utilitaires commencent toutes par `ldap_foo_` et correspondent aux opérations du protocole (ex: `ldapsearch`, `ldapcompare`, `ldapadd`, `ldapdelete`, `ldapmodify`, `ldapmodrdn`).

### Slide 19/33 : Modèle fonctionnel - ldapsearch
* `ldapsearch -x -H ldap://localhost \ -b "dc=example,dc=org" \ "(objectclass=inetOrgPerson)" cn mail` : Commande type pour effectuer une recherche dans l'annuaire.
  * `-x` : Indique une authentification simple.
  * `-H` : Spécifie l'URI du serveur.
  * `-b` : Spécifie la base de recherche (base DN).
  * `"(objectclass=inetOrgPerson)"` : Filtre appliqué à la recherche.
  * `cn mail` : Précise les attributs à retourner.

### Slide 27/33 : OpenLDAP - Implémentation
* `slapd`, `ldap-utils` : Paquets à installer pour utiliser l'implémentation OpenLDAP.
* `slapd`, `slurpd` : Noms des démons (services) d'OpenLDAP.

### Slide 28/33 : OpenLDAP - Configuration
* `/etc/ldap/slapd.d` : Dossier contenant la configuration d'OpenLDAP.

### Slide 29/33 : Open LDAP-TLS - Configuration (serveur)
* Fichiers de certificats impliqués dans la configuration TLS : `/etc/ldap/tls/ca.crt`, `/etc/ldap/tls/server.crt`, et `/etc/ldap/tls/server.key`.
* Pour ajouter cette configuration, on utilise un fichier LDIF avec les mots clés `dn: cn=config`, `changetype: modify`, et `add: olcTLSCACertificateFile` (entre autres).

### Slide 30/33 : OpenLDAP - Configuration LDAPS (port 636)
* `/etc/default/slapd` : Fichier à modifier pour activer LDAPS.
* `SLAPD_SERVICES="ldap:/// ldaps:/// ldapi:///"` : Ligne à y insérer.
* `systemctl restart slapd` : Commande à exécuter ensuite pour redémarrer le service.

### Slide 31/33 : OpenLDAP - Tests connexion sécurisée
* `ldapsearch -ZZ -H ldap://server -x \ -b "dc=example,dc=org" "(objectclass=*)"` : Teste la connexion sécurisée via StartTLS (port 389).
  * `-ZZ` : Argument spécifique pour forcer l'initiation de StartTLS.
* `ldapsearch -H ldaps://server -x \ -b "dc=example,dc=org" "(objectclass=*)"` : Teste la connexion sécurisée via LDAPS.
  * `ldaps://` : Indique explicitement de se connecter sur le port dédié 636.
<br>

<p style="display:flex;justify-content:space-between;align-items:center;">
<a href="../Chapitre-4/Commande.md">⬅️ Chapitre précédent</a> | 
<a href="../README.md">Vers le readme ➡️</a>
</p>