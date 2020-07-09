# O comando IF

* Em shell, ele é um testador de intruções(comando), não condições(comparações)
* Ele é responsável por testar a variável `$?`, junto com o `test`
* Para testar condições, use o comando `test` e o `if` para testar a saída do `test`

**Sintaxe:**

```
One line:

if CMD; then CMD1; else CMD2; fi

Multiple line:

if CMD
then
    CMD1
    CMDn
else
    CMD2
    CMDm
fi


Pode-se usar elif, preferencialmente, case.
```

**Exemplo**

```
#!/bin/bash
#
# Script para incluir usuários no /etc/passwd
#
if grep -q ^$1 /etc/passwd # grep -q ^$1 /etc/passwd -> manda somente o retorno $?
then
        echo Usuário \'$1\' já existe !!
else
        if useradd $1 2>&- # Useradd gerará erro caso o user não seja root, fechou-se a saída primária
        then
                echo Usuário \'$1\' incluído em /etc/passwd !!
        else
                echo "Problemas no cadastramento. Você é root?"
        fi
fi

# Nesse script, utilizou-se um if adicional para testar se o comando useradd foi executado corretamente.
# Fechar a saída de erros: 2> /dev/null, ou 2>&-, ou preferencialmente ## Obs: Não funcionou no bash com o comando grep.
# Testar: if grep ^$1 /etc/passwd 2>&-
```

-------
# O comando test (dicas)

* O test é built-in do shell `[ ... ]`
* Sua nova e indicada sintaxe é `[[ ... ]]`
* Com a nova opção, pode-se usar REGEX e Expansão de arquivos
* Para informações, utilizar `man test`

**Exemplos**

```
if [[ CMD =~ REGEX ]]; then CMD1; else CMD2; fi


```

**Operadores test**

```
Opções do Comando test para arquivos:

Opção		Verdadeiro se:
-e arq			arq existe
-s arq			arq existe e tem tamanho maior que zero
-f arq			arq existe e é um arquivo regular
-d arq			arq existe e é um diretório;
-r arq			arq existe e com direito de leitura
-w arq			arq existe e com direito de escrita
-x arq			arq existe e com direito de execução
arq1 -nt arq2	arq1 é mais novo que arq2
arq1 -ot arq2	arq1 é mais antigo que arq2

Opções do comando test para cadeias de caracteres:

Opção		Verdadeiro se:
-z cadeia	Tamanho de cadeia é zero
-n cadeia	Tamanho de cadeia é maior que zero
cadeia		cadeia tem tamanho maior que zero
c1 = c2		As cadeias c1 e c2 são idênticas

Opções do comando test para números:

Opção		Verdadeiro se:	Significado
n1 -eq n2	n1 e n2 são iguais	equal
n1 -ne n2	n1 e n2 não são iguais	not equal
n1 -gt n2	n1 é maior que n2	greater than
n1 -ge n2	n1 é maior ou igual a n2	greater or equal
n1 -lt n2	n1 é menor que n2	less than
n1 -le n2	n1 é menor ou igual a n2	less or equal

Operadores:

Operador		Finalidade
Parênteses ( )	Agrupar
Exclamação !	Negar
-a				E lógico
-o				OU lógico

```

**Exemplos**

**Testa diretórios**
```
#!/bin/bash
#
#Otimizado
#
if test ! -d dir        #test é um programa, seus argumentos são separados por espaços
                        # Cria o diretorio, somente se ele nao existir.
then
        mkdir dir
fi
cd dir
```

**Comparar cadeias**

```
#!/bin/bash
cad1=1
cad2=01

if test $cad1 = $cad2
then
        echo Variáveis são iguais
else
        echo Variaveis diferentes
fi

```

**Pode-se usar**

```
#!/bin/bash
#
#Otimizado
#
if [ ! -d dir ] #test é um programa, seus argumentos são separados por espaços
                        # Cria o diretorio, somente se ele nao existir.
then
        mkdir dir
fi
cd dir
```
**Teste com IF**
* Recebe como parametro, o nome de um arquivo;
* Se executado, salvará este arquivo com o nome original, seguido de ~, e colocorá este arquivo dentro do vi

```
$ cat vira
#!/bin/bash
#
#  vira - vi resguardando arquivo anterior
#         == =            »

# Verificando se foi passado 1 parametro
if  [ "$#" -ne 1 ]
then
    echo "Erro -> Uso: $0 "
    exit 1
fi

Arq=$1
# Caso o arquivo não exista, nao ha copia para ser salva
if  [ ! -f "$Arq" ]
then
    vi $Arq
    exit 0
fi

# Se nao puder alterar o arquivo vou usar o vi para que?
if  [ ! -w "$Arq" ]
then
    echo "Voce nao tem direito de gravacao em $Arq"
    exit 2
fi

# Ja que esta tudo OK, vou salvar a copia e chamar o vi
cp -f $Arq $Arq~
vi $Arq
exit 0
```

