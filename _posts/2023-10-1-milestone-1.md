---
layout: post
title: Milestone 1
---

## 1. Acquisition de données

La LNH rend disponible de nombreuses données par le biais d'une API publique dont le end-point est le suivant : https://statsapi.web.nhl.com/api/v1/game/[GAME_ID]/feed/live/. Chaque requête envoyé à ce end-point renvoie un fichier json contenant des renseignements sur la partie en question.

Les parties ont un identifiant (GAME_ID) qui suit une structure comme suit: 2017020008

<ul>
  <li>Les 4 premiers chiffres désignent la saison (2017 dans le cas de notre exemple)</li>
  <li>Les deux chiffres suivants font référence au type de partie (01 = présaison, 02 = saison régulière, 03 = séries éliminatoires, 04 = match des étoiles)</li>
  <li>Les 4 derniers chiffres désignent le numéro de la partie (0008 dans le cas de notre exemple)</li>
</ul>

Avec ces renseignements en main, il nous est donc possible d'effectuer une série de requêtes au serveur de la LNH afin de télécharger les données d'une saison en particulier. Par exemple, si nous désirons télécharger les données pour la saison régulière de 2017, il suffit d'aller chercher la partie 2017020001 (2017 pour l'année, 02 pour spécifier que nous voulons les parties de la saison régulière et 0001 pour spécifier le numéro de la première partie de la saison). Ensuite, nous pouvons envoyer une requête pour la partie 2017020002, puis 2017020003, 2017020004, etc.

## 2. Outil de débogage interactif

Nous avons créé un outil de débogage interactif. Son principe est assez simple. L'utilisateur peut sélectionner les parties de la saison régulière ou les parties des séries élimiatoires. Ensuite, il peut choisir la saison de son choix (2017, 2018, 2019, 2020 ou 2021). Ces deux options permettent d'ajuster le contenu d'un menu déroulant contenant les parties (menu déroulant GAME_ID). Par exemple, en choisissant "Playoffs" et "2018", le menu déroulant GAME_ID ne contiendra que les parties des séries éliminatoires de 2018. Ensuite, il nous suffit de choisir une partie qui nous intéresse. Un <i>int slider</i> (EVENT ID) nous permet de parcourir tous les événements de la partie sélectionnée. Un petit tableau HTML nous indique les équipes et le score pour chaque partie. Par ailleurs, un tableau interactif dessiné avec la librairie matplotlib nous permet de visualiser la location sur la patinoire et la description de chaque événement.

