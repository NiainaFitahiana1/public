# Guide de diagnostic et de récupération --- Huawei E8372h-153

**Objectif :** faire diagnostiquer le modem Huawei Wingle E8372h-153
dans un cybercafé équipé de Windows, puis rechercher une récupération
adaptée à sa révision exacte sans risquer de le rendre inutilisable.

> **Consigne essentielle : ne flasher aucun firmware tant que le modèle
> exact, la révision matérielle, la version installée et la
> compatibilité du fichier n'ont pas été vérifiés.** Un firmware destiné
> à un E3372, E8372h-820 ou à une autre variante n'est pas
> interchangeable automatiquement.

------------------------------------------------------------------------

## 1. Identification du modem

Modèle inscrit sur l'étiquette : **Huawei E8372h-153**

Informations déjà relevées sous Ubuntu 22.04 :

  Élément                                    Résultat
  ------------------------------------------ ---------------------
  Identifiant USB en mode HiLink             `12d1:14db`
  Identifiant USB transitoire au démarrage   `12d1:1f01`
  Adresse MAC de l'interface USB             `0C:5B:8F:27:9A:64`
  Fabricant USB                              `HUAWEI_MOBILE`
  Produit USB                                `HUAWEI_MOBILE`
  Révision USB (`bcdDevice`)                 `1.02`
  Interface Linux                            `enx0c5b8f279a64`
  Pilote Linux                               `cdc_ether`

**Attention :** `bcdDevice 1.02` est une révision USB déclarée par le
périphérique. Ce n'est pas la version du firmware.

Le deuxième modem Huawei branché sur le PC Ubuntu est un appareil
différent. Ses interfaces `ttyUSB*` et `wwx001e101f0000` ne concernent
pas le E8372h-153. Ne pas les utiliser pour une opération de réparation.

------------------------------------------------------------------------

## 2. Symptômes constatés

-   Le modem est alimenté et apparaît dans `lsusb`.
-   Il passe de `12d1:1f01` à `12d1:14db` pendant son démarrage.
-   Sous Ubuntu, le pilote `cdc_ether` crée bien l'interface réseau USB.
-   L'interface reste toutefois `NO-CARRIER`, avec `carrier = 0`.
-   Les compteurs RX/TX restent à zéro.
-   L'accès à `192.168.8.1` échoue, même après avoir forcé une route
    vers l'interface du Wingle.
-   Le voisin ARP `192.168.8.1` reste `INCOMPLETE`.
-   Le voyant Wi-Fi ne s'allume jamais, même brièvement.
-   Le voyant réseau vert a été observé clignotant une fois toutes les
    deux secondes sur le chargeur.
-   Le bouton RESET a déjà été essayé sans rétablir le Wi-Fi.

**Interprétation prudente :** le modem est détecté en USB, mais son
interface réseau HiLink ne communique pas. Le Wi-Fi ne démarre pas
visiblement. Cela peut venir du firmware, de la configuration ou d'une
panne matérielle. Les tests actuels ne permettent pas de trancher.

------------------------------------------------------------------------

## 3. Matériel à apporter au cybercafé

-   Le Huawei E8372h-153 seul.
-   Son câble ou adaptateur USB habituel.
-   Une carte SIM, si possible, mais effectuer aussi un test sans SIM.
-   Un ordinateur Windows avec droits administrateur si possible.
-   Une connexion Internet pour consulter les ressources officielles
    Huawei et vérifier les fichiers.
-   Si possible, un petit hub USB alimenté ou une alimentation USB
    stable.

Ne pas brancher simultanément le deuxième modem Huawei pendant les
opérations de diagnostic ou de récupération.

------------------------------------------------------------------------

## 4. Diagnostic sous Windows --- avant toute réparation

### Étape A --- relever les identifiants USB

1.  Brancher uniquement le E8372h-153.
2.  Ouvrir le **Gestionnaire de périphériques** (`devmgmt.msc`).
3.  Repérer le périphérique Huawei, éventuellement dans :
    -   Cartes réseau ;
    -   Modems ;
    -   Ports (COM et LPT) ;
    -   Contrôleurs de bus USB ;
    -   Lecteurs de disque.
4.  Ouvrir **Propriétés → Détails → Numéros d'identification du
    matériel**.
5.  Copier les valeurs `VID_xxxx&PID_xxxx` et, si disponible,
    l'identifiant de révision.
6.  Noter tous les changements d'identifiant USB au branchement et au
    démarrage.

Ne pas supposer que le nom commercial affiché par Windows suffit à
identifier la variante.

### Étape B --- vérifier l'interface HiLink

1.  Vérifier si Windows crée une carte réseau Huawei.
2.  Ouvrir l'invite de commandes et exécuter :

``` bat
ipconfig /all
```

3.  Repérer uniquement la carte réseau correspondant au E8372h-153.
4.  Si elle reçoit une adresse IP, noter l'adresse de la passerelle.
5.  Essayer dans un navigateur l'adresse de passerelle indiquée. Sur
    beaucoup de modèles HiLink, l'adresse par défaut est
    `http://192.168.8.1`, mais il faut utiliser la passerelle réellement
    affichée si elle est différente.

Si aucune carte réseau n'apparaît, ou si elle est présente sans lien,
noter le message exact. Ne pas modifier au hasard les paramètres réseau
des autres appareils.

### Étape C --- rechercher les informations de firmware

