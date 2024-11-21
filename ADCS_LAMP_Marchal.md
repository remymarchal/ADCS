# ADCS : Installer un certificat dans un LAMP  
**/!\ Ce document part du principe que vous avez déjà votre Root CA dans votre liste de certificats de confiance /!\\**
## 1/ Générer une clé privée et une demande de certificat (.csr)  
Dans votre LAMP, commencez par vous placer dans le directory **/etc/ssl/private**, puis utilisez la commande  
```
openssl genrsa -out nom_de_la_clé.key 2048
```
Pour générer une clé privée.  
Placez-vous ensuite dans le directory **/etc/ssl**, où nous allons créer et éditer un fichier **.conf** dans lequel figurera ces lignes :  
```
[req]
default_bits = 2048
prompt = no
default_md = sha256
req_extensions = req_ext
distinguished_name = dn
[ dn ]
C=FR
ST=Votre_région
L=Votre_ville
O=Votre_entreprise
OU=IT
emailAddress=Votre_email_entreprise
CN = Nom_du_LAMP
[ req_ext ]
subjectAltName = @alt_names
[ alt_names ]
DNS.1 = Nom_DNS_LAMP1
DNS.2 = Nom_DNS_LAMP2
```  
Ce fichier servira à pré-indiquer des informations pour la demande de certificat **.csr**, faites avec cette commande :  
```
openssl req -new -key /etc/ssl/private/nom_de_la_clé.key -out nom_de_la_demande.csr -config /etc/ssl/nom_de_la_conf.conf
```
## 2/ Créer le certificat sur le serveur AD CS  
Exportez le fichier **.csr** dans votre serveur AD CS.  
J'ai personnellement utilisé un partage réseau via SMB. Dans le serveur ADDS, créez un dossier et partagez-le (n'oubliez pas les autorisations).  
Ensuite, dans le LAMP, installez le client CIFS :  
```
sudo apt install cifs-utils
```
Montez le partage réseau Windows :  
```
sudo mount -t cifs //Serveur_ADCS/Dossier_partagé /mnt -o username=Administrateur,password=Votre_mdp
```
Copiez le fichier dans le dossier partagé :  
```
cp /etc/ssl/nom_de_la_demande.csr /mnt
```
Une fois fait, démontez le partage :  
```
sudo umount /mnt
```
Rendez-vous à présent sur le serveur ADCS, dans l'invite de commandes, déplacez-vous dans le dossier dans lequel se trouve le fichier **.csr** et faites la commande :  
```
certreq -submit -attrib "CertificateTemplate:Votre_modèle_de_certificat" nom_de_la_demande.csr
```  
Une fenêtre s'ouvre, sélectionnez votre Root CA puis cliquez **OK** :  
![Liste des autorités de certification](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_LAMP/image1.png)  
Entrez ensuite le nom de votre fichier, puis **Enregistrer** :  
![Enregistrer le certificat](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_LAMP/image2.png)  
Vous aurez votre certificat **.cer** dans le dossier sélectionné.
## 3/ Configurer Apache2 sur le LAMP
Exporter le fichier **.cer** (que nous venons de créer) dans votre LAMP.  
J'ai personnellement utilisé une autre méthode que le partage réseau, car j'aime me compliquer la vie : j'ai déposé le fichier dans un repository public Github, et j'ai ensuite effectué un **wget** depuis le LAMP en me plaçant dans **/etc/ssl/certs** en amont :  
![wget](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_LAMP/image3.png)  
![wget marche !](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_LAMP/image4.png)  
Configurez ensuite votre fichier de conf apache2 dans **/etc/apache2/sites-available**, en modifiant/ajoutant ces lignes dans le **\<VirtualHost \*:443\>** :  
```
SSLCertificateFile      /etc/ssl/certs/nom_de_la_cert.cer
SSLCertificateKeyFile   /etc/ssl/private/nom_de_la_clé.key
```
Relancez le service apache2 avec :  
```
systemctl restart apache2.service
```
Votre page web est maintenant considérée comme sécurisée par vos ordinateurs du domaine (s'ils ont la Root CA dans leur banques de certificats de confiance évidemment) :  
![Page web apache depuis un client Windows 10](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_LAMP/image5.png)  
