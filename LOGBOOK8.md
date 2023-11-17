# SQL Injection Attack LAB

## Setup

Para começar a realizar este lab começamos por adicionar uma nova entrada nos hosts conhecidos pela máquina virtual, <b>www.seed-server.com</b>, <b>sudo nano etc/hosts</b> e adicionando <b>10.9.0.5 www.seed-server.com</b>. Depois, executamos os containers usando <b>dcbuild</b> <b<#docker-compose build</b> e <b>dcup</b> <b<#docker-compose up</b>. Por último executamos <b>mysql -u root -pdees</b> e <b>use sqllab_users</b> para executar como superuser e selecionar o schema.