**Comparar números**

```
#!/bin/bash
cad1=1
cad2=01

if test $cad1 -eq $cad2
then
        echo Variáveis são iguais
else
        echo Variaveis diferentes
fi

```

**Testando conectores**

```
Obs: -a tem precedencia sobre -o.
# $Familia = felino -o $Familia = canideo -a $Genero = onça -o $Genero = lobo , seria avaliado como:
# $Familia = canideo -a $Genero = onça
# Logo, agrupou-se os conectores


#!/bin/bash
#
familia=felino
genero=gato

if test \( $familia = canidea -o $familia = felino \) -a \( $genero = lobo -o $genero = leao \)
then
        echo Cuidado
else
        echo Ta ok!
fi

```

-------
# Abreviar o comando test

**Conector AND - &&(equivalente ao then)**
* Se o 1º for VERDADEIRO, o 2º teste será feito, mas se o 1º for falso, não há necessidade pois o resultado sempre será FALSO

**Com o conector OR - || (Cumpre funções do else, sem precisa do if)**
* Se o 1º for VERDADEIRO, o 2º teste não será feito pois sempre dará VERDADEIRO, mas se for FALSO, o 2º teste terá de ser feito


```
Valores Booleanos		E			OU
VERDADEIRO-VERDADEIRO	VERDADEIRO	VERDADEIRO
VERDADEIRO-FALSO		FALSO		VERDADEIRO
FALSO-VERDADEIRO		FALSO		VERDADEIRO
FALSO-FALSO	FALSO		FALSO		FALSO
```

**Exemplos**
```
## Se não existir dir, então, crie ele
[ ! -d dir ] && mkdir dir
cd dir

## Dir existe?, ou, crie ele
[ -d dir ] || mkdir dir
cd dir

## Caso cd for mal sucedido, será criado dir, mas não será executado cd, logo, deve-se agrupar os comandos:
cd dir 2>&- || { 
mkdir dir
cd dir 
}
```

**Exemplos**
```
ls teste {
cmd1
cmd2
} || ou teste2 (SE um do comandos dentro das chaves de erro, o OU será executado)

cd dir 2> /dev/null || {
	mkdir dir
	cd dir
}
```

--------
# Conectores Lógicos:

* `&&` equivale ao then 
* `||` equivale ao else

**Dicas**

```
	  AND(&&) OR(||)
VV		V		V	
VF		F		V
FV		F		V
FF		F		F
```

```
ls a* && ls d*
aloha  aniv  arq  arq1  arq2  arq3  arq.bkp  arqs  arquivo.txt
data  datas  dia  dire  doa

ls a* || ls d*
aloha  aniv  arq  arq1  arq2  arq3  arq.bkp  arqs  arquivo.txt

ls a* && ls xxx
aloha  aniv  arq  arq1  arq2  arq3  arq.bkp  arqs  arquivo.txt
ls: cannot access 'xxx': No such file or directory

ls a* || ls xxx
aloha  aniv  arq  arq1  arq2  arq3  arq.bkp  arqs  arquivo.txt

ls fff || ls xxx
ls: cannot access 'fff': No such file or directory
ls: cannot access 'xxx': No such file or directory

ls fff && ls xxx
ls: cannot access 'fff': No such file or directory
```
--------
# Novo comando test (Com metacaracteres)

* Faz uso dos caracteres de expansão de nomes de arquivos
* ` ? * [...] ...etc `
* E também, faz uso de REGEX

**Sintaxe: `[[ ... ]]`**

``` 
Expressões Condicionais Para Padrões
Expressão						Retorna
cadeia == padrao				Verdadeiro se cadeia casa com padrão
cadeia = padrao					Verdadeiro se cadeia casa com padrão
cadeia != padrao				Verdadeiro se cadeia não casa com padrao
cadeia =~ REGEX					Verdadeiro se cadeia casa com Expressão Regular REGEX
cadeia1 < cadeia2				Verdadeiro se cadeia1 vem antes de cadeia2 alfabeticamente
cadeia1 > cadeia2				Verdadeiro se cadeia1 vem depois de cadeia2 alfabeticamente
expr1 && expr2	"E" lógico, 	verdadeiro se ambos expr1 e expr2 são verdadeiros
expr1 || expr2	"OU" lógico, 	verdadeiro se expr1 ou expr2 for verdadeiro

```

**Exemplos**

