# Loops

* Blocos de comandos, podem estar entre instruções `if then else`, `case`, `do done`
* Em Shell, todas as intruções de loop executam os blocos entre `do e done`
* As instruções de loop são `for, while, until`

--------
# Usando o FOR

* For em shell é `builtin`, faz parte do código fonte
* Assume cada um dos valores da lista `VAL1, VAL2 ..etc`
* E para cada um desses valores, executa um bloco de comandos `CMD1, CMD2 ...etc`

**Sintaxe**
```
for VAR in VAL1 VAL2 ... VALn
do
    CMD1
    CMD2
    CMD3
done

# No lugar de do e done, poderia-se ter usado chaves { ... }
```


**Exemplos**

* Lista diretorios, substituindo espaço por :
```
#!/bin/bash
# Aprendendo for
#
for Arq in *
do
    echo -n $Arq:     # A opcao -n é para nao saltar linha (omite o ENTER)
done

```

**Com strings**
```
for palavra in Curso de shell
do
    echo "$palavra"
done
``` 
-----
# Segunda sintaxe FOR (sem IN)

* `$var` assumirá cada um dos parâmetros passados para o programa.
* './script.sh param1 param2 paramN'

**Sintaxe**
```
for VAR
do
    CMD1
    CMD2
    CMDn
done
```

```
#!/bin/bash
# cat musicas
# album 1^Artista1~Musica1:Artista2~Musica2

# Recebe parte dos nomes de musicas como parâmetro e
# lista os intérpretes. Se o nome for composto, deve
# ser passado entre aspas.
# ex. "Eu nao sou cachorro, não" "Churrasquinho de Mãe"
#
if  [ $# -eq 0 ]
then
    echo Uso: $0 musica1 [musica2] ... [musican]
    exit 1
fi
IFS="
:"
for Musica
do
    echo $Musica
    Str=$(grep -i "$Musica" musicas) || {
        echo "    Não encontrada"
        continue
    }
    for ArtMus in $(echo "$Str" | cut -f2 -d^)
    do
        echo "    $ArtMus" | grep -i "$Musica" | cut -f1 -d~
    done
done
```

## Contagem com FOR
```
$ for i in $(seq 9)
> do
>     echo -n "$i "
> done
1 2 3 4 5 6 7 8 9

$ for i in {1..9}
> do
>     echo -n "$i "
> done
1 2 3 4 5 6 7 8 9

$ for i in $(seq 3 9)
> do
>     echo -n "$i "
> done
3 4 5 6 7 8 9

$ for i in $(seq 0 3 9)
> do
>     echo -n "$i "
> done
0 3 6 9

```
------
# Terceiro tipo de FOR 

Onde:

* VAR=INI - Significa que a variável VAR será inicializada com o valor INI;
* COND - Siginifica que o loop ou laço do for será executado enquanto VAR não atingir a condição COND;
* INCR - Significa o incremento (positivo ou negativo) que a variável VAR sofrerá em cada passada do loop.


**Sintáxe:**

```
for ((VAR=INI; COND; INCR))
do
    CMD1
    CMD2
    CMDn
done
```

**Exemplo**
```
for ((i=1; i<=9; i++)); do echo -n "$i "; done
```

* O uso de parênteses duplos, chama o interpretador aritmético do shell
* Interpreta qualquer literal, como um nome de variável

**O comando LET**

* `for`, levou $i até 19, mas
* Como o incremento `let` estava do `echo`, a listagem foi até 20.
* Ou seja, o incremento saiu do escopo do for, e passou para o bloco de comandos
* Foi necessário zerar a variável (unset $var)

```
for ((; i<=19;)); do let i++; echo -n "$i "; done
Saída: 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
```

* `Let` pode ser usado separadamente de loops
```
let j++
echo $j
1
let j++
echo $j
2
``` 


**Outro Exemplo**

```
#Listar arquivos e numerá-los

for arq in *; do let i++; echo "$i -> $arq"; done
ou...
for arq in *; do echo "$((++i)) -> arq"; done

Saída:
1 -> arq1
2 -> arq2
3 -> arq3
4 -> arq4
```

**Contar as palavras de um arquivo**
```
unset i; unset j
for i in $(cat arq); do echo "$((j++)) -> $i "; done

ou...

#!/bin/bash
# Conta a qtd de palavras de um arquivo
# Passando-o via parâmetro
# As palavras podem estar separadas por espaços ou new lines
#
if [ $# -ne 1 ]
then
        echo uso: $0 /caminho/do/arquivo # $0 tem o nome do programa!
        exit 2
fi
cont=0
for palavra in $(cat $1)
do
        cont=$((cont+1)) # Ou `let cont ++`
done
echo O arquivo $1 tem $cont palavras.

ou...

wc -w < arquivo 
```

