## TR (translate/delete chars)

* Substitui para substituir, comprimir, remover caracteres

* Sintáxe: ` tr [options] CAD1 [CAD2]`

* Recebe o texto via stdin
* Por isso, é necessario apontar uma entrada, Ex: `tr -opcoes < arquivo-entrada > arquivo-saida` 
* Troca as ocorrências de `CAD1` pelo seu correspondente em `CAD2`


## Principais opções:

```
Opção  	  Significado 
-s	  	  Comprime n ocorrências de CAD1 em apenas uma  
-d	  	  Remove os caracteres de CAD1  
```
* Escape Sequences

```
Seqüência  	  Significado  	  		Octal
\t	  		  Tabulação  			\011
\n	  		  Nova linha  			\012
\v	  		  Tabulação Vertical  	\013
\f	  		  Nova Página  			\014
\r	  		  Início da linha <^M>  \015
\\	  		  Uma barra invertida  	\0134
```

**Pode-se usar intervalos ou valores especificos**
```
## Pode-se usar intervalos ou valores especificos
$ cat > aloha
AAA
BBB

$ cat aloha | tr -s "A-B"
A ... B
$ cat aloha | tr -s "A,B"
A ... B
```
-----
## Trocando caracteres:

**Trocar as ocorrências de O por A**
```
$echo Exemplo bobo | tr o a
Exempla baba
O correto: $ tr o a <<< "Exemplo bobo"
```
**Traduzir valor de váriável (Maiúsculo/Minúsculo)**
```  
## Resp=$(tr SN sn <<< "$Resp ") # Troca todo S ou N (maiúsculo) pelo seu correspondente minúsculo
echo "Insira Sim ou Nao"
read resp
resp=$(tr SN sn <<< "$resp ")
echo "$resp"
```
**Transformar letras maiúsculas em minúsculas de um arquivo**
```
$ tr A-Z a-z < arq-maisculo > /tmp/$$
$ mv -f /tmp/$$ arq-minusculo
```
**Transformar letras maiúsculas em minúsculas de um arquivo**
```
$ tr '\n' ' ' < entrada.txt > saída.txt
```
**Maiúsculas em minúsculas:**
```
tr '[:upper:]' '[:lower:]'
```

-----
## Removendo caracteres 

* Usa o mesmo conceito de troca, utilizando escape sequences ou blank spaces

**Para testes, tornar o arquivo confuso, executavel**
```
$ cat confuso
echo leia Programação Shell Linux do Julio Cezar Neves > livro;cat livro;pwd;ls;rm -f lixo 2>/dev/null;cd ~

# Execução:
$ cat confuso
echo leia Programação Shell Linux do Julio Cezar Neves > livro;cat livro;pwd;ls;rm -f lixo 2>/dev/null;cd ~

# Remover os ponto e vírgulas // OBS detalhe para espaço após o \n (enter)
# Pelos testes, poderia fazer sem espaços, mas sem espaços em ambos os argumentos ";" e "\n"
$ tr "; " "\n " < confuso
echo leia Programação Shell Linux do Julio Cezar Neves
pwd
ls
rm -f lixo 2>/dev/null
cd ~
```

-----
## Comprimir caracteres (-s squeeze-repeats)

```
$ paste -s date-atual date-passado
Seg Jun 19 14:59:54 BRT 2017
Seg Jun  5 10:12:33 BRT 2017

## Há 2 espaços na data do segundo arquivo, será necessário comprimir os espaços para o cut funcionar 
$ cut -f4 -d" " date-atual date-passado
14:59:54
5

## Manipulando
$ cat date-atual date-passado | tr -s " " > todas-datas
$ cat todas-datas
Seg Jun 19 14:59:54 BRT 2017
Seg Jun 5 10:12:33 BRT 2017

# Saída correta do CUT
cut -f4 -d" " todas-datas
14:59:54
10:12:33

# Resumindo em 1 linha :)
cat date-atual date-passado | tr -s " " | cut -f4 -d " "
14:59:54
10:12:33
```

**Remover tabs**
```
$cat olts | tr -s "\t"
```

-----
## Deletando caracteres 

* A opção -d remove o caractere especificado em todo o arquivo

**Removendo carriage-return em arquivos windows**

```
$ cat -ve ArqDoDOS.txt
Este arquivo^M$$
foi gerado pelo^M$$
DOS/Rwin e foi^M$$
baixado por um^M$$
ftp mal feito.^M$$

$ tr -d '\r' < ArqDoDOS.txt > /tmp/$$
$ mv -f /tmp/$$ ArqDoDOS.txt
```

**Procurar usuarios logados a mais de um dia**
```
data=$(date | cut -c5-10)
data=$(who | tr -s " " | cut -f3 -d" ")
##ou...
who | grep -v "$data"
##Exemplo elaborado:
who | grep  "$data" | cut -f1 -d" "
```
