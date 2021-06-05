---
layout: post
title: 'EDA Monthly project'
author: ppamppamman
categories: [TIL, Dev, EDA]
image: https://i.pinimg.com/736x/3a/3f/35/3a3f35f05e98ae2ed31d1fadbbb6ee42.jpg
image_desc: '데이터를 처음 다뤄보는 빰빰맨이다.'
---

디지몬의 붐은 온다... 라고 작년부터 말하고 다녔다.  
디지몬 데이터는 캐글에 없었다. 이번에는 포켓몬이지만, 디지몬의 붐은 올 것이다...

## Intro

사람들의 일자리를 만들어 기업체를 운영한다는 것은 아주 어려운 일이다. 코로나로 일자리 파이가 쪼그라드는 이 시점에는 사업의 흥망에 대해 낙관적인 전망을 기대하기는 어렵다.  
어려운 상황임에도 불구하고, 수십년간 많은 사람들의 일자리를 챙겨온 대단한 인물이 있다.

<img src="https://user-images.githubusercontent.com/13144573/108943091-88ad4f80-769b-11eb-8221-5ea21562d0c3.png" width="150px" />

**비주기**, 상록시티의 관장이자, 1996년 2월 27일부터 20년 넘게 로켓단을 이끌어 오고 있는 인물이다.

그를 서술 하고 있는 작중 행적에 따르면, 그의 목표는 돈과 포켓몬에 의한 세계정복이다. 때문에 강한 포켓몬들을 모으는 것이 주로 하는 일이라 밝히고 있다. 정리하자면, 돈과 세계정복을 위해 로켓단이라는 전국적인 기업을 만들어 일자리를 창출해왔고, 피고용인들로 하여금 강한 포켓몬을 수집하고 있는 것이었다.

이런 목표로 움직이고 있는 로켓단인데, 왜 우리들의 기억에 남아있는 로켓단은 약한 모습인 걸까?

<img src="https://2.gall-img.com/tdgall/files/attach/images/82/292/886/059/cbc7fb47b05db0edbbb48fee0e3ca596.jpg" width="70%"/>

<center> <s>날아간다옹</s> </center>

## Purpose of executing EDA

- 대전 승리 확률과 승리 확률에 미치는 유의미한 변수를 확인하여 로켓단이 승리하기 위한 큰그림을 제시한다.

```python
import numpy as np
import pandas as pd
import matplotlib as plt
import seaborn as sns

%matplotlib inline
```

```python
import random

random.seed(1)
pokemon = pd.read_csv("./input/pokemon.csv")
# pokemon = pokemon.rename(index=str, columns={"#": "Number"})
combat = pd.read_csv("./input/combats.csv")

pokemon.head() #
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
      <th>#</th>
      <th>Name</th>
      <th>Type 1</th>
      <th>Type 2</th>
      <th>HP</th>
      <th>Attack</th>
      <th>Defense</th>
      <th>Sp. Atk</th>
      <th>Sp. Def</th>
      <th>Speed</th>
      <th>Generation</th>
      <th>Legendary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Bulbasaur</td>
      <td>Grass</td>
      <td>Poison</td>
      <td>45</td>
      <td>49</td>
      <td>49</td>
      <td>65</td>
      <td>65</td>
      <td>45</td>
      <td>1</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Ivysaur</td>
      <td>Grass</td>
      <td>Poison</td>
      <td>60</td>
      <td>62</td>
      <td>63</td>
      <td>80</td>
      <td>80</td>
      <td>60</td>
      <td>1</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Venusaur</td>
      <td>Grass</td>
      <td>Poison</td>
      <td>80</td>
      <td>82</td>
      <td>83</td>
      <td>100</td>
      <td>100</td>
      <td>80</td>
      <td>1</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Mega Venusaur</td>
      <td>Grass</td>
      <td>Poison</td>
      <td>80</td>
      <td>100</td>
      <td>123</td>
      <td>122</td>
      <td>120</td>
      <td>80</td>
      <td>1</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Charmander</td>
      <td>Fire</td>
      <td>NaN</td>
      <td>39</td>
      <td>52</td>
      <td>43</td>
      <td>60</td>
      <td>50</td>
      <td>65</td>
      <td>1</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>

```python
combat.head()
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
      <th>First_pokemon</th>
      <th>Second_pokemon</th>
      <th>Winner</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>266</td>
      <td>298</td>
      <td>298</td>
    </tr>
    <tr>
      <th>1</th>
      <td>702</td>
      <td>701</td>
      <td>701</td>
    </tr>
    <tr>
      <th>2</th>
      <td>191</td>
      <td>668</td>
      <td>668</td>
    </tr>
    <tr>
      <th>3</th>
      <td>237</td>
      <td>683</td>
      <td>683</td>
    </tr>
    <tr>
      <th>4</th>
      <td>151</td>
      <td>231</td>
      <td>151</td>
    </tr>
  </tbody>