-----
# Inter Field Separator (IFS)

* Variável do sistema `$IFS`
* Separador de campos
* O For utiliza os valores dessa variável, e nao listas separadas por brancos

**OBS**

```
## Diversas intruções além do for usam esta variável, assim sendo,
quando você alterá-la, copie-a antes para outra variável e, após usá-la, volte o seu valor original.

## No bash, pode-se usar as sequências de escape (escape sequences), aquelas começadas com uma contrabarra, 
com a seguinte sintaxe: VAR=$'\L' onde L é um caractere válido numa sequência de escape

## Valor padrão (Espaço <TAB> <ENTER>)
IFS=$' \t\n'
## Validar
echo -n "$IFS" | od -h
```

**Teste**
```
echo "$IFS" | od -h
0000000 0920 0a0a
0000004

Conteúdo da Variável $IFS
Hexadecimal	Significado
09			<TAB>
20			<ESPAÇO>
0a			<ENTER>
```

**Exemplos**

* A variavel IFS foi setada para `ENTER :` pois
* É ele que separa os blocos `ArtistaN~MusicasN`
* Desta forma, a variavel `$ArtMus`, recebe cada um desses blocos
* Com isso, o `cut` sempre exibira o segundo campo

```
cat musicas
album 1^Artista1~Musica1:Artista2~Musica2
album 2^Artista3~Musica3:Artista4~Musica4
album 3^Artista5~Musica5:Artista6~Musica6
album 4^Artista7~Musica7:Artista1~Musica3
album 5^Artista9~Musica9:Artista10~Musica10

```

```
#!/bin/bash
# Dado um artista, mostra as suas musicas
# versao 2

if  [ $# -eq 0 ]
then
    echo Voce deveria ter passado pelo menos um parametro
    exit 1
fi

IFS="
:"

for ArtMus in $(cut -f2 -d^ musicas)
do
    echo "$ArtMus" | grep -i "^$@~" > /dev/null && echo $ArtMus | cut -f2 -d~
done
```

-------
# Expansões aritméticas (interpretador aritmético)

* Devem ser constituidas por números, ou operadores abaixo:

```
Expansão 		Aritmética
Operadores		Resultado
id++, id--		pós-incremento e pós-decremento de variáveis
++id, -?id		pré-incremento e pré-decremento de variáveis
**				exponenciação
*, /, %			multiplicação, divisão, resto da divisão
+,-				adição, subtração
<=, >=, <, >	comparação
==, !=			igualdade, desigualdade
&&				E lógico
||				OU lógic
```

------
# O comando while

* O while testa um comando e executa um bloco de instruções enquanto este comando for bem sucedido
* Para testar condições, utilize `while` testando o comando `teste`
* Como observado no comando `if`

```
while COMANDO	# Pode ser uma ou diversas instruções, interligadas por pipes
do
   CMD1
   CMD2
   ...
   CMDn
done
```


**Exemplos**
```
while who | grep -q whoami; do sleep 1; echo teste; done
```


**Monitorar processos em Background**
```
#!/bin/bash
# Executa e monita um processo em background
# Poderia-se usar while ps | grep $! > /dev/null
#
$1 &    # Coloca em bg
while ps | grep -q $!
do
        sleep 5
done
echo Fim do processo $1

```

**Dicas**
```
read -p "prompt de leitura" var
```

```
#!/bin/bash
# Cadastra CDs (versao 4)
#
clear
read -p "Título do Álbum: " Tit
[ "$Tit" ] || exit 1    # Fim da execução se título vazio
if grep "^$Tit\^" musicas > /dev/null # Pesquisa linhas começadas (regex ^) por $Tit seguida de um circunflexo
then
    echo Este álbum já está cadastrado
    exit 1
fi
Reg="$Tit^"
Cont=1
oArt»
while true
do
    echo Dados da trilha $Cont:
    read -p "Música: " Mus
    [ "$Mus" ] || break       # Sai se vazio
    read -p "Artista: $oArt // " Art
    [ "$Art" ] && oArt="$Art" # Se vazio Art anterior
    Reg="$Reg$oArt~$Mus:"     # Montando registro
    Cont=$((Cont + 1))
    # A linha anterior tb poderia ser Cont=$((Cont++)) ou let Cont++
done
echo "$Reg" >> musicas
sort musicas -o musicas
```

-----
# O Comando until

* Funciona igual ao `while`, porém, ao contrário
* `while` executa bloco de instruções do loop `enquanto` um comando for bem sucedido
* `until` executa o bloco do loop `até que` o comando seja bem sucedido


