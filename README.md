# DM programmation interface Ocaml : Cut The Rope

**[Français](#français)** · **[English](#english)**

---

## Français

### Préambule

Le jeu nécessite un écran de 1000 de hauteur par 800 de largeur minimum. Sur windows, il faut aussi vérifier dans `Affichage` que le zoom soit bien à 100%.

D'abord, il est important qu'avant de lancer le jeu, vous ouvrez une première fois le fichier et y changez la variable `working_path` pour le chemin du dossier où a été dézippé le jeu. Pensez bien à remplacer les `\` par `\\` et d'en ajouter un à la fin comme dans l'exemple.

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/7c2606d0c6c1fed453adc4f634b43d8b3fffc606/Captures%20du%20jeu/Working_Path.PNG)

Nous vous conseillons aussi, si vous avez le temps de consulter le fichier `Reedme_level_creation.txt` pour créer vos propres niveaux (en plus pas besoins de relancer le jeu pour en ajouter un).

Note: Le jeu s'adapte de win32 à unix/cigwin, cependant ce dernier aura des textes moins sympatique (il n'y a pas besoin de changer le open_graph, c'est automatique). Voici une vidéo de la version Windows pour voir le jeu sous son plus beau jour:

### Présentation du jeu

Notre jeu se base sur Cut The Rope, un jeu Android/IOS créer par ZeptoLab. Le principe est simple faire manger un objet (un bonbon dans le jeu d'origine et un hamburger dans le notre) à un dinosaure (ici Marcus).

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/620776c732366b00c2d4711826739d8bb7124e26/Captures%20du%20jeu/Welcome.PNG)

Lors du lancement du jeu, vous deverez attendre un chargement d'environ 1/1.5 minutes, cependant pas d'inquiètude vous pourrez soit vous laisser emporter par le récit de Marcus afin d'en apprendre plus sur la back-story de jeu, ou bien jouer au mini-jeu proposé (ce dernier ce jouant avec les touches 'a' pour aller à gauche et 'e' pour aller à droite et dont le but est de manger le plus de hamburger possible, notre record est de 23). On notera que cela reste un mini-jeu et le chargement prime sur ce dernier, ainsi il freeze en fin de chargement d'un fichier.

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/9cb564be4db2ccb528b6bd9d0445db36bb4f2106/Captures%20du%20jeu/Chargement.PNG)

Ensuite lors de la partie il faudra donner le hamburger à Marcus.

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/a7b1ccfd436a1b1904840773ffd259feedbeaafb/Captures%20du%20jeu/Feed_Marcus.PNG)

Cependant des piques que le hamburger devra éviter vous compliquerons rapidement la tâche.

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/c7ea7e053627fa3e7232d3355b1e467b2ccb884c/Captures%20du%20jeu/Spikes_Example.PNG)

Après votre premier niveau jouer (et même pas besoin de gagner) vous aurez la possibilité d'accèder au menu, par le biais de ce dernier vous aurez accès à plein de niveaux (et même aussi les votre si vous en créer).

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/6dbf8514092b76865950279578a3407c208a39aa/Captures%20du%20jeu/Menu.PNG)

### Développement et enjeux du projet

#### La physique

Tout d’abord il fallait simuler la physique des cordes. Afin de se faire, nous avons décidé d’employer une méthode appelée “Verlet’s integration”
(suite à de nombreuses autres idées abandonnées) qui est une alternative à la méthode d’Euler:
[explication de la méthode en détail](https://fr.wikipedia.org/wiki/Int%C3%A9gration_de_Verlet).

#### Le stockage des données

Pour notre projet deux types d’objet on dû être stocké:
des images ou des gifs (sous forme de png sequence)
des niveaux

**1. Gestion des images**

Afin de gérer les images nous avons procédé ainsi:

A l’aide de python, en particulier des modules PIL et numpy, nous avons transformé le format png en des fichiers txt en clair (format propre à notre programme le .brc,
créé avec un programme personnel python assez simple [ici](https://colab.research.google.com/drive/18S-ul2-umBW8ydUtrBednBEv2MOYPWHJ?usp=sharing)).
Les fichier brc se présentent sous le format suivant:

1. La première ligne comporte les dimensions l’image “largeurxhauteur”.

2. Les lignes suivantes contiennent les valeurs RGBA (“r g b a”):
- R (quantité de rouge) de 0 à 255
- G (quantité de vert) de 0 à 255
- B (quantité de bleu) de 0 à 255
- A (transparence) de 0 à 255

Nous avons ensuite mit en place une fonction de lecture de ce format sous Ocaml, qui transforme le fichier en liste de couleur graphics, qui par la suite est interprètée comme une image par la fonction make_image (on notera que cette dernière nécessite la fenêtre graphique ouverte et que l'image crée à un fort taux de corruption lors de la fermeture de la fenêtre).

**2. Gestion des niveau**

Afin de gérer les niveaux, le procédé est très similaire, en effet il s’agit aussi d’un fichier texte avec une nouvelle extension (.niv). Ce dernier est lu afin d’obtenir une structure de plusieurs tableau_dynamiques1 qui correspond aux différents éléments du niveau. (Bien qu’il soit en anglais, le fichier Readme associé au jeu explique comment créer son propre niveau et l’ajouter au jeu).

*note:*

Une autre problématique rencontrée fut la gestion des array, ces derniers étant immuable après création et chaque niveau contenant un nombre variable d’objet (même au cours de la partie le nombre de liens et points varie) il était nécessaire de mettre en place une structure pour gérer les donnés.

Ainsi, le type tableau dynamique est un array à taille variable utilisant 4 méthodes pour fonctionner (on notera que l’array support n’est pas récupérable), soit Tab un tableau de ce type:
- Tab.add valeur,  ajoute la valeur au tableau;
- Tab.id id, regarde le contenue de la case en position id;
- Tab.remove id, retire du tableau la valeur en id;
- Tab.size (), retourne la longueur utilisé du tableau (le tableau étant éventuellement plus long en mémoire).

#### L'esthétique

Lors du processus de création l’apparence du code aussi bien que du jeu a été soigné. Ce dernier se divise en section afin que par le nom de la section et de la fonction son utilité soit le plus limpide possible. La structure se décompose ainsi:
- Imports
- Constantes
- Variables globales
- Types
- Fonctions outils / de vérification
- Fonction d'update 
- Fonctions d'affichage 
- Fonctions d'importation
- Fonction de gestion du jeu
- Main (Launcher du jeu)

Quant à l’apparence du jeu en tant que tel, nous avons voulu rester fidèle au jeu d’origine tout en apportant notre propre touche (l’histoire lors de l’écran de chargement met en place un univers différent avec un problématique similaire). Les écrans de chargement et de transition ont aussi reçu beaucoup d’attention, le premier étant très présent (le jeu met en effet une soixantaine de secondes à se lancer dû aux images à charger, par exemple les fichiers Back.brc et Front.brc qui font tous les deux 800.001 lignes). Ce dernier est donc accompagné d'un mini-jeu et d'un histoire à lire assez simples mais fonctionnels.

---

## English

### Foreword

The game needs a screen at least 1000 px tall by 800 px wide. On Windows, also check in Display that the zoom is set to 100%.

Before you launch the game, open the file once and change the `working_path` variable to the path of the folder where the game was unzipped. Make sure to replace `\` with `\\` and add one at the end, as in the example.

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/7c2606d0c6c1fed453adc4f634b43d8b3fffc606/Captures%20du%20jeu/Working_Path.PNG)

We also recommend, if you have time, checking the `Reedme_level_creation.txt` file to create your own levels (no need to relaunch the game to add one).

Note: the game adapts from win32 to unix/cygwin, though the latter has less friendly text rendering (no need to change `open_graph`, that's automatic). Here's a video of the Windows version to see the game at its best:

### Game overview

Our game is based on Cut The Rope, an Android/iOS game created by ZeptoLab. The idea is simple: feed an object (a candy in the original game, a hamburger in ours) to a dinosaur (here, Marcus).

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/620776c732366b00c2d4711826739d8bb7124e26/Captures%20du%20jeu/Welcome.PNG)

When the game launches, you'll wait through a loading screen of about 1 to 1.5 minutes. No worries though: you can either follow Marcus's story to learn more about the game's backstory, or play the mini-game on offer (controlled with 'a' to move left and 'e' to move right, the goal being to eat as many hamburgers as possible, our record is 23). Note this is just a mini-game and loading takes priority over it, so it freezes once a file finishes loading.

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/9cb564be4db2ccb528b6bd9d0445db36bb4f2106/Captures%20du%20jeu/Chargement.PNG)

Then, during a level, you need to feed the hamburger to Marcus.

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/a7b1ccfd436a1b1904840773ffd259feedbeaafb/Captures%20du%20jeu/Feed_Marcus.PNG)

Spikes that the hamburger has to avoid will quickly make things harder.

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/c7ea7e053627fa3e7232d3355b1e467b2ccb884c/Captures%20du%20jeu/Spikes_Example.PNG)

After playing your first level (you don't even need to win it), you'll unlock the menu, which gives access to a range of levels, including any you've created yourself.

![This is an image](https://github.com/Adem-hub/ProjetOcaml/blob/6dbf8514092b76865950279578a3407c208a39aa/Captures%20du%20jeu/Menu.PNG)

### Development and challenges

#### Physics

First, we needed to simulate rope physics. To do this we settled on a method called Verlet integration (after abandoning several other approaches), an alternative to Euler's method: [detailed explanation of the method](https://fr.wikipedia.org/wiki/Int%C3%A9gration_de_Verlet).

#### Data storage

Two types of objects needed to be stored for this project: images or gifs (as PNG sequences), and levels.

**1. Image handling**

To handle images, here's what we did:

Using Python, in particular the PIL and numpy modules, we converted PNG files into plain text files (our program's own format, `.brc`, created with a fairly simple personal Python script [here](https://colab.research.google.com/drive/18S-ul2-umBW8ydUtrBednBEv2MOYPWHJ?usp=sharing)). brc files look like this:

1. The first line holds the image dimensions, "width x height".
2. The following lines hold RGBA values ("r g b a"):
- R (amount of red), 0 to 255
- G (amount of green), 0 to 255
- B (amount of blue), 0 to 255
- A (transparency), 0 to 255

We then built a reader for this format in OCaml that turns the file into a list of graphics colors, later interpreted as an image by the `make_image` function (note this requires the graphics window to be open, and the resulting image has a high corruption rate once the window closes).

**2. Level handling**

Level handling follows a similar process: it's also a text file, with a different extension (`.niv`). It's read into a structure of several `tableau_dynamique` (dynamic array) matching the level's different elements. (Although it's in English, the readme bundled with the game explains how to create your own level and add it to the game.)

*Note:*

Another issue we ran into was handling arrays: they're immutable once created, and each level holds a variable number of objects (even the number of links and points changes during play), so we needed a structure to manage that data.

The dynamic array type is a resizable array with 4 methods (note the underlying array itself can't be reclaimed), call it `Tab`:
- `Tab.add value`, adds the value to the array;
- `Tab.id id`, looks up the content at position id;
- `Tab.remove id`, removes the value at id;
- `Tab.size ()`, returns the array's used length (the array may be longer in memory).

#### Look and feel

Care went into both the code's appearance and the game's during development. The code is split into sections so that section and function names make their purpose as clear as possible. The structure breaks down as:
- Imports
- Constants
- Global variables
- Types
- Utility / check functions
- Update function
- Display functions
- Import functions
- Game management function
- Main (game launcher)

As for the game's look itself, we wanted to stay close to the original while adding our own touch (the story during the loading screen sets up a different world with a similar premise). The loading and transition screens also got a lot of attention, the first one especially, since it shows up often (the game takes around sixty seconds to start due to the images it has to load, for example the `Back.brc` and `Front.brc` files, each 800,001 lines long). It comes with a simple but functional mini-game and story to read.
