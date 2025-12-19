# PKI Lab

## Setup do Ambiente

Construímos e executamos os containers Docker:

![](images/guiao11/dcbuild.png)

![](images/guiao11/dcup.png)

Adicionamos os sites requeridos ao arquivo "/etc/hosts", completando o setup do DNS:

![](images/guiao11/dnssetup.png)

## Tarefa 1

Para a tarefa 1, criamos uma organização *root* CA, para podermos emitir certificados digitais para outros sites.

1. Copiamos o arquivo "openssl.cnf" para o diretório onde iremos trabalhar:

![](images/guiao11/cpssl.png)

2. Editamos o arquivo, descomentando a linha "unique_subject":

![](images/guiao11/uncomment.png)

3. Criamos o diretório "demoCA" e os arquivos necessários dentro dele, além de editar o arquivo "serial" para incluir um número aleatório em formato de string:

![](images/guiao11/demoCAcmds.png)
![](images/guiao11/demoCAcmds2.png)
![](images/guiao11/demoCAcmds3.png)

4. Executamos o comando e preenchemos as informações relevantes: 

![](images/guiao11/sslcmd.png)

![](images/guiao11/CAinfo.png)

Após estes passos, registramos a organização "Evil Empire" como uma *root* CA. Podemos verificar as informações relacionadas ao certificado:

![](images/guiao11/certinfo.png)
![](images/guiao11/certinfo2.png)
![](images/guiao11/certinfo3.png)

E também as informações relacionadas à chave:

![](images/guiao11/chaveinfo.png)
![](images/guiao11/chaveinfo2.png)
![](images/guiao11/chaveinfo3.png)
![](images/guiao11/chaveinfo4.png)
![](images/guiao11/chaveinfo5.png)

### Questões

1. Que parte do certificado indica que isto é um certificado CA?

2. Que parte do certificado indica que isto é um certificado *self-signed*?

3. No algoritmo RSA, temos um expoente público *e*, um expoente privado *d*, um módulo *n* e dois números secretos *p* e *q*, tal que n = *pq*. Por favor, identifica os valores destes elementos nos teus ficheiros de certificado e chaves.

## Tarefa 2

Para a tarefa 2, vamos gerar um pedido para que a root CA assine o certificado de um site.

Executamos o seguinte comando:

![](images/guiao11/sslrequest.png)

Podemos verificar as informações do pedido:

![](images/guiao11/reqinfo.png)
![](images/guiao11/reqinfo2.png)

## Tarefa 3

Para a task 3, vamos aceitar o pedido feito pelo site pela *root* CA, ou seja, assinar o certificado do site.

1. Descomentamos a linha "copy_extensions" do arquivo de configuração do *openssl*:

![](images/guiao11/copyext.png)

2. Executamos o comando para certificar o site com a *root* CA:

![](images/guiao11/sslcert.png)

3. Checamos o estado do certificado:

![](images/guiao11/certified.png)
![](images/guiao11/certified2.png)

## Tarefa 4

Para a tarefa 4, faremos *setup* de um site HTTPS baseado no Apache.

1. Criamos o arquivo de configuração para o Apache utilizar, utilizando as informações do site estabelecido:

![](images/guiao11/websiteconfigs.png)
![](images/guiao11/websiteconfigs2.png)

2. Movemos:

* o arquivo de configuração do Apache para o diretório "image_www".
* o certificado da CA e o certificado e chave pública do site para o diretório "image_www/certs".

![](images/guiao11/moveconfcerts.png)

3. Criamos uma página para o site, para testarmos seu funcionamento mais tarde:

![](images/guiao11/indexsetup.png)

4. Editamos o Dockerfile para incluir o nosso site no setup:

![](images/guiao11/dockerfile1.png)
![](images/guiao11/dockerfile2.png)

5. Reiniciamos o container Docker para aplicar as mudanças, com *dcdown*, *dcbuild --no-cache* ("--no-cache" para evitar que configurações antigas permaneçam) e *dcup -d*.

6. Iniciamos o serviço Apache dentro da shell do container:

![](images/guiao11/apachestart.png)

7. Verificamos que o site criado funciona, bem como "www.bank32.com":

![](images/guiao11/workingsites.png)
![](images/guiao11/workingsites2.png)

### Setup dos Aliases

Para fazer com que os *aliases* funcionem, é necessário adicioná-los ao arquivo de configuração do Apache para a porta 80:

![](images/guiao11/aliasaddingapache.png)

Com isso feito, reiniciamos o container (*dcdown*, *dcbuild*, *dcup*) e iniciamos o Apache novamente:

![](images/guiao11/apachestartalias.png)

Temos, então, os *aliases* funcionando:

![](images/guiao11/workingalias.png)

### Browsing do Website

Ao tentar acessar os sites com "https://" no início do URL, recebemos um aviso de segurança:

![](images/guiao11/warning.png)

Isso acontece pois o browser utilizado (Firefox) não reconhece a CA que assinou os certificados do nosso site.

Para consertar este problema, carregamos os certificados no Firefox.

1. Visitamos a página "about:preferences#privacy" e clicamos em "View Certificates":

![](images/guiao11/viewcerts.png)

2. Na aba "Authorities", clicamos em "Import...":

![](images/guiao11/import.png)

3. Selecionamos o certificado da *root* CA criada anteriormente, adicionando-a à lista de CAs confiáveis do nosso browser:

![](images/guiao11/selectca.png)

![](images/guiao11/trustwebsite.png)

![](images/guiao11/evilempirecert.png)

Com isso, ao acessar o site normalmente, já não recebemos mais o aviso de segurança, e o símbolo do cadeado aparece fechado (o que significa que o site é considerado seguro pelo nosso browser):

![](images/guiao11/nowarning.png)

## Tarefa 5

Para a tarefa 5, simularemos um ataque MITM por meio do DNS.

1. Criamos o site "www.instagram.com" falso e o assinamos com nossa *root* CA, como foi feito nas tarefas 2 e 3.

1.1. Criação do pedido pelo site:

![](images/guiao11/reqexample.png)

1.2. Aceitação do pedido pela *root* CA:

![](images/guiao11/acceptexample.png)
![](images/guiao11/acceptexample2.png)

**Nota**: para o último comando funcionar, foi necessário mover, temporariamente, o certificado da *root* CA criada para o mesmo diretório no qual o comando seria executado e onde estava o arquivo de configuração do *openssl*. 

2. Adicionamos este site ao Dockerfile e ao Apache, como foi feito na tarefa 4:

2.1. Colocamos o certificado da *root* CA e o certificado e chave gerados para "www.instagram.com" no diretório "image_www/certs".

2.2. Criamos o arquivo de configuração do Apache:

![](images/guiao11/configexample.png)

2.3. Criamos uma página para o site:

![](images/guiao11/fooled.png)

2.4. Modificamos o Dockerfile para incluir o novo site no setup do container:

![](images/guiao11/dockerex.png)
![](images/guiao11/dockerex2.png)

2.5. Reconstruímos o container (*dcdown*, *dcbuild*, *dcup*) e iniciamos o servidor Apache:

![](images/guiao11/apachestartex.png)

3. Modificamos o arquivo "/etc/hosts" para incluir o site falso:

![](images/guiao11/dnssetupex.png)

Quando o DNS é resolvido, a vítima (no caso, nós, já que se trata de um ataque simulado) acessará o "www.instagram.com" malicioso.

4. Ao tentar acessar no site "www.instagram.com", verificamos que o ataque foi bem sucedido:

![](images/guiao11/instafooled.png)

Ao invés de sermos direcionados ao site oficial do Instagram, acessamos o site falso.

## Tarefa 6

