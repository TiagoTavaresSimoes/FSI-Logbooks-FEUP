# CTF Semana #3 (Wordpress CVE)

## Objetivo
A resolução do desafio CTF proposto, tinha como objetivo a familiarização com o uso de CVEs no mundo real, entender a importância da partilha pública destas vulnerabilidades para a segurança informática e observar de forma prática como o conhecimento de uma dessas vulnerabilidade pode num ataque a um sistema. Para tal, foi proposto o desafio de conseguir fazer login como administrador num servidor wordpress utilizando uma CVE com exploit conhecido.


## Processo de resolução do desafio CTF
Seguindo os objetivos listados na secção de tarefas do guião:
- Começamos por navegar pela aplicação web para registar informações que pudessem vir a ser úteis ao nosso objetivo. Imediatamente na página inicial, a secção relativa a comentários recentes, dá-nos a informação da existência de 2 utilizadores: admin e Orval Sanford. No footer do website, o link utilizado revela também a utilização do popular plugin para Wordpress, WooCommerce. Navegando até á página de compra do plano de WordPress Hosting, onde os utilizadores referidos comentaram, reparamos na existência de uma secção para informação adicional. Através dessa secção, conseguimos não apenas identificar as versões do Wordpress e do plugin do WooCommerce utilizadas, como também, e a destacar, a utilização da versão 5.4.3 do plugin Booster for WooCommerce.

- Utilizamos de seguida um motor de busca para pequisar vulnerabilidades conhecidas das versões de wordpress e plugins encontradas.
A pesquisa relativas á versão 5.8.1 do WordPress, apesar de revelar várias vulnerabilidades, nenhuma parecia apropriada a algo que nos desse a capacidade de fazer login no servidor, até que pesquisando sobre vulnerabilidades específicas á versão 5.4.3 do plugin Booster for WooCommerce, imediatamente obtivemos informação relativamente a uma conhecida vulnerabilidade (CVE:2021-34646) relativa a funções de verificação e ativação de email, presente em versões inferiores á 5.4.4 do mesmo, que o torna suscetível a um bypass da autenticação, permitindo que atacantes impersonem utilizadores arbitários, incluindo administradores, e consigam fazer log in como esse utilizador.

- Tendo obtido o CVE da vulnerabilidade desejada, submetemos a flag na plataforma CTF e completamos o desafio 1. De seguida, utilizamos o motor de busca para pesquisar exploits específicos para esse CVE e encontramos no site exploit-db, o exploit de id 50299.

## Descrição da vulnerabilidade
CVE : 2021-34646\
Pontuação de severidade de 9.8, de acordo com CVSS 3.x\
Inicialmente descoberta pela equipa da Wordfence Threat Intelligence, a 30 de Julho de 2021\
Corrigida a 30 de Agosto de 2021

A vulnerabilidade permite




## Explicação do funcionamento do exploit