</table>
</div>

```python
print("pokemon: " + str(pokemon.shape))
print("combat: " + str(combat.shape))
```

    pokemon: (800, 12)
    combat: (50000, 3)

### 결측치 확인

```python
combat.isnull().sum()
```

    First_pokemon     0
    Second_pokemon    0
    Winner            0
    dtype: int64

```python
pokemon.isnull().sum()
```

    #               0
    Name            1
    Type 1          0
    Type 2        386
    HP              0
    Attack          0
    Defense         0
    Sp. Atk         0
    Sp. Def         0
    Speed           0
    Generation      0
    Legendary       0
    dtype: int64

- 이 값에서 Type 2에 Null값이 많은 이유는 포켓몬의 세대가 바뀌어오면서 (1세대 레드, 그린, 블루 -> 2세대 골드, 실버 -> 3세대 루비, 사파이어 ...) 메인 타입 말고 보조 타입을 정의하는 포켓몬이 생겨났기 때문이다.
- 이름 없는 포켓몬도 하나 있다.
- 결측지를 제거하면 분석에 대해 명확한 진행이 되지 않을 것 같아 결측치에 대한 처리를 삭제가 아닌 수정으로 진행하도록 한다.

```python
print("==== Name = NaN, 누구냐 넌 ==== ")

if pokemon[pokemon['Name'].isnull()].size != 0:
    who = pokemon[pokemon['Name'].isnull()]
    print(who, '\n')

    print('앞 포켓몬 : ', pokemon['Name'][int(who.index[0])-1])
    print('뒤 포켓몬 : ', pokemon['Name'][int(who.index[0])+1])
    pokemon[pokemon['#'] ==  63]
else:
    print("이름이 비어있는 포켓몬이 없습니다.")
    # who.Name = "Primeape"

```

    ==== Name = NaN, 누구냐 넌 ====
         # Name    Type 1 Type 2  HP  Attack  Defense  Sp. Atk  Sp. Def  Speed  \
    62  63  NaN  Fighting    NaN  65     105       60       60       70     95

        Generation  Legendary
    62           1      False

    앞 포켓몬 :  Mankey
    뒤 포켓몬 :  Growlithe

- 도감에 따르면, 망키와 가디 사이에는 성원숭이 있다. Type 1이 Fighting인 것을 보면 맞다.

```python
pokemon.loc[(pokemon['#'] ==  63), 'Name'] = "Primeape"
pokemon[pokemon['#'] ==  63]
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
      <th>#</th>
      <th>Name</th>
      <th>Type 1</th>
      <th>Type 2</th>
      <th>HP</th>
      <th>Attack</th>
      <th>Defense</th>
      <th>Sp. Atk</th>
      <th>Sp. Def</th>
      <th>Speed</th>
      <th>Generation</th>
      <th>Legendary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>62</th>
      <td>63</td>
      <td>Primeape</td>
      <td>Fighting</td>
      <td>NaN</td>
      <td>65</td>
      <td>105</td>
      <td>60</td>
      <td>60</td>
      <td>70</td>
      <td>95</td>
      <td>1</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>

```python
totalWins = combat.Winner.value_counts() # 전체 승리
countWins = combat.groupby('Winner').count()