```python
%matplotlib notebook
from ipywidgets import *
import matplotlib.pyplot as plt
from matplotlib import image
import numpy as np
import csv
import ast
import warnings
warnings.filterwarnings("ignore", category=DeprecationWarning)

dir_path = os.path.join('.','..','data\processed\csv')
files = os.listdir(dir_path)

game_dic = {
  "Regular Season": "02",
  "Playoffs": "03",
}

# Helper functions

def display_game(var):
    season = B.value
    game_type = A.value
    game_list = []
    
    for file in files:
        temp_name = file.replace('.csv', '')
        
        if temp_name[:4] == str(season) and temp_name[4:-4] == game_dic[str(game_type)]:
            game_list.append(temp_name)
      
    C.options = game_list

    return game_list

def update_int_slider(game_list):
    
    raw_data_path= os.path.join('.','..','data','raw','json')
    file = C.value+".json"
    file_path = os.path.join(raw_data_path, file)

    if os.path.isfile(file_path):
        game = Game(path_or_url = file_path)
        df = game.clean
        D.max = df.index.values.tolist()[-1]
        
        info_text.value="""
        <table>
          <tr>
            <th style="padding:10px"></th>
            <th style="padding:10px">Home</th>
            <th style="padding:10px">Away</th>
          </tr>
          <tr>
            <td style="padding-right:10px">Teams</td>
            <td>{}</td>
            <td>{}</td>
          </tr>
          <tr>
            <td style="padding-right:10px">Final Score</td>
            <td>{}</td>
            <td>{}</td>
          </tr>
        </table>
        """.format(game.teams['home']['triCode'],game.teams['away']['triCode'],
                   game.final_score['home'], game.final_score['away'])        
    else:
        print(file_path + " est un fichier non existant")
    return

def update_graph(game_list):

    raw_data_path= os.path.join('.','..','data','raw','json')
    file = C.value+".json"
    file_path = os.path.join(raw_data_path, file)

    if os.path.isfile(file_path):
        game = Game(path_or_url = file_path)
        df = game.clean
        value = df.iloc[D.value]
        
        # update puck location
        location.set_ydata(value['y'])
        location.set_xdata(value['x'])
        
        event_description = str(value['event_description'])
              
        plt.title(event_description)
        
    else:
        print(file_path + " est un fichier non existant")
    return


# Widgets

info_text = widgets.HTML()

A = Dropdown(options=["Regular Season", "Playoffs"], value='Regular Season', description='Game Type')
A.observe(display_game)

B = Dropdown(options=["2017", "2018", "2019", "2020", "2021"], value="2017", description='Season')
B.observe(display_game)

C = Dropdown(options=[], description='Game ID')
C.observe(update_int_slider, names='value')

# max initialisé à 1, mais mis à jour la ligne suivante
D = IntSlider(value=0, min=0, max=1, step=1, description='Event ID')
# Pour avoir les valeurs de la partie affichée (exemple: 67 events)
initial_game_list = display_game(0)
D.observe(update_graph, names='value')

A.default_value = 'Regular Season'
B.default_value = "2017"
C.default_value = "2017020001"
D.default_value = "0" 

defaulting_widgets = [A,B,C,D]

default_value_button = Button(description='Default Values')

def set_default(button):
    for widget in defaulting_widgets:
        widget.value = widget.default_value

default_value_button.on_click(set_default)

display(A,B,C,D, default_value_button)

# infos sur les parties (table html)

display(info_text)

info_text.value="""
<table>
  <tr>
    <th style="padding:10px"></th>
    <th style="padding:10px">Home</th>
    <th style="padding:10px">Away</th>
  </tr>
  <tr>
    <td style="padding-right:10px">Teams</td>
    <td>WPG</td>
    <td>TOR</td>
  </tr>
  <tr>
    <td style="padding-right:10px">Final Score</td>
    <td>2</td>
    <td>7</td>
  </tr>
</table>
"""

# image (graphique matplotlib)

plt.rcParams["figure.figsize"] = [7.00, 3.50]
plt.rcParams["figure.autolayout"] = True
im = plt.imread("C:\\Users\\joels\\PycharmProjects\\Project-DS-IFT6758-A23\\figures\\nhl_rink.png")

fig, axes = plt.subplots()

im = axes.imshow(im, extent=[-100, 100, -42.5, 42,5])

location, = axes.plot(-36, -28, marker='o', color="blue")

plt.title('Josh Morrissey Wrist Shot saved by Frederik Andersen')

plt.show()
```
![alt text](https://raw.githubusercontent.com/julien-hebert-doutreloux/Project-Blog-IFT6758-A23/main/public/outil_deboguage_screenshot.jpg)

## 3. Nettoyer les données

Les données qui nous sont envoyées par l'API de la LNH sont vastes. Pour un projet de sciences des données comme le nôtre, il faut premièrement décider de quelles données nous jugeons utiles. Par ailleurs, il nous faut aussi choisir une structure pour nos données. Dans notre cas, nous avons extrait les données pertinentes de chaque JSON et nous avons sauvegardés ces derniers sous la forme d'un fichier CSV. Par ailleurs, nous avons créer une classe GAME qui nous permet de convertir n'importe quel JSON brute en un dataframe pandas contenant les données utiles.

Voici une ligne de notre dataframe à titre d'exemple:
![alt text](https://raw.githubusercontent.com/julien-hebert-doutreloux/Project-Blog-IFT6758-A23/main/public/df_exemple.jpg)

Nous pouvons remarquer que le champ de « force » (égal, avantage numérique ou en désavantage numérique) n'est indiqué que pour les buts et non pas pour les tirs. De plus, il ne précise pas le nombre de joueurs sur la glace (5 contre 4, ou 5 contre 3, etc.). Une façon simple d'inclure cette information serait de regarder les pénalités données pour chaque partie. Par exemple, si les Canadiens de Montréal recoivent une pénalité de 2 minute à la 7e minute de jeu de la 1ère période, puis une autre pénalité de 2 minutes à la 8e minute de jeu de la 1ère période, alors nous savons qu'entre la 8e minute et la 9e minute de jeu de la 1ère période les Canadiens sont en désavantage numérique à 5 contre 3.

Par ailleurs, voici trois caractéristiques supplémentaires que nous proposons de créer à partir des données disponibles:
<ol>
  <li>
Nous avons téléchargé les saisons 2017 à 2021. Pour chaque partie, nous avons les statistiques des joueurs participants. Une donnée intéressant serait de compiler les statistiques individuelles pour chaque joueur sur l'ensemble des saisons 2017 à 2021 afin de créer un score global pour chaque joueur. Ce score serait particulièrement utile pour faire des prédictions avec nos données ou tout simplement pour rendre nos données plus intéressantes. Par exemple, si un excellent joueur prend une pénalité, l'événement est beaucoup plus grave que si le même événement arrive à un joueur de dernier ordre. Par ailleurs, nous pourrions connaitre la force des gardiens de buts de chaque équipe. Nous pourrions aussi évaluer la force d'une équipe à partir de la force de ses joueurs.
  </li>
  <li>
Nous pourrions reprendre la logique évoquée au point précédent, mais cette fois-ci en l'appliquant aux équipes. Nous pourrions évaluer la tendance de plusieurs variables sur n nombres de parties précédentes. Cette tendance pourrait inclure le nombre de buts marqués par partie, le ratio victoires/défaites, etc. Nous pourrions donc entre autres savoir si une équipe est lancée dans une série fulgurantes de victoires ou si elle traverse un désert marqué de défaites. Nous pourrions aussi savoir si une équipe en particulier prend beaucoup de pénalités. Nous pourrions également recréer le classement des équipes selon leurs nombres de victoires et de défaites.
  </li>
  <li>
Nous pourrions reprendre la logique évoquée aux deux points précédents, mais cette fois-ci en l'appliquant à l'ensemble de la LNH. Par exemple, aucune équipe canadienne n'a gagné la coupe Stanley depuis 1993. Connaître ce genre de tendance globale éclaire notre compréhension des dynamiques au sein de la LNH. Nous pourrions aussi évaluer les ratios de victoire/défaite entre les différentes conférences et les différentes divisions. Nous pourrions évaluer les chances d'une équipe de gagner la coupe Stanley selon sa position dans le classement. 
  </li>
</ol>

## 4. Visualisations simples
Test de graphique interactif exporté avec plotly.
{% raw %}{% include test.html %}{% endraw %} 
## 5. Visualisations avancées
