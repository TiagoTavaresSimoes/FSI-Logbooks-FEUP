###LAB SEMANA 4

##Task 1

Ao utilizarmos os comandos <b>printev</b> ou <b>env</b> poderemos ver as varáveis ambiente, mas falando de algumas variáveis ambiente específicas, tal como PWD, teremos de utilizar os comandos <b>env|grep PWD</b> ou <b>printenv PWD</b>.

No caso de definir ou cancelar variáveis ​​de ambiente teremos de usar o <b>export</b> e <b>unset</b>. Lembrando que estes 2 comandos não sáo programas separados: são 2 comandos internos do Bash.


##Task 2

Começaremos esta task ao compilar o programa myprintenv.c ("gcc myprintenv.c").

De seguida modificamos esse código, retirando o comentário ("printenv()") que se encontra no processo pai, comentando essa mesma função no processo filho.
Ao compilar de novo o código, reparamos que não houve nenhuma diferença. Para comprovar essa indiferença utilizamos o comando diff e comprovamos que nada foi impresso. 
Logo concluímos que todas as variáveis de ambiente do pai sáo herdados pelo filho.

##Task 3

Com esta task, o nosso objetivo é analisar o comportamento das varáveis ambiente quando o programa é executado via a função execve().

Esta mesma função tem como objetivo chamar um sistema para carregar um novo comando e mais tarde executá-lo. 

Ao executarmos o ficheiro <b>myenv.c</b>, com a expressão <b>"NULL"</b> no terceiro parâmetro da função, as variáveis ambiente não vão ser registadas nesse parâmetro, uma vez que este é nulo. Logo o return é nulo.

Ao substituírmos <b>"NULL"</b> pela variável <b>"environ"</b>, as variáveis de ambiente serão registadas.