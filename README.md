# Projeto - Will it rain tomorrow?

O Projeto Will it rain tomorrow? foi desenvolvido com o objetivo de gerar uma previsão de chuvas a partir de uma base de dados climáticos da Austrália.

## Começando

A partir de dados de vento e chuva em uma determinada data, é necessário fazer uma previsão se haverá chuva na data seguinte.

## Testando

O objetivo é obter uma previsão que obtenha o menor erro calculado com o Precision Score, considerando que o modelo irá atender um usuário final que precisa saber se irá chover amanhã para poder decidir se ele vai para o trabalho de bicicleta amanhã.


### Pré-requisitos

* Navegador Web
* Jupyter Notebook

## Desenvolvimento

### Conhecendo os dados

A base de dados a ser utilizada para a criação do modelo é composta de:

* 1 csv com dados de chuvas
* 8 csvs com dados de vento

Os dados são descritos conforme a tabela a seguir:

Variable | Description
---------|------------
Date   |  The date of observation
Location   |  The common name of the location of the weather station
MinTemp   |  The minimum temperature in degrees celsius
MaxTemp   |  The maximum temperature in degrees celsius
Rainfall   |  The amount of rainfall recorded for the day in mm
Evaporation   |  The so-called Class A pan evaporation (mm) in the 24 hours to 9am
Sunshine   |  The number of hours of bright sunshine in the day.
WindGustDir   |  The direction of the strongest wind gust in the 24 hours to midnight
WindGustSpeed   |  The speed (km/h) of the strongest wind gust in the 24 hours to midnight
WindDir9am   |  Direction of the wind at 9am
WindDir3pm   |  Direction of the wind at 3pm
WindSpeed9am   |  Wind speed (km/hr) averaged over 10 minutes prior to 9am
WindSpeed3pm   |  Wind speed (km/hr) averaged over 10 minutes prior to 3pm
Humidity9am   |  Humidity (percent) at 9am
Humidity3pm   |  Humidity (percent) at 3pm
Pressure9am   |  Atmospheric pressure (hpa) reduced to mean sea level at 9am
Pressure3pm   |  Atmospheric pressure (hpa) reduced to mean sea level at 3pm
Cloud9am   |  Fraction of sky obscured by cloud at 9am. This is measured in "oktas", which are a unit of eigths. It records how many eigths of the sky are obscured by cloud. A 0 measure indicates completely clear sky whilst an 8 indicates that it is completely overcast.
Cloud3pm | Fraction of sky obscured by cloud (in "oktas": eighths) at 3pm. See Cload9am for a description of the values
Temp9am |  Temperature (degrees C) at 9am
Temp3pm |  Temperature (degrees C) at 3pm
Precipitation9am |  The amount of rain in mm prior to 9am
Precipitation3pm |  The amount of rain in mm prior to 3pm
AmountOfRain |  The amount of rain in mm
Temp |  Temperature (degrees C)
Humidity |  Humidity (percent)
RainToday |  Boolean: 1 if precipitation (mm) in the 24 hours to 9am exceeds 1mm, otherwise 0
RainTomorrow |  The target variable. Did it rain tomorrow?

### Tratamento

Para poder utilizar um modelo de aprendizado de máquina, é necessário transformar as colunas 'raintoday' e 'raintomorrow' em colunas numéricas, e em formato booleano. Foi acrescentada uma coluna de 'season', referente à estação do ano na data da observação. Foi acrescentada também uma coluna de estados, para agrupamento das localidades, com o auxílio de uma tabela armazenada em 'au_state.csv', cosntruída especificamente para este dataset. No entanto, o fator de localização não foi considerado no presente modelo.

As colunas de direção de vento foram categorizadas em dados numéricos de forma hierárquica, e os valores nulos foram preenchidos com o valor da moda de cada coluna. Foram utilizadas as funções OrdinalEncoder e SimpleImputer da biblioteca sklearn. Embora os dados de direção de vento não tenham nenhuma relação de hierarquia, espera-se que, uma vez que o modelo não está considerando o dado de localidade, as colunas de direção de vento tenham pouca contribuição no modelo de previsão, até que, eventualmente possam ser desconsiderados no modelo.

As demais colunas foram preenchidas com a mediana (utilizando a função SimpleImputer), e então normalizadas por meio da função StandardScaler, da biblioteca sklearn.

### Análise exploratória de dados

Por meio da análise de dados, concluiu-se que:

* os dados estão desbalanceados, uma vez que há muito mais registros de dias que não choveram no dia posterior do que registros com chuva no dia seguinte
* a coluna 'temp' não apresentava dados consistentes com os dados das outras colunas de temperatura, e a maioria dos valores desta coluna estavam acima dos respectivos valores de 'maxtemp'. Por isso, a coluna foi desconsiderada
* as colunas 'modelo-vigente' e 'amountOfRain' representavam vazamento de dados, porque são dados referentes à coluna target ('raintomorrow')
* os dados de temperatura e humidade são representados por mais de duas colunas cada uma, e seus dados têm correlação. Por conta disso, em uma eventual redução de dimensionalidade, essas colunas podem ser escolhidas
* as colunas 'sunshine', 'evaporation', 'cloud9am' e 'cloud3pm' possuem muitos valores nulos. Por isso, quando da redução de dimensionalidade, essas colunas podem desconsideradas na construção do modelo.

