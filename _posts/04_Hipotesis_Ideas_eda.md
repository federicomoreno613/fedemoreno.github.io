---
title: "NLP"
date: 2020-01-28
tags: [data wrangling, data science, messy data]
header:
  image: 
excerpt: "Data Wrangling, Data Science, Messy Data"
mathjax: "true"
---

### Hipotesis e ideas


por Federico Moreno
federicomoreno613@gmail.com


```python
import pandas as pd
import numpy as np
import re
import ast
import matplotlib as plt
import seaborn as sns
import datetime
import seaborn as sns
import matplotlib.pyplot as plt

```


```python
#Como estructura de la carpeta de trabajo separe las resoluciones en pdf del excel inicial
path_herramientas = "..\\data\\original\\Resoluciones\\"
path_original = "..\\data\\original\\"
```


```python
#Para aquellos que deseen chequear donde esta su carpeta de trabajo.
import os
os.getcwd()
```




    'c:\\Users\\fede_\\Desktop\\ODSL\\nlp-juzgado\\notebooks'




```python
set_unificado = pd.read_excel(path_original + "set_de_datos_unificado_juzgado.xlsx")
set_unificado.shape
```




    (3835, 69)




```python
violencia = set_unificado[set_unificado["VIOLENCIA_DE_GENERO"] == "si"]
violencia.shape

```




    (891, 69)




```python

```


```python
violencia['FECHA_DEL_HECHO'] = violencia['FECHA_DEL_HECHO'].fillna(0)

violencia = violencia[violencia["FECHA_DEL_HECHO"] != 0]
```


```python
violencia.shape
```




    (666, 69)




```python
violencia["FECHA_DEL_HECHO"]
```




    1        25_3_16
    2        24_7_14
    4        5_01_14
    9        8_07_16
    20       21_8_14
              ...   
    3341     13_1_20
    3342     13_1_20
    3343     13_1_20
    3347    28_10_17
    3355    03_11_19
    Name: FECHA_DEL_HECHO, Length: 666, dtype: object




```python
violencia["FECHA_DEL_HECHO"] = violencia.FECHA_DEL_HECHO.replace('_','/', regex=True)

```


```python
paleta = sns.color_palette("YlOrRd", 10)
```


```python

violencia['FECHA_DEL_HECHO'] = pd.to_datetime(violencia['FECHA_DEL_HECHO'] ,errors ='coerce')
violencia['año'] = violencia['FECHA_DEL_HECHO'].dt.year
violencia['mes'] = violencia['FECHA_DEL_HECHO'].dt.month
```


```python
#paleta = sns.color_palette("YlOrRd", 10)

paleta = sns.color_palette("coolwarm", 7)

pd.crosstab(index=violencia["año"], columns="count") 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>col_0</th>
      <th>count</th>
    </tr>
    <tr>
      <th>año</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2012</th>
      <td>2</td>
    </tr>
    <tr>
      <th>2013</th>
      <td>17</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>23</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>35</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>80</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>144</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>189</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>143</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>33</td>
    </tr>
  </tbody>
</table>
</div>




```python

sns.set(palette="rocket",color_codes=False,rc={'figure.figsize':(6,6)})
ax = sns.countplot(violencia['año'],palette="rocket")


ax.set_title('Cantidad de casos anuales')
plt.show()
```


![svg](04_Hipotesis_Ideas_files/04_Hipotesis_Ideas_14_0.svg)



```python
sns.set(style="darkgrid", color_codes=True,rc={'figure.figsize':(6,6)})
ax = sns.countplot(violencia['mes'], palette = "rocket")

ax.set_title('Cantidad de casos mensuales')
plt.show()
```


![svg](04_Hipotesis_Ideas_files/04_Hipotesis_Ideas_15_0.svg)



```python
dataframe = violencia[["ART_INFRINGIDO", "CONDUCTA","V_FISICA","V_PSIC","V_ECON","V_SEX","V_SOC","V_AMB","V_SIMB"]]
dataframe = dataframe.replace("no", 0)
dataframe = dataframe.replace("si", 1)
```


```python
articulo = dataframe[["CONDUCTA","ART_INFRINGIDO"]]

```


```python
import matplotlib.pyplot as plt

