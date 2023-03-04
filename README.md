# **Relatório de Análise de Dados Imobiliários**

Este relatório tem como objetivo apresentar a análise de dados de um dataset imobiliário, utilizando a linguagem de programação Python e suas bibliotecas.

## A Questão do Négocio:

A análise foi feita para a Roof Imóveis, que é uma das maiores empresas do ramo imobiliário Brasileiro e que deseja expandir sua área de atuação fazendo um investimento internacional.
O objetivo final desta análise é identificar quais são os cinco imóveis recomendados e quais os cinco que não recomendados à Roof realizar investimentos. Para isso, serão definidos alguns critérios e métricas para avaliação dos imóveis.

## O Entendimento do negócio:

O Condado de King, fundado em 1852, é um dos 39 condados do estado norte-americando de Washington. De acordo com o censo nacional de 2020, é o 12° condado mais populoso do país, sendo o mais populoso do estado de Washington, com mais de 2.2 milhões de habitantes.

## Estruturação do Dataset:

O dataset imobiliário é composto, originalmente, por 21 variáveis que descrevem as características dos imóveis, tais como número de quartos, número de banheiros, área total do terreno, área construída, número de andares, entre outras, tendo um total de 21613 registros. Foram realizados tratamentos para eliminar as outliers, que podem prejudicar a análise dos dados.