```
$ echo $H
13
$ [[ $H == [0-9] || $H == 1[0-2] ]] || echo Hora inválida
Hora inválida 
$H=12
$ [[ $H == [0-9] || $H == 1[0-2] ]] || echo Hora inválida
$
---------
$ var=a
$ [[ $var == ? ]] && echo var tem um caractere
var tem um caractere
$ var=aa
$ [[ $var == ? ]] && echo var tem um caractere
$
```

## Test com Regex

* Foi incorporado na versão 3 do bash
* Necessário o uso do operador `=~`, para fazer comparações com REGEX

**Exemplos**
```
cargo=senador
[[ $cargo =~ ^(governa|sena|verea)dora?$ ]] && echo existe || echo nao existe
existe

cargo=mula
[[ $cargo =~ ^(governa|sena|verea)dora?$ ]] && echo existe || echo nao existe
nao existe
```

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


```

**Resultados**

```
1) Passando um horário válido:

$ criticahora.sh 12:34
Horário aceito
12:34 12:34     # <= Gerado pelo cmd da última linha do script
```

----------
# Operadores aritméticos `(( 1 == 1 ))` :

* Aqui podemos usar ` ==, !=, >, >=, <, <= `

```
num=5
((num+3 == 8 )) && echo valido || echo invalido
valido

```
----------
# O comando CASE

* Compara padrões e executa blocos. 

**Dicas com case**
* A partir do bash 4.0, o case recebeu mais 2 terminadores
* ;;  - Termina o bloco de CMD e sai do case;
* ;;& - Termina o bloco de comandos e testa a próxima condição
* ;&  - Termina o bloco de comandos e executa (sem testar) o próximo bloco

* Posso também, usar classes POSIX nas seleções do case

```
~/.bashrc
export LC_COLLATE=C
Sem isso, a ordem dos caracteres do Bash não necessariamente obedecem à ordem que esperamos dele
```

**Exemplo com elif -> `else if` **

```
if  [ $opc -eq 1 ]
then
   inclusao    # Chama rotina de inclusão
elif [ $opc -eq 2 ]
then
   exclusao    # Chama rotina de exclusão
elif [ $opc -eq 3 ]
then
   alteracao    # Chama rotina de alteração
elif [ $opc -eq 4 ]
then
   exit
else
   echo Digite uma opção entre 1 e 4
fi

```

**Exemplo com CASE**

```
# $var é compara aos padrões PADR1..PADR2
# Caso um deles atenda, o bloco correspondente será executado
# Após o fim, o fluxo se desviará após esac

case $Var in
   PADR1) CMD1
          CMD2
          ...  
          CMDn ;; # Termina a execução
   PADR2) CMD1
          CMD2
          ...
          CMDn ;; Termina a execução
   PADRn) CMD1
          CMD2
          ...
          CMDn ;; Termina a execução
esac

##Duplo ponto e virgula não é necessário antes do esac
## Também pode-se usar
## ;;& - Após terminar a execução de um bloco, não sai do case, testará os prox. padrões
## ;& - Executa o bloco, sem testar seu padrão
```

**Caracteres Para Formação de Padrões**
```
Caractere	Significado
*			Qualquer caractere ocorrendo zero ou mais vezes
?			Qualquer caractere ocorrendo uma vez
[...]		Lista de caracteres e classes POSIX
|			"OU" lógico
```

**Com CASE, o script acima seria**
```
case $opc in
   1) inclusao ;;
   2) exclusao ;;
   3) alteracao ;;
   4) exit ;;
   *) echo Digite uma opção entre 1 e 4 #Atende qualquer coisa, menos o intervalo 1 a 4
esac

```

**Script boas vindas**
```
#!/bin/bash
# Programa que dá bomdia, boatarde, boanoite
# Baseando-se na hora do sistema
#
hora=$(date +%H)
case $hora in
        0? | 1[01]) echo Bom dia
                ;;
        1[2-7]) echo Boa tarde
                ;;
        *) echo Boa noite
                ;;
esac
exit
```

```
case "$1" in
        [[:print:]] )  echo $1 é um caractere imprimível;;&
        # O terminador ;;& testará o próximo padrão
        [[:alnum:]] )  echo $1 é um carac. alfa/numérico;;&
        [[:alpha:]] )  echo $1 é um carac. alfabético   ;;&
        [[:lower:]] )  echo $1 é uma letra minúscula    ;;&
        [[:digit:]] )  echo $1 é um caractere numérico  ;&
        # O terminador ;& executará o próximo bloco...
        %%%@@@^^^-- )  echo "************************"  ;;
        #     ... mesmo com esse padrão maluco.
esac
```
**Valida se dir existe**
```
#!/bin/bash
var=$1
case dir in
	[[ -f $var ]]) echo arquivo existe;;
	*) echo arquivo nao existe
	esac
```