ax = sns.countplot(data = articulo, x = 'CONDUCTA',
              order = articulo['CONDUCTA'].value_counts().index)
ax.set_xticklabels(ax.get_xticklabels(),fontsize=7, rotation=40, ha="right")

ax.set_title('Cantidad de casos por conducta')
plt.show()
```


![svg](04_Hipotesis_Ideas_files/04_Hipotesis_Ideas_18_0.svg)



```python
grafico_data = pd.crosstab(index=articulo["CONDUCTA"], 
                     columns="count")                  
grafico_data.sort_values(by = ['count'], ascending = [False])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>col_0</th>
      <th>count</th>
    </tr>
    <tr>
      <th>CONDUCTA</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>amenazas</th>
      <td>339</td>
    </tr>
    <tr>
      <th>hostigamiento</th>
      <td>99</td>
    </tr>
    <tr>
      <th>lesiones</th>
      <td>61</td>
    </tr>
    <tr>
      <th>desobediencia_a_la_autoridad</th>
      <td>32</td>
    </tr>
    <tr>
      <th>Hostigamiento</th>
      <td>27</td>
    </tr>
    <tr>
      <th>acoso_sexual_callejero</th>
      <td>17</td>
    </tr>
    <tr>
      <th>tenencia_de_arma</th>
      <td>15</td>
    </tr>
    <tr>
      <th>daños</th>
      <td>14</td>
    </tr>
    <tr>
      <th>ingreso_a_domicilio_sin_autorizacion</th>
      <td>12</td>
    </tr>
    <tr>
      <th>usurpacion</th>
      <td>9</td>
    </tr>
    <tr>
      <th>impedimento_de_contacto_de_menor_con_padre_no_conviviente</th>
      <td>7</td>
    </tr>
    <tr>
      <th>abuso_sexual</th>
      <td>7</td>
    </tr>
    <tr>
      <th>portacion_de_arma</th>
      <td>6</td>
    </tr>
    <tr>
      <th>abuso_de_armas</th>
      <td>4</td>
    </tr>
    <tr>
      <th>atentado_contra_la_autoridad</th>
      <td>4</td>
    </tr>
    <tr>
      <th>maltrato</th>
      <td>3</td>
    </tr>
    <tr>
      <th>privacion_ilegitima_de_la_libertad</th>
      <td>3</td>
    </tr>
    <tr>
      <th>robo</th>
      <td>3</td>
    </tr>
    <tr>
      <th>exhibiciones_obscenas</th>
      <td>1</td>
    </tr>
    <tr>
      <th>incendios_y_otros_estragos</th>
      <td>1</td>
    </tr>
    <tr>
      <th>abuso_de_autoridad_e_incumplimiento_deberes_funcionario_publico</th>
      <td>1</td>
    </tr>
    <tr>
      <th>hurto</th>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
ax = sns.countplot(data = articulo, x = 'ART_INFRINGIDO',
              order = articulo['ART_INFRINGIDO'].value_counts().index, palette ='coolwarm')
ax.set_xticklabels(ax.get_xticklabels(),fontsize=7, rotation=40, ha="right")

plt.figure(figsize = (20,20))

ax.set_title('Cantidad de casos por ART infringido')

plt.show()
```


![svg](04_Hipotesis_Ideas_files/04_Hipotesis_Ideas_20_0.svg)



    <Figure size 1440x1440 with 0 Axes>



```python
grafico2 = pd.crosstab(index=articulo["ART_INFRINGIDO"], 
                     columns="count")                  
