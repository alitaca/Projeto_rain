# Projeto - Will it rain tomorrow?

O Projeto Will it rain tomorrow? foi desenvolvido com o objetivo de gerar uma previsão de chuvas a partir de uma base de dados climáticos da Austrália.

## Começando

A partir de dados de vento e chuva em uma determinada data, é necessário fazer uma previsão se haverá chuva na data seguinte.

## Testando

O objetivo é obter uma previsão que obtenha


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

### Análise de dados



### Escolha do modelo



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