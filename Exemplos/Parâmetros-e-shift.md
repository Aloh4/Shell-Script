## Passagem de parâmetros:

## Significado das Principais Variáveis Referentes aos Parâmetros

```
Variável	Significado
$0		Contém o nome do programa  
$#		Contém a quantidade de parâmetros passados  
$* ou  $@	Contém o conjunto de todos os parâmetros (idêntico ao $@, difere somente em parâmetro(s) com espaço em branco) 
		Elimina a necessidade de passar parâmetros entre aspas "param1 param2 ..."
"$!" 		Possui o número do último processo em background
"$?" 		Contém o retorno do último comando executado

Conta os parâmetros passados:
msg=teste
echo "${#msg}"
5

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

## Variaveis do sistema

**$REPLY / ${REPLY}**
```
# A variavel `$REPLY` do bash, possui a última cadeia lida de um read
```

**${PIPESTATUS[@]}**
```
* Ver todas as execuções (via pipe)
* `echo "$?"` mostra somente o status do ultimo comando (neste caso, pwd foi bem sucessido)
* `PIPESTATUS` é um array do sistema e mostrará o status dos demais comandos via pipe
* `[@]` significa todos os elementos do array


ls naoexiste | pwd
/home/whoami/shell
ls: cannot access 'naoexiste': No such file or directory

echo ${PIPESTATUS[@]}
2 0

echo ${PIPESTATUS[@]}
0
```

**${BASH_REMATCH[@]}**
* Array Batch-Rematch
* Tem todos os casamentos
```
cat criticahora.sh
#!/bin/bash
# V1
# Recebe horário como parâmetro
# valida se está no formato HH:MM

if [[ $1 =~ ^([01][0-9]|2[0-3]):([0-5][0-9])$ ]]
then
        echo Horário válido
else
        echo Horário inválido
fi

echo ${BASH_REMATCH[@]}	# Se não houver casamento, retorna vazio.

# OBS:
# BASH_REMATCH é o nome de uma variável do Shell, tipo vetor (array)
# Armazena os dados de um casamento via Expressões Regulares utilizando o test
# Ou seja, ela poderia ser usado para lista elementos específicos:
# `${BASH_REMATCH[0]} ... ${BASH_REMATCH[1]} ... ${BASH_REMATCH[2]}`
# Lista-se todos os elementos do Array `${BASH_REMATCH[@]}`

# No indice zero = casamento total ocorrido
# Os demais indices, equivalem aos retrovisores dos grupos da REGEX.

--- é possivel acessar elementos especificos do array -- 
echo ${BASH_REMATCH[2]}

##Saida
./criticahora.sh 18:00
Horário válido
00
```

## Parâmetros

* Parâmetros recebem 'nomes numéricos' `$1 $2 $3 ...etc` 
* São formados por apenas 1 número
* Por isso variáveis não podem se iniciar com números !

* Pode-se usar o set para passar parâmetros:
```
set teste
root@whoami:~# echo $1
teste

```

```
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

-----
## Comando shift

* Sintaxe `shift n1...n2...n3..etc`
* O default de `shift` é `shift 1`
* `N` pode assumir qualquer valor numérico
* Despreza os `N` primeiros parâmetros anteriores
* Torna o parâmetro de ordem `N+1` o primeiro, ou seja o `$1`
* `$1 shift $2 $3` 		usando `shift` o parâmetro 2 se torna o novo $1
* `$1 $shift 2 $2 $3` 	usando `shift 2` o parâmetro 3 se torna o novo $1
 
```
http://ss64.com/bash/shift.html
http://www.tldp.org/LDP/Bash-Beginners-Guide/html/sect_09_07.html
```

```
* Testando $11, vê-se que o shell interpretou $1 seguido do algarismo 1
root@whoami:~/shell# cat parametros2
#!/bin/bash
# Programa para testar passagem de parâmetros (2º version)
echo O programa $0 recebeu $# parâmetros:
echo "11o. parametro -> $11"
Saída:
root@whoami:~/shell# parametros2 passando parametros para testar
O programa ./parametros2 recebeu 4 parâmetros:
11o. parametro -> passando1
```

