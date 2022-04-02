# Attention-Is-All-You-Need
Attention Is All You Need explained -  Mostrou que não era necessário a utilização de RNN.

----

#### Videos

Attention Is All You Need

https://www.youtube.com/watch?v=iDulhoQ2pro

CS480/680 Lecture 19: Attention and Transformer Networks

https://www.youtube.com/watch?v=OyFJWRnt_AY

https://towardsdatascience.com/attention-is-all-you-need-discovering-the-transformer-paper-73e5ff5e0634

http://peterbloem.nl/blog/transformers

https://www.youtube.com/watch?v=FWFA4DGuzSc&list=WL&index=3

https://www.youtube.com/watch?v=iH-wmtxHunk

explicação de word-embeddins

https://www.youtube.com/watch?v=gQddtTdmG_8

Transformer Neural Networks - EXPLAINED! (Attention is all you need)

https://www.youtube.com/watch?v=TQQlZhbC5ps



aparentemente excelente explicação

https://jalammar.github.io/illustrated-transformer/



http://nlp.seas.harvard.edu/2018/04/03/attention.html



https://github.com/tensorflow/tensor2tensor



https://blog.ml.cmu.edu/2020/03/20/are-sixteen-heads-really-better-than-one/



---



ver sobre :

- hidden representation

- softmax function

- wordvector

---

##### Duvidas

- [Transduction models](https://machinelearningmastery.com/transduction-in-machine-learning/) -  predicting specific examples given specific examples from a domain
- Attention mechanism  - mecanismo para o decoder utilizar as partes mais relevantes do input. Cuidou do problema dos modelos RNN que só performavam bem em tradução de frases pequenas.
- Transformer is a new simple network architerutem, that dispense recurrent e convolutions
- Como é feito o calculo do self-attetion?
- verificar como são os modelos recorrentes e as convolutions
- Este modelo só serve para  tradução? acho q não! verificar.
- o que é o hidden state

---

# Resumo

##### Abstract:

Este artigo propos uma nova arquitetura, chamada Transformers, que dispensa o uso de modelos recorrentes(classificação e tradução de frases pequenas) e de convoluções.

Em tarefas de tradução este modelo teve uma qualidade superior, alem de ser "mais paralelizável" e precisou de menos treinamento.

Além disso, o modelo se mostrou generalizável para outras tarefas.

##### Introdução

RNN, LSTM and Gated recurrent Neural networks estao estabelecidos como os melhores modelos para trabalhar como problemas com sequencia ou transdução (inferencia), como tradução.

Modelos Recorrentes se baseam na posição do input. Gerando um sequencia de "hidden state" <img src="https://render.githubusercontent.com/render/math?math=h_t ">. O que impede a paralelização do trabalho de treinamento. Além da utilização severa da memória.
$$
h_t \quad h_{t-1}
$$

- Attention mechanism - 

O modelo transformer evita a utilização de recorrencia, ao invés de depender exclusivamente do mecanismo atention para desenhar dependencias globais entre input e output.

Permitindo um aumento significante de paralelização.



#### Background

Para reduzir os calculos sequencias é necessário a utilização de convoluções. Porém o numero de calculos necessários cresce linearmente ou logaritimicamtne. Fazendo o aprendizado ser mais dificil. No Transformer isto é reduzido à um numero constante de cálculos. 

##### Arquitetura do Modelo



![image-20220331195702515](imagens/image-20220331195702515.png)

A maioria dos modelos trandutores tem uma estrutura encoder-decoder. No transformers o encoder mapeia a sequencia de inputs <img src="https://render.githubusercontent.com/render/math?math=(x_1, \dots, x_n)"> para uma sequencia de representação <img src="https://render.githubusercontent.com/render/math?math=\vec{z} = (z_1, \cdots, z_n)">. Dado <img src="https://render.githubusercontent.com/render/math?math=\vec{z} "> , o decoder gera uma sequencia de outputs <img src="https://render.githubusercontent.com/render/math?math=(y_1, \cdots, y_n) "> um elemento de cada vez.



![image-20220402113908797](imagens/image-20220402113908797.png)

![image-20220402120226039](imagens/image-20220402120226039.png)



No transformer a forma de adicionar a informação da posição foi somar o vetor de input embeddings com o vetor de posição.

![image-20220402124611457](imagens/image-20220402124611457.png)

Critérios necessários para o vetor de posição são:

- Encoder único para cada etapa

- distância consistente entre duas etapas 

- Possibilidade de generalizar para sentenças longas

- ser determinístico

  

  #### Solução

  

  ![image-20220402125206569](imagens/image-20220402125206569.png)

![image-20220402125258473](imagens/image-20220402125258473.png)



---

### Mulit-head Attention

![image-20220402125849361](imagens/image-20220402125849361.png)
