# **Buffer-Overflow Attack Lab - Set-UID Version**

## **Questão 1**

Começou-se por desabilitar o Address Space Randomization e mudar a shell de “dash” para “zsh”, shell esta, vulnerável a ataques relacionados com o SetUID.

![](./images/guiao5/im1.png)

A seguir, compilou-se e executaram-se os shell codes de 32-bit e 64-bit e nenhuma diferença aparente foi identificada na sua execução. A única diferença observada limita-se às instruções do shellcode em si, que variam devido às diferenças entre as arquiteturas.

![](./images/guiao5/im2.png)

Na Task 2, criamos o “badfile”, alterámos o valor L1 na Makefile, como pedido, para 108 (100 + 8 * G, em que G = 1) e compilamos com make stack-L1, que definiu stack-L1 como SetUID, mas não stack-L1-dbg, detalhe este que será relevante mais à frente.
Stack-L1 sofre de um possível buffer overflow, uma vez que, em bof, é copiado para um buffer com tamanho máximo de 108 bytes (no nosso caso), uma string com tamanho máximo de 517 bytes recorrendo a strcpy, função esta que não verifica acessos "out-of-bounds", resultando na possibilidade de escrever 517 bytes para um bufer de tamnho 100.

![](./images/guiao5/im3.png)

![](./images/guiao5/im4.png)

![](./images/guiao5/im5.png)

De seguida, executamos stack-L1-dbg em debug mode com o GDB e, seguindo o exposto no guião #5 presente no Moodle, fizemos com que os endereços de stack-L1-dbg dentro do GDB fossem os mesmos que os fora dele:

![](./images/guiao5/im6.png)

Definimos a função bof como o breakpoint:

![](./images/guiao5/im7.png)

Executamos o programa com "run" e, de seguida, corremos o comando "next" para que o registo ebp (frame pointer) fosse atualizado para o valor do início do stack frame de bop:

![](./images/guiao5/im8.png)

Agora, é importante descobrir os valores de ebp e de buffer, uma vez que, para conseguirmos executar propriamente o exploit, será necessário reescrever o return address posicionado acima do frame pointer anterior que se encontra, por sua vez, imediatamente acima do stack frame do bop. Ora, isto que dizer que, para reescrevermos o return address com o valor pretendido, temos de o colocar na posição (offset buffer-ebp) + 4, uma vez que correponderá à distância entre a primeira posição do buffer e o início do stack frame  mais os 4 bytes necessários para ultrapassar o frame pointer anterior e chegar ao return address.

Para isso, executamos os seguintes comandos no gdb e obtivemos os respetivos valores:

![](./images/guiao5/im9.png)

Com estes valores podemos obter o seu offset: 0x76 = 116.

![](./images/guiao5/im10.png)

Sendo assim, temos o que precisarmos para tentarmos criar o nosso exploit. O exploit, cujo esqueleto se encontra em exploit.py, insere, no ficheiro "badfile", texto com um tamanho de 517 chars (bytes) e, como o buffer em bof só está capacitado a receber 108 chars (bytes), haverá um buffer overflow.
1. O offset do return address deverá ser, como indicado anteriormente, de 116 + 4, ou seja, 120.
2. O shellcode foi copiado diretamente do shellcode de 32-bit fornecido na Task 1.
3. O offset do shellcode, com base nos slides da aula teórica, deve estar posicionado acima do return address e, como tal, arbitrariamente, atribuímo-lhe o offset de 300. Isto é representado pela imagem abaixo retirada dos slides da aula teórica ("Software Security (Part 1)")

![](./images/guiao5/im11.png)

