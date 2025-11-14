# **Format-String Vulnerability Lab**

- Setup do Ambiente

Começou-se por desabilitar o Address Space Randomization

![](./images/guiao6/protec.png)

Ao executar o comando make dentro da pasta server-code, o arquivo vulnerável format.c é compilado. Esse arquivo contém uma vulnerabilidade do tipo format string.

Durante a compilação, o compilador emite um aviso indicando uma potencial falha de segurança relacionada ao uso da função printf dentro da função vulnerável myprintf, evidenciando o risco associado a essa implementação.

![](./images/guiao6/comp.png)

Também é importante observar que o arquivo é compilado com a flag -z execstack, a qual torna a stack executável. Essa configuração intencionalmente enfraquece as medidas de segurança padrão, permitindo a execução de código injetado na pilha e viabilizando o sucesso do ataque. Em seguida, o comando “make install” envia os arquivos para a pasta “fmt-containers”, para que os containers possam utilizá-lo.

![](./images/guiao6/container.png)

O arquivo server.c presente nesta pasta atua como ponto de entrada principal do servidor. Sempre que uma conexão TCP é estabelecida, o servidor invoca o programa format, configurando essa conexão como a entrada padrão (stdin) do processo.

A seguir, foram:

- Definidas aliases para facilitar o uso dos containers

![](./images/guiao6/aliasescontainer.png)

- Construidos os containers

![](./images/guiao6/makedocker.png)

- Iniciados os containers contruidos, num terminal à parte para possibilitar a leitura dos logs

![](./images/guiao6/initdocker.png)


## **Questão 1**
### **Task 1**

Primeiro testamos a conexão através de uma mensagem benigna simples:

![](./images/guiao6/hello.png)

No terminal com o docker obtemos o input e outras informações de endereços:

![](./images/guiao6/helloout.png)

Utilizando o script build_string.py fornecido no diretório attack-code, construímos um payload destinado a provocar o "crash" do programa, que é o encerramento da execução do após falha do mesmo.

![](./images/guiao6/payload.png)

Esse payload funciona porque a string %n enviada ao programa faz com que o printf interprete um valor presente na stack como um ponteiro e tente escrever nele o número de bytes já impressos — que, neste caso, é 0.
Se o endereço escolhido na pilha for inválido (por exemplo, não mapeado, protegido ou somente leitura), a tentativa de escrita falha e o programa sofre um "crash".

Isso implica que o payload pode não causar o "crash" na primeira execução, já que o endereço interpretado pelo %n pode, ocasionalmente, apontar para um endereço válido.

Em seguida, enviamos o arquivo badfile contendo o payload malicioso:

![](./images/guiao6/crash.png)

Na imagem acima, podemos ver o input utilizado no primeiro terminal (mais acima), seguido do output recebido do servidor (mais abaixo).
Podemos verificar que o programa encerrou sua execução em falha, já que não existem mais logs após o valor inicial da variável “target”, numa execução normal, o output de “myprintf” aparecia logo após essa linha.


### **Task 2**
#### **2.A**
Para essa task, foi contruido o seguinte payload:

![](./images/guiao6/payload2.png)

Este payload envia “AAAA” e “%x” 64 vezes. “%x” mostra o valor presente na stack no endereço, em hexadecimal.
E enviando o mesmo para o servidor:

![](./images/guiao6/payloaduse2.png)

Obtivemos:

![](./images/guiao6/payload2out.png)

No endereço target + 16, é possível observar o nosso input — AAAA, representado em memória como 0x41414141.
O deslocamento de 16 ocorre porque o endereço de target está a 64 bytes de distância do início do nosso input na stack; como cada posição referenciada pelo especificador de formato corresponde a 4 bytes, temos 64 / 4 = 16.

O valor 64 foi determinado por meio de um processo de iteração: inicialmente utilizou‑se o valor 100, verificando que o input ainda aparecia no output do servidor. A partir daí, o valor foi sendo ajustado de forma progressiva até que o input passasse a surgir na última posição exibida, identificando o deslocamento exato correto.


#### **2.B**
Para a task 2.B, foi construido o seguinte payload:

![](./images/guiao6/payload3.png)

Sabendo que o endereço do input está a 16 posições de distância do endereço de target, podemos inserir o endereço da secret message diretamente no nosso payload.
Em seguida, utilizamos 63 especificadores %x para descartar os valores intermediários irrelevantes na stack e, finalmente, exibimos o conteúdo do endereço do input utilizando %s.

![](./images/guiao6/payloadout3.png)

Ao enviar o payload, obtivemos o conteúdo da secret message, que é “A secret message”.











### **Task 3**

#### **3.A: Change the value to a different value**

#### **3.B: Change the value to 0x5000**

## **Questão 2**

