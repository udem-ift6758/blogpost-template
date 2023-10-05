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

# display_game(initial_game_list)

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

# info
info_text = widgets.HTML()
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
# image

plt.rcParams["figure.figsize"] = [7.00, 3.50]
plt.rcParams["figure.autolayout"] = True
im = plt.imread("C:\\Users\\joels\\PycharmProjects\\Project-DS-IFT6758-A23\\figures\\nhl_rink.png")

fig, axes = plt.subplots()

im = axes.imshow(im, extent=[-100, 100, -42.5, 42,5])

location, = axes.plot(-36, -28, marker='o', color="blue")

plt.title('Josh Morrissey Wrist Shot saved by Frederik Andersen')

plt.show()
```
![alt text](https://github.com/julien-hebert-doutreloux/Project-Blog-IFT6758-A23/blob/main/public/outil_deboguage_screenshot.jpg)

## 3. Nettoyer les données

Les données qui nous sont envoyées par l'API de la LNH sont vastes. Pour un projet de sciences des données comme le nôtre, il faut premièrement décider de quelles données nous jugeons utiles. Par ailleurs, il nous faut aussi choisir une structure pour nos données. Dans notre cas, nous avons extrait les données pertinentes de chaque JSON et nous avons sauvegardés ces derniers sous la forme d'un fichier CSV. Par ailleurs, nous avons créer une classe GAME qui nous permet de convertir n'importe quel JSON brute en un dataframe pandas contenant les données utiles.

## 4. Visualisations simples
## 5. Visualisations avancées
