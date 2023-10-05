---
layout: post
title: Milestone 1
---

## 1. Acquisition de données
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
{% include outil_deboguage_screenshot.jpg %}
## 3. Nettoyer les données
## 4. Visualisations simples
## 5. Visualisations avancées
