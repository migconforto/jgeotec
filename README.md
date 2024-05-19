# Identificação de favelas e comunidades urbanas na Zona Sul do Rio de Janeiro utilizando machine learning
Favelas e comunidades urbanas possuem características próprias e para traçar políticas públicas adequadas para estas localidades é necessário acompanhar sua expansão ou retração ao longo do tempo. Para superar a dificuldade em obter dados precisos e atualizados em períodos intercensitários, propõe-se o uso de dados de sensoriamento remoto para a identificação de favelas e comunidades urbanas. Com imagens orbitais da Zona Sul do Rio de Janeiro, realizamos a identificação de favelas e comunidades urbanas utilizando algoritmos de aprendizado de máquina e algoritmos de segmentação por superpixel em dados abertos do Google Earth. Foram testados, por validação cruzada, sete algoritmos em um conjunto aleatório de cinco imagens com resolução 500x500 pixels na Zona Sul do Rio de Janeiro, dentre os quais destacam-se o XGBoost e o Light GBM, que apresentaram F1-Score médio de 44,60% e 43,64% e F1-Score máximo de 57,10% e 56,42%, respectivamente.

O município do Rio de Janeiro, capital do estado homônimo, é a segunda maior metrópole do Brasil. Em 2010, a cidade contava com 763 favelas nas quais 1,4 milhões de habitantes moravam, representando 22% da população total do Estado [IBGE 2010]. Neste trabalho, foram selecionados 63 dos 210 setores censitários da Zona Sul rotulados como aglomerados subnormais (IBGE, 2013), com a finalidade de realizar a classificação binária destes entre ‘Favela’ e ‘Não Favela’ por métodos de aprendizagem de máquina. Para seleção dos setores, uma amostragem aleatória inversa dos setores foi realizada, dessa forma a amostra pode ser expandida quando necessário sem desrespeitar os princípios da amostragem aleatória.
Para garantir o melhor resultado no estudo, utilizamos imagens de alta resolução do Google Earth obtidas em março de 2024. Foram utilizadas imagens de resolução espacial de 1 metro por pixel contendo as bandas vermelho, verde e azul (RGB). As imagens selecionadas para treino e teste possuem dimensão de 500 por 500 pixels (0,25 quilômetros quadrados).

Por haver setores não selecionados no conjunto da amostra dentro das imagens escolhidas, foi feito um recorte desses setores na leitura das imagens, a fim de não gerar um erro no treinamento dos algoritmos classificadores. A Figura 1 ilustra o processo de retirada dos setores não amostrados e sobreposição da máscara à imagem original para uma das imagens selecionadas. Na figura temos: a imagem de satélite original que foi selecionada, a máscara criada através do Quantum GIS, a imagem de satélite sem os setores classificados como aglomerados subnormais que não fazem parte da amostra e, por último, a sobreposição da máscara à imagem com os setores não selecionados removidos.

![imagem de satélite e máscara](https://github.com/migconforto/jgeotec/blob/main/image/original_mascara.png)

O pré-processamento de imagens tem como objetivo facilitar a visualização da imagem ou adequá-la para análises quantitativas através de correções de defeitos ou realces das regiões de interesse na imagem além do tratamento de dados quantitativos já realizado pelos modelos de aprendizado de máquina (GOMES, 2001). Nessa etapa foram implementadas seis técnicas de pré-processamento para extração de features em imagens: índice de forma espacial (KOENDERINK; VAN DOORN, 1992), superpixel de Felzenszwalb (FELZENSZWALB; HUTTENLOCHER, 2004), superpixel SLIC (ACHANTA et al., 2012), Padrão Binário Local (LBP) (OJALA; PIETIKAINEN; HARWOOD, 1994), Histograma de Gradientes Orientados (HOG) (DALAL; TRIGGS, 2005) e Matriz de Coocorrência de Níveis de Cinza (GLCM) (JIDE et al., 2020). A Figura 2 mostra como cada método foi implementado.

![features](https://github.com/migconforto/jgeotec/blob/main/image/features.jpg)

As informações da etapa de pré-processamento são incorporadas à imagem original como um novo conjunto de bandas, dando origem à imagem de 40 bandas. Após a adição dessas informações, a imagem passa por uma vetorização pixel a pixel, onde cada posição do vetor-imagem contém as novas bandas do pixel, gerando um vetor de formato (250.000, 40). A vetorização da imagem possibilita a utilização de múltiplos algoritmos de classificação. Para o estudo em questão, foram selecionados sete algoritmos: XGBoost, LightGBM, MLP Classifier, Random Forest, K-Means, Naive-Bayes e Logistic-regression.

Para o conjunto de cinco imagens selecionadas, foram feitas classificações de favelas e aglomerados urbanos utilizando os sete algoritmos mencionados na seção anterior. A escolha dos melhores algoritmos foi feita a partir dos resultados médios para o F1-Score e IoU. Dos sete algoritmos de classificação inicialmente propostos, o XGBoost e Light GBM se destacam dos demais, pois demonstram uma aparente adequação à classificação correta quando comparados à máscara original. Apesar da classificação aparentemente similar entre os resultados obtidos com a aplicação destes algoritmos e a alta acurácia para quatro dos sete algoritmos propostos (Tabela 1), foi obtida melhor comparação real e identificação do desempenho ao observarmos as métricas F1-Score e IoU em conjunto da ilustração das predições para a máscara (Figura 3). Ao compararmos o F1-Score, podemos dividir os modelos em 3 grupos:
- Performance melhor (acima de 40%): XGBoost, Light GBM, Naive Bayes
- Performance intermediária (entre 30% e 40%): MLP Classifier, Random Forest
- Performance pior (abaixo de 30%): K-Means, Logistic-regression

| Model | Accurracy | Precision | Recall | F1-Score | IoU |
|---|---|---|---|---|---|
| XGBoost | 81.24% | 43.10% | 55.82% | 44.60% | 56.32% |
| Light GBM | 81.68% | 43.88% | 52.44% | 43.64% | 56.02% |
| MLP Classifier | 83.94% | 54.02% | 33.54% | 36.64% | 53.42% |
| Random Forest | 69.32% | 36.54% | 64.96% | 37.30% | 54.87% |
| Naive-Bayes | 70.60% | 34.16% | 75.18% | 42.54% | 49.71% |
| K-Means | 44.92% | 19.58% | 48.7% | 23.22% | 48.53% |
| Logistic-Regression | 83.06% | 42.72% | 18.34% | 23.44% | 41.27% |

![resultados](https://github.com/migconforto/jgeotec/blob/main/image/resultados.png)

O código está disponível [aqui](https://github.com/migconforto/jgeotec/blob/main/Roteiro_classificacao_jgeotec.ipynb)
