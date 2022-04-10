
- **Antes de A.i.A.Y.N**

- **O que é a arquitetura Transformer**

- **Como é sua estrutura**

- **Quais são os diferencias dessa arquitetura**

---
###### Antes de A.i.A.Y.N
- RNN, LSTM and Gated recurrent Neural networks estão estabelecidos como os melhores modelos para trabalhar como problemas com sequencia ou transdução (inferência), como tradução.


- Modelos Recorrentes se baseiam na posição do input. Gerando uma sequência de "hidden state” <img src="https://render.githubusercontent.com/render/math?math=h_t ">.  O que impede a paralelização do trabalho de treinamento. Além de utilização severa da memória.

- Tarefas como traducao eram resolvidas com arquitetura de sequencia para sequencia que sao redes neurais que transformam dada sequencia de elementos, como uma frase, em outra sequencia (exemplo, traducao de texto).
Estes modelos são compostos basicamente por um encoder e um decoder. O encoder pega e sequencia e mapeia uma uma dimensão maior. Este vetor é inserido no decoder que transforma em uma saida sequencial, que pode ser a tradução de uma sentença, por exemplo.

- Em 2015 um novo modelo de encoder-decoder com o Attention, para tarefas de tradução. Desde então o mecanismo de Attention, se tornou componente essencial para modelos de sequencia.

	Pois ele resolveu umm problema de "perda de memória" para sequencias muito grandes. Pois passou a considerar todos os hidden states e os seus respectivos pesos.