grafico2.sort_values(by = ['count'], ascending = [False])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>col_0</th>
      <th>count</th>
    </tr>
    <tr>
      <th>ART_INFRINGIDO</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>149bis</th>
      <td>326</td>
    </tr>
    <tr>
      <th>52</th>
      <td>111</td>
    </tr>
    <tr>
      <th>92</th>
      <td>47</td>
    </tr>
    <tr>
      <th>239</th>
      <td>32</td>
    </tr>
    <tr>
      <th>189bis</th>
      <td>21</td>
    </tr>
    <tr>
      <th>53_inc5</th>
      <td>17</td>
    </tr>
    <tr>
      <th>183</th>
      <td>14</td>
    </tr>
    <tr>
      <th>89</th>
      <td>13</td>
    </tr>
    <tr>
      <th>150</th>
      <td>12</td>
    </tr>
    <tr>
      <th>67bis</th>
      <td>12</td>
    </tr>
    <tr>
      <th>149bis_parr2</th>
      <td>12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>7</td>
    </tr>
    <tr>
      <th>119</th>
      <td>7</td>
    </tr>
    <tr>
      <th>181_inc1</th>
      <td>6</td>
    </tr>
    <tr>
      <th>65bis</th>
      <td>5</td>
    </tr>
    <tr>
      <th>104</th>
      <td>4</td>
    </tr>
    <tr>
      <th>238</th>
      <td>4</td>
    </tr>
    <tr>
      <th>141</th>
      <td>3</td>
    </tr>
    <tr>
      <th>181_inc3</th>
      <td>3</td>
    </tr>
    <tr>
      <th>164</th>
      <td>3</td>
    </tr>
    <tr>
      <th>248</th>
      <td>1</td>
    </tr>
    <tr>
      <th>129</th>
      <td>1</td>
    </tr>
    <tr>
      <th>53_inc3</th>
      <td>1</td>
    </tr>
    <tr>
      <th>186</th>
      <td>1</td>
    </tr>
    <tr>
      <th>162</th>
      <td>1</td>
    </tr>
    <tr>
      <th>90</th>
      <td>1</td>
    </tr>
    <tr>
      <th>149ter_inc2b)</th>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
group_by_cond_art =  articulo.groupby(['CONDUCTA','ART_INFRINGIDO'])
correlacion = pd.DataFrame(group_by_cond_art.size().reset_index(name = "Group_Count"))
correlacion
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CONDUCTA</th>
      <th>ART_INFRINGIDO</th>
      <th>Group_Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Hostigamiento</td>
      <td>52</td>
      <td>25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Hostigamiento</td>
      <td>53_inc5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>abuso_de_armas</td>
      <td>104</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>abuso_de_autoridad_e_incumplimiento_deberes_fu...</td>
      <td>248</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>abuso_sexual</td>
      <td>119</td>
      <td>7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>acoso_sexual_callejero</td>
      <td>65bis</td>
      <td>5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>acoso_sexual_callejero</td>
      <td>67bis</td>
      <td>12</td>
    </tr>
    <tr>
      <th>7</th>
      <td>amenazas</td>
      <td>149bis</td>
      <td>326</td>
    </tr>
    <tr>
      <th>8</th>
      <td>amenazas</td>
      <td>149bis_parr2</td>
      <td>12</td>
    </tr>
    <tr>
      <th>9</th>
      <td>amenazas</td>
      <td>149ter_inc2b)</td>
      <td>1</td>
    </tr>
    <tr>
      <th>10</th>
      <td>atentado_contra_la_autoridad</td>
      <td>238</td>
      <td>4</td>
    </tr>
    <tr>
      <th>11</th>
      <td>daños</td>
      <td>183</td>
      <td>14</td>
    </tr>
    <tr>
      <th>12</th>
      <td>desobediencia_a_la_autoridad</td>
      <td>239</td>
      <td>32</td>
    </tr>
    <tr>
      <th>13</th>
      <td>exhibiciones_obscenas</td>
      <td>129</td>
      <td>1</td>
    </tr>
    <tr>
      <th>14</th>
      <td>hostigamiento</td>
      <td>52</td>
      <td>86</td>
    </tr>
    <tr>
      <th>15</th>
      <td>hostigamiento</td>
      <td>53_inc3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>16</th>
      <td>hostigamiento</td>
      <td>53_inc5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>17</th>
      <td>hurto</td>
      <td>162</td>
      <td>1</td>
    </tr>
    <tr>
      <th>18</th>
      <td>impedimento_de_contacto_de_menor_con_padre_no_...</td>
      <td>1</td>
      <td>7</td>
    </tr>
    <tr>
      <th>19</th>
      <td>incendios_y_otros_estragos</td>
      <td>186</td>
      <td>1</td>
    </tr>
    <tr>
      <th>20</th>
      <td>ingreso_a_domicilio_sin_autorizacion</td>
      <td>150</td>
      <td>12</td>
    </tr>
    <tr>
      <th>21</th>
      <td>lesiones</td>
      <td>89</td>
      <td>13</td>
    </tr>
    <tr>
      <th>22</th>
      <td>lesiones</td>
      <td>90</td>
      <td>1</td>
    </tr>
    <tr>
      <th>23</th>
      <td>lesiones</td>
      <td>92</td>
      <td>47</td>
    </tr>
    <tr>
      <th>24</th>
      <td>maltrato</td>
      <td>53_inc5</td>
      <td>3</td>
    </tr>
    <tr>
      <th>25</th>
      <td>portacion_de_arma</td>
      <td>189bis</td>
      <td>6</td>
    </tr>
    <tr>
      <th>26</th>
      <td>privacion_ilegitima_de_la_libertad</td>
      <td>141</td>
      <td>3</td>
    </tr>
    <tr>
      <th>27</th>
      <td>robo</td>
      <td>164</td>
      <td>3</td>
    </tr>
    <tr>
      <th>28</th>
      <td>tenencia_de_arma</td>
      <td>189bis</td>
      <td>15</td>
    </tr>
    <tr>
      <th>29</th>
      <td>usurpacion</td>
      <td>181_inc1</td>
      <td>6</td>
    </tr>
    <tr>
      <th>30</th>
      <td>usurpacion</td>
      <td>181_inc3</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
