# Projet de reconnaissance d'espèces d'oiseauc avec CNN

## Introduction et objectifs initiaux 
L'objectif initial de ce projet était de développer un systèmede reconnaissance d'espèces d'oiseaux en temps réel, capable d'analyser des images ou vidéos capturées par une caméra connectée à un Raspberry Pi installé dans un jardin ou un espace naturel. Ce dispositif devait permettre de détecter automatiquement les oiseaux présents dans le champ de la caméra, d'identifier leur espèce et d'enregistrer la photo avec l'espèce reconnue.

L'idée était donc de faire des tests dans des conditions réelles, sur le terrain, afin d'avoir une application concrète et opérationnelle pour l'observation et la connaissance de la faune locale. 

Cependant, face à des contraintes de temps, nous avons dû adapter nos objectifs. Nous sommes passés à une phase de test sur des images fixes, directement depuis un ordinateur ou éventuellement via la webcam de l'ordinateur, ce qui a permis de valider le modèle CNN dans un cadre contrôlé avec dans les améliorations futures l'intégration de notre objectif de base. 

## Choix du dataset
Nous avons choisi d'utiliser le dataset 20_UK_Garden_Birds proposé sur Kaggle. Ce choix n'a pas été fait au hasard, bien qu'il existe des jeux de données beaucoup plus volumineux et diversifiés. Ce dataset couvre majoritairement des espèces d'oiseaux présentes en Belgique ce qui correspondait à notre zone géographique cible. Il offre un compromis raisonnable entre taille, diversité d'espèces et qualité des images et il contient un nombre équilibré d'images par classe (espèce), ce qui facilite l'apprentissage. 

Cependant, le dataset est de taille relativement limitée. De plus, le manque de diversité dans les conditions de prise de vue (angles, écliarages,..) peut limiter la robustesse. 

Nous avons décidé de ne pas créer notre propre dataset, ce qui aurait permis de coller parfaitement aux besoins finaux, notamment avec des images capturées sur site. Cela est dû à plusieurs raisons, notamment le manque de temps disponible pour collecter et annoter un nombre suffisant d'images. Aussi,la nécessité de s'appuyer sur un jeu de données déjà labellisé pour commencer rapidement l'entraînement. 


## Démarche suivie 
## 1.Préparation du dataset 
Nous avons d'abord vérifié l'équilibre entre les classes avec un nombre d'images comparable par espèce. Nous avons ajouté manuellement une classe "sans oiseaux" afin que le modèle apprenne à reconnaître les imagesne contenant aucun oiseau. Cette classe était essentielle dans la vision initiale où le Raspberry Pi devait filtrer les photos prises et indiquer si elles étaient exploitables. Cependant, les résultats d'apprentissage pour cette classe "vide" n'étaient pas satisfaisants, ce qui a conduit à revoir à la baisse l'objectif initial. 

## 2.Pré-traitement des images
Les images originales sont de taille : XXX. Pour des raisons matérielles (limitations de RAM, puissance de calcul limitée,...) ainsi que pour uniformiser les entrées du réseau, nous avons redimensionné toutes les images à une taille standard de 256*256 pixels. Ce choix impose que toutes les images (d'entrainement de test et prédiction) aient la même taille, condition indispensable pour que le modèle fonctionne correctement. 

## 3.Entraînement du modèle CNN 
Nous avons utilisé PyTorch avec un modèle à 6 couches convolutionnelles. Le format d'entrée des images PyTorch est (N,C,H,W) où : 
N = taille du batch (nombre d'images traitées simultanément) 
C = nombre de canaux (3 pour les images de couleur : RGB) 
H,W = hauteur et largeur des images 
Cette organisation diffère de celle utilisée dans OpenCv il est donc très important de réaliser cette étape. 

## 4.Augmentation du dataset
Afin de pallier la taille limitée du dataset, nous avons appliqué des transformations aléatoires sur chaque image (rotation, zoom, translation, changement de luminosité). Chaque image a été transformée 10 fois de manière différente et aléatoire, créant ainsi un dataset artificiellement agrandi. Ce choix a été dicté par le fait qu'une seule transformation par image ne suffisait pas à faire converger correctement le modèle. L'augmentation a ainsi permis d'améliorer les performances de notre modèle. 

## 5.Division des données
Le dataset a été divisé en 80% d'images pour l'entraînement et 20% pour le test. Aucun jeu de validation n'a été utilisé, cette absence s'explique par le fait que nous n'avons pas testé plusieurs architectures ou réglages entre eux mais nous nous sommes concentrés sur une unique configuration. Le jeu de test a donc servi à évaluer la performance finale du modèle. 



## Analyse du modèle
Chaque couche convolutionnelle a  un rôle précis: 
L'extraction progressive de caractéristiques simples (bords, textures) vers des motifs complexes spécifiques à chaque espèces. 
L'utilisation de filtres 3*3 est un choix classique. 
Les couches de pooling réduisent la dimension spatiale tout en conservant l'essentiel, limitant le surapprentissage. 
Les couches fully connected en fin de réseau permettent la classification finale. 


## Points manquants pour atteindre les objectifs initiaux 
Plus de temps pour implémenter et optimiser la reconnaissance en temps réel sur Raspberry Pi, avec gestion directe de la caméra ainsi que l'amélioration des performances sur la classe "aucun oiseau" pour éviter les faux positifs. 


## Améliorations futures possibles 
Intégration d'un module audio pour la reconnaissance des chants d'oiseaux 





## ⚙️ Setup Instructions

### 🖥️ On your development PC:

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/bird-recognition-pi.git
   cd bird-recognition-pi
   ```

2. Create a virtual environment and install requirements:
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   ```

3. Train the model (or skip if already trained):
   ```bash
   python train.py
   ```

4. Export the trained model:
   ```bash
   torch.save(model.state_dict(), 'model.pth')
   ```



## Structure du projet 
```
bird-recognition-pi/
├── dataset/
│   └── (bird images per species)
├── model/
│   ├── cnn.py
│   └── model.pth
├── train.py
├── recognize.py
├── utils.py
├── requirements.txt
└── README.md
```

---

## Exemple 


## 📜 License

MIT License — see `LICENSE` file for details.
```