---
###### O que é a arquitetura Transformer
Este [artigo](https://arxiv.org/pdf/1706.03762.pdf) propôs uma nova arquitetura, chamada Transformers, que dispensa o uso de modelos recorrentes (classificação e tradução de frases pequenas) e de convoluções.


Em tarefas de tradução este modelo teve uma qualidade superior, além de ser "mais paralelizável" e precisou de menos treinamento.

Além disso, o modelo se mostrou generalizável para outras tarefas.

---
###### Como é sua estrutura

###### Input Embendding
- Word Embendding: forma de representar palavras em comformidade com o seu contexto. O vetores que representam as palavras ficam próximos dos vetores utilizados em contexto semelhante. Por exemplo, cachorro está afastado de carro, mesmo tendo proximidade na ordem alfabetica.

###### Positional Encodding
o proposito é adicionar informação de posicao antes de inserir o embedding no encoder. Pois o transformer foi desenvolvido  originalmente  para tarefas de tradução, e para essas tarefas a posição da informação é bem relevante.

Os modelos de RNN levam em consideração a posição da informação, já que eles processam a informação sequencialmente. Porém, como essa estrutura foi retirada, deve ser inserido algum mecanismo para considerar a posição da informação e permitir a paralelização.

###### Positional Encodding - Solução

-  Positional Encodding: 
	Critérios necessários para o vetor de posição são:

		- Encoder único para cada etapa

		- distância consistente entre duas etapas 

		- Possibilidade de generalizar para sentenças longas

		- ser determinístico

Essa foi forma econtrada pelos pesquisadores de atender todos esses critérios.


Links de artigos que explicam o motivo para usar essa forma de positional-encoding

https://kazemnejad.com/blog/transformer_architecture_positional_encoding/
https://timodenk.com/blog/linear-relationships-in-the-transformers-positional-encoding/


###### Mulit-head Attention
Self-Attention em detalhe:
 -  - **Primeiro passo** criar 3 vetores a partir de cada input (embedding vector de cada palavra). Criamos 3 vetores o Query, o Key e o Value. Esse vetores foram criados a partir da multiplicação de cada matriz obtida no treinamento com o vetor de embedding.

	- **Segundo passo** calculando o pontuação. Precisamos calcular a pontuação de cada palavra que geralmente vem depois da palavra analisada. Neste exemplo, "Thinking",  será analisado quais palavras tem maiores chance de vir depois dela. O Vetor SCORE é calculado tomando o produto escalar do vetor QUERY com o vetor KEY da respectiva palavra que estamos pontuando. Então, se estivermos processando a self-attention para a palavra na posição #1, a primeira pontuação seria o produto escalar de q1 e k1. A segunda pontuação seria o produto escalar de q1 e k2. 

	- **Terceiro e quarto passo** é dividir as pontuações por 8 (a raiz quadrada da dimensão dos vetores chave usados no artigo – 64. Isso leva a ter gradientes mais estáveis. Pode haver outros valores possíveis aqui, mas este é o padrão), então passe o resultado através de uma operação softmax. O Softmax normaliza as pontuações para que sejam todas positivas e somam 1.
	
	- **Quinto passo** é multiplicar cada vetor de valor pela pontuação softmax (em preparação para soma-los). A intuição aqui é manter intactos os valores da(s) palavra(s) em que queremos focar e abafar palavras irrelevantes (multiplicando-as por números minúsculos como 0,001, por exemplo).
	- **Sexto passo** é somar os vetores de valor ponderado. Isso produz a saída da camada de *self-atettion* nesta posição (para a primeira palavra)
	
###### Add e Norm

Primeiro calculamos a soma do vetor de output do Attention block e o input Embending Vector. Depois tem uma camada de normalização. Sabemos que a normalização tem os seguintes benefícios:

	- Reduz o viés

	- Impede que os pesos divirgam

	- Permite um treinamento mais rápido.

- Solução: Layer Normalization
	
Pegamos a media e a variancia de todas as caracteristicas da sentenca. Depois da normalização teremos matrizes com média zero e variancia 1. Este método se mostrou mais eficiente que o bacth normalization. [Para saber detalhes sobre Layer normalization	](https://arxiv.org/abs/1607.06450)

###### Feed Forward
Cada camada do encoder e do decoder, contém uma camada totalmente conectada, que é aplicada a cada posição separadamente e igualmente. Isto consiste em duas transformações lineares com um ativação ReLu entre elas. O Objetivo principal é processar o output um camada Attention de uma forma que melhor sirva o input para a camada Attention seguinte. [Detalhes sobre feed-forward layers, no atirgo "Transformer Feed-Forward Layers Are Key-Value Memories
"](https://arxiv.org/pdf/2012.14913.pdf), neste artigo eles encontraram que esta arquitetura era capaz de capturar alguns padrões de caracteristicas linguisticas.

Podemos observar na Figura 2 que neuronios nas camadas mais baixas, capturam frequentemente padrões superficiais, enquanto camadas mais altas capturam padrões semânticos.

![image-20220402172016115](imagens/image-20220402172016115.png)

![image-20220402172203607](imagens/image-20220402172203607.png)


###### Decoder

Decoder pode trabalhar em diferentes modos.

- Modo de Treinamento: Decoder ajusta os parametros da rede.
	**Masked- Multi-head Attention** : mecanismo que esconde as palavras do resultado correto, durante a fase de treinamento para impedir que o modelo tenha o resultado antes de fazer a previsão e o ajustes dos pesos.

- Modo de teste: Decoder está fazendo a tradução das sentenças.

	**Decoder em fase de teste: (tarefa de tradução)**

	Decoder escolhe qual a palavra é a mais provável que seja a tradução mais correta. Enquanto o encoder pode ser executado paralelamente, o decoder precisa ser executado de forma serial, pois ele precisa do resultado da previsão para prever o seguinte.

###### Keys and Values
Pegamos a saida do encoder, e fazemos duas copias e aplicamos  duas transformações lineares nelas. Uma delas será a Key e a outra será o Value para todo o self-Attention mecanism. O ultimo ingrediente é a Query vem do decoder. O decoder pega a sentenca objetivo e produz um output Para o self attetinion.



---
###### Quais são os diferencias dessa arquitetura

- Paralelizável
	Tornando o treinamento muito mais rápido
- Generalizável

- 
---

Introdução

Os problemas sequenciais eram resolvidos com RNN e GRNN.

Task como traducao era resolvido com arquitetura de sequencia para sequencia .
Seq2seq sao redes neurais que transformam dada sequencia de elementos, como uma frase, em outra sequencia (exemplo, traducao de texto).

seq2seq models são compostos basicamente por um encoder e um decoder. O encoder pega e sequencia e mapeia uma uma dimensão maior. Este vetor é inserido no decoder que transforma em uma saida sequencial, que pode ser a tradução de uma sentença, por exemplo.

Em 2015 um novo modelo de encoder-decoder com o Attention, para tarefas de tradução. Desde então o mecanismo de Attention, se tornou componente essencial para modelos de sequencia.

Pois ele resolveu umm problema de "perda de memória" para sequencias muito grandes. Pois passou a considerar todos os hidden states e os seus respectivos pesos.

A arquitetura tansformer, proposta no artigo "Attention Is All You Need" não usa processamento sequencial. Mostra que podemos usar apenas o mecanismo "Attention". Permitindo paralelização.

## Detalhes do Modelo

Muitos modelos de transdução eficientes tem estrutura encoder-decoder. O modelo transformer proposto, também tem essa estrutura.

## Encoder

![trad_encoder](imagens/trad_encoder.PNG)

# inputs
 pri
 
# positional encoding
o proposito é adicionar informação de posicao antes de inserir o embedding no encoder. Pois o transformer foi para originalmente para tarefas de tradução, e para essas tarefas a posição da informação é bem relevante.

Os modelos de RNN levam em consideração a posição da informação, já que eles processam a informação sequencialmente. Porém, como essa estrutura foi retirada, deve ser inserido algum mecanismo para considerar a posição da informação e permitir a paralelização. 

Critérios necessários para o vetor de posição são:

- Encoder único para cada etapa

- distância consistente entre duas etapas 

- Possibilidade de generalizar para sentenças longas

- ser determinístico

Solução proposta foi:

![image-20220402125206569](imagens/image-20220402125206569.png)

![image-20220402125258473](imagens/image-20220402125258473.png)

Links de artigos que explicam o motivo para usar essa forma de positional-encoding

https://kazemnejad.com/blog/transformer_architecture_positional_encoding/

https://timodenk.com/blog/linear-relationships-in-the-transformers-positional-encoding/


# Multi-head Attention

## self-attention
---
## Add e Norm 

Primeiro calculamos a soma do vetor de output do Attention block e o input Embending Vector. Depois tem uma camada de normalização.
Sabemos que a normalização tem os seguintes benefícios:
- Reduz o viés
- Impede que os pesos divirgam
- Permite um treinamento mais rápido.

- Layer Normalization

Pegamos a media e a variancia de todas as caracteristicas da sentenca. Depois da normalização teremos matrizes com média zero e variancia 1. Este método se mostrou mais eficiente que o bacth normalization. [Para saber detalhes sobre Layer normalization](https://arxiv.org/abs/1607.06450)


![layernorm](imagens/layernorm.png)

---
# Feed Forward

Cada camada do encoder  e do decoder, contém uma camada totalmente conectada, que é aplicada a cada posição separadamente e igualmente. Isto consiste em duas transformações lineares com um ativação ReLu entre elas. O Objetivo principal é processar o output um camada Attention de uma forma que melhor sirva o input para a camada Attention seguinte.
[Detalhes sobre feed-forward layers](https://arxiv.org/pdf/2012.14913.pdf), neste artigo eles encontraram que esta arquitetura era capaz de capturar alguns padrões de caracteristicas linguisticas

feed_forward_caract
![feed_forward_caract](imagens/feed_forward_caract.PNG)

Podemos observar na Figura 2 que neuronios nas camadas mais baixas, capturam frequentemente padrões superficiais, enquanto camadas mais altas capturam padrões semânticos.
 - **Exemplos:
 
 ![table_exemplos_semanticos](imagens/table_exemplos_semanticos.PNG)
 
Shallows -> vem da propria palavra.
Semantic -> Bases tem significado militar.

---
# Decoder

Decoder pode trabalhar em diferentes modos.

- Modo de Treinamento: Decoder ajusta os parametros da rede.
- Modo de teste: Decoder está fazendo a tradução das sentenças. 

**Decoder em fase de teste: (tarefa de tradução)

 
 Decoder escolhe qual a palavra é a mais provável que seja a tradução mais correta.
 Enquanto o encoder pode ser executado paralelamente, o decoder precisa ser executado de forma serial, pois ele precisa do resultado da previsão para prever o seguinte.
 
** Decoder em fase de treinamento

---
# Masked- Multi-head Attention

mecanismo que esconde as palavras do resultado correto, durante a fase de treinamento para impedir que o modelo tenha o resultado antes de fazer a previsão e o ajustes dos pesos.

[Video of masked self-attention](https://www.youtube.com/watch?v=piT1_k8b9uM)
---
# Keys and Values

Pegamos a saido do encoder, e fazemos duas copias fazendo duas transformações lineares. Uma delas será a Key e a outra será o Value para todo o selfAttention mecanism.
O ultimo ingrediente é a Query vem do decoder. O decoder pega a sentenca objetivo e produz um output
Para o self attetinion.


![keys_values](imagens/keys_values.PNG)
---
xxxxxxxx
xxxxxxxx
![image-20220402170231562](imagens/1_8eriEDJZisidMG_yyEDEAA.gif)

![xxx](imagens/xxxx.PNG)
![xxx](imagens/xxxx.png)
![xxx](imagens/xxxx.gif)\\
trad_encoder.png