dataframe.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ART_INFRINGIDO</th>
      <th>CONDUCTA</th>
      <th>V_FISICA</th>
      <th>V_PSIC</th>
      <th>V_ECON</th>
      <th>V_SEX</th>
      <th>V_SOC</th>
      <th>V_AMB</th>
      <th>V_SIMB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>149bis</td>
      <td>amenazas</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>52</td>
      <td>hostigamiento</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>149bis</td>
      <td>amenazas</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>149bis</td>
      <td>amenazas</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>20</th>
      <td>149bis</td>
      <td>amenazas</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
tipos_v = dataframe.loc[:, dataframe.columns != 'CONDUCTA']
#Remplazos nulos y s/d por 0
tipos_v = tipos_v.fillna(0)
tipos_v = tipos_v.replace("s/d",0)

```


```python
#Creo una tabla de contingencia que luego utilizaré en el heatmap
tabla = tipos_v.groupby(['ART_INFRINGIDO'], as_index=True).sum()
tabla = tabla.div(tabla.sum(1), 0).assign(Total=lambda tabla: tabla.sum(axis=1))
#.mul(100).round(2)
#tabla


```


```python
tabla2 = tabla.transpose()

plt.figure(figsize = (20,20))
sns.heatmap(tabla2,annot = True,square = True,   fmt='.0%', cbar = False)

plt.title('% de violencia por Articulo Infringido', fontsize = 16) 
```




    Text(0.5, 1.0, '% de violencia por Articulo Infringido')




![svg](04_Hipotesis_Ideas_files/04_Hipotesis_Ideas_26_1.svg)



```python
tipos_coduct = dataframe.loc[:, dataframe.columns != 'ART_INFRINGIDO']
tipos_coduct.isnull().sum(axis=0)
tipos_coduct = tipos_coduct.fillna(0)
tipos_coduct = tipos_coduct.replace("s/d",0)
```


```python
tabla_conducta = tipos_coduct.groupby(['CONDUCTA'], as_index=True).sum()
tabla_conducta = tabla_conducta.div(tabla_conducta.sum(1), 0).assign(Total=lambda tabla: tabla.sum(axis=1))
tabla_conducta_t = tabla_conducta.transpose()


plt.figure(figsize = (20,20))
htmap = sns.heatmap(tabla_conducta_t,annot = True,square = True,   fmt='.0%', cbar = False, cmap="BuPu")

#htmap.set_xticklabels(htmap.get_xticklabels(), rotation=45, horizontalalignment='right')

htmap.set_yticklabels(htmap.get_yticklabels(), rotation=45, horizontalalignment='right')

plt.title('% de violencia por conducta', fontsize = 16) 

plt.xticks(
    rotation=45, 
    horizontalalignment='right',
     
)

htmap
```




    <matplotlib.axes._subplots.AxesSubplot at 0x131b0141848>




![svg](04_Hipotesis_Ideas_files/04_Hipotesis_Ideas_28_1.svg)



```python

```
