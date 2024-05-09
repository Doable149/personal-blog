+++
title = 'Correction des Problèmes de Latence Nvidia sur KDE Plasma 5/6 sur X11'
date = 2024-05-03T18:19:28+02:00
draft = false
+++

Bonjour lecteurs de cet article !

Certains d'entre vous qui utilisent un écran à haute fréquence d'affichage (style 144hz) avec un deuxième écran à une fréquence différente, disons 60 Hz, ont peut-être remarqué des saccades ou des lags lors du lancement d'applications telles que les paramètres systèmes, et il se peut également que les fenêtres ne se déplacent pas au dessus de 60 images par secondes.

J'ai réussi à résoudre ce problème en fusionnant plusieurs solutions trouvées sur plusieurs publications Reddit et sur des forums de KDE Plasma. Voici ce que vous devez faire pour résoudre cela :

## Désactiver l'OpenGL Flipping

Pour ce faire, vous devez vous rendre dans nvidia-settings -> OpenGL Settings -> Décocher "Allow OpenGL Flipping".

{{< figure src="nvidia-settings.webp" title="Paramètres Nvidia" >}}

Vous devriez normalement constater une différence dans les saccades de la souris et votre curseur ne devrait pas "sauter" de trames lors du lancement d'applications.

Pour conserver ce changement, vous devez modifier le fichier de configuration XOrg dans `/etc/X11/xorg.conf`.

Il n'est pas créé par défaut, nvidia-settings peut le générer pour vous si vous le lancez avec l'utilisateur root avec cette commande :

sudo nvidia-settings


Pour générer le fichier XOrg avec nvidia-settings en mode root, allez à :

Configuration de l'affichage du serveur X -> Enregistrer dans le fichier de configuration X et enregistrez-le dans `/etc/X11/xorg.conf` :

Menu OpenGL

Et ensuite, pour désactiver complètement l'OpenGL Flipping, ajoutez ceci à votre fichier xorg.conf dans la section "Device" de la configuration :

```shell
Option         "NoFlip" "true"
```

Enregistrez et redémarrez votre ordinateur.

## Désactiver la synchronisation verticale KWIN et forcer le pipeline de composition complet

Pour ce faire, vous devrez ajouter quelques lignes à votre fichier /etc/environment.

Ce fichier doit être édité par l'utilisateur root, utilisez votre éditeur préféré et ajoutez ces lignes :


```shell
__GL_SYNC_DISPLAY_DEVICE=DP-0 
VDPAU_NVIDIA_SYNC_DISPLAY_DEVICE=DP-0
```

`__GL_SYNC_DISPLAY_DEVICE` => Indiquez votre moniteur avec une fréquence de rafraîchissement élevée

`VDPAU_NVIDIA_SYNC_DISPLAY_DEVICE` => Même chose

Pour obtenir l'identifiant du périphérique d'affichage (comme DP-0), vous pouvez utiliser nvidia-settings et aller dans la Configuration de l'affichage du serveur X.

Sélectionnez votre moniteur dans la disposition et lisez le champ de sélection, il devrait indiquer "DP-X sur GPU-0" où X est l'ID du port DisplayPort utilisé :

Utilisez cette valeur (DP-0 par exemple) dans les articles __GL_SYNC et VDPAU du fichier d'environnement et enregistrez-le.

Encore une fois dans le même fichier, ajoutez ces trois lignes après les deux autres :

```shell
KWIN_X11_REFRESH_RATE=144000
KWIN_X11_NO_SYNC_TO_VBLANK=1
KWIN_X11_FORCE_SOFTWARE_VSYNC=1
```
Signification des valeurs :

`KWIN_X11_REFRESH_RATE` : Fréquence de rafraîchissement de votre moniteur multipliée par 1000

`KWIN_X11_NO_SYNC_TO_VBLANK` : Ne pas utiliser le vblank pour le compositeur kwin

`KWIN_X11_FORCE_SOFTWARE_VSYNC` : Forcer l'utilisation de la synchronisation verticale logicielle de kwin

Allez dans nvidia-settings en mode sudo comme précédemment, puis allez dans la Configuration de l'affichage du serveur X et cliquez sur le bouton "Avancé..." en bas de la fenêtre.

Sélectionnez votre moniteur haute fréquence et cochez "Forcer le pipeline de composition" et "Forcer le pipeline de composition complet".

Appliquez et enregistrez votre fichier de configuration X comme précédemment dans /etc/X11/xorg.conf.

Redémarrez votre ordinateur.

J'espère que votre expérience est maintenant plus fluide. Pour moi, ça a fonctionné comme un charme !

Sources:

- [Mon post sur reddit](https://www.reddit.com/r/EndeavourOS/comments/p8b2kf/for_those_that_have_stutterlags_problems_with_kde)
- [Un tutoriel sur reddit pour gérer deux écran à rafraîchissement différent](https://www.reddit.com/r/linux_gaming/comments/ap781g/living_with_144hz_with_60hz_secondary_screens/)
- [Bug sur le tracker KDE](https://bugs.kde.org/show_bug.cgi?id=433094)
