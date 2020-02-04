<img src="https://placeimg.com/1000/350/nature">

# Script em Python para adequação de nomenclatura botânica a partir do banco de dados da Flora do Brasil 2020

## Motivação

Para quem trabalha com vegetação natural, o uso correto da nomenclatura botânica é essencial, sendo um dos fundamentos para o uso sustentável dos recursos naturais, principalmente em uma país megadiverso como o nosso. 

Neste contexto,  o projeto [Flora do Brasil](http://floradobrasil.jbrj.gov.br/reflora/listaBrasil/PrincipalUC/PrincipalUC.do#CondicaoTaxonCP), do Jardim Botânico do Rio de Janeiro, fruto da colaboração de diversos taxonomistas do Brasil, é uma iniciativa de grande valor, pois reúne informações atuais e corretas das espécies vegetais de nosso megadiverso país. 

No site da **Flora do Brasil**, a conferência de nomenclaturas botânicas, como, por exemplo, os nomes aceitos e sinonímias, pode ser realizada de forma bastante intuitiva, a partir da consulta de um formulário online. Porém, quando trabalhamos com listagens grandes, provinientes de **levantamentos florísticos, fitossociológicos e inventários florestais**, este trabalho manual se torna pouco produtivo.

Desta forma, por ser uma demanda frequetemente relacionado às minhas atividades profissionais, resolvi escrever um script e função em linguagem de programação em Python, para automatizar esse processo. 


## Por que Python?

Pela grande comunidade de usuários ativios e por ter um potencial incrível na área de Ciência de Dados, principalmente na aquisição e manipulação de dados não estruturados.

Aqui utilizei a versão do Python disponbilizada pela distribuição [Anaconda](https://www.anaconda.com/), que já vem com várias bibliotecas importantes, como o [Pandas](https://pandas.pydata.org/) e o [Numpy](https://numpy.org/). 

## Etapas

### 1) Carregando pacotes e módulos


```python
import pandas as pd
import difflib
import zipfile
```

###  2) Importação de arquivos

São necessários dois arquivos:
1. Base original da [Flora do Brasil](http://ipt.jbrj.gov.br/jbrj/resource?r=lista_especies_flora_brasil), disponível de forma pública, na seção de Download, com a descrição  ***Data as a DwC-A file***. Conforme informação do site, este arquivo é atualizado semanalmente. Fazer o download do arquivo na pasta do projeto.
2. Arquivo com relação das espécies a ser corrigida, no formato csv, que também deve estar dentro da pasta do projeto. Arquivo exemplo disponível [aqui](https://1drv.ms/u/s!AuA3UlSxIwNHg61fi4IEylwLuvnvog?e=7qCM8O)


```python
#Importação da base de dados da Flora do Brasil

with zipfile.ZipFile('dwca-lista_especies_flora_brasil-v393.222.zip') as zip:
            with zip.open('taxon.txt') as myZip:
                flora_df = pd.read_csv(myZip, sep='\t', low_memory=False)
                
my_species=pd.read_csv("my_species.csv")
```

### 3) Verificando importações e análises exploratórias

Os métodos <em>head</em> e <em>tail</em> permitem verificar as primeiras e últimas linhas dos arquivos importados, o que é útil para checar a ocorrência de algum erro na importação.


```python
flora_df.head()
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
      <th>id</th>
      <th>taxonID</th>
      <th>acceptedNameUsageID</th>
      <th>parentNameUsageID</th>
      <th>originalNameUsageID</th>
      <th>scientificName</th>
      <th>acceptedNameUsage</th>
      <th>parentNameUsage</th>
      <th>namePublishedIn</th>
      <th>namePublishedInYear</th>
      <th>...</th>
      <th>genus</th>
      <th>specificEpithet</th>
      <th>infraspecificEpithet</th>
      <th>taxonRank</th>
      <th>scientificNameAuthorship</th>
      <th>taxonomicStatus</th>
      <th>nomenclaturalStatus</th>
      <th>modified</th>
      <th>bibliographicCitation</th>
      <th>references</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>12</td>
      <td>12</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Agaricales</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2018-08-10 11:58:06.954</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
    </tr>
    <tr>
      <td>1</td>
      <td>13</td>
      <td>13</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Auriculariales</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2014-09-26 11:01:06.918</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
    </tr>
    <tr>
      <td>2</td>
      <td>14</td>
      <td>14</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Boletales E.-J.Gilbert</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>E.-J.Gilbert</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2019-12-10 16:45:09.658</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
    </tr>
    <tr>
      <td>3</td>
      <td>15</td>
      <td>15</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Geastrales</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2015-03-05 17:48:09.918</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
    </tr>
    <tr>
      <td>4</td>
      <td>16</td>
      <td>16</td>
      <td>NaN</td>
      <td>120180.0</td>
      <td>NaN</td>
      <td>Helotiales</td>
      <td>NaN</td>
      <td>Ascomycota Caval.-Sm.</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2014-03-17 08:17:41.765</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 26 columns</p>
</div>




```python
flora_df.tail()
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
      <th>id</th>
      <th>taxonID</th>
      <th>acceptedNameUsageID</th>
      <th>parentNameUsageID</th>
      <th>originalNameUsageID</th>
      <th>scientificName</th>
      <th>acceptedNameUsage</th>
      <th>parentNameUsage</th>
      <th>namePublishedIn</th>
      <th>namePublishedInYear</th>
      <th>...</th>
      <th>genus</th>
      <th>specificEpithet</th>
      <th>infraspecificEpithet</th>
      <th>taxonRank</th>
      <th>scientificNameAuthorship</th>
      <th>taxonomicStatus</th>
      <th>nomenclaturalStatus</th>
      <th>modified</th>
      <th>bibliographicCitation</th>
      <th>references</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>123498</td>
      <td>606941</td>
      <td>606941</td>
      <td>NaN</td>
      <td>113348.0</td>
      <td>NaN</td>
      <td>Fridericia trichoclada (DC.) Kaehler &amp; L.G. Lo...</td>
      <td>NaN</td>
      <td>Fridericia Mart.</td>
      <td>Taxon 68(4): 765 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>Fridericia</td>
      <td>trichoclada</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(DC.) Kaehler &amp; L.G. Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 16:01:03.511</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
    </tr>
    <tr>
      <td>123499</td>
      <td>606942</td>
      <td>606942</td>
      <td>NaN</td>
      <td>113938.0</td>
      <td>112896.0</td>
      <td>Tanaecium dichotomum (Jacq.) L.G.Lohmann</td>
      <td>NaN</td>
      <td>Tanaecium Sw. emend L.G.Lohmann</td>
      <td>Taxon 68(4): 65 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>Tanaecium</td>
      <td>dichotomum</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Jacq.) L.G.Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 14:42:18.643</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
    </tr>
    <tr>
      <td>123500</td>
      <td>606943</td>
      <td>606943</td>
      <td>NaN</td>
      <td>113938.0</td>
      <td>113777.0</td>
      <td>Tanaecium parviflorum (Mart. ex DC.) Kaehler &amp;...</td>
      <td>NaN</td>
      <td>Tanaecium Sw. emend L.G.Lohmann</td>
      <td>Taxon 68(4): 765 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>Tanaecium</td>
      <td>parviflorum</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Mart. ex DC.) Kaehler &amp; L.G.Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NaN</td>
      <td>2020-01-28 14:50:53.874</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
    </tr>
    <tr>
      <td>123501</td>
      <td>606944</td>
      <td>606944</td>
      <td>NaN</td>
      <td>113938.0</td>
      <td>113712.0</td>
      <td>Tanaecium paradoxum (Sandwith) Kaehler &amp; L.G.L...</td>
      <td>NaN</td>
      <td>Tanaecium Sw. emend L.G.Lohmann</td>
      <td>Taxon 68(4): 765 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>Tanaecium</td>
      <td>paradoxum</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Sandwith) Kaehler &amp; L.G.Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 14:58:07.396</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
    </tr>
    <tr>
      <td>123502</td>
      <td>606945</td>
      <td>606945</td>
      <td>NaN</td>
      <td>26542.0</td>
      <td>43353.0</td>
      <td>Didymopanax calvus (Cham.) Decne. &amp; Planch.</td>
      <td>NaN</td>
      <td>Didymopanax Decne. &amp; Planch.</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Didymopanax</td>
      <td>calvus</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Cham.) Decne. &amp; Planch.</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 17:04:58.733</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 26 columns</p>
