# Operações Aritméticas:

## Comando `expr`

**Principais operadores:**

```
Operador	| Significado
+				Soma
-				Subtração
*				Multiplicação
/				Divisão
%				Resto da divisão
ARG1 | ARG2		Volta ARG1 se ele não for nulo nem 0, senão ARG2
ARG1 & ARG2		Volta ARG1 se nenhum argumento é nulo ou 0, senão 0
ARG1 < ARG2		Volta 1 se ARG1 menor que ARG2, senão 0
ARG1 <= ARG2	Volta 1 se ARG1 menor ou igual a ARG2, senão 0
ARG1 = ARG2		Volta 1 se ARG1 é igual a ARG2, senão 0
ARG1 != ARG2	Volta 1 se ARG1 é não igual a ARG2, senão 0
ARG1 >= ARG2	Volta 1 se ARG1 maior ou igual a ARG2, senão 0
ARG1 > ARG2		Volta 1 se ARG1 maior que ARG2, senão 0
```

* As comparações são feitas aritmeticamente se ambos os operandos forem numéricos ou equivalentes. 
* Se um ou ambos não o for, a comparação será léxica;
* Muitos de seus operadores, como: >, <, !, *, |, &, ? são significantes para o Shell e portanto precisam de ser escapados

** Exemplos praticos TR **
```
## TR (translate/delete chars)

Principais opções:

Opção  	  Significado 
-s	  	  Comprime n ocorrências de CAD1 em apenas uma  
-d	  	  Remove os caracteres de CAD1  

Escape Sequences

Seqüência  	  Significado  	  		Octal
\t	  		  Tabulação  			\011
\n	  		  Nova linha  			\012
\v	  		  Tabulação Vertical  	\013
\f	  		  Nova Página  			\014
\r	  		  Início da linha <^M>  \015
\\	  		  Uma barra invertida  	\0134

## Trocando caracteres:

#Trocar as ocorrências de O por A
$echo Exemplo bobo | tr o a
Exempla baba
O correto: $ tr o a <<< "Exemplo bobo"

# Traduzir valor de váriável (Maiúsculo/Minúsculo)
## Resp=$(tr SN sn <<< "$Resp ") # Troca todo S ou N (maiúsculo) pelo seu correspondente minúsculo
echo "Insira Sim ou Nao"
read resp
resp=$(tr SN sn <<< "$resp ")
echo "$resp"

#Transformar letras maiúsculas em minúsculas de um arquivo
$ tr A-Z a-z < arq-maisculo > /tmp/$$
$ mv -f /tmp/$$ arq-minusculo

# Removendo caracteres 

* Usa o mesmo conceito de troca, utilizando escape sequences ou blank spaces

# Para testes, tornar o arquivo confuso, executavel
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

#Comprimir caracteres (-s squeeze-repeats)

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

# Remover tabs
$cat olts | tr -s "\t"

**OBS**
## Pode-se usar intervalos ou valores especificos

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

** Obs: Pode-se usar escape sequences para, como no exemplo "Remover OLTS"

Deletando caracteres 

* A opção -d remove o caractere especificado em todo o arquivo

## Removendo carriage-return em arquivos windows

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

## Procurar usuarios logados a mais de um dia
data=$(date | cut -c5-10)
data=$(who | tr -s " " | cut -f3 -d" ")
##ou...
who | grep -v "$data"
##Exemplo elaborado:
who | grep  "$data" | cut -f1 -d" "

```

-----
## Comand `bc` 

* Usado para expressões complexas
* Usado quando há casas decimais
* Caso o nº já usar de casas decimais, não há necessidade de usar o scale 

$ echo "(2 + 3) * 5" | bc          # Parênteses usados para dar precedência
25
Ou... bc <<< "(2 + 3) * 5"

* Para se utilizar casas decimais, a precição será feita com a opção scale.
$ echo "scale=2; 7*5/3" | bc
11.66

## Quando um fator é informado com  decimais, não é necessário utilizar o scale.
$ echo "scale=3; 12.345*3" | bc
37.035

## Usando variáveis
$ num=5
$ echo "scale=2; ((3 + 2) * $num + 4) / 3" | bc
9.66

## Somando nº de uma coluna com CUT e BC
# TR transformou todas as new-lines em +
# Colocou zero adicional ao fim, para não gerar erro no bc
$ echo $(cut -f3 num | tr '\n' +)0 | bc
ou...
$ echo $(cut -f3 num | tr -s '\n' +)0 | bc
ou...
$cut -f3 colunas-arit | paste -sd+ | bc


## Somar 2 colunas:
cut -f2-3 colunas-arit | tr "\t" + | bc

---
## Mudança de base com BC
## obase=BASE_SAIDA 
$ echo "obase=16; 11579594" | bc
B0B0CA          # B, zero, B, zero, C e A
## ibase=BASE_ENTRADA -> retorna decimal por padrão
$ echo "ibase=16; B0B0CA" | bc
11579594

```
## Transformou input decimal em output hexadecimal
echo "obase=16; 31" | bc
1F
## Transformou input hexadecimal em output decimal
echo "ibase=16; 1F" | bc
31

## Usando 2 argumentos:
# Output hexadecimal, input decimal
echo "obase=16; ibase=10; 31" | bc
1F

# Output decimal, input hexadecimal
echo "obase=10; ibase=16; 1F" | bc
31

## Exemplo: Octal para hexadecimal
## Obs: Sempre usar obase primeiro
## Quando você especifica ibase=8 e depois obase=16, ele interpretará 16 na base 8, ou seja, usará a base 14 como saída
# Binario=2, Octal=8, Hexa=16, Decimal=10

$ echo "ibase=8; obase=16; 512" | bc
198
$ echo "obase=16; ibase=8; 512" | bc
14A

```