countFirst = combat.groupby('First_pokemon').count()
countSecond = combat.groupby('Second_pokemon').count()

print("Count by first winner shape: " + str(countFirst.shape))
print("Count by second winner shape: " + str(countSecond.shape))
print("Total Wins shape : " + str(totalWins.shape))
```

    Count by first winner shape: (784, 2)
    Count by second winner shape: (784, 2)
    Total Wins shape : (783,)

- 확인했을 때 이긴 포켓몬 수와 토탈 승리 수가 다르다. 한 포켓몬이 승리를 한번도 하지 못한 것 같다.

```python
find_losing_pokemon= np.setdiff1d(countFirst.index.values, countWins.index.values)-1
losing_pokemon = pokemon.iloc[find_losing_pokemon[0],]
print(losing_pokemon)
```

    #                 231
    Name          Shuckle
    Type 1            Bug
    Type 2           Rock
    HP                 20
    Attack             10
    Defense           230
    Sp. Atk            10
    Sp. Def           230
    Speed               5
    Generation          2
    Legendary       False
    Name: 230, dtype: object

<img src="https://cdn.bulbagarden.net/upload/thumb/c/c7/213Shuckle.png/1200px-213Shuckle.png" width="150px" />
<center> Shuckle 단단지 </center>
<center> 정답은 단단지였습니다! </center>

포켓몬GO에 이르러서는 단단지가 GO로켓단의 주축이 되었다. 알고보니 로켓단이 약하다는 것에 대한 고증이 이루어진 이벤트였던 것!  
https://pokemongolive.com/post/gofestweeklychallenge-teamgorocket/?hl=ko

```python
numberOfWins = countWins.sort_index()
numberOfWins['Total Fights'] = countFirst.Winner + countSecond.Winner
numberOfWins['Win Percentage']= numberOfWins.First_pokemon/numberOfWins['Total Fights']
# print(numberOfWins)