![Descrição das variáveis](https://user-images.githubusercontent.com/106493763/222857828-68279078-a17c-4930-affb-43efc86f4d43.png)

### Coleta e Transformação dos dados:

A importação do dataset *`'kc_house_data.csv'`*, foi realizada e transformada em um dataframe, nomeado como *`df`*. Com a função *`df.info()`*, obtemos algumas informações importantes, como por exemplo, os dtypes de cada variável. 

![Df Info](https://user-images.githubusercontent.com/106493763/222857944-15023741-2152-43b7-bf6c-db1cc584fb91.png)

Onde é possível verificar que a variável *`'date'`* era definida como object. Para resolver isso, usamos o seguinte código para transformá-la no dtype datetime: 
*`df['date'] = pd.to_datetime(df['date'], format='%Y%m%dT%H%M%S')`*

![Df Info 2](https://user-images.githubusercontent.com/106493763/222857952-2a04c483-2ede-45ed-ade1-0f97f826a779.png)

Podemos verificar na imagem acima, que o dtype da variável *`'date'`*  foi transformado para datetime, e com isso o uso de memória do dataframe foi reduzido de 4.8mb para 3.5mb.

Para uma melhor afinidade com os dados, todas as variáveis que estavam na unidade de medida ft², foram convertidas em novas colunas utilizando o m² como unidade de medida. Após isso, as antigas colunas em ft² foram excluídas do dataframe.

Foi utilizada a função *`round()`* nas variáveis *`'bathrooms'`*, *`'bedrooms'`*,*` 'floors'`*, *`'waterfront'`* e *`'grade'`*, por os seguintes motivos: 
- Não existem imóveis com 1.25 quartos/banheiros/andares, tais tipos de valores podem ser considerados inadequados. Por haver muitos valores desta maneira, serão apenas arredondados para o número mais próximo, pois excluir uma grande quantidade de dados pode interferir mais na análise do que apenas arredondá-los ao número mais próximo.
- Na coluna *`'waterfront'`*, a qual é uma coluna categórica, os valores devem ser '0' (Não), ou '1' (Sim), ou seja, não podem existir valores como 1.25, 0.5, etc.

Utilizando a função *`df.isna().sum()`*, é possível ver que não há dados nulos em nenhuma das colunas.

Com a função *`df.duplicated().sum()`*, vemos que não há linhas duplicadas, pois o output é 0. Por isso, podemos verificar se algum imóvel foi vendido mais de uma vez, com base na quantidade de vezes que o valor de *`'id'`* se repete. Usando a o seguinte código:
*`df.id.duplicated().sum()`*, temos o output do valor 177, o que quer dizer que existem 177 imóveis que foram vendidos mais de uma vez. Então, será criado o dataframe *`df_dup_sales`*, o qual contém apenas os imóveis que já foram vendidos mais de uma vez. Serão criadas as colunas *`'profit'`* e *`'percent_profit'`*, para analisarmos seus lucros.

Com isso, é criado um dataframe final sobre estes imóveis, nomeado de *`highest_profits`*, onde estão os dez imóveis que obtiveram mais lucros em suas vendas, dentro desse período de um ano, utilizaremos este dataframe para analisar as características médias e seus lucros. As características médias encontradas na análise do mesmo, serão tomadas como métricas para uma boa valorização do imóvel.

![Highest P Medias](https://user-images.githubusercontent.com/106493763/222858698-4fb3a5b7-e38c-4425-a344-28091cce0517.png)

![Highest Profits Medias](https://user-images.githubusercontent.com/106493763/222859690-b81c8d78-05c7-48a0-894a-7f1b245c671a.png)

Para ter certeza de que bons imóveis serão escolhidos, serão aplicados dois critérios, que servem de requísitos mínimos para que um imóvel possa ser selecionado, sendo eles:
- O imóvel deve ter uma nota de condição igual ou maior que 3.
- O imóvel deve ter sido construído ou reformado há, no máximo, 30 anos.

###  Detecção e Tratamento de Outliers

Para visualização de outliers, são usados o gráficos do tipo boxplot.

![Boxplots](https://user-images.githubusercontent.com/106493763/222860966-0f2b295d-1051-4fb1-8c66-3c34295b6ac6.png)

Com esses boxplots, verificamos que existem imóveis registrados com a quantidade de quartos ou banheiros igual a zero.  Esses imóveis serão excluidos do dataframe, antes de dar continuação na detecção e tratamento das outliers.

Para a detecção, foi utilizado o método interquartil, o qual definie a distância interquartil, o limite superior e inferior da variável escolhida, e mostra a quantidade de valores que estão fora desses limites. Com isso, criamos um dataframe, nomeado de *`df_outlier`*, o qual informa a quantidade de outliers para cada variável e sua porcentagem em relação a quantidade total de registros do dataframe que foi aplicado o método interquartil.

![Df Outlier](https://user-images.githubusercontent.com/106493763/222858017-839e031e-521b-410c-a6a3-0edf79dc7b17.png)

Analisando as variáveis indicadas como outliers, pode chegar as seguintes conclusões:
- Como as variáveis *`'sqm_living'`*, *`'bedrooms'`* e *`'bathrooms'`*, são variáveis que possuem pouca porcentagem de outliers em relação à quantidade de dados do dataframe, essas outliers serão removidas, sem gerar problemas na análise final.
- Por mais que a variável *`'price'`* tenha uma relação de outliers baixa, elas não serão exlcuidas, pois há vários fatores que podem alterar seu valor, não nos dando a certeza de que realmente são outliers.
- Ao verificar manualmente, é possível concluir que a variável *`'floor'`*, tem como valor máximo: 4 andares, e como valor mínimo: 1 andar. Então, não serão consideradas outliers e, consequentemente, não serão excluídas do dataframe.
- Verificando a variável *`'sqm_lot'`*, possui como valor máximo: 95.139 m², o que é mais de 10x maior que um campo de futebol, considerando que um campo de futebol têm, em média, 8.250 m². Sendo assim, tais outliers, terão de ser excluídas, pois se tornam mais prejudiciais à análise se mantidas do dataset.

![SQM LOT OUTLIER](https://user-images.githubusercontent.com/106493763/222858036-e3c128ab-db67-4289-b87d-6ab6d1effc4f.png)

Ou seja, serão excluídas as outliers nas variáveis: 'sqm_living', 'sqm_lot', 'bedrooms' e 'bathrooms'.

Para a exclusão de todas essas outliers, foram criadas duas funções, chamadas de *`limits()`*, a qual cria duas variáveis, com base no valor do limite superior e inferior da variável escolhida, e *`drop_outliers()`*, a qual usa esses limites como filtro, para excluir todos os valores fora deles.

Para finalizar a estruturação do dataset, e seguir à análise exploratória, foi criada uma nova coluna no dataframe, nomeada de *`'city'`*, a qual tem como valor o nome da cidade de localização do respectivo imóvel. Para encontrar as cidades, foi usado como base o zipcode de cada imóvel. 
Para que isso seja possível é necessário instalar e importar a biblioteca *`pyzipcode`*, podendo ser feito da seguinte forma: 
```python
pip install pyzipcode
from pyzipcode import ZipCodeDatabase
```

## Métricas e Parâmetros para Avaliação de um Imóvel:

Foram utilizadas as seguintes métricas para avaliação dos imóveis:
1. Imóveis nas cidades de Bellevue, Kirkland, Redmond, Sammamish e Mercer Island: estas cidades foram escolhidas devido a fatores como IDH (Índice de Desenvolvimento Humano), taxa de desemprego, média salarial, custo e qualidade de vida, educação, índice de criminalidade. Para isso foi criado o dataframe *`df_five`*, que contêm apenas imóveis localizados nessas cidades, sendo nele que as avaliações são feitas.
2. Nota da condição do imóvel igual ou maior que 3: imóveis com nota de condição inferior a 3 foram considerados em mal estado. 
3. Construção ou reforma nos últimos 30 anos: imóveis construídos ou reformados nos últimos 30 anos estão dentro das normas de construção civil, tendo menor chances de probelmas, o que acaba influênciando na hora da venda.
4. Características médias dos imóveis com maiores lucros na revenda: foram analisados imóveis que obtiveram maiores lucros ao serem vendidos mais de uma vez, a fim de identificar as características que mais contribuíram para o aumento de valor (valorização).
5. Média dos imóveis nas 5 cidades selecionadas: foi calculada a média das características dos imóveis nas cidades de Bellevue, Kirkland, Redmond, Sammamish e Mercer Island, com o intuíto de encontrar imóveis acima da média com um preço competitivo, o que aumenta seu potencial de valorização.

## Análise Exploratória dos Dados:

#### Preço médio de cada cidade:

![Preço medio x cidade](https://user-images.githubusercontent.com/106493763/222858057-6596b6e2-2236-41d9-8c0d-414980ff1bd1.png)

Pode-se ver que cidade com os imóveis mais caros é Mercer Island, e a cidade com imóveis mais baratos é Redmond, onde o preço médio de Redmond é menor que a metade do preço médio dos imóveis de Mercer Island.

#### Valor médio do m² em cada cidade:

![Valor médio m2 x Cidade](https://user-images.githubusercontent.com/106493763/222858071-b3ddd974-6517-477c-8cd8-38a7ce98796d.png)

Mercer Island possui o maior valor médio por m² habitável, já Sammamish, possui o menor valor médio por m² haitável, o qual se aproxima ao valor de Redmond.

#### Relação entre preço, área habitável, quantidade de quartos e banheiros do imóvel:

![Preço, m2, bed e bath](https://user-images.githubusercontent.com/106493763/222858086-648ccad6-cb20-422a-b041-7a7df6e9aa99.png)

Quanto mais caro for o imóvel, maior sua área, assim como, a quantidade de quartos e banheiros.

#### Relação entre preço, qualidade dos materiais e a condição do imóvel:

![Preço, grade, cond](https://user-images.githubusercontent.com/106493763/222858193-128b2658-d241-4334-bf8e-75f0d0075199.png)

A condição do imóvel, não é um fator que pode alterar o preço do mesmo de maneira significativa, já a qualidade dos materiais de construção utilizados, tem uma grande influência no preço do imóvel.

#### Influência da vista sobre o preço do imóvel:

![Inf vista preço](https://user-images.githubusercontent.com/106493763/222858239-8723a5ba-1dda-4dc5-bd44-6dd1bf77848f.png)

A nota sobre à vista do imóvel, e se o mesmo é ou não a beira-mar, não tem influência sobre o preço. Entretando, o fato do imóvel ser ou não a beira-mar, tem grande influência sobre a nota de quão bela é a vista do imóvel.

#### Correlação de Variáveis com Heatmap:

![Correlações ](https://user-images.githubusercontent.com/106493763/222858251-62f455bf-e279-49df-a37f-a07d3f195206.png)

Analisando o heatmap, é possível concluir que as 3 variáveis que mais se correlacionam com a a variável *`'price'`*, são:
1. *'`sqm_living'`*: 0.68
2. *'`grade'`*: 0.64
3. *'`sqm_above'`*: 0.54

## Melhores e Piores Imóveis para Investimento:

### Melhores Imóveis para Investimento:

![5 melhores](https://user-images.githubusercontent.com/106493763/222858272-571d0473-860e-4bd6-99a6-175e868f1d97.png)

Localização dos imóveis:

![Mapa 5 melhores](https://user-images.githubusercontent.com/106493763/222858282-9981dda8-1d92-4ae9-ba1b-62b96611c64a.png)

Os imóveis acima foram escolhidos comos os 5 melhores por:
- Preço: 
	  Os preços são abaixo da média de suas respectivas cidades.
      3 dos 5 imóveis escolhidos, possuem também, o preço abaixo da média dos            imóveis que obtiveram maior lucro em suas vendas.
      Conclusão: Todos os imóveis escolhidos estão com ótimos valores para compra/    investimento, mas para ter certeza, é necessário verificar as outras características   dos imóveis.
- Quantidade de Quartos, Banheiros, e Andares: 
	  Todos os imóveis escolhidos possuem quantidades maiores do que a média de     'highest_profits'.
      Apenas o imóvel com valor mais baixo, possui menos do que 5 quartos, sendo o   único que não supera as duas médias de comparação. (O que pode ser justificado por ter um preço muito abaixo das duas médias)
- Qualidade dos Materiais de Construção: 
	  Todos os imóveis possuem uma boa nota em relação aos materiais de construção, onde nenhuma delas fica abaixo da média do df 'highest_profits' (a qual é a         menor média entre as duas utilizadas como parâmetros - tendo valor de 6.7).
- Área Habitável: 
	  Todos os imóveis possuem a área habitável maior do que as duas médias de         comparação, sendo também, maior do que os 15 imóveis mais próximos. Tal fato pode chamar a atenção na hora da venda, por ser maior do que os outros imóveis da vizinhança, e ter um preço competitivo ou até abaixo da média.
- Valor por m²:
	  Todos os imóveis selecionados possuem valor por m² muito mais baixo do que as médias usadas como parâmetros.
- Tempo de Construção ou Reforma:
	  Todos os imóveis foram construídos ou reformados nos últimos 30 anos.

#### Conclusão:
Todos esses fatores mostram que estes imóveis estão abaixo do seu valor justo, tornando-os ótimas oportunidades para uma grande valorização, e podendo, no futuro, ocasionar em grandes lucros na hora das vendas.

### Melhores Imóveis para Investimento:

![5 piores](https://user-images.githubusercontent.com/106493763/222858294-da41294e-3b87-4806-9763-0ed89b68fe71.png)

Localização dos imóveis:

![Mapa 5 piores](https://user-images.githubusercontent.com/106493763/222858297-deb66788-fe51-4edf-833c-5a9874d515cf.png)

Os imóveis acima foram escolhidos comos os 5 piores por:
- Preço:
	  Ter um preço acima da média de suas respectivas cidades, e da média dos imóveis com maiores lucros.
- Quantidade de Quartos, Banheiros: 
	  Todos os imóveis escolhidos, exceto o de menor preço, possuem a mesma            quantidade da média encontrada no df 'highest_profits'.
- Área Habitável: 
	  Os imóveis escolhidos tem uma área habitável próxima à média dos imóveis com mais lucro, porém alguns deles estão abaixo.
- Valor por m²:
	  Todos os imóveis selecionados possuem valor por m² muito mais alto do que as médias usadas como comparação, podendo ser mais que o dobro das médias         utilizadas como comparação.

#### Conclusão:
Os dois principais fatores que fazem com que estes imóveis se tornem investimentos ruins, são: Preço e Valor por m². As características, em geral, não são péssimas, e algumas estão próximas às médias de comparação, porém, o valor cobrado por tais imóveis é extremamente alto, o que faz com que haja muitas alternativas com preços mais baixos, com características melhores e até acima das médias. Seu valor por m² é extremamente alto, chegando a ser mais de 2x maior do que o valor médio por m² das cinco melhores cidades para se viver, no Condado de King. Com base nisso, podemos concluir que, estes imóveis possuem um baixíssimo potencial de obter um bom lucro na hora de suas vendas, por conta de suas características que não se destacam de maneira positiva, e o preço extremamente alto cobrado nos mesmos.