### Escolha do modelo

Para fazer os testes de modelo, os dados foram repartidos em dados de teste e dados de treino, utilizando-se a função train_test_split da biblioteca sklearn. Os parâmetros escolhidos foram de 20% dos dados para teste, com uma separação estruturada de acordo com os dados do target (coluna 'raintomorrow'), por causa do desbalanceamento dos dados.

Em um primeiro momento, para a escolha do modelo foram desconsideradas as colunas 'date', 'amountOfRain', 'modelo_vigente', 'temp', 'temp9am', 'temp3pm', 'humidity', e 'location'. Os dados de 'location' e 'date' foram substituídos pelas colunas 'season' e 'state'. As demais, foram excluídas por conta das conclusões da análise de dados. 

Os modelos testados foram:
* LGBM (biblioteca lightgbm)
* regressão logística (sklearn)
* árvore de decisão (sklearn)
* Bagging (sklearn)
* KNN (sklearn)
* RandomForest (sklearn)

Os modelos foram avaliados por meio do ROC_AUC_SCORE.

O modelo escolhido nesta etapa, por apresentar o maior score foi o modelo LGBM.

Após os testes de modelo, foi feita uma análise da curva de aprendizado (função learning_curve da biblioteca sklearn) para avaliação do tamanho dos dados de treino. Entretanto, as conclusões da análise não foram implementadas, pois a análise deverá ser refeita uma vez que deverão ser utilizados modelos separados de acordo com os dados de estação do ano ou localidade.

### Feature Selection

Da biblioteca mlxtend, foi utilizada a função SequentialFeatureSelector, com o método de Backward Stepwise Selection para a escolha das colunas a serem utilizadas no modelo.

As colunas escolhidas pelo algoritmo de Feature Selection foram: 'maxtemp', 'rainfall', 'sunshine', 'humidity3pm', 'pressure9am', 'pressure3pm', 'cloud3pm', 'wind_gustspeed', 'wind_speed3pm', 'wind_dir9am', 'wind_dir3pm' e 'state'

Porém, as colunas 'sunshine' e 'cloud3pm' serão desconsideradas apesar desse resultado, pois apresentam muitos dados nulos.   

### Outros testes

Utilizando a função RandomizedSearchCV da biblioteca sklearn, foi feito um teste de tunagem de parâmetros do modelo LGBM. Porém, o modelo com os novos parâmetros apresentou um ROC_AUC_SCORE ligeiramente menor. Ou seja, não houve melhoria do modelo com a alteração de parâmetros.

O segundo teste feito para a melhoria do modelo foi definir um modelo LGBM para cada estação do ano. O primeiro teste foi feito para os dados de verão, porém, o resultado também não apresentou melhora significativa no ROC_AUC_SCORE.

## Resultados

O modelo final tem as seguintes características:

* Modelo LGBM:
** Parâmetros: max_depth=4, n_estimators=70, colsample_bytree=.7
* Colunas:
** 'maxtemp', 'rainfall', 'humidity3pm', 'pressure9am', 'pressure3pm', 'wind_gustspeed', 'wind_speed3pm', 'wind_dir9am', 'wind_dir3pm', 'state'
* Cross-validation score:
** AVG Precision Score: 0.74

## Construído com

As ferramentas utilizadas para criar o projeto

* [Python](https://www.python.org/) - Usado no desenvolvimento do projeto
* [Jupyter Notebook](https://jupyter.org/) - Usado no desenvolvimento do projeto

### Bibliotecas

* [pandas](https://pandas.pydata.org/)
* [numpy](https://numpy.org/)
* [matplotlib](https://matplotlib.org/)
* [seaborn](https://seaborn.pydata.org/)
* [scipy](https://www.scipy.org/)
* [sklearn](https://scikit-learn.org/)
* [lightbgm](https://lightgbm.readthedocs.io/)

### Data Sources

Os dados de entrada para o projeto estão no repositório.

```
data_rain/rain_data_aus.csv
data_rain/wind_table_01.csv
data_rain/wind_table_02.csv
data_rain/wind_table_03.csv
data_rain/wind_table_04.csv
data_rain/wind_table_05.csv
data_rain/wind_table_06.csv
data_rain/wind_table_07.csv
data_rain/wind_table_08.csv
data_rain/au_state.csv
```


## Link do Repositório Online

* [Repositório](https://github.com/alitaca/Projeto_rain.git) - Link para o repositório de dados.

## Autores

* **Aline** - *Trabalho Inicial* - [alitaca](https://github.com/alitaca)


## Licença

Este projeto está sob licença - veja o arquivo LICENSE.md para detalhes.

## Expressões de gratidão

* Aos professores da Ironhck, em especial a Rai e o Adriano :heart:
* Aos queridos colegas Guilherme e Eduardo que ajudaram com bons insights :star: