# Séance 1 - Commande MCC basique

## Commande de MCC - niveau basique
Objectifs :

•	Générer 4 PWM en complémentaire décalée pour contrôler en boucle ouverte le moteur en respectant le cahier des charges,

•	Inclure le temps mort (voir doc transistors)

•	Vérifier les signaux de commande à l'oscilloscope,

•	Prendre en main le hacheur,

•	Faire un premier essai de commande moteur.

Fréquence PWM = 20kHz : compromis entre vitesse commutation des transistors et ondulations de courant : plus commutation rapide, moins on a d’ondulations de courant mais on augmente les pertes par commutation au niveau des transistors. On se place à 20kHz aussi parce que c’est dans l’inaudible.

##6.1. Génération de 4 PWM

Générer quatre PWM sur les bras de pont U et V pour controler le hacheur à partir du timer déjà attribué sur ces pins.

Cahier des charges :

•	Fréquence de la PWM : 20kHz

•	Temps mort minimum : à voir selon la datasheet des transistors (faire valider la valeur)

•	Résolution minimum : 10bits.

Pour les tests, fixer le rapport cyclique à 60%.
Une fois les PWM générées, les afficher sur un oscilloscope et les faire vérifier par votre professeur.

MOSFET : irf540n 
![image](https://github.com/Chatvolant/TP_actionneurs/assets/143954035/44a8f106-10d1-4d9b-a6ec-42af40752080)

Trr : Temps de commutation diode interne
Timer 1 : bus APB2
Temps mort 200ns

PWM en mode up de base mais nous on veut mode up down (comptage, décomptage)
Donc on divise ARR/2 (ARR était à 8499)