4. Quanto ao return address, como o shellcode se encontra acima do return address e para evitar que o programa tentasse executar alguma sequência de bits que não NOPs ou o shellcode, o que resultaria no risco de o programa falhar, atribuímo-lhe o endereço de ebp mais um offset (0xffffcab8 + 100), relativamente arbitrário, de modo a que o resultado seja superior à posição do return address, mas inferior ao do shellcode. 
Tentar atribuir um offset preciso, ou seja, diretamente para o shellcode é desnecessário, devido à presença dos NOPs que nos permitem retornar para antes do shellcode, desde que não haja bytes "inválidos" pelo "caminho", uma vez que a CPU executará esses NOPs até chegar ao shellcode. Para além disso, esse método (apontar diretamente para o shellcode) é, também, propenso a falhas, pois, como se verá a seguir, quando o valor dos endereços da stack variam, o exploit pode deixar de funcionar.

Terminamos com o seguinte exploit.py: 

![](./images/guiao5/im12.png)

Depois, corremos o exploit.py, para que este alterasse o "badfile".

![](./images/guiao5/im13.png)

Para confirmar se o exploit realmente funciona, voltamos a executar stack-L1-dbg (de maneira a que os endereços dentro e fora da stack no GDB sejam os mesmos) no GDB e tivemos sucesso: uma shell foi iniciada. No entanto, é uma shell normal, uma vez que, como referido anteriormente, este programa não foi definido como SetUID.

![](./images/guiao5/im14.png)

Executando stack-L1-dbg como "/home/seed/Desktop/labs/seed-labs/category-software/Buffer_Overflow_Setuid/Labsetup/code/stack-L1-dbg" fora do GDB, podemos verificar que o exploit funcionou novamente, mas, mais uma vez, é uma shell normal pelo motivo acima referido.

![](./images/guiao5/im15.png)

Se agora tentarmos executar o programa stack-L1 da seguinte forma: "/home/seed/Desktop/labs/seed-labs/category-software/Buffer_Overflow_Setuid/Labsetup/code/stack-L1", isto é, o mesmo programa mas compilado sem a flag de debugging, mas o qual foi definido como SetUID pela Makefile, vemos que o exploit funciona e, desta vez, obtivemos realmente uma "root shell".

![](./images/guiao5/im16.png)

![](./images/guiao5/im17.png)

No entanto, se tentarmos executar como ./stack-L1, o programa falha com "Segmentation Fault". Isto deve-se ao facto de, por exemplo argv[0] ser menor, logo os endereços da stack são diferentes e o return address aponta para algum endereço inválido (ou porque o programa executou NOPs até chegar a bytes que não correspondem a instruções válidas, ou porque o return address apontava diretamente para esses bytes "inválidos"). 

![](./images/guiao5/im18.png)

Como o tamnho de argv[0] diminui, é provável que os endereços da stack estejam acima do esperado. Sendo ssim, aumentamos o return address para "+ 200", mas, mesmo assim, o programa falhou, novamente, com "Segmentation Fault"

![](./images/guiao5/im19.png)

Tentamos novamente aumentar o return address, desta vez, para "+ 300" e, desta vez, o exploit funcionou, pois o programa deu-nos uma "root shell".

![](./images/guiao5/im20.png)

![](./images/guiao5/im21.png)


## **Questão 2**

Para esta questão, utilizaremos o exploit.py em que o return address corresponde a 0xffffcab8 + 100 (o exploit.py inicial), uma vez que o programa utilizado será stack-L1-dbg. Faremos também com que os endereços no GDB correspondam aos que seriam fora do GDB.

![](./images/guiao5/im12.png)

![](./images/guiao5/im22.png)

De seguida, define-se bof como o breakpoint e executa-se o programa com "run":

![](./images/guiao5/im23.png)

E corre-se o comando "next" duas vezes, isto é, após a execução do strcpy e depois de se dar o overflow:

![](./images/guiao5/im24.png)

A imagem abaixo corresponde aos 84 primeiros endereços de 32 bits:

![](./images/guiao5/im25.png)

E, abaixo, podemos verificar que o shellcode começa a partir do endereço 0xffffcb70 e o return address encontra-se no endereço 0xffffcabc (0xffffcab8 + 4 tal como o pretendido). O return address

![](./images/guiao5/im26.png)
![](./images/guiao5/im27.png)