results2 = pd.merge(pokemon, numberOfWins, right_index = True, left_on='#')
results3 = pd.merge(pokemon, numberOfWins, left_on='#', right_index = True, how='left')
```

- 패배 기준

```python
results3[np.isfinite(results3['Win Percentage'])].sort_values(by = ['Win Percentage']).head(10)
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
      <th>#</th>
      <th>Name</th>
      <th>Type 1</th>
      <th>Type 2</th>
      <th>HP</th>
      <th>Attack</th>
      <th>Defense</th>
      <th>Sp. Atk</th>
      <th>Sp. Def</th>
      <th>Speed</th>
      <th>Generation</th>
      <th>Legendary</th>
      <th>First_pokemon</th>
      <th>Second_pokemon</th>
      <th>Total Fights</th>
      <th>Win Percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>289</th>
      <td>290</td>
      <td>Silcoon</td>
      <td>Bug</td>
      <td>NaN</td>
      <td>50</td>
      <td>35</td>
      <td>55</td>
      <td>25</td>
      <td>25</td>
      <td>15</td>
      <td>3</td>
      <td>False</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>138.0</td>
      <td>0.021739</td>
    </tr>
    <tr>
      <th>189</th>
      <td>190</td>
      <td>Togepi</td>
      <td>Fairy</td>
      <td>NaN</td>
      <td>35</td>
      <td>20</td>
      <td>65</td>
      <td>40</td>
      <td>65</td>
      <td>20</td>
      <td>2</td>
      <td>False</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>122.0</td>
      <td>0.024590</td>
    </tr>
    <tr>
      <th>638</th>
      <td>639</td>
      <td>Solosis</td>
      <td>Psychic</td>
      <td>NaN</td>
      <td>45</td>
      <td>30</td>
      <td>40</td>
      <td>105</td>
      <td>50</td>
      <td>20</td>
      <td>5</td>
      <td>False</td>
      <td>4.0</td>
      <td>4.0</td>
      <td>129.0</td>
      <td>0.031008</td>
    </tr>
    <tr>
      <th>236</th>
      <td>237</td>
      <td>Slugma</td>
      <td>Fire</td>
      <td>NaN</td>
      <td>40</td>
      <td>40</td>
      <td>40</td>
      <td>70</td>
      <td>40</td>
      <td>20</td>
      <td>2</td>
      <td>False</td>
      <td>4.0</td>
      <td>4.0</td>
      <td>123.0</td>
      <td>0.032520</td>
    </tr>
    <tr>
      <th>576</th>
      <td>577</td>
      <td>Munna</td>
      <td>Psychic</td>
      <td>NaN</td>
      <td>76</td>
      <td>25</td>
      <td>45</td>
      <td>67</td>
      <td>55</td>
      <td>24</td>
      <td>5</td>
      <td>False</td>
      <td>5.0</td>
      <td>5.0</td>
      <td>128.0</td>
      <td>0.039062</td>
    </tr>
    <tr>
      <th>188</th>
      <td>189</td>
      <td>Igglybuff</td>
      <td>Normal</td>
      <td>Fairy</td>
      <td>90</td>
      <td>30</td>
      <td>15</td>
      <td>40</td>
      <td>20</td>
      <td>15</td>
      <td>2</td>
      <td>False</td>
      <td>5.0</td>
      <td>5.0</td>
      <td>115.0</td>
      <td>0.043478</td>
    </tr>
    <tr>
      <th>394</th>
      <td>395</td>
      <td>Wynaut</td>
      <td>Psychic</td>
      <td>NaN</td>
      <td>95</td>
      <td>23</td>
      <td>48</td>
      <td>23</td>
      <td>48</td>
      <td>23</td>
      <td>3</td>
      <td>False</td>
      <td>6.0</td>
      <td>6.0</td>
      <td>130.0</td>
      <td>0.046154</td>
    </tr>
    <tr>
      <th>209</th>
      <td>210</td>
      <td>Wooper</td>
      <td>Water</td>
      <td>Ground</td>
      <td>55</td>
      <td>45</td>
      <td>45</td>
      <td>25</td>
      <td>25</td>
      <td>15</td>
      <td>2</td>
      <td>False</td>
      <td>6.0</td>
      <td>6.0</td>
      <td>125.0</td>
      <td>0.048000</td>
    </tr>
    <tr>
      <th>291</th>
      <td>292</td>
      <td>Cascoon</td>
      <td>Bug</td>
      <td>NaN</td>
      <td>50</td>
      <td>35</td>
      <td>55</td>
      <td>25</td>
      <td>25</td>
      <td>15</td>
      <td>3</td>
      <td>False</td>
      <td>7.0</td>
      <td>7.0</td>
      <td>133.0</td>
      <td>0.052632</td>
    </tr>
    <tr>
      <th>752</th>
      <td>753</td>
      <td>Spritzee</td>
      <td>Fairy</td>
      <td>NaN</td>
      <td>78</td>
      <td>52</td>
      <td>60</td>
      <td>63</td>
      <td>65</td>
      <td>23</td>
      <td>6</td>
      <td>False</td>
      <td>8.0</td>
      <td>8.0</td>
      <td>133.0</td>
      <td>0.060150</td>
    </tr>
  </tbody>
</table>
</div>

- 승리 기준

