## O comando eval

* O Shell resolve todos os parâmetros de `CMD` e devolve para `eval`
* `eval` passa o `CMD` novamente para a execução 
* Logo, o comando será interpretado pelo shel 2 vezes

**Sintaxe**
```
eval CMD
```

```
var1=3
var2=var1
echo $var2 -> Saída:  var1
---------
eval echo $`echo $var2`
3

eval echo \$$var2
3

## Ou seja, na primeira passagem, var2 é expandido para var1 (seu valor)
## Na segunda passada, var1 seria expandida, produzindo o resultado esperado !
```


```
var2='ls $var1'
var1='l*'

$var2
ls: cannot access '$var1': No such file or directory

eval $var2
learq.sh  lista_frutas.sh  loop
```

```
# Lista todos os arquivos .sh, questiona se quer executar !
 
#!/bin/bash
#
# Lista numerando os programas com extensão .sh no
# diretório corrente e executa o escolhido pelo operador
#
function Erro
{
    echo "
    ************************ERRO***********************
    Uso: $0 NNN, onde NNN é uma das opções apresentadas"
    exit 1
}
clear; i=1
printf "%11s\t%s\n\n" Opção Programa
CASE='case $opt in'
for arq in *.sh
do
    printf "\t%03d\t%s\n" $i $arq
    CASE="$CASE
        "$(printf "%03d)\t %s;;" $i $arq)
    i=$((i+1))
done
printf "\t%d\t%s\n\n" 999 "Fim do programa" # Linha incluida
CASE="$CASE
        999)     exit;;                     # Linha incluida
          *)     Erro;;
esac"
read -n3 -p "Informe a opção desejada: " opt
printf -v opt '%03d' $opt		            # Preenche $opt com zeros à esquerda
echo
eval "$CASE"
```

-----
## Sinais de processos

* Sâo os sinais mandados para os processos em execução
* Podem ser listados com `kill -l`

```
kill -SIG PID
```

```
Sinais Mais Importantes
Sinal	Gerado por:
0	EXIT	Fim normal do programa
1	SIGHUP	Quando recebe um kill -HUP
2	SIGINT	Interrupção pelo teclado (<CTRL+C>)
3	SIGQUIT	Interrupção pelo teclado (<CTRL+\>)
15	SIGTERM	Quando recebe um kill ou kill -TERM
17	SIGCHLD	Fim de um processo filho(subshell)
28	SIGWINCH	Mudança no tamanho da janela gráfica
```

-----
## O trap

* Faz a monituração de um programa através dos sinais recebidos por ele

**obs**
```
Validar:
Se a linha de comandos do trap estiver entre aspas, será resolvida quando o Shell a vir pela 1ª vez;
Se a linha de comandos do trap estiver entre apóstrofos, será resolvida quando o Shell receber o sinal monitorado por ela
Para desfazer um trap "CMDs" SIG, onde CMDs é uma linha de comandos e SIG é um sinal devemos fazer trap SIG
Para matar o processo <PID> só devemos fazer kill -9 <PID> se ele não terminar com um simples kill <PID>
```

```
## Os comandos serão executados caso o programa receba o sinal S1 S2 ...
## As aspas são necessárias caso o trap possua mais de um comando associado
## Os comandos também podem ser uma função interna ou externa de outro script

trap "CMD1; CMD2; CMDn" S1 S2 S3 ... SN

ou...

trap 'CMD1; CMD2; CMDn' S1 S2 ... SN
```

** Exemplo**
```
## Trecho de um script
ftp -ivn $RemoComp << FimFTP >> /tmp/$$ 2>> /tmp/$$
    user $Fulano $Segredo
    binary
    get $Arq
FimFTP

## Em seu inicio, deve-se adicionar

## Neste caso, usou-se aspas para o shell poder interpretar já no inicio da execução
trap "rm -f /tmp/$$" 0
trap "exit" 1 2 3 15

ou ...

trap "rm -f /tmp/$$ ; exit" 0 1 2 3 15

## Caso o ftp for interrompido por algum desses sinais, antes do programa encerrar, o arquivo /tmp/$$ seria removido
## Caso na linha do trap, não houvesse exit, ao final da execução desta linha, o fluxo do programa retornaria
## para o ponto em que estava quando recebeu o sinal que originou a execução deste trap
```

* Lista os sinais aceitos
```
trap -l
```

**Ignorar interrupcoes**
```
## Caso receba ctrl+c, não faça nada
trap sinal1 sinal2 sinal3 ...etc

trap 2
ou...
trap - SINAL
```

**Exemplo**
```
trap "echo já era" 17
sleep 3 &
[1] 1577

já era
[1]+  Done                    sleep 3

## O sinal 17 é emitido cada vez que um subshell encerra sua execução
```

------
## O comando getopts

* Recupera as opções e seus argumentos de uma lista de parâmetros
* Como letras e npumeros após um sinal de `-`
* Ou seja, ele `fatia` as opções e argumentos passados a um script

**Sintaxe**
```
getopt OPCS VAR
```

* `OPC` deve explicitar uma cadeia de caracteres com todas as opções reconhecidas pelo script
* Sendo assim, `-a -b -c`, `OPC` deve ser `abc`
* Caso deseje que uma opção seja seguida por um argumento, utilize `:` depois da letra `a:bc`
* `OPC` não pode conter interrogação !
* O `getopts` interpreta que a opção `-a` tem a forma:

