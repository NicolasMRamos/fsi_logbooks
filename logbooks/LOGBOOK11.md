# PKI Lab

## Setup do Ambiente

Construímos e executamos os containers Docker:

![](images/guiao11/dcbuild.png)

![](images/guiao11/dcup.png)

Adicionamos os sites requeridos ao arquivo "/etc/hosts", completando o setup do DNS:

![](images/guiao11/dnssetup.png)

Nota: foram adicionados 3 sites, cada um correspondendo a um dos elementos do grupo. Isso foi feito puramente por efeitos de conveniência, para a execução e registo das tarefas sem inconsistências.

## Tarefa 1

Para a tarefa 1, temos de criar uma organização *root* CA, para podermos emitir certificados digitais para outros sites.

1. Copiar o arquivo "openssl.cnf" para o diretório onde iremos trabalhar:

![](images/guiao11/cpssl.png)

2. Editar o arquivo, de modo a descomentar a linha indicada:

![](images/guiao11/uncomment.png)

3. Criar o diretório "demoCA" e os arquivos necessários dentro dele, além de editar o arquivo "serial" para incluir um número aleatório em formato de string:

![](images/guiao11/demoCAcmds.png)
![](images/guiao11/demoCAcmds2.png)
![](images/guiao11/demoCAcmds3.png)

4. Executar o comando e preencher as informações relevantes: 

![](images/guiao11/sslcmd.png)

![](images/guiao11/CAinfo.png)

Após estes passos, nos registramos como uma *root* CA. Podemos verificar as informações relacionadas ao certificado:

![](images/guiao11/certinfo.png)
![](images/guiao11/certinfo2.png)
![](images/guiao11/certinfo3.png)

E informações relacionadas à chave:

![](images/guiao11/chaveinfo.png)
![](images/guiao11/chaveinfo2.png)
![](images/guiao11/chaveinfo3.png)
![](images/guiao11/chaveinfo4.png)
![](images/guiao11/chaveinfo5.png)

## Tarefa 2

Para a tarefa 2, vamos gerar um pedido de certificado para a *root* CA criada assinar e certificar um *web server*.
 
Executamos o seguinte comando:

![](images/guiao11/sslrequest.png)

Podemos verificar a informação do pedido:

![](images/guiao11/reqinfo.png)
![](images/guiao11/reqinfo2.png)

## Tarefa 3

Para a task 3, vamos aceitar o pedido gerado pelo *web server* com a *root* CA.

1. Descomentamos a linha "copy_extensions" do arquivo de configuração do *openssl*:

![](images/guiao11/copyext.png)

2. Corremos o comando para certificar o *web server*:

![](images/guiao11/sslcert.png)

3. Checamos o estado do certificado:

![](images/guiao11/certified.png)
![](images/guiao11/certified2.png)

## Tarefa 4

## Tarefa 5

## Tarefa 6

