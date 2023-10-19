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




## TAREFA 3




## TAREFA 4
