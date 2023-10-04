# CTF Semana #3 (Wordpress CVE)

## Objetivo
A resolução do desafio CTF proposto, tinha como objetivo a familiarização com o uso de CVEs no mundo real, entender a importância da partilha pública destas vulnerabilidades para a segurança informática e observar de forma prática como o conhecimento de uma dessas vulnerabilidade pode num ataque a um sistema. Para tal, foi proposto o desafio de conseguir fazer login como administrador num servidor wordpress utilizando uma CVE com exploit conhecido.


## Processo de resolução do desafio CTF
Seguindo os objetivos listados na secção de tarefas do guião:
- Começamos por navegar pela aplicação web para registar informações que pudessem vir a ser úteis ao nosso objetivo. Imediatamente na página inicial, a secção relativa a comentários recentes, dá-nos a informação da existência de 2 utilizadores: admin e Orval Sanford. No footer do website, o link utilizado revela também a utilização do popular plugin para Wordpress, WooCommerce. Navegando até á página de compra do plano de WordPress Hosting, onde os utilizadores referidos comentaram, reparamos na existência de uma secção para informação adicional. Através dessa secção, conseguimos não apenas identificar as versões do Wordpress e do plugin do WooCommerce utilizadas, como também, e a destacar, a utilização da versão 5.4.3 do plugin Booster for WooCommerce.
- Utilizamos de seguida um motor de busca para pequisar vulnerabilidades conhecidas das versões de wordpress e plugins encontradas.


## Descrição da vulnerabilidade



## Explicação do funcionamento do exploit