* Shift tornou o 2o. o novo $1
* Shift 2 tornou o 4o. o novo $1  e imprimiu testar (4º parametro recebido)
* Sem a utilização de shift 2, seria exibido até PARA, pois não haveria saídas $$ para exibir testar

```
root@whoami:~/shell# cat parametros2
#!/bin/bash
# Programa para testar passagem de parâmetros (2º version)
echo O programa $0 recebeu $# parâmetros:
echo "11o. parametro -> $11"
shift
echo "2o. parametro -> $1"
shift 2
echo "4o. parametro -> $1"

Saída:
root@whoami:~/shell# parametros2 passando parametros para testar
O programa ./parametros2 recebeu 4 parâmetros:
11o. parametro -> passando1
2o. parametro -> parametros
4o. parametro -> testar
```

## Como faço para pegar somente o último parâmetro que foi passado de maneira dinâmica?

**Garantindo que o script receberá N parametros**

```
set a b c
shift $(($# - 1)) # Mata todos menos o último parâmetro
echo $1           # O único parâmetro que sobrou
				  # Poderia expandir para -2...
ou...			
# De maneira estática
shift -2 


Onde $(($# - 1)) subtrai 1 da quantidade de parâmetros ($#) e os joga fora (shift).
Digamos que eu tenha passado a, b e c como parâmetro. Nesse caso $# tem 3, do qual subtraímos 1 sobrando 2, 
que seria a quantidade de parâmetros desprezados pelo shift, restando somente o que era o último.
```
# Expansão de parâmetros

* Usa-se o índice do parâmetro dentro das chaves ex: `${11}`
* Torna-o uma coisa única e não $11 = $1 e 1
* A expansão de parâmetros `${}`, não é a expansão de chaves `{1..10}`
* Logo, pode-se usar a expansão de parâmetros `${11}`, junto com a expansão de chaves `{1..10}`

```
root@whoami:~/shell# cat parametros3
#!/bin/bash
# Programa para testar passagem de parâmetros (3º version)
echo O programa $0 recebeu $# parâmetros:
echo "1o. parametro -> $1"
echo "2o. parametro -> $2"
echo "11o. parametro ->${11}"

Saída:
root@whoami:~/shell# parametros3 {a..k}
O programa ./parametros3 recebeu 11 parâmetros:
1o. parametro -> a
2o. parametro -> b
11o. parametro ->k

```

*Para que o shell interprete a passagem de vários parâmetros, 
é necessário proteger o parâmetro "$1" e também a entrada do parâmetro " "

* Sem proteger o parâmetro, o shell tentará procurar a cadeia 'album' nos arquivos '2 e musicas(do grep)'

```
script: grep $1 musicas
funcionamento: grep album 2 musicas // Onde $1 contem 'album' e '2'

root@whoami:~/shell# muslist "album 2"
grep: 2: No such file or directory
album 1^Artista1~Musica1:Artista2~Musica2
album 2^Artista3~Musica3:Artista4~Musica4

```
* Protegendo o parâmetro e a entrada:
```
script: grep "$1" musicas	
funcionamento: grep "$1" musicas // Onde $1 contém 'album 2'

root@whoami:~/shell# muslist "album 2"
album 2^Artista3~Musica3:Artista4~Musica4
```

* O Método correto é utilizando ` $* ` 
* Dessa forma "$2" que significa "todos os parâmetros", seria substituido por `album 2`

```
script: grep "$*" musicas 
funcionamento: grep "$*" musicas // Onde todas as entradas seriam interpretadas por $* sem a necessidade de " "

root@whoami:~/shell# muslist album 2
album 2^Artista3~Musica3:Artista4~Musica4

```

