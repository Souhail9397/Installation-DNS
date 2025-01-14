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
  
  
### ♠️ Configurer le serveur DNS pour résoudre les noms de domaine externes (Internet)  
  
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
  
### ♣️ Créer une zone de recherche directe (Domaine -> Adresse IP)  
  
>Lorsqu'on souhaite configurer un serveur DNS, il est important de configurer au moins :  
>  
>🔍 Des zones de recherche directe : pour résoudre les noms de domaine en adresses IP  
>🔎 Des zones de recherche inverse : pour pouvoir connaître le nom de domaine d'une machine à partir de son adresse IP  
  
➡️ Toujours dans la fenêtre DNS Manager ouverte à l'étape précédente, dérouler le nom du serveur, faire un clic droit sur `Forward Lookup Zones` puis cliquer sur `New Zone`  
  
![image](https://github.com/user-attachments/assets/f4cacc48-494b-40a6-81b4-735d636698c5)  
  
➡️ Une fenêtre d'installation apparaît. Cliquer sur `Next`  
  
➡️ **Zone Type** : Primary Zone    
  
➡️ **Active Directory Zone Replication Scope** : `To all DNS servers running on domain controllers in this domain: wilders.lan`  
  
➡️ **Zone name** : wilders.lan (ceci est un exemple)  
  
➡️ **Dynamic Update** : pour des raisons de sécurité, cocher `Do not allow dynamic updates`  
  
### :clubs: Créer une zone de recherche inversée (Adresse IP -> Domaine)  
  
Il est fortement recommandé de créer également la zone de recherche inversée avant de créer les enregistrements DNS.  
En effet, une fois les zones de recherche directe et inversée créées, il sera possible de créer des enregistrements DNS, ainsi que les pointeurs associés en même temps.  
  
➡️ Faire un clic droit sur `Reverse Lookup Zones` puis cliquer sur `New Zone`  
  
➡️ **Zone Type** : Primary Zone  
  
➡️ **Active Directory Zone Replication Scope** : `To all DNS servers running on domain controllers in this domain: wilders.lan`  
  
➡️ **Reverse Lookup Zone Name** : Cocher `IPv4 Reverse Lookup Zone` puis rentrer l'identifiant réseau (Ex: 172.20.0)  
  
>Pour la zone de recherche inversée, Windows Server crée par défaut un fichier de zone dont le nom est basé sur l'ID réseau inversé et le suffixe « in-addr.arpa.dns »    
>Remarque : « inaddr.arpa » est un domaine spécial (standard) utilisé pour la résolution DNS inversée dans IPv4. Pour IPv6, il s'agit de « ip6.arpa » ou « ip6.int »
  
➡️ **Dynamic Update** : Cocher `Do not allow dynamic updates` par mesure de sécurité puis terminer  
  
## :three: Configurer la Zone de Recherche Directe  
  
Lorsque vous créez une zone sur un serveur DNS, vous utiliserez principalement 4 types d'enregistrements DNS :  
  
- Les enregistrements A ou AAAA pour pointer un sous-domaine vers une adresse IPv4 (pour le type A) ou IPv6 (pour le type AAAA)  
- Les enregistrements CNAME pour pointer le sous-domaine "www" vers la même adresse IP que votre domaine racine (exemple : informatiweb.lan)  
- Les enregistrements MX pour spécifier quel serveur de messagerie, Google, Hotmail, ... utilisera pour envoyer le courrier à vos adresses email professionnelles "contact@informatiweb.lan".  
- Les enregistrements TXT pour prouver à Google Analytics ou à d'autres services en ligne que vous êtes le propriétaire de votre site  
  
### ❄️ Enregistrement A ou AAAA  
  
Pour créer un enregistrement A ou AAAA, faire un clic droit sur la Zone de Recherche Directe fraîchement créée puis `New Host (A or AAAA)...`  
  
➡️ **Nom** : Entrer le nom du serveur, par exemple `srvwin01`  
  
➡️ **IP address** : Entrer l'adresse IP du serveur DHCP  
  
➡️ Cocher `Create associated pointer (PTR) record` afin de créer automatiquement le pointeur associé dans la zone de recherche inversée puis cliquer sur `Add Host`    
  
### 🌀 Enregistrement CNAME  
  
Pour créer un enregistrement CNAME, faire un clic droit sur la Zone de Recherche Directe puis `New Alias (CNAME)...`  
  
➡️ **Alias name** : Entrer un nom au choix, par exemple dns-server, puis cliquer sur `OK`    
  
➡️ **Fully qualified domain name (FQDN) for target host:** : cliquer sur le bouton `Browse` et rechercher la machine qu'on souhaite associer à ce CNAME. Dans notre cas, il s'agit de notre serveur 
 
  ![image](https://github.com/user-attachments/assets/48a98a22-f6c2-466c-ad3e-66d222732d28)  
  
![image](https://github.com/user-attachments/assets/10ebf573-bcaf-4709-af5e-b0dc07973112)  
  
![image](https://github.com/user-attachments/assets/38b0cd65-4d98-482a-8b07-45511faa0638)  
  
![image](https://github.com/user-attachments/assets/1809fe8c-f497-43b4-a940-b7ee0b170816)  
  
![image](https://github.com/user-attachments/assets/e80ed94e-3a97-48af-af8f-fd4fc5750944)  
  
## :four: Test de la configuration DNS mise en place  
  
Pour tester que la configuration DNS que nous venons de mettre en place est correcte, nous allons utiliser l'outil `nslookup`  
Il s'agit d'un outil en ligne de commande utilisé pour interroger les serveurs DNS. Il permet de vérifier la résolution de noms de domaine en adresses IP et vice versa, ainsi que de diagnostiquer des problèmes liés au DNS.  
  
➡️ **Test du Host A "srvwin01" :** `nslookup srvwin01`  
  
![image](https://github.com/user-attachments/assets/7649fdad-4df8-4a37-8fff-db1943cc2914)  

  
➡️ **Test de la machine cliente avec réservation IP :** `nslookup DESKTOP-CTOKK97.wilders.lan`  
  
![image](https://github.com/user-attachments/assets/76c1e6bd-34f0-4dc6-a2d2-d88659f41fb1)  
  
➡️ **Test du CNAME attribué au serveur "dns-server" : `nslookup dns-server`  
  
![image](https://github.com/user-attachments/assets/ae388baa-720b-4f68-80e4-b7c84e28c1e1)  
  
✔️ Ces commandes ne retournent aucun message d'erreur. Notre configuration DNS a donc correctement été effectuée 👍



  




 
 
 