</div>



Em particular, é possível observar que o arquivo importado do site da Flora do Brasil tem 123.503 linhas por 26 colunas


```python
flora_df.shape
```




    (123503, 26)



Para a lista de espécie didática, do [arquivo exemplo](https://1drv.ms/u/s!AuA3UlSxIwNHg61fi4IEylwLuvnvog?e=7qCM8O), temos alguns casos que serão considerados neste processo:
* No índice 0, Acca sellowina escrito de forma incorreta (Aca sloviana);
* No índice 7, uma espécie não indenticada como NI
* Nos índices 10 (<em>Sebastiania commersoniana</em>) e 13 (<em>Gochnatia polymorpha</em>), nomes não aceitos.


```python
my_species
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
      <th>species</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Aca sloviana</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Allophylus edulis</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Allophylus guaraniticus</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Annona rugulosa</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Zanthoxylum rhoifolium</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Banara tomentosa</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Blepharocalyx salicifolius</td>
    </tr>
    <tr>
      <td>7</td>
      <td>NI</td>
    </tr>
    <tr>
      <td>8</td>
      <td>Campomanesia xanthocarpa</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Casearia decandra</td>
    </tr>
    <tr>
      <td>10</td>
      <td>Sebastiania commersoniana</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Cedrela fissilis</td>
    </tr>
    <tr>
      <td>12</td>
      <td>Celtis iguanaea</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Gochnatia polymorpha</td>
    </tr>
    <tr>
      <td>14</td>
      <td>Citronella paniculata</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Maytenus boaria</td>
    </tr>
    <tr>
      <td>16</td>
      <td>Coutarea hexandra</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Cupania vernalis</td>
    </tr>
    <tr>
      <td>18</td>
      <td>Dalbergia frutescens</td>
    </tr>
  </tbody>
</table>
</div>



### 4) Removendo nomes dos autores botânicos das espécies

Na etapa de análise de dados, normalmente trabalha-se com os nomes científicos, sem a presença dos nomes das autoridades botânicas. Por isso, faz sentido removermos os autores dos nomes das espécies no arquivo da Flora do Brasil.


```python
# Primeiramente, criando um nova coluna scientificName1, a partir da remoção dos autores de scientificName
flora_df['scientificName1'] = flora_df.apply(lambda row : row['scientificName'].replace(str(row['scientificNameAuthorship']), ''), axis=1)

# Depois, removendo um "espaço" que ficou residual, no final de 'scientificName1
flora_df['scientificName1'] = flora_df['scientificName1'].str.rstrip()
flora_df
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
      <th>id</th>
      <th>taxonID</th>
      <th>acceptedNameUsageID</th>
      <th>parentNameUsageID</th>
      <th>originalNameUsageID</th>
      <th>scientificName</th>
      <th>acceptedNameUsage</th>
      <th>parentNameUsage</th>
      <th>namePublishedIn</th>
      <th>namePublishedInYear</th>
      <th>...</th>
      <th>specificEpithet</th>
      <th>infraspecificEpithet</th>
      <th>taxonRank</th>
      <th>scientificNameAuthorship</th>
      <th>taxonomicStatus</th>
      <th>nomenclaturalStatus</th>
      <th>modified</th>
      <th>bibliographicCitation</th>
      <th>references</th>
      <th>scientificName1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>12</td>
      <td>12</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Agaricales</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2018-08-10 11:58:06.954</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Agaricales</td>
    </tr>
    <tr>
      <td>1</td>
      <td>13</td>
      <td>13</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Auriculariales</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2014-09-26 11:01:06.918</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Auriculariales</td>
    </tr>
    <tr>
      <td>2</td>
      <td>14</td>
      <td>14</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Boletales E.-J.Gilbert</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>E.-J.Gilbert</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2019-12-10 16:45:09.658</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Boletales</td>
    </tr>
    <tr>
      <td>3</td>
      <td>15</td>
      <td>15</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Geastrales</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2015-03-05 17:48:09.918</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Geastrales</td>
    </tr>
    <tr>
      <td>4</td>
      <td>16</td>
      <td>16</td>
      <td>NaN</td>
      <td>120180.0</td>
      <td>NaN</td>
      <td>Helotiales</td>
      <td>NaN</td>
      <td>Ascomycota Caval.-Sm.</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2014-03-17 08:17:41.765</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Helotiales</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>123498</td>
      <td>606941</td>
      <td>606941</td>
      <td>NaN</td>
      <td>113348.0</td>
      <td>NaN</td>
      <td>Fridericia trichoclada (DC.) Kaehler &amp; L.G. Lo...</td>
      <td>NaN</td>
      <td>Fridericia Mart.</td>
      <td>Taxon 68(4): 765 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>trichoclada</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(DC.) Kaehler &amp; L.G. Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 16:01:03.511</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Fridericia trichoclada</td>
    </tr>
    <tr>
      <td>123499</td>
      <td>606942</td>
      <td>606942</td>
      <td>NaN</td>
      <td>113938.0</td>
      <td>112896.0</td>
      <td>Tanaecium dichotomum (Jacq.) L.G.Lohmann</td>
      <td>NaN</td>
      <td>Tanaecium Sw. emend L.G.Lohmann</td>
      <td>Taxon 68(4): 65 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>dichotomum</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Jacq.) L.G.Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 14:42:18.643</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Tanaecium dichotomum</td>
    </tr>
    <tr>
      <td>123500</td>
      <td>606943</td>
      <td>606943</td>
      <td>NaN</td>
      <td>113938.0</td>
      <td>113777.0</td>
      <td>Tanaecium parviflorum (Mart. ex DC.) Kaehler &amp;...</td>
      <td>NaN</td>
      <td>Tanaecium Sw. emend L.G.Lohmann</td>
      <td>Taxon 68(4): 765 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>parviflorum</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Mart. ex DC.) Kaehler &amp; L.G.Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NaN</td>
      <td>2020-01-28 14:50:53.874</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Tanaecium parviflorum</td>
    </tr>
    <tr>
      <td>123501</td>
      <td>606944</td>
      <td>606944</td>
      <td>NaN</td>
      <td>113938.0</td>
      <td>113712.0</td>
      <td>Tanaecium paradoxum (Sandwith) Kaehler &amp; L.G.L...</td>
      <td>NaN</td>
      <td>Tanaecium Sw. emend L.G.Lohmann</td>
      <td>Taxon 68(4): 765 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>paradoxum</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Sandwith) Kaehler &amp; L.G.Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 14:58:07.396</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Tanaecium paradoxum</td>
    </tr>
    <tr>
      <td>123502</td>
      <td>606945</td>
      <td>606945</td>
      <td>NaN</td>
      <td>26542.0</td>
      <td>43353.0</td>
      <td>Didymopanax calvus (Cham.) Decne. &amp; Planch.</td>
      <td>NaN</td>
      <td>Didymopanax Decne. &amp; Planch.</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>calvus</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Cham.) Decne. &amp; Planch.</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 17:04:58.733</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Didymopanax calvus</td>
    </tr>
  </tbody>