**Sintaxe**
```
until COMANDO
do
   CMD1
   CMD2
   ...
   CMDn
done
``` 


**Exemplos**
```
# O comando será bem sucedido, quando o user Julio for encontrado no `who`
# Até que isso aconteça, o comando sleep colocará o programa em espera por 30s

$ cat chegada.sh
#!/bin/bash

until who | grep julio
do
    sleep 30
done
echo $(date "+ Em %d/%m às %H:%Mh") >> relapso.log
```

```
#!/bin/bash
# Cadastra CDs (versao 5)
#
Para»
until [ "$Para" ]
do
    clear
    read -p "Título do Álbum: " Tit
    if  [ ! "$Tit" ] # Se titulo vazio...
    then
        Para=1     # Liguei flag de saída
    else
        if grep "^$Tit\^" musicas > /dev/null
        then
            echo Este álbum já está cadastrado
            exit 1
        fi
        Reg="$Tit^"
        Cont=1
        oArt»
        while [ "$Tit" ]
        do
            echo Dados da trilha $Cont:
            read -p "Música: " Mus
            [ "$Mus" ] || break       # Sai se vazio
            read -p "Artista: $oArt // " Art
            [ "$Art" ] && oArt="$Art" # Se vazio Art anterior
            Reg="$Reg$oArt~$Mus:"     # Montando registro
            Cont=$((Cont + 1))  
            # A linha anterior tb poderia ser Cont=$((Cont++)) ou let Cont++
        done
        echo "$Reg" >> musicas
        sort musicas -o musicas
    fi
done
```

-------
# Atalhos de loop (continue e break)

* `break` aborta um loop de maneira controlada
* `continue` continua um loop antes de chegar ao `done`


**Sintaxe**

```
break [INT]

e

continue [INT]


# O valor default de int é 1, representa a quantidade dos loops mais internos sobre os quais os comandos irão atuar
``` 

**Exemplos**

**Backup de arquivos deletados**

```
#/bin/bash
#
#  Salvando Copia de Arquivo Antes de Remove-lo
#

if  [ $# -eq 0 ]  #  Tem de ter um ou mais arquivos para remover
then
    echo "Erro -> Uso: erreeme arq [arq] ... [arq]"
    echo "  O uso de metacaracteres é permitido. Ex. erreeme arq*"
    exit 1
fi

MeuDir="/tmp/$LOGNAME" # Variavel do sist. Contém o nome do usuário.
if  [ ! -d $MeuDir ]   # Se não existir o meu diretório sob o /tmp...
then
    mkdir $MeuDir      # Vou cria-lo
fi

if  [ ! -w $MeuDir ]   # Se não posso gravar no diretório...
then
    echo Impossivel salvar arquivos em $MeuDir. Mude permissao...
    exit 2
fi

Erro=0                 # Variavel para indicar o cod. de retorno do prg
for Arq                # For sem o "in" recebe os parametros passados
do
    if  [ ! -f $Arq ]  # Se este arquivo não existir...
    then
   echo $Arq nao existe.
   Erro=3
   continue       # Volta para o comando for
    fi

 DirOrig=`dirname $Arq` # Cmd. dirname informa nome do dir de $Arq
    if  [ ! -w $DirOrig ]  # Verifica permissão de gravacaoo no diretório
    then
   echo Sem permissao de remover no diretorio de $Arq
   Erro=4
   continue           # Volta para o comando for
    fi

    if  [ "$DirOrig" = "$MeuDir" ] # Se estou "removendo da lixeira"...
    then
   echo $Arq ficara sem copia de seguranca
   rm -i $Arq         # Pergunta antes de remover
   [ -f $Arq ] || echo $Arq removido  # Será que o usuario removeu?
   continue
    fi

    cd $DirOrig      #  Guardo no fim do arquivo o seu diretorio
    pwd >> $Arq      #+ original para usa-lo em um script de undelete
    mv $Arq $MeuDir  # Salvo e removo
    echo $Arq removido
done
exit $Erro # Passo eventual numero do erro para o codigo de retorno

```


**Restaurando arquivos deletados**

```
#!/bin/bash
#
# Restaura arquivos deletados via erreeme
#

if [ $# -eq 0 ]
then
    echo "Uso: $0 <Nome do Arquivo a Ser Restaurado>"
    exit 1
fi
# Pega nome do diretório original na última linha
Dir=$(tail -1 /tmp/$LOGNAME/$1)
# O grep -v exclui última linha e cria o
# arquivo com diretorio e nome originais
grep -v $Dir /tmp/$LOGNAME/$1 > $Dir/$1
# Remove arquivo que jah estava moribundo
rm /tmp/$LOGNAME/$1
```
