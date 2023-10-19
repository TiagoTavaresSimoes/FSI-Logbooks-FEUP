### LAB SEMANA 4

## Task 1

Ao utilizarmos os comandos <b>printev</b> ou <b>env</b> poderemos ver as varáveis ambiente, mas falando de algumas variáveis ambiente específicas, tal como PWD, teremos de utilizar os comandos <b>env|grep PWD</b> ou <b>printenv PWD</b>.

No caso de definir ou cancelar variáveis ​​de ambiente teremos de usar o <b>export</b> e <b>unset</b>. Lembrando que estes 2 comandos não sáo programas separados: são 2 comandos internos do Bash.


## Task 2

Começaremos esta task ao compilar o programa myprintenv.c ("gcc myprintenv.c").

De seguida modificamos esse código, retirando o comentário ("printenv()") que se encontra no processo pai, comentando essa mesma função no processo filho.
Ao compilar de novo o código, reparamos que não houve nenhuma diferença. Para comprovar essa indiferença utilizamos o comando diff e comprovamos que nada foi impresso. 
Logo concluímos que todas as variáveis de ambiente do pai sáo herdados pelo filho.

## Task 3

Com esta task, o nosso objetivo é analisar o comportamento das varáveis ambiente quando o programa é executado via a função execve().

Esta mesma função tem como objetivo chamar um sistema para carregar um novo comando e mais tarde executá-lo. 

Ao executarmos o ficheiro <b>myenv.c</b>, com a expressão <b>"NULL"</b> no terceiro parâmetro da função, as variáveis ambiente não vão ser registadas nesse parâmetro, uma vez que este é nulo. Logo o return é nulo.

Ao substituírmos <b>"NULL"</b> pela variável <b>"environ"</b>, as variáveis de ambiente serão registadas.

## Task 4

Com esta task, o objetivo é demonstrar como as variáveis de ambiente são afetadas quando um novo programa é executado via a função 'system()'. Esta função começa por iniciar um shell e solicita a este que execute o comando especificado como um argumento.

Ao executarmos o ficheiro, o programa chama 'system("/usr/bin/env")', esse executa o comando <b>'/usr/bin/env'</b>. O comando 'env' é utilizado para exibir as variáveis ambiente do processo atual, ou seja, vai iniciar u novo processo na shell que vai exeutar 'env', eset por sua vez exibe as variaveis ambiente o programa em C que chamou em 'system()'.

## Task 5

O Objetivo desta task é entender como as variáveis de ambiente definidas num shell(processo pai) afeta um programa com permissões \setuid, quando este é executado como um processo filho e entender como isto afeta a segurança do sistema.

O programa fornecido permite listar todas as variáveis de ambiente em um processo. Compilamos este programa e definmos as permissões \setuid neste, e em seguida, definir algumas variáveisde ambienteno shell. Quando o programa \setuid é executado  partir do shell, coseguimos observar que as variáveis de ambiente definidas no shell pai estão disponiveis para o programa \setuid (processso filho).
 
## Task 6

O objetivo desta task é demonstrar um problema de segurança com programas \setuid que chamam a função 'system()'. A função 'system()' executa um comando na shell, mas essa pode ser perigosa quando usadas em programas \setuid, pois este pode ser manipulado por variaveis de ambiente, como a variável<b>PATH</b>, esta variavel pode fazer com que o programa \setuid execute um comando malicioso em vez do pretendido.

Com esta task conseguimos perceber que os programas \setuid que chamam a função 'system()' se não forem utilizados corretamente pode levar a vulnerabilidades de segurança, permitindo assim que um atacante execute comandos com privilegios root.



## CTF 2 - Linux Environment

Ao abordar o CTF, a primeira ação após a conexão com o servidor foi analisar os arquivos presentes e verificar as suas permissões. Na pasta principal <b>"/home/flag"</b>, que tinha acesso apenas para leitura, identificamos diversos arquivos, nomeadamente o <b>"my-script.sh"</b>. Este script, além de utilizar variáveis de ambiente, é executado constantemente a cada minuto. O executável <b>"reader"</b> é derivado do arquivo "main.c". De seguida, encontramos um diretório chamado "tmp", que é usado para criação de arquivos temporários. Decidimos então criar um arquivo <b>"teste1.txt"</b> neste local para registar a saída de dados. Para garantir total acesso, aplicamos o comando <b>"chmod 777 teste1.txt"</b>. Finalmente, criamos o <b>"teste1.c"</b> para nos auxiliar na solução deste desafio.

### O código <b>teste1.c</b> é deste formato:

```c++
#include <stdlib.h>

int access(const char *pathname, int mode){
    system("/usr/bin/cat /flags/flag.txt > /tmp/teste1.txt");
    return 0;
} 
```

De seguida decidimos compilar o programa utilizando o comando <b>gcc -fPIC -g -c teste1.c</b> e após isso corremos <b>gcc -shared -o teste1.so teste1.o -lc</b>.

Mas para concluirmos, tivemos de alterar a variável de ambiente <b>LD_PRELOAD</b> para, neste caso, <b>tmp/teste1.so</b>, utilizando os comandos:

<b>echo 'LD_PRELOAD=/tmp/teste1.so' > env</b>
<b>cat env</b>

A seguir tivemos de esperar um minuto para que a flag tivesse no nosso ficheiro, neste caso o <b>teste1.txt</b>.