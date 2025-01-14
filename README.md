# Installation-DNS  
----  
Ce tutoriel a pour but de guider pour l'installation et la configuration d'un serveur DNS sur Windows Server 2022.  
  
## :one: Installation du rôle DNS  
  
Sur Server Manager, cliquer sur `Add roles and features`  
  
![image](https://github.com/user-attachments/assets/e7ff3bf0-c77e-4678-b3be-1097f0b20c44)  
  
➡️ **Before You Begin :** `Next`  
  
➡️ **Installation Type :** `Role-based or feature-based installation`  
  
➡️ **Server Selection :** cocher `Select a server from the server pool`, séléctionnez votre serveur, puis cliquer sur `Next`  
  
➡️ Cliquer sur `Next` pour toutes les étapes suivantes, jusqu'à arriver au bouton `Install`  
  
Patienter jusqu'à la fin de l'installation et fermer la fenêtre d'installation. Maintenant que le serveur DNS est installé, il va falloir le configurer pour le rendre fonctionnel.  
  
## 2️⃣ Configuration DNS  
  
  
### 🅰️ Configurer le serveur DNS pour résoudre les noms de domaine externes (Internet)  
  
Par défaut, un serveur DNS local ne pourra résoudre que les noms de domaine locaux pour lesquels une ou plusieurs zones de recherche directe et inverse ont été créées.
Pour que le serveur DNS puisse également « résoudre » les noms de domaine externes (ceux trouvés sur Internet), il faut d'abord configurer les redirecteurs.  
   
Sur Server Manager, cliquer sur `Tools` puis `DNS`. La console DNS manager s'ouvrira.  
  
![image](https://github.com/user-attachments/assets/d213ed08-cfb6-461d-b98f-013df7000f99)   
  
⚠️ **Il est nécéssaire que le Windows Server ait un accès Internet. Bien s'assurer qu'une carte réseau en Accès par pont soit activée sur VirtualBox par exemple.**  
  
➡️ Faire un clic droit sur le nom du Windows Server (Ex: `SRVWIN01`) puis cliquer sur `Properties`  
  
➡️ Aller dans l'onglet `Forwarders` puis cliquer sur `Edit`  
  
➡️ Ajouter les serveurs DNS publics de Google : 8.8.8.8 et 8.8.4.4 comme sur la capture d'écran ci-dessous :
  
![image](https://github.com/user-attachments/assets/ae66116a-28a3-4425-8a4c-3b4c5bbeed68)  
  
➡️ Cliquer sur `Apply` puis `OK` pour sauvegarder les modifications  
  
✔️ **Désormais, si vous essayez de résoudre un nom de domaine Internet (comme "informatiweb-pro.net" par exemple), votre serveur DNS redirigera la requête vers l'un des serveurs DNS de Google pour obtenir la réponse et vous la renvoyer**  
  
### 🅱️ Créer une zone de recherche directe (Domaine -> Adresse IP)  
  
>Lorsqu'on souhaite configurer un serveur DNS, il est important de configurer au moins :  
>  
>🔍 Des zones de recherche directe : pour résoudre les noms de domaine en adresses IP  
>🔎 Des zones de recherche inverse : pour pouvoir connaître le nom de domaine d'une machine à partir de son adresse IP  
  
➡️ Toujours dans la fenêtre DNS Manager ouverte à l'étape précédente, dérouler le nom du serveur, faire un clic droit sur `Forward Lookup Zones` puis sur `New Zone`  
  
![image](https://github.com/user-attachments/assets/f4cacc48-494b-40a6-81b4-735d636698c5)  
  
➡️ Une fenêtre d'installation apparaît. Cliquer sur `Next`  
  
➡️ **Zone Type** : Primary Zone    
  
➡️ **Active Directory Zone Replication Scope** : `To all DNS servers running on domain controllers in this domain: wilders.lan`  
  
➡️ **Zone name** : wilders.lan (ceci est un exemple)  
  
➡️ **Dynamic Update** : pour des raisons de sécurité, cocher `Do not allow dynamic updates`  