```python
results3[np.isfinite(results3['Win Percentage'])].sort_values(by = ['Win Percentage'], ascending = False ).head(10)
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
      <th>#</th>
      <th>Name</th>
      <th>Type 1</th>
      <th>Type 2</th>
      <th>HP</th>
      <th>Attack</th>
      <th>Defense</th>
      <th>Sp. Atk</th>
      <th>Sp. Def</th>
      <th>Speed</th>
      <th>Generation</th>
      <th>Legendary</th>
      <th>First_pokemon</th>
      <th>Second_pokemon</th>
      <th>Total Fights</th>
      <th>Win Percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>154</th>
      <td>155</td>
      <td>Mega Aerodactyl</td>
      <td>Rock</td>
      <td>Flying</td>
      <td>80</td>
      <td>135</td>
      <td>85</td>
      <td>70</td>
      <td>95</td>
      <td>150</td>
      <td>1</td>
      <td>False</td>
      <td>127.0</td>
      <td>127.0</td>
      <td>129.0</td>
      <td>0.984496</td>
    </tr>
    <tr>
      <th>512</th>
      <td>513</td>
      <td>Weavile</td>
      <td>Dark</td>
      <td>Ice</td>
      <td>70</td>
      <td>120</td>
      <td>65</td>
      <td>45</td>
      <td>85</td>
      <td>125</td>
      <td>4</td>
      <td>False</td>
      <td>116.0</td>
      <td>116.0</td>
      <td>119.0</td>
      <td>0.974790</td>
    </tr>
    <tr>
      <th>703</th>
      <td>704</td>
      <td>Tornadus Therian Forme</td>
      <td>Flying</td>
      <td>NaN</td>
      <td>79</td>
      <td>100</td>
      <td>80</td>
      <td>110</td>
      <td>90</td>
      <td>121</td>
      <td>5</td>
      <td>True</td>
      <td>121.0</td>
      <td>121.0</td>
      <td>125.0</td>
      <td>0.968000</td>
    </tr>
    <tr>
      <th>19</th>
      <td>20</td>
      <td>Mega Beedrill</td>
      <td>Bug</td>
      <td>Poison</td>
      <td>65</td>
      <td>150</td>
      <td>40</td>
      <td>15</td>
      <td>80</td>
      <td>145</td>
      <td>1</td>
      <td>False</td>
      <td>115.0</td>
      <td>115.0</td>
      <td>119.0</td>
      <td>0.966387</td>
    </tr>
    <tr>
      <th>153</th>
      <td>154</td>
      <td>Aerodactyl</td>
      <td>Rock</td>
      <td>Flying</td>
      <td>80</td>
      <td>105</td>
      <td>65</td>
      <td>60</td>
      <td>75</td>
      <td>130</td>
      <td>1</td>
      <td>False</td>
      <td>136.0</td>
      <td>136.0</td>
      <td>141.0</td>
      <td>0.964539</td>
    </tr>
    <tr>
      <th>476</th>
      <td>477</td>
      <td>Mega Lopunny</td>
      <td>Normal</td>
      <td>Fighting</td>
      <td>65</td>
      <td>136</td>
      <td>94</td>
      <td>54</td>
      <td>96</td>
      <td>135</td>
      <td>4</td>
      <td>False</td>
      <td>124.0</td>
      <td>124.0</td>
      <td>129.0</td>
      <td>0.961240</td>
    </tr>
    <tr>
      <th>726</th>
      <td>727</td>
      <td>Greninja</td>
      <td>Water</td>
      <td>Dark</td>
      <td>72</td>
      <td>95</td>
      <td>67</td>
      <td>103</td>
      <td>71</td>
      <td>122</td>
      <td>6</td>
      <td>False</td>
      <td>122.0</td>
      <td>122.0</td>
      <td>127.0</td>
      <td>0.960630</td>
    </tr>
    <tr>
      <th>716</th>
      <td>717</td>
      <td>Meloetta Pirouette Forme</td>
      <td>Normal</td>
      <td>Fighting</td>
      <td>100</td>
      <td>128</td>
      <td>90</td>
      <td>77</td>
      <td>77</td>
      <td>128</td>
      <td>5</td>
      <td>False</td>
      <td>118.0</td>
      <td>118.0</td>
      <td>123.0</td>
      <td>0.959350</td>
    </tr>
    <tr>
      <th>164</th>
      <td>165</td>
      <td>Mega Mewtwo Y</td>
      <td>Psychic</td>
      <td>NaN</td>
      <td>106</td>
      <td>150</td>
      <td>70</td>
      <td>194</td>
      <td>120</td>
      <td>140</td>
      <td>1</td>
      <td>True</td>
      <td>119.0</td>
      <td>119.0</td>
      <td>125.0</td>
      <td>0.952000</td>
    </tr>
    <tr>
      <th>349</th>
      <td>350</td>
      <td>Mega Sharpedo</td>
      <td>Water</td>
      <td>Dark</td>
      <td>70</td>
      <td>140</td>
      <td>70</td>
      <td>110</td>
      <td>65</td>
      <td>105</td>
      <td>3</td>
      <td>False</td>
      <td>114.0</td>
      <td>114.0</td>
      <td>120.0</td>
      <td>0.950000</td>
    </tr>
  </tbody>
</table>
</div>

### 시각화

```python
import matplotlib.pyplot as plt
sns.set_color_codes("pastel")
ax = sns.countplot(x="Type 1", hue="Legendary", data=results3)
plt.xticks(rotation= 90)
plt.xlabel('Type 1')
plt.ylabel('Total ')
plt.title("Type 1 Pokemons")
```

    Text(0.5, 1.0, 'Type 1 Pokemons')

![png](output_23_1.png)

```python
ax = sns.countplot(x="Type 2", hue="Legendary", data=results3)
plt.xticks(rotation= 90)
plt.xlabel('Type 2')
plt.ylabel('Total ')
plt.title("Type 2 Pokemon")
```

    Text(0.5, 1.0, 'Type 2 Pokemon')

![png](output_24_1.png)

- 타입별 승률

```python
results3.groupby('Type 1').agg({"Win Percentage": "mean"}).sort_values(by = "Win Percentage")
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
      <th>Win Percentage</th>
    </tr>
    <tr>
      <th>Type 1</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Fairy</th>
      <td>0.329300</td>
    </tr>
    <tr>
      <th>Rock</th>
      <td>0.404852</td>
    </tr>
    <tr>
      <th>Steel</th>
      <td>0.424529</td>
    </tr>
    <tr>
      <th>Poison</th>
      <td>0.433262</td>
    </tr>
    <tr>
      <th>Bug</th>
      <td>0.439006</td>
    </tr>
    <tr>
      <th>Ice</th>
      <td>0.439604</td>
    </tr>
    <tr>
      <th>Grass</th>
      <td>0.440364</td>
    </tr>
    <tr>
      <th>Water</th>
      <td>0.469357</td>
    </tr>
    <tr>
      <th>Fighting</th>
      <td>0.475616</td>
    </tr>
    <tr>
      <th>Ghost</th>
      <td>0.484027</td>
    </tr>
    <tr>
      <th>Normal</th>
      <td>0.535578</td>
    </tr>
    <tr>
      <th>Ground</th>
      <td>0.541526</td>
    </tr>
    <tr>
      <th>Psychic</th>
      <td>0.545747</td>
    </tr>
    <tr>
      <th>Fire</th>
      <td>0.579215</td>
    </tr>
    <tr>
      <th>Dark</th>
      <td>0.629726</td>
    </tr>
    <tr>
      <th>Electric</th>
      <td>0.632861</td>
    </tr>
    <tr>
      <th>Dragon</th>
      <td>0.633587</td>
    </tr>
    <tr>
      <th>Flying</th>
      <td>0.765061</td>
    </tr>
  </tbody>
