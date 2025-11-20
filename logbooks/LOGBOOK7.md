# XSS Lab

## Setup do Ambiente

Primeiramente, temos de colocar os websites para os ataques na pasta "/etc/hosts", para que sejam acessíveis após a iniciação dos containers:

![](./images/guiao7/cathostspt1.png)
![](./images/guiao7/cathostspt2.png)

Depois, construímos e corremos os containers para iniciar a base de dados e tornar os sites acessíveis:

![](./images/guiao7/dcbuildpt1.png)
![](./images/guiao7/dcbuildpt2.png)
![](./images/guiao7/dcup.png)

## Questão 1

### Task 1

Para a *task* 1, foi utilizado o site "www.seed-server.com" com a segunda conta disponibilizada, "alice":

![](./images/guiao7/accelgg.png)

![](./images/guiao7/logintask1.png)

Ao navegar para a página "Profile" e clicar em "Edit Profile", verificaremos que é possível escrever código JavaScript funcional no campo "Brief Description". Será utilizado o código disponibilizado pelo guião: 

![](./images/guiao7/scripttask1.png)

Este trecho de código exibirá um alerta no ecrã quando qualquer utilizador visitar o perfil "alice":

![](./images/guiao7/xsstask1.png)

### Task 2

Para a *task* 2, será, novamente, escrito código JavaScript no campo "Brief Description", o mesmo disponibilizado no guião:

![](./images/guiao7/scripttask2.png)

De um modo similar ao código da *task* anterior, será exibido um alerta no ecrã para qualquer utilizador que visitar o perfil "alice".
Porém, desta vez, o alerta exibirá os cookies do utilizador:

![](./images/guiao7/xsstask2.png)

### Task 3

Para a *task* 3, diferentemente das anteriores, o objetivo do código JavaScript será para roubar informações, não exibi-las.

Primeiramente, é necessário abrir um servidor TCP para ouvir no *port* onde serão transmitidas as informações desejadas, isto é,
os cookies do utilizador:

![](./images/guiao7/portlisten.png)

Após isso, escrevemos o código malicioso JavaScript disponibilizado no guião:

![](./images/guiao7/scripttask3.png)

Este código insere um elemento "img" que envia os cookies da vítima para o *port* 5555, utilizando um pedido "HTTP GET".

Ao salvar as alterações, podemos verificar a existência de uma imagem aparentemente vazia:

![](./images/guiao7/imginserted.png)

Se checarmos o servidor iniciado no terminal, é possível confirmar que o ataque foi bem sucedido: obtivemos os mesmos cookies apresentados
anteriormente na *task* 2, isto é, os cookies do utilizador "alice":

![](./images/guiao7/cookies.png)

![](./images/guiao7/xsstask3.png)

### Task 4

## Questão 2

Há várias modalidades de ataques XSS (Reflected, Stored ou DOM). Em qual/quais pode enquadrar este ataque e porquê?