Si l'interface d'administration s'ouvre, relever et photographier les
informations affichées dans les rubriques du type :

-   Informations sur l'appareil ;
-   Informations sur le périphérique ;
-   Version logicielle ;
-   Version du WebUI ;
-   Version du firmware ;
-   IMEI (ne pas publier cette donnée) ;
-   État du réseau.

Ne pas partager publiquement l'IMEI, le numéro de série, les mots de
passe Wi-Fi ou les identifiants de connexion.

Si l'interface ne s'ouvre pas, noter ce fait : il ne faut pas inventer
une version de firmware à partir de `12d1:14db` ou de `bcdDevice 1.02`.

------------------------------------------------------------------------

## 5. Vérifications non destructives

Avant toute tentative de flashage :

1.  Tester le modem seul sur un port USB différent.
2.  Tester avec une alimentation USB stable.
3.  Faire un essai sans carte SIM, puis avec une SIM fonctionnelle.
4.  Vérifier si un bouton Wi-Fi/WPS existe sur cette variante et s'il
    permet d'activer le Wi-Fi selon la documentation exacte du modèle.
5.  Vérifier si le réseau Wi-Fi apparaît après un démarrage complet.
6.  Relever les voyants et leur rythme exact.
7.  Vérifier si le périphérique se déconnecte et se reconnecte
    spontanément dans le Gestionnaire de périphériques.

Le RESET a déjà été essayé. Il n'est pas nécessaire de le répéter sans
indication précise de la procédure officielle applicable à cette
variante.

------------------------------------------------------------------------

## 6. Recherche du firmware correct

La recherche doit porter précisément sur :

**Huawei E8372h-153**

Ne pas confondre avec :

-   E8372h-820 ;
-   E8372h-608 ;
-   E3372h-153 ;
-   E3372s ;
-   E5573 ;
-   d'autres variantes E8372.

Avant de télécharger ou d'exécuter un fichier, demander au technicien de
confirmer :

-   que le paquet est destiné explicitement au **E8372h-153** ;
-   que la révision matérielle est compatible ;
-   que le firmware principal et le WebUI sont compatibles entre eux ;
-   que la méthode de flashage est prévue pour cette variante ;
-   que le fichier provient d'une source fiable ;
-   que la somme de contrôle (SHA-256) correspond à une valeur publiée
    par une source fiable, si disponible ;
-   que le modem peut être récupéré en cas d'échec.

Des noms de versions ont été évoqués pendant le diagnostic, mais leur
compatibilité avec cet exemplaire n'a pas été confirmée. **Ne pas les
considérer comme des recommandations d'installation :**

``` text
E8372h-153TCPU-V200R001B333D63SP00C1217
E8372h-153 WEBUI-V100R007B100D12SP00C03
```

Ne pas installer un fichier simplement parce que son nom contient
`E8372h-153`.

------------------------------------------------------------------------

## 7. Règles de sécurité pendant une éventuelle récupération

-   Ne jamais débrancher le modem pendant une mise à jour.
-   Utiliser une alimentation stable et un port USB fiable.
-   Ne pas lancer plusieurs outils de flashage en même temps.
-   Ne pas utiliser un firmware destiné à un autre modèle ou une autre
    variante.
-   Ne pas effectuer de court-circuit sur des points de test de la
    carte.
-   Ne pas utiliser de commandes `dd`, `fastboot`, `balong_flash` ou
    d'écriture mémoire sans procédure vérifiée spécifiquement pour ce
    modèle.
-   Ne pas supprimer ou remplacer les pilotes au hasard.
-   Ne pas lancer un exécutable trouvé sur un forum sans vérifier sa
    provenance.
-   Si le logiciel de mise à jour indique un modèle incompatible,
    **annuler**.
-   Si aucune méthode fiable n'est disponible, arrêter le flashage et
    demander un diagnostic matériel.

------------------------------------------------------------------------

## 8. Résultat à obtenir du cybercafé

Avant de repartir, demander au technicien de fournir :

-   [ ] Les identifiants USB exacts relevés sous Windows.
-   [ ] La liste des interfaces et périphériques détectés.
-   [ ] La présence ou l'absence d'une carte réseau Huawei.
-   [ ] Le résultat d'accès à la passerelle HiLink.
-   [ ] La version firmware et WebUI, si elles sont lisibles.
-   [ ] Le nom exact et la source de tout firmware proposé.
-   [ ] La preuve de compatibilité avec E8372h-153.
-   [ ] La méthode de récupération en cas d'échec.
-   [ ] Le résultat du test Wi-Fi après diagnostic ou réparation.

**Ne pas autoriser le technicien à flasher le modem s'il ne peut pas
confirmer le modèle, la compatibilité du firmware et la méthode de
récupération.**

------------------------------------------------------------------------

## 9. Conclusion

Le E8372h-153 est reconnu par Ubuntu en USB, mais son interface réseau
HiLink ne reçoit ni ne transmet de paquets. Le voyant Wi-Fi reste
éteint. Le diagnostic actuel ne permet pas de conclure définitivement à
une corruption du firmware : une panne matérielle ou un problème de
configuration reste possible.

La priorité au cybercafé est donc de récupérer les informations réelles
du périphérique sous Windows et, si possible, de lire la version
firmware/WebUI. Le flashage ne doit être envisagé qu'après
identification exacte du matériel et validation d'un paquet compatible.
