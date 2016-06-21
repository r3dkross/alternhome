# alternhome
Tuto pour l'accès ssh et scp sur hl

Source: https://www.jeedom.com/forum/viewtopic.php?f=39&t=5541&hilit=altui&start=340

Merci à amg0 créateur du plugin ALtui pour son travail et son aide précieuse

1.Installer le Plugin AltUI sur Homelive :

- Se connecter à son interface de gestion Homelive
- Effectuer une sauvegarde de la configuration et la télécharger
- Observer l'url depuis laquelle le fichier à été téléchargé.
- Cette URL ressemble à :
https://relay2.home-live.orange.fr/rela ... /backup.sh
- Copiez collez cette URL dans le navigateur et après 0A60000C0DD881AE113191772E71AF4D2BD4A0, ajoutez ceci :
port_3480/data_request?id=action&serviceId=urn:micasaverde-com:serviceId:HomeAutomationGateway1&action=CreatePlugin&PluginNum=8246&Version=29881
- Vous obtenez une URL du type : https://relay2.home-live.orange.fr/rela ... sion=26896
- Entrez cette URL dans un nouvel onglet de votre navigateur.
- Cela va installer Altui. Vous allez obtenir un message d'erreur rouge, puis un "OK" en dessous.
- Patientez 2 min.
- Vous remarquerez également qu'un nouvel objet a été ajouté sur votre interface homelive, l'objet "altui".
- Copiez de nouveau l'url et y ajouter : port_3480/data_request?id=lr_ALTUI_Handler&command=home# url
- Vous obtenez une url du type :
https://relay2.home-live.orange.fr/rela ... mand=home# url
- Validez et patientez 3min.
- Vous voila connecté sur AltUI.
- Vous pouvez maintenant effectuer la mise à jour du plugin ALTUI via le menu prévu à cet effet.

2.Entrer ces lignes de codes sous altui>divers>commande os:

cat /etc/config/dropbear

vi /etc/config/dropbear

uci add dropbear dropbear

uci set dropbear.@dropbear[-1].PasswordAuth=on
uci set dropbear.@dropbear[-1].RootPasswordAuth=on
uci set dropbear.@dropbear[-1].Interface=*
uci set dropbear.@dropbear[-1].Port=2222
uci set dropbear.@dropbear[-1].RootLogin=on

uci commit dropbear

/etc/init.d/dropbear restart


firewall:

cat /etc/config/firewall

vi /etc/config/firewall

uci add firewall rule

uci set firewall.@rule[-1].src=wan
uci set firewall.@rule[-1].target=ACCEPT
uci set firewall.@rule[-1].proto=tcp
uci set firewall.@rule[-1].dest_port=2222

uci commit firewall

/etc/init.d/firewall restart

sed -i -e "s/mode=/mode=1#/g" /usr/bin/mios-service-update_permissions.sh

reboot

3.Changer les coordonnées GPS de Homelive pour récupérer les bons horaires de coucher/lever de soleil:

-Récupérer les valeurs latitude et longitude en hexadécimal sur internet: http://www.coordonnees-gps.fr par exemple
-Pour Marseille il s'agit de latitude=43.3 et longitude=5.4
-Sous Altui créer une scène peu importe le nom
-Dans le champ lua rentrer les paramètres suivants:

  luup.attr_set ("latitude", 43.3, 0 )
  return true
  
-remplacez 43.3 par la valeur de votre latitude et refaite une autre scène pour la longitude
-Exécutez la scène
-Redémarrer le moteur luup dans l'onglet divers, faites un refresh de la page et normalement c'est good
-Pour vérifier créer une scène avec la condition du lever ou du coucher, enregistrez la
-Allez dans l'onglet plus>Controleur
-Vers le bas vous devriez trouver les latitudes et longitudes modifiées
