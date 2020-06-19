#Passagem de parâmetros:

## Significado das Principais Variáveis Referentes aos Parâmetros

```
Variável	Significado
$0		Contém o nome do programa  
$#		Contém a quantidade de parâmetros passados  
$* ou  $@	Contém o conjunto de todos os parâmetros (idêntico ao $@, difere somente em parâmetro(s) com espaço em branco)  
"$!" 		Possui o número do último processo
"$?" 		Contém o retorno do último comando executado

root@whoami:~# ls named
ls: cannot access 'named': No such file or directory
root@whoami:~# echo $?
2
root@whoami:~# cat nome
Frei Ada Brusque
root@whoami:~# echo $?
0

https://www.tutorialspoint.com/unix/unix-special-variables.htm

```

## Parâmetros

* Parâmetros recebem 'nomes numéricos' `$1 $2 $3 ...etc` 
* São formados por apenas 1 número
* Por isso variáveis não podem se iniciar com números !

```
Programa:

*Obs: Utilizou \ para o shell não interpretar as aspas, sendo exibida durante a execução do programa.
root@whoami:~/shell# cat parametros
#!/bin/bash
# Passagem de parametros
echo "1o. parametro -> $1"
echo "2o. parametro -> $2"
echo "3o. parametro -> $3"
echo Todos de uma só \"tacada\": $*

root@whoami:~/shell# ./parametros aa bb cc
1o. parametro -> aa
2o. parametro -> bb
3o. parametro -> cc

*Aspas inibem a visualização de espaços pelo Shell, considerando AB um único parâmetro.
root@whoami:~/shell# parametros "ab" b c
1o. parametro -> ab
2o. parametro -> b
3o. parametro -> c

root@whoami:~/shell# parametros aa bb cc ee
1o. parametro -> aa
2o. parametro -> bb
3o. parametro -> cc
Todos de uma só "tacada": aa bb cc ee

* Utilizando $* 
* aa e bb foram interpretados como um único parâmetro:
root@whoami:~/shell# parametros "aa bb" cc ee
1o. parametro -> aa bb
2o. parametro -> cc
3o. parametro -> ee
Todos de uma só "tacada": aa bb cc ee

Oque faz o set ? 
set {A..M} # Passando 13 parametros para o bash
echo $0 - $3 - $13 - ${13}
saida: bash - C - A3 - M
```