</table>
<p>123503 rows × 27 columns</p>
</div>



### 5) Correção de erros tipográficos na lista florística, a partir da coluna scientificName1

Para a correção dos erros tipográficos a partir da grafia correta encontrada na **Flora do Brasil**, utilizamos o módulo [diffilib](https://docs.python.org/2/library/difflib.html), que, conforme a documentação, basea-se no o algorítimo [Gestalt Pattern Matching](https://en.wikipedia.org/wiki/Gestalt_Pattern_Matching). 

Deve-se ter atenção, pois este passo é computacionalmente intensivo, de forma que pode ser bem lento no caso de listagens florísticas extensas. Além disso, é importante uma análise <em>a posteriori</em> dos resultados desta etapa, para verificar eventuais incoerências na correção dos nomes científicos.


```python
possibilities = flora_df['scientificName1']
species_typo_corrected = my_species['species'].apply(lambda row: difflib.get_close_matches(row, possibilities, n=1) [0] if difflib.get_close_matches(row, possibilities) else row)

```

Como resultado, podemos observar que Aca sloviana ficou devidamente corrigida para <em>Acca sellowiana</em>. 

Importante ressaltar que as espécies sem referência com similaridade no arquivo da Flora do Brasil, como é o caso de NI, a nomenclatura da mesma é mantida.


```python
species_typo_corrected.df = pd.DataFrame(species_typo_corrected)
species_typo_corrected.columns=['species']
species_typo_corrected.df
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
      <th>species</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Acca sellowiana</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Allophylus edulis</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Allophylus guaraniticus</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Annona rugulosa</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Zanthoxylum rhoifolium</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Banara tomentosa</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Blepharocalyx salicifolius</td>
    </tr>
    <tr>
      <td>7</td>
      <td>NI</td>
    </tr>
    <tr>
      <td>8</td>
      <td>Campomanesia xanthocarpa</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Casearia decandra</td>
    </tr>
    <tr>
      <td>10</td>
      <td>Sebastiania commersoniana</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Cedrela fissilis</td>
    </tr>
    <tr>
      <td>12</td>
      <td>Celtis iguanaea</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Gochnatia polymorpha</td>
    </tr>
    <tr>
      <td>14</td>
      <td>Citronella paniculata</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Maytenus boaria</td>
    </tr>
    <tr>
      <td>16</td>
      <td>Coutarea hexandra</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Cupania vernalis</td>
    </tr>
    <tr>
      <td>18</td>
      <td>Dalbergia frutescens</td>
    </tr>
  </tbody>
</table>
</div>



### 6) Correção de sinonías botânicas

Com os eventuais erros tipográficos corrigidos, agora podemos fazer a verificação da existência de nomes não aceitos e sinônimos, com a subsequente correção.


```python
species_syno_corrected=[]

for species in species_typo_corrected:
    
    line = flora_df[flora_df['scientificName1'] == species]
    accepted_name = line["acceptedNameUsage"]
    species_syno_corrected.append(list(accepted_name))
    species_syno_corrected_df=pd.DataFrame(species_syno_corrected) 
    final_table=pd.concat([species_typo_corrected, species_syno_corrected_df],  axis = 1) 
    final_table.columns =['Species', 'Correction']
    final_table['Correction'] = final_table.apply(lambda x: x['Species'] if pd.isnull(x['Correction']) else x['Correction'], axis=1)
    final_table['Correction'] = final_table['Correction'].str.extract(r'(^\w*\s*\w*)')
    
    
    

final_table
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
      <th>Species</th>
      <th>Correction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Acca sellowiana</td>
      <td>Acca sellowiana</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Allophylus edulis</td>
      <td>Allophylus edulis</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Allophylus guaraniticus</td>
      <td>Allophylus guaraniticus</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Annona rugulosa</td>
      <td>Annona rugulosa</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Zanthoxylum rhoifolium</td>
      <td>Zanthoxylum rhoifolium</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Banara tomentosa</td>
      <td>Banara tomentosa</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Blepharocalyx salicifolius</td>
      <td>Blepharocalyx salicifolius</td>
    </tr>
    <tr>
      <td>7</td>
      <td>NI</td>
      <td>NI</td>
    </tr>
    <tr>
      <td>8</td>
      <td>Campomanesia xanthocarpa</td>
      <td>Campomanesia xanthocarpa</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Casearia decandra</td>
      <td>Casearia decandra</td>
    </tr>
    <tr>
      <td>10</td>
      <td>Sebastiania commersoniana</td>
      <td>Gymnanthes klotzschiana</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Cedrela fissilis</td>
      <td>Cedrela fissilis</td>
    </tr>
    <tr>
      <td>12</td>
      <td>Celtis iguanaea</td>
      <td>Celtis iguanaea</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Gochnatia polymorpha</td>
      <td>Moquiniastrum polymorphum</td>
    </tr>
    <tr>
      <td>14</td>
      <td>Citronella paniculata</td>
      <td>Citronella paniculata</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Maytenus boaria</td>
      <td>Maytenus boaria</td>
    </tr>
    <tr>
      <td>16</td>
      <td>Coutarea hexandra</td>
      <td>Coutarea hexandra</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Cupania vernalis</td>
      <td>Cupania vernalis</td>
    </tr>
    <tr>
      <td>18</td>
      <td>Dalbergia frutescens</td>
      <td>Dalbergia frutescens</td>
    </tr>
  </tbody>
</table>
</div>



Observe que <em>Gochnatia polymorpha</em> foi atualizado para <em>Gymnanthes klotzschiana</em>. Por sua vez, <em>Gochnatia polymorpha</em> atualizado para <em>Moquiniastrum polymorphum</em>.

### 7) Como etapa final, a criação das funções "flora_clean", "flora_typo_correct"  e "flora_accepted_names"

A partir dos scripts acima, podemos definir funções específicas para serem aproveitas em projetos de manipulação e limpeza de dados botânicos


```python
# Funcao flora_clean, para limpeza dos dados originais, para posterior aplicação nas funções subsequentes

def flora_clean (flora_df):
    ''' flora_df = arquivo importado a partir do banco de dados da Flora do Brasil'''
    
    flora_df['scientificName1'] = flora_df.apply(lambda row : row['scientificName'].replace(str(row['scientificNameAuthorship']), ''), axis=1)
    flora_df['scientificName1'] = flora_df['scientificName1'].str.rstrip()
    return flora_df

# Função flora_typo_correct, para correcao de erros tipográficos

def flora_typo_correct (my_species,flora_clean):
   
    ''' 
    my_species = arquivo importado a partir da listagem florística a ser corrigida
    flora_clean = Arquivo da Flora do Brasil limpo, após a aplicação da função flora_clean
    
    '''
    
    possibilities = flora_clean['scientificName1']
    species_typo_corrected = my_species['species'].apply(lambda row: difflib.get_close_matches(row, possibilities, n=1) [0] if difflib.get_close_matches(row, possibilities) else row)
    species_typo_corrected.df = pd.DataFrame(species_typo_corrected)
    species_typo_corrected.columns=['species']
    species_typo_corrected.df
    return species_typo_corrected.df


def flora_accepted_names(my_species_clean, data_clean):
    
    ''' 
    my_clean_species = lista florística corrigida, a partir da aplicação de flora_typo_correct
    flora_clean = Arquivo da Flora do Brasil limpo, após a aplicação da função flora_clean
    
    '''
    
    species_syno_corrected=[]
    
    for species in my_species_clean['species']:
        
        line = data_clean[data_clean['scientificName1'] == species]
        accepted_name = line["acceptedNameUsage"]
        species_syno_corrected.append(list(accepted_name))
        species_syno_corrected_df=pd.DataFrame(species_syno_corrected) 
        final_table=pd.concat([my_species_clean, species_syno_corrected_df],  axis = 1) 
        final_table.columns =['Species', 'Correction']
        final_table['Correction'] = final_table.apply(lambda x: x['Species'] if pd.isnull(x['Correction']) else x['Correction'], axis=1)
        final_table['Correction'] = final_table['Correction'].str.extract(r'(^\w*\s*\w*)')
    return final_table

```

### 8) Aplicando as funções


```python
#Importação da base de dados da Flora do Brasil

with zipfile.ZipFile('dwca-lista_especies_flora_brasil-v393.222.zip') as zip:
            with zip.open('taxon.txt') as myZip:
                data_df = pd.read_csv(myZip, sep='\t', low_memory=False)
                
minha_lista=pd.read_csv("my_species.csv")
```


```python
#Aplicação da função flora_clean

data_clean=flora_clean(data_df)

data_clean
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
      <th>id</th>
      <th>taxonID</th>
      <th>acceptedNameUsageID</th>
      <th>parentNameUsageID</th>
      <th>originalNameUsageID</th>
      <th>scientificName</th>
      <th>acceptedNameUsage</th>
      <th>parentNameUsage</th>
      <th>namePublishedIn</th>
      <th>namePublishedInYear</th>
      <th>...</th>
      <th>specificEpithet</th>
      <th>infraspecificEpithet</th>
      <th>taxonRank</th>
      <th>scientificNameAuthorship</th>
      <th>taxonomicStatus</th>
      <th>nomenclaturalStatus</th>
      <th>modified</th>
      <th>bibliographicCitation</th>
      <th>references</th>
      <th>scientificName1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>12</td>
      <td>12</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Agaricales</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2018-08-10 11:58:06.954</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Agaricales</td>
    </tr>
    <tr>
      <td>1</td>
      <td>13</td>
      <td>13</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Auriculariales</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2014-09-26 11:01:06.918</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Auriculariales</td>
    </tr>
    <tr>
      <td>2</td>
      <td>14</td>
      <td>14</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Boletales E.-J.Gilbert</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>E.-J.Gilbert</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2019-12-10 16:45:09.658</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Boletales</td>
    </tr>
    <tr>
      <td>3</td>
      <td>15</td>
      <td>15</td>
      <td>NaN</td>
      <td>120181.0</td>
      <td>NaN</td>
      <td>Geastrales</td>
      <td>NaN</td>
      <td>Basidiomycota</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2015-03-05 17:48:09.918</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Geastrales</td>
    </tr>
    <tr>
      <td>4</td>
      <td>16</td>
      <td>16</td>
      <td>NaN</td>
      <td>120180.0</td>
      <td>NaN</td>
      <td>Helotiales</td>
      <td>NaN</td>
      <td>Ascomycota Caval.-Sm.</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ORDEM</td>
      <td>NaN</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2014-03-17 08:17:41.765</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Helotiales</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>123498</td>
      <td>606941</td>
      <td>606941</td>
      <td>NaN</td>
      <td>113348.0</td>
      <td>NaN</td>
      <td>Fridericia trichoclada (DC.) Kaehler &amp; L.G. Lo...</td>
      <td>NaN</td>
      <td>Fridericia Mart.</td>
      <td>Taxon 68(4): 765 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>trichoclada</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(DC.) Kaehler &amp; L.G. Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 16:01:03.511</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Fridericia trichoclada</td>
    </tr>
    <tr>
      <td>123499</td>
      <td>606942</td>
      <td>606942</td>
      <td>NaN</td>
      <td>113938.0</td>
      <td>112896.0</td>
      <td>Tanaecium dichotomum (Jacq.) L.G.Lohmann</td>
      <td>NaN</td>
      <td>Tanaecium Sw. emend L.G.Lohmann</td>
      <td>Taxon 68(4): 65 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>dichotomum</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Jacq.) L.G.Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 14:42:18.643</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Tanaecium dichotomum</td>
    </tr>
    <tr>
      <td>123500</td>
      <td>606943</td>
      <td>606943</td>
      <td>NaN</td>
      <td>113938.0</td>
      <td>113777.0</td>
      <td>Tanaecium parviflorum (Mart. ex DC.) Kaehler &amp;...</td>
      <td>NaN</td>
      <td>Tanaecium Sw. emend L.G.Lohmann</td>
      <td>Taxon 68(4): 765 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>parviflorum</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Mart. ex DC.) Kaehler &amp; L.G.Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NaN</td>
      <td>2020-01-28 14:50:53.874</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Tanaecium parviflorum</td>
    </tr>
    <tr>
      <td>123501</td>
      <td>606944</td>
      <td>606944</td>
      <td>NaN</td>
      <td>113938.0</td>
      <td>113712.0</td>
      <td>Tanaecium paradoxum (Sandwith) Kaehler &amp; L.G.L...</td>
      <td>NaN</td>
      <td>Tanaecium Sw. emend L.G.Lohmann</td>
      <td>Taxon 68(4): 765 2019</td>
      <td>2019</td>
      <td>...</td>
      <td>paradoxum</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Sandwith) Kaehler &amp; L.G.Lohmann</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 14:58:07.396</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Tanaecium paradoxum</td>
    </tr>
    <tr>
      <td>123502</td>
      <td>606945</td>
      <td>606945</td>
      <td>NaN</td>
      <td>26542.0</td>
      <td>43353.0</td>
      <td>Didymopanax calvus (Cham.) Decne. &amp; Planch.</td>
      <td>NaN</td>
      <td>Didymopanax Decne. &amp; Planch.</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>calvus</td>
      <td>NaN</td>
      <td>ESPECIE</td>
      <td>(Cham.) Decne. &amp; Planch.</td>
      <td>NOME_ACEITO</td>
      <td>NOME_CORRETO</td>
      <td>2020-01-28 17:04:58.733</td>
      <td>Flora do Brasil 2020 em construção. Jardim Bot...</td>
      <td>http://reflora.jbrj.gov.br/reflora/listaBrasil...</td>
      <td>Didymopanax calvus</td>
    </tr>
  </tbody>
</table>
<p>123503 rows × 27 columns</p>
</div>




```python
#Aplicação da função flora_typo_correct

my_species_clean=flora_typo_correct(minha_lista, data_clean)
```


```python
my_species_clean
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
      <th>species</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Acca sellowiana</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Allophylus edulis</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Allophylus guaraniticus</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Annona rugulosa</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Zanthoxylum rhoifolium</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Banara tomentosa</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Blepharocalyx salicifolius</td>
    </tr>
    <tr>
      <td>7</td>
      <td>NI</td>
    </tr>
    <tr>
      <td>8</td>
      <td>Campomanesia xanthocarpa</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Casearia decandra</td>
    </tr>
    <tr>
      <td>10</td>
      <td>Sebastiania commersoniana</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Cedrela fissilis</td>
    </tr>
    <tr>
      <td>12</td>
      <td>Celtis iguanaea</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Gochnatia polymorpha</td>
    </tr>
    <tr>
      <td>14</td>
      <td>Citronella paniculata</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Maytenus boaria</td>
    </tr>
    <tr>
      <td>16</td>
      <td>Coutarea hexandra</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Cupania vernalis</td>
    </tr>
    <tr>
      <td>18</td>
      <td>Dalbergia frutescens</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Aplicação da função flora_typo_correct

flora_accepted_names(my_species_clean, data_clean)
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
      <th>Species</th>
      <th>Correction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Acca sellowiana</td>
      <td>Acca sellowiana</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Allophylus edulis</td>
      <td>Allophylus edulis</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Allophylus guaraniticus</td>
      <td>Allophylus guaraniticus</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Annona rugulosa</td>
      <td>Annona rugulosa</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Zanthoxylum rhoifolium</td>
      <td>Zanthoxylum rhoifolium</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Banara tomentosa</td>
      <td>Banara tomentosa</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Blepharocalyx salicifolius</td>
      <td>Blepharocalyx salicifolius</td>
    </tr>
    <tr>
      <td>7</td>
      <td>NI</td>
      <td>NI</td>
    </tr>
    <tr>
      <td>8</td>
      <td>Campomanesia xanthocarpa</td>
      <td>Campomanesia xanthocarpa</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Casearia decandra</td>
      <td>Casearia decandra</td>
    </tr>
    <tr>
      <td>10</td>
      <td>Sebastiania commersoniana</td>
      <td>Gymnanthes klotzschiana</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Cedrela fissilis</td>
      <td>Cedrela fissilis</td>
    </tr>
    <tr>
      <td>12</td>
      <td>Celtis iguanaea</td>
      <td>Celtis iguanaea</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Gochnatia polymorpha</td>
      <td>Moquiniastrum polymorphum</td>
    </tr>
    <tr>
      <td>14</td>
      <td>Citronella paniculata</td>
      <td>Citronella paniculata</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Maytenus boaria</td>
      <td>Maytenus boaria</td>
    </tr>
    <tr>
      <td>16</td>
      <td>Coutarea hexandra</td>
      <td>Coutarea hexandra</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Cupania vernalis</td>
      <td>Cupania vernalis</td>
    </tr>
    <tr>
      <td>18</td>
      <td>Dalbergia frutescens</td>
      <td>Dalbergia frutescens</td>
    </tr>
  </tbody>
</table>
</div>



## Considerações finais

A partir do script em Python apresentado, foi possível explorar o banco de dados da ***Flora do Brasil***, para automatizar a checagem das nomenclaturas botânicas, que uma atividade crítica em análises de dados vegetacionais.  


Como etapas seguintes, pretendo melhorar performance do scripts, para que rode mais rápido, e criar uma interface web por meio do [sreamlit](https://www.streamlit.io/)

## Referência
Brazil Flora G (2020): Brazilian Flora 2020 project - Projeto Flora do Brasil 2020. v393.222. Instituto de Pesquisas Jardim Botanico do Rio de Janeiro. Dataset/Checklist. doi:10.15468/1mtkaw
