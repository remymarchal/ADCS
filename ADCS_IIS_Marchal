# ADCS : Installer un certificat dans IIS  
**/!\ Ce document part du principe que vous avez déjà un serveur IIS avec une liaison https /!\\**
## 1/ Créer un nouveau modèle
Sur le serveur ADCS, ouvrez la console **certsrv** (que vous pouvez trouver dans **Outils > Autorité de certification**, ou ouvrir via **Windows + R > certsrv.msc**). Rendez-vous ensuite dans **Votre_CA > Clique droit sur Modèles de certificats > Gérer**. La console des modèles de certificat devrait s'ouvrir :  
![Console des modèles de certificat](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image1.png)  
Nous allons créer un modèle à partir d'un autre déjà existant, le modèle **Serveur Web**. Pour se faire, cliquez droit sur **Serveur Web** puis **Dupliquer le modèle**. Une nouvelle fenêtre s'ouvre :  
![Propriétés du nouveau modèle](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image2.png)  
Vous pouvez d'ores et déjà vous rendre dans l'onglet **Général** pour y modifier le **Nom complet du modèle** ainsi que le **Nom du modèle**, la modification de ce dernier étant normalement automatique. Vous pouvez également modifier la période de validité, si l'envie vous prend :  
![Propriétés du nouveau modèle - Général](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image3.png)  
Dans l'onglet **Traitement de la demande**, cochez **Autoriser l'exportation de la clé privé**  
Enfin, dans l'onglet **Sécurité**, ajoutez l'ordinateur hôte du serveur IIS (si l'ordinateur hôte est également le serveur AD, ajoutez plutôt le groupe **Contrôleurs du domaine**), puis autorisez **Lecture**, **Inscrire** et **Inscription automatique** :  
![Propriétés du nouveau modèle - Sécurité](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image4.png)  
Validez ensuite la création du modèle en cliqunt sur **Appliquer** puis **OK**.  
Rendez-vous dans la première console **certsrv**, dans laquelle nous allons faire une nouvelle fois **Clique droit sur Modèles de certificats > Nouveau > Modèle de certificat à délivrer**, afin, comme la manipulation l'indique, d'ajouter notre nouveau modèle à la liste de certificats que notre CA délivrera :  
![certsrv - Ajout du modèle](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image5.png)  
## 2/ Demander le certificat pour l'ordinateur  
Rendez-vous sur le serveur IIS, où nous allons ouvrir la console **certml (Windows + R > certml.msc) > Personnel > Clique droit sur Certificats > Toutes les tâches > Demander un nouveau certificat**. Cette fenêtre devrait s'ouvrir :  
![Inscription de certificats](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image6.png)  
Cliquez sur **Suivant**, puis sélectionnez **Stratégie d'inscription à Active Directory**. Une nouvelle fenêtre s'ouvre, sur laquelle devrait figurer notre modèle nouvellement créé :  
![Inscription de certificats - Demander un certificats](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image7.png)  
Cliquez sur **Détails > Propriétés**. Dans l'onglet **Objet**, renseignez le **Nom commun** dans le **Nom du sujet** par le nom de votre serveur IIS. Vous pouvez également renseigner d'autre informations, comme des alias DNS dans **Autre nom** :  
![Inscription de certificats - Propriétés du certificat - Objet](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image8.png)  
Dans l'onglet **Général**, vous pouvez ajouter un nom convivial et une description (conseillé).Enfin, dans l'onglet **Autorité de certification**, sélectionnez votre Root CA :  
![Inscription de certificats - Propriétés du certificat - Autorité de certification](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image9.png)  
Cliquez sur **OK > Inscription > Terminer**. Le certificat devrait apparaître dans la liste :  
![certml - Personnel - Certificats](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image10.png)  
## 3/ Configurer le serveur IIS avec le nouveau certificat  
Rendez-vous dans le **Gestionnaire des services Internet (IIS) (trouvable dans Outils) > Votre_serveur_IIS > Sites > Votre_page_web > Liaisons**. Modifier votre liaison https déjà existante, et sélectionnez votre **Certificat SSL** (il apparaitra sous son nom convivial si vous lui en avez donné un) :  
![Gestionnaire IIS - Liaisons](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image11.png)  
Cliquez **OK**.
Votre page web est maintenant considérée comme sécurisée par vos ordinateurs du domaine (s'ils ont la Root CA dans leur banques de certificats de confiance évidemment) :  
![Page web en https via un Windows 10](https://raw.githubusercontent.com/remymarchal/ADCS/refs/heads/main/Md_IIS/image12.png)  