```
-a ARGUMENTO
-aARGUMENTO
```

* `VAR` define uma variável que cada vez que o comando `getopts` for executado
*+ receberá a proxima opção dos parâmetros posicionais e colocará em `VAR`

* `OBS`: `getops` armazena o argumento na variável `OPTARG`
* Se uma opção não possui argumento, ou o mesmo não é encontrado
* a variável `OPTARG` será matada (`unset`) 

**Exemplo**
```
#!/bin/sh

#  Execute assim:
#
#       getopts.sh -h -Pimpressora arq1 arq2
#
#  e note que as informacoes de todas as opcoes sao exibidas
#
#  A cadeia 'P:h' diz que a opcao -P eh uma opcao complexa
#+ e requer um argumento, e que -h eh uma opcao simples
#+ que nao requer argumentos.

while getopts 'P:h' OPT_LETRA
do
    echo "getopts fez a variavel OPT_LETRA igual a '$OPT_LETRA'
       OPTARG eh '$OPTARG'"
done
used_up=$((OPTIND - 1))
echo "Dispensando os primeiros \$OPTIND-1 = $used_up argumentos"
shift $used_up
echo "O que sobrou da linha de comandos foi '$*'"
```

-------
## Named pipes e Sincronização de processos

* Chamado de **FIFO** , acrônimo para `First in First Out`
* Refere a propriedade em que a ordem dos bytes entrando no pipe é a mesma que a da saída
* O name, em named pipe, na verdade é o nome de um arquivo !
* Possibilidade de iniciar 2 processos assincronos e os sincronizá-los
* Os arquivos tipo named pipes, são exibidos pelo comando `ls`


**Exemplo**
```
ls -l pipe1
->>> prw-r-r--   1   julio   dipao   0 Jan 22 23:11 pipe1|
```

```
mkfifo pipe1
ls -l pipe1
prw-r--r-- 1 root root 0 Jul 20 08:51 pipe1
```

**Experimento**
```
## Com 2 sessões abertas, faça
1º sessão
ls -l > pipe1

2º sessão
cat < pipe1

## A saída do comando da primeira sessão, sairá na segunda
```

* Named pipes permitem programas sem relação se comunicarem
* Também podem sincronizar processos
* Um processo irá `ouvir` e outro `falar`

-------
## Bloqueio de arquivos

* Bloqueia arquivos em concorrência de escrita

```
#!/bin/bash
PIPE="/tmp/pipe_contador" # arquivo named pipe
# dir onde serao colocados os arquivos contadores de cada pagina
DIR="/var/www/contador"

[ -p "$PIPE" ] || mkfifo "$PIPE"

while :
do
    for URL in $(cat < $PIPE)
    do
        FILE="$DIR/$(echo $URL | sed 's,.*/,,')"
        # OBS1: no sed acima, como precisava procurar
        #       uma barra,usamos vírgula como separador.
        # OBS2: quando rodar como daemon comente a proxima linha
        echo "arquivo = $FILE"

        n="$(cat $FILE 2> /dev/null)" || n=0
        echo $((n=n+1)) > "$FILE"
    done
done
```

# Estudar melhor toda a parte de named pipes

------
## Substituição de processos (process substituion)

* Ocorre quando se coloca um comando ou pipeline de comandos entre parenteses e `<` ou `>` a frente do parenteses a esquerda
* O conteúdo entre parênteses será executado, dando a saída como se ele viesse de um arquivo em disco, nesse caso um named pipe temporário.


**Exemplo**
```
mkfifo tubo
ls -l tubo

ls > tubo
lr-x------ 1 root root 64 Jul 21 17:46 /dev/fd/63 -> 'pipe:[65152]' << ## numero do processo no kernel

1º cat <(ls -l) # ls -l se tornou um arquivo de leitura do cat
2º ls -l <(cat) # cat tornou-se um arquivo para LS lista Validação ( é um named pipe /dev/fd/63)  

# 1º O comando ls -l será executado em um subshell
# Redirecionará a saída para um named pipe temporário
# O shell cria, nomeia e o remove

##Outras dicas:
/dev/fd/0 entrada primaria
/dev/fd/1 saída primaria
/dev/fd/2erros
```

**Validando**
```
# /dev/fd/63 dispositivo logico

ls -l >(cat)
l-wx------ 1 root root 64 Jul 20 10:01 /dev/fd/63 -> 'pipe:[23750]'

```

**Exemplo pratico**

* Validar backup entre diretorios
```
cmp <(cat dir/*) <(cat dir.bkp/*) >/dev/null || echo backup furado
```

* Lista arquivos, numere-os e ao final exiba o total de arquivos do dir corrente
```
while read arq
do
	((i++)) # assim nao eh necessario inicializar i
    echo "$i: $arq"
done < <(ls)
echo "No diretorio corrente (`pwd`) existem $i arquivos"
```

**O exemplo acima poderia ser feito com**
```
ls | cat -n -
cat -n <(ls)
```

**Outro Exemplo**
```
cat <(seq -s ' ' 5)  

ou 

cat <(seq 5 | tr '\n' ' ') 
```






