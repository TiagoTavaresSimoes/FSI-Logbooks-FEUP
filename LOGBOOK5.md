## TAREFA 1
O principal objetivo de um ataque de buffer-overflow é injetar código malicioso no programa atacado, de forma a fazer uso dos privilégios deste para o executar. Uma vez que Shellcode é usado na maioria de ataques de injeção de código, esta primeira tarefa tem como objetivo uma familiarização inicial a como este funciona.

Shellcode, é basicamente um pedaço de código que executa uma shell, o que observamos no código c partilhado no guião:

```c
#include <stdio.h>
int main() {
	char *name[2];
	name[0] = "/bin/sh";
	name[1] = NULL;
	execve(name[0], name, NULL);
}
```

Este simples código, usa o comando "/bin/sh", e os argumentos guardados em "name", na chamada ao comando execve(), para executar uma shell. No entanto, não é possível compilar este código e usar o binário gerado como o nosso shellcode. O Lab apresenta, portanto, o código binário gerado apartir de código assembly, o qual é brevemente explicado no guião, para uma arquitetura de 32 e de 64 bits. Este código encontra-se no ficheiro "call_shellcode.c":

```c
const char shellcode[] =
#if __x86_64__
    "\x48\x31\xd2\x52\x48\xb8\x2f\x62\x69\x6e"
    "\x2f\x2f\x73\x68\x50\x48\x89\xe7\x52\x57"
    "\x48\x89\xe6\x48\x31\xc0\xb0\x3b\x0f\x05"
#else
    "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f"
    "\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31"
    "\xd2\x31\xc0\xb0\x0b\xcd\x80"
#endif
;
```

Quando o ficheiro é compilado com a flag -m32, o código binário de 32 bits (bloco else), é guardado em "shellcode", caso contrário, é guardado o de 64 bits (primeiro bloco).

Todas as instruções de compilação já estão especificadas no Makefile providenciado. Compilamos o código com o comando "make", que gerou 2 ficheiros de output (um com o código de 64 bits e outro com o de 32) e, executando ambos, verificamos que  é aberta uma shell (neste caso zsh, por causa das proteções) no terminal, no mesmo diretório onde é executado o programa.



## TAREFA 2
A segunda tarefa consistia em perceber como funciona o programa vulnerável deste lab, que se encontra no ficheiro "stack.c". Para isso começamos por analisar o ficheiro.

A vulnerabilidade está no facto da função "strcpy()", não verificar os limites do buffer para o qual estamos a tentar copiar. O programa lê para um array de caracteres "str", com um tamanho máximo de 517 bytes, o conteúdo (também 517 bytes, se tudo correr bem) de um ficheiro "badfile".

```c
char str[517];
FILE *badfile;
badfile = fopen("badfile", "r");
fread(str, sizeof(char), 517, badfile);
```

De seguida é chamada a função "bof()", com o array "str" como argumento.

```c
bof(str);
```

Nesta função, "strcpy()" irá copiar os 517 bytes do array "str" para um buffer de tamanho "BUF_SIZE", definido como apenas 100 bytes, e por isso um buffer-overflow irá ocorrer.

```c
#ifndef BUF_SIZE
#define BUF_SIZE 100
#endif
//...
char buffer[BUF_SIZE];

strcpy(buffer, str);	
```

Uma vez que o programa é "SET-UID root-owned", explorando esta vulnerabilidade existe a possibilidade do utilizador conseguir acesso a uma root shell (se o conteúdo do ficheiro "badfile" visar a isso, é claro).

Em termos de compilação é de destacar alguns aspetos:
 - As opcões "-fno-stack-protector" e "-z execstack" devem ser usadas de forma a desativar a "StackGuard" e as proteções não executáveis da stack.
 
 - De forma a tornar o programa em um programa "Set-UID root-owned", primeiramente mudamos a ownership do programa para root, usando o comando "sudo chown root stack", e depois mudamos a permissão para 4755, deforma a habilitar o bit de "Set-UID". Esta deve ser a ordem utilizada, uma vez que alterar a ownership para root, desativaria o bit de "Set-UID".
Todos estes comandos já vêm incluídos no Makefile providenciado, pelo que estamos prontos a continuar com as restantes tarefas, e podemos compilar o código com "make", no momento apropriado.



## TAREFA 3




## TAREFA 4