</table>
</div>

이 정도로 정리가 된다.  
특이한 점은 요정 타입이 압도적으로 약하고, 비행속성이 압도적으로 강하다는 점이다.  
약한 속성 : 요정, 돌, 강철, 독, 벌레  
강한 속성 : 비행, 용, 전기, 어둠, 불

- 산점도

```python
col = ['Type 1','HP', 'Attack', 'Defense', 'Sp. Atk', 'Sp. Def', 'Speed', 'Win Percentage']
sns.pairplot(results3.loc[:,col].dropna())
```

    <seaborn.axisgrid.PairGrid at 0x7f90c373fa90>

![png](output_29_1.png)

```python
def correlation_matrix(df):
    from matplotlib import pyplot as plt
    from matplotlib import cm as cm

    fig = plt.figure()
    ax1 = fig.add_subplot(111)
    cmap = cm.get_cmap('jet', 50)
    cax = ax1.imshow(df.corr(), interpolation="nearest", cmap=cmap)
    ax1.grid(True)
    plt.title('relations')
    labels=['Type 1','HP', 'Attack', 'Defense', 'Sp. Atk', 'Sp. Def', 'Speed', 'Win %']
    ax1.set_xticklabels(labels,fontsize=7)
    ax1.set_yticklabels(labels,fontsize=7)
    fig.colorbar(cax, ticks=[0.00,.05,.10,.15,.20,.25,.30,.35,.40,.45,.50,.55,.60,.65,.70,.75,.8,.85,.90,.95,1])
    plt.show()

correlation_matrix(results3.loc[:,col])
# 레퍼런스 https://datascience.stackexchange.com/questions/10459/calculation-and-visualization-of-correlation-matrix-with-pandas
```

    /Users/jaeuk/Documents/ppamppamman/programmers-KDT/programmers_kdt_II/kdt-env/lib/python3.6/site-packages/ipykernel_launcher.py:12: UserWarning: FixedFormatter should only be used together with FixedLocator
      if sys.path[0] == '':
    /Users/jaeuk/Documents/ppamppamman/programmers-KDT/programmers_kdt_II/kdt-env/lib/python3.6/site-packages/ipykernel_launcher.py:13: UserWarning: FixedFormatter should only be used together with FixedLocator
      del sys.path[0]

