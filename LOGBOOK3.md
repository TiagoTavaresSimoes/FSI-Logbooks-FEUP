# CTF Semana #3 (Wordpress CVE)

## Objetivo
A resolução do desafio CTF proposto, tinha como objetivo a familiarização com o uso de CVEs no mundo real, entender a importância da partilha pública destas vulnerabilidades para a segurança informática e observar de forma prática como o conhecimento de uma dessas vulnerabilidade pode num ataque a um sistema. Para tal, foi proposto o desafio de conseguir fazer login como administrador num servidor wordpress utilizando uma CVE com exploit conhecido.

## Processo de resolução do desafio CTF
Seguindo os objetivos listados na secção de tarefas do guião:
- Começamos por navegar pela aplicação web para registar informações que pudessem vir a ser úteis ao nosso objetivo. Imediatamente na página inicial, a secção relativa a comentários recentes, dá-nos a informação da existência de 2 utilizadores: admin e Orval Sanford. No footer do website, o link utilizado revela também a utilização do popular plugin para Wordpress, WooCommerce. Navegando até á página de compra do plano de WordPress Hosting, onde os utilizadores referidos comentaram, reparamos na existência de uma secção para informação adicional. Através dessa secção, conseguimos não apenas identificar as versões do Wordpress e do plugin do WooCommerce utilizadas, como também, e a destacar, a utilização da versão 5.4.3 do plugin Booster for WooCommerce.

- Utilizamos de seguida um motor de busca para pequisar vulnerabilidades conhecidas das versões de wordpress e plugins encontradas.
A pesquisa relativas á versão 5.8.1 do WordPress, apesar de revelar várias vulnerabilidades, nenhuma parecia apropriada a algo que nos desse a capacidade de fazer login no servidor, até que pesquisando sobre vulnerabilidades específicas á versão 5.4.3 do plugin Booster for WooCommerce, imediatamente obtivemos informação relativamente a uma conhecida vulnerabilidade (CVE:2021-34646) relativa a funções de verificação e ativação de email, presente em versões inferiores á 5.4.4 do mesmo, que o torna suscetível a um bypass da autenticação, permitindo que atacantes impersonem utilizadores arbitários, incluindo administradores, e consigam fazer log in como esse utilizador.

- Tendo obtido o CVE da vulnerabilidade desejada, submetemos a flag na plataforma CTF e completamos o desafio 1.

- De seguida, utilizamos o motor de busca para pesquisar exploits específicos para esse CVE e encontramos no site exploit-db, o exploit de id 50299.

- Fizemos download do script python, após uma breve análise para perceber por alto como o exploit funcionava, corremos o código, passando o url do servidor de wordpress e id 1 como argumentos (uma vez que apenas sabíamos da existência de 2 utilizadores no servidor testamos correr o script com id = 1 e id = 2, confirmando que o primeiro corresponde ao admin e o segundo ao Orval Sanford).

- Usando o primeiro link gerado, conseguimos fazer login como admin.

- Finalmente, acedemos ao link "http://ctf-fsi.fe.up.pt:5001/wp-admin/edit.php", e copiamos a flag "flag{please don't bother me}" encontrada no post "Message to our employees", submetendo na plataforma e completando o desafio 2.

## Descrição da vulnerabilidade
CVE : 2021-34646\
Pontuação de severidade de 9.8, de acordo com CVSS 3.x\
Inicialmente descoberta pela equipa da Wordfence Threat Intelligence, a 30 de Julho de 2021\
Corrigida a 30 de Agosto de 2021

A vulnerabilidade deve-se a uma fraqueza na maneira como a função reset_and_mail_activation_link, presente no ficheiro "~/includes/class-wcj-emails-verification.php", gera tokens para a verificação de email. Quando um user regista uma nova conta, o Booster for WooCommerce plugin manda uma verificação de email contendo um token único. O user tem de clicar num link com este token para verificar o endereço de email. A função mencionada acima, cria um token fazendo hashing do timestamp Unix atual, usando o conhecido algoritmo MD5. O problema está no facto do timestamp não ser um valor secreto (nº de segundos após 1/1/1970), permitindo que seja adivinhado com uma precisão razoável. Além disso, o algoritmo MD5, é já conhecido por sofrer de extensas vulnerabilidades, e não é adequado para a geração de tokens seguros, uma vez que é relativamente rápido e fácil de fazer brute-force. Tudo isto significa que um atacante é capaz de gerar os seus próprios tokens, adivinhando o timestamp e fazendo hash do mesmo.

## Explicação do funcionamento do exploit

O exploit encontrado (exploit-db id: 50299), trata-se de um script python, que tira vantagem das fraquezas mencionadas na secção anterior:
- O script recebe 2 argumentos: o url base do website que queremos atacar e o id do user da conta que queremos impressionar.

- Usando esses valores, constrói um email de verificação.

- Envia uma GET request para o url de verificação, e guarda a resposta, se apropriado.

- Através da função "email_time_to_timestamp(s)", converte um tempo de email, obtido pelo header "Date" na resposta HTTP, num timestamp Unix.

- Gera, pelo algoritmo MD5, 3 hashes de timestamps diferentes (o exato, e dos 2 segundos anteriores), para contar com potenciais erros de timing devido a atrasos.

- Para cada hash gerado, constrói um url de login.

- Finalmente, dá print de todos os urls de login gerados. Um deles deve em princípio permitir fazer login como o user especificado.
