# Séance 1 - Commande MCC basique

## Commande de MCC - niveau basique
Objectifs :

•	Générer 4 PWM en complémentaire décalée pour contrôler en boucle ouverte le moteur en respectant le cahier des charges,

•	Inclure le temps mort (voir doc transistors)

•	Vérifier les signaux de commande à l'oscilloscope,

•	Prendre en main le hacheur,

•	Faire un premier essai de commande moteur.

Fréquence PWM = 20kHz : compromis entre vitesse commutation des transistors et ondulations de courant : plus commutation rapide, moins on a d’ondulations de courant mais on augmente les pertes par commutation au niveau des transistors. On se place à 20kHz aussi parce que c’est dans l’inaudible.

## 1. Génération de 4 PWM

Générer quatre PWM sur les bras de pont U et V pour controler le hacheur à partir du timer déjà attribué sur ces pins.

Cahier des charges :

•	Fréquence de la PWM : 20kHz

•	Temps mort minimum : à voir selon la datasheet des transistors (faire valider la valeur)

•	Résolution minimum : 10bits.

Pour les tests, on a fixé le rapport cyclique à 60% (demandé dans l'énoncé du TP).
On a ensuite fait afficher les PWM sur un oscillo et on les a faites vérifiées par le prof.

MOSFET : irf540n 
![alt text](https://github.com/Chatvolant/TP_actionneurs/blob/main/mosfet_source_drain_characteristics.png)

Trr : Temps de commutation diode interne  
Timer 1 : bus APB2
Temps mort : 200ns (on s'est basé sur la valeur du Trr pour fixer le tps mort à 200 ns)

PWM en mode up de base mais nous on veut mode up down (comptage, décomptage)
Donc on divise ARR/2 (On avait mis ARR à 8499 comme on pensait au début qu'on était seulement en mode up pour le comptage)

![alt text](https://github.com/Chatvolant/TP_actionneurs/blob/main/pwm.png)

Il ne faut pas oublier de démarrer la génération des PWM. Cela se fait par le biais de fonctions HAL

```c
HAL_TIM_PWM_Start (&htim1, TIM_CHANNEL_1);
	HAL_TIMEx_PWMN_Start (&htim1, TIM_CHANNEL_1);
	HAL_TIM_PWM_Start (&htim1, TIM_CHANNEL_2);
	HAL_TIMEx_PWMN_Start (&htim1, TIM_CHANNEL_2);
```

Prochain objectif : Programmer le rapport cyclique avec l'UART en boucle ouverte (via le shell)

## 2. Commande de vitesse

dans le fichier shell.c on code le changement du rapport cyclique (le code s'éxécute seulement si on écrit speed XX avec XX la valeur entre 0 et 100 pour le rapport cyclique)

![alt text](https://github.com/Chatvolant/TP_actionneurs/blob/main/code_speed_v1.png)

On obtient les signaux suivants  


On remarque que les signaux ne sont pas décalés entre eux, c'est parce qu'on a oublié de prendre en compte la complémentarité des deux autres signaux. Donc on met ça _100-alpha_speed_ au lieu de _alpha_speed_ pour CCR2

![alt text](https://github.com/Chatvolant/TP_actionneurs/blob/main/code_speed_v2.png)  
La dernière ligne du code peut aussi s'écrire de la façon suivante : 

```c
TIM1->CCR2=(TIM1->ARR)-(TIM1->CCR1);
```



# Séance 2 - Commande en boucle ouverte, mesure de vitesse et de courant

## 1. Commande de la vitesse
Ajout de commandes au projet :  

- Commande start : permet de fixer le rapport cyclique à 50% (vitesse nulle) et d'activer la génération des pwm (HAL_TIM_PWM_Start et HAL_TIMEx_PWMN_Start),
- Commande stop : permet de désactiver la génération des PWM.
- Commande speed XXXX : permet de définir le rapport cyclique à XXXX/PWM_MAX (montée progressive à cette vitesse afin de réduire l'appel à courant)

On les ajoute au shell.c, où on a codé la commande speed  

```c

		else if(strcmp(argv[0],"start")==0){
			TIM1->CCR1=(50*(TIM1->ARR))/100; //on fixe le rapport cyclique à 50%
			TIM1->CCR2=(50*(TIM1->ARR))/100; //on fixe le rapport cyclique à 50%

			HAL_TIM_PWM_Start (&htim1, TIM_CHANNEL_1);
			HAL_TIMEx_PWMN_Start (&htim1, TIM_CHANNEL_1);
			HAL_TIM_PWM_Start (&htim1, TIM_CHANNEL_2);
			HAL_TIMEx_PWMN_Start (&htim1, TIM_CHANNEL_2);
		}

		else if(strcmp(argv[0],"stop")==0){
			 HAL_TIM_PWM_Stop (&htim1, TIM_CHANNEL_1);
				}
```

On a rencontré quelques problèmes  avec la carte : les transistors ne fonctionnnaient pas correctement et du coup on avait un moteur qui s'emballait.