-----
Interpretador aritmético do Shell

* Sintáxe `$((expressao aritmetica))`
* Obs: Alguns shells não entende essa expressão, ex: Bourne Shell (sh)


## Tabelas:

* Operadores Aritméticos

```
Expressão	Resultado
id++ id--	Pós-incremento e pós-decremento de variáveis
++id -?id	Pré-incremento e pré-decremento de variáveis # Não funcionou -?id, apenas com --id
**			Exponenciação
* / %		Multiplicação, divisão, resto da divisão
+ -			Adição, subtração
=			Atribuição
```

* Operadores Lógicos

```
Expressão	Resultado
<= >= < >	Comparação
== !=		Igualdade, desigualdade
&&			E lógico
||			OU lógico
```


## Exemplos:

```
echo $(((2+5)*5))

## Com variáveis
# Funciona com/ sem $ precedente
tres=3
echo $(((2+tres)*5))
echo $(((2+$tres)*5))

## Sequencias
# Obs: Sempre que uma variável nao existe, ela é iniciada em 0
$ unset i
$ echo $((i++))		# Pós-incrementação
0					# echo foi executado, e só então realizado o incremento da variável
$ echo $i
1
$ echo $((++i))		# Pré-incrementação
2					# Realizado o incremento da variável, e depois o comando echo
$ echo $i
2

## Outros exemplos válidos
$ echo $((i+=3))
5
$ echo $i
5
$ echo $((i*=3))
15
$ echo $i
15
$ echo $((i%=2))
1
$ echo $i
1

* Poderiam ser:
i=$((i+3))
i=$((i*3))
i=$((i%2))

## Testar tempo de execução com shell e expr`
$ time for ((i=1; i<200; i++)); { expr 0 + 0 > /dev/null; }
real	0m0.188s
user	0m0.012s
sys	0m0.036s

$ time for ((i=1; i<200; i++)); { echo $((0 + 0)) > /dev/null; }
real	0m0.003s
user	0m0.004s
sys	0m0.000s

```

## Operador ternário -------------------------------

Sintáxe: `COND ? VERDADEIRO : FALSO`
* OBS: Tanto VERDADEIRO quanto FALSO, devem devolver valores numéricos.

COND		É uma condição numérica;
VERDADEIRO	Será executado caso COND seja verdadeira;
FALSO		Será executado caso COND seja falso.

* Exemplos:
```
$ echo $var
50
$ var=$((var>40 ? var-40 : var+40))
$ echo $var
10
$ var=$((var>40 ? var-40 : var+40))
$ echo $var
50
```

Caso a variável $var seja maior que 40 (var>40), então (?) faça 
$var igual a $var menos 40 (var-40), senão (:) faça 
$var igual a $var mais 40 (var+40). 
** Interrogação (?) e dois pontos (:) fazem o papel de então e senão **

Outros exemplos (maneira intrínseca (builtin) com let)

$ echo $var
50
$ let var='var>40 ? var-40 : var+40'
$ echo $var
10
$ let var='var>40 ? var-40 : var+40'
$ echo $var
50

## Trocando de base -------------------------------

* Para trabalhar com bases diferentes da decimal, usa-se

`BASE#NUM`

* Base, é um nº de 2 a 64 decimal - > que representa o sistema de numeração
* NUM, algarismos em conformidade com o sistema númerico definido em BASE
* Se Base for omitida, então 10 será o default.
* Algarismos maiores que 9, são representados por letras minúsculas, maiúsculas, @ e _
* Se base for menor ou igual a 36, maiúsculas/minúsculas podem ser usadas indiferentemente para definir algarismos maiores que 10
* Obs: Constantes começadas em zero são interpretadas como octal, se iniciadas com 0x ou 0X são tratadas como hexadecimal


* Exemplos
```

$ echo $[2#11]
3
$ echo $((16#a))
10
$ echo $((16#A))
10
$ echo $((2#11 + 16#a))
13
$ echo $[64#a]
10
$ echo $[64#A]
36
$ echo $((64#@))
62
$ echo $((64#_))
63

## Constantes começadas com um zero são interpretadas como octal e as iniciadas com 0x ou 0X são tratadas como hexadecimal:
$ echo $((0x10+0))
16
$ echo $((010+0))
8

* O ex acima pode gerar problemas, exemplo:
$ date -d 08/09/2018 "+%m"
08          # Repare que o mês tem um zero à esquerda

$ echo $(($(date -d 08/09/2018 "+%m") + 1))
bash: 08: valor muito grande para esta base de numeração (token com erro é "08")

## O erro é devido a tentativa de soma de 08+1, que o shell entenderá como base octal:
$ echo $((08+1))

## Correção:
$ echo $((10#$(date -d 08/09/2018 "+%m") + 1))

## Usando operadores ternários:
$ Data=08/09/2018
$ echo $((10#$(date -d $Data "+%m") + 1 == 13 ? 1 : 10#$(date -d $Data "+%m") + 1))
9
$ Data=08/12/2018
$ echo $((10#$(date -d $Data "+%m") + 1 == 13 ? 1 : 10#$(date -d $Data "+%m") + 1))
9

## Obs: o comando utizado "-d" representa uma data especifica para o date, nao a atual
## Obs: %% são formatadores... %y %m %d

```