![png](output_30_1.png)

이 중 특징이 두드러지게 나타나는 speed에 대해서 확인해보면 다음과 같다

```python
sns.regplot(x="Speed", y="Win Percentage", data=results3, logistic=True).set_title("Speed vs Win Percentage")
sns.lmplot(x="Speed", y="Win Percentage", data=results3, hue = 'Type 1',  logistic=True)
```

    <seaborn.axisgrid.FacetGrid at 0x7f90c1c8c780>

![png](output_32_1.png)

![png](output_32_2.png)

속도 능력치의 경우 승률에 있어 거의 비례하는 모습을 보여주고 있다.

## 결론

- 물, 노말, 벌레, 풀 타입이 가장 흔한 메인타입이고 비행, 땅, 독이 가장 흔한 서브타입이다.
  - 로켓단이 지속가능한 경영을 하기 위해서는 해당 타입들에 대해 상성인 타입의 포켓몬을 수집해야 한다.
- 가장 많이 이기는 포켓몬 타입은 비행, 용, 전기, 어둠이다. 가장 적게 이기는 포켓몬 유형은 요정, 바위, 강철, 독, 벌레이다.
  - 로켓단이 세계적인 기업이 되기 위해서는 특히 비행타입을 많이 모아야하며 차순위로 용타입 전기타입 어둠타입에 관심을 가져야 한다.
- 승률과 가장 높은 상관관계를 가지는 속성은 속도이며 그다음으로는 공격력이라 할 수 있다. 반대로 방어력, 체력은 승률에 비교적 적은 상관관계를 가지는 것을 볼 수 있다.
  - 로켓단은 빠르고 강력한 공격력을 지닌 포켓몬을 모아야 한다. 단단지 같은 방어력과 체력이 높은 포켓몬은 피해야 할 것이다.

<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSeKAz6rmJjrHBfacuv0Dwm3h_ohjaZFY5rjw&usqp=CAU" width="50%"/>

볼 때는 아무 생각이 없었지만, 데이터를 보고 나니 **전기 타입**에게 당하고 있는 이 장면은 철저하게 고증된 장면인 것이었다.

### 끝
