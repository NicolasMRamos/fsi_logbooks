# Secret Key Incription Lab

## Setup do Ambiente

Para este lab, será necessário apenas fazer setup dos containers docker incluídos na pasta de setup:

![](images/guiao9/dcbuild.png)
![](images/guiao9/dcup.png)

## Tarefa 1

Utilizando a frequência com que as letras e combinações apareçem no texto (obtidas a partir do programa "freq.py"), podemos inferir
sobre o que algumas delas representam. Por exemplo:

![](images/guiao9/1gramfreq.png)

Aqui, podemos pensar que, na cifra, n representa uma vogal, já que são as letras mais comuns em textos, num geral.

De acordo com a Wikipedia, "the" é o trigrama mais comum no Inglês:

![](images/guiao9/3gramex.png)

Verificando a frequência dos trigramas:

![](images/guiao9/3gramfreq.png)

Podemos assumir que "ytn" corresponde a "the".

Para fazer essa substituição, executamos o comando "tr":

![](images/guiao9/trcomm.png)

Comparando os arquivos, é possível verificar as mudanças:

![](images/guiao9/catcipher.png)

![](images/guiao9/catout.png)

Nessa fase inicial, conseguimos descobrir outras letras e palavras a partir da análise de outras frequências.
Depois de um certo ponto, podemos descobrir as outras letras por intuição, pois estarão mais completas.

No final, descobrimos que a cifra é a seguinte:

![](images/guiao9/trcommtranslated.png)

O arquivo final traduzido está em "translated.txt".

## Tarefa 2

Considere os modos de cifra aes-128-ecb; aes-128-cbc e aes-128-ctr. Gere um ficheiro plaintext.txt com pelo menos 1000 bytes, e cifre-o com estes três modos, respondendo aos seguintes pontos:
- Ao cifrar, que flags teve que especificar? Qual a diferença entre estes diversos modos?
- Ao decifrar, que flags teve que especificar? Qual a diferença principal entre aes-128-ctr e os restantes modos?

## Tarefa 5

Utilize o ficheiro plaintext.txt gerado anteriormente, e considere os três modos de cifra especificados anteriormente. Altere o byte 50*G, onde G é o número do vosso grupo prático (de 1 a 9) -- pode utilizar o editor bless, já instalado no ambiente disponibilizado.

Para cada um dos modos de cifra, indique quantos bytes de informação se perdem ao corromper um byte do criptograma. Verifique se esta perda se verifica quando se tenta ler a decifração dos criptogramas alterados.

Pista: desenhar o esquema correspondente a estes modos de cifra tende a facilitar a análise.

## Desafio

![](/images/guiao9/desafio.png)