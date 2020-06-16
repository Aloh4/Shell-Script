# Variáveis

```
# var="Uma variavel pode
ser quebrada em 2 linhas"

# echo $var						// Shell interpreta os espaços/TABS/enters e os remove (deixa apenas 1)
Uma variavel pode ser quebrada em 2 linhas

# echo '$var'					// Apóstrofo não interpreta $, e apenas replicará o texto
$var

# echo "$var"					// Parênteses interpreta $, e não deixa o Shell formatar espaços,TABs e enters.
Uma variavel pode
ser quebrada em 2 linhas
```

**Priorização de comando, $var recebe como valor a saída do comando ls -l e não a string, ls -l**

```
$ var=$(ls -l)	-> execução: echo "$var"
$ var=`ls -l`	-> execução: echo "$var"

A $var protegida por aspas interpreta o sinal da variável $, com essa proteção, a saída sairá formatada pois o shell não verá espaços em branco.
Se o vesse, trocaria todos os multipls (Espaços, enters, TABS) por apenas 1, tornando a saída não formatada.

## Exemplo:
$ var=$(ls -l)

Formatado:
echo "$var"
total 92 
-rw-r--r-- 1 root root 133 Apr 1 09:02 aniv 
-rw-r--r-- 1 root root 18 Apr 1 09:02 datas 
......

Sem formatar:

echo $var
total 84 -rw-r--r-- 1 root root 0 Apr 27 21:35 aloha -rw-r--r-- 1 
root root 133 Apr 1 09:02 aniv -rw-r--r-- 1 root root 15 May 11 00:41 arq -rw-r--r-- 1 root root 6 Apr 27 11:20 arq1 -rw-r--r-- 1 root root 54 May 10 21:24

```

**Gambiarra:**

```
## Executar comandos por variáveis, direto pelo shell :
$ var=ls        -> execução: ~#$var	// Atribui a string ls a variável.
$ var='ls -l' 	-> execução: ~#$var	// Atribui a string ls -l a variável, o apóstrofo faz o shell ignorar o espaço.
Exemplo:
$var

```
