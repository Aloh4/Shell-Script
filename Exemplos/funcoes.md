## Dicas gerais

```
funcoes podem ser feitas direto no shell, basta apenas chama-la depois
function nome { comandos }
function nome { declare -l variaveis ; comandos }
function nome { declare -i variaveis ; comandos }

oque faz o declare? faz a variavel ser local
```

## Funções internas

* São funções declaradas dentro do próprio script
* Parâmetros passados para um script, não se confudem com aqueles passados as funções
* Ou seja, `$1` de um script é diferente do `$1` de uma de suas funções
* Variáveis podem ser criadas como local, tendo efeito somento dentro da função, e por isso, permite os parâmetros posicinais dentro da função `$1..$2..$n`
* Pode-se fazer `funcao || echo Erro na função funcao`
* `exit` encera o programa
* `return (entre 0 e 255)` passa o codigo de retorno como o `exit`, mas encerra somente a função


**Sintaxe**
```
nome_funcao ()
{
CMD1
CMD2
...
}

-- ou --
nome_funcao
	{
	...
	}

```

**Estruturação**
```
Variáveis Globais
Funções
Corpo do Programa
```

**Exemplo**

1 - O texto da pergunta que será feita e será posicionada 3 linhas acima do fim da tela;
2 - A resposta que será tratada como valor padrão (default);
3 - A outra resposta possível.

```
# Recebendo um parâmetro
Pergunta ()
{
  #  A função recebe 3 parâmetros na seguinte ordem:
  #+ $1 - Mensagem a ser exibida na tela;
  #+ $2 - Valor a ser aceito com resposta default;
  #+ $3 - O outro valor aceito.
  #  Supondo que $1=Aceita, $2=s e $3=N, a linha a
  #+ seguir colocaria em Msg o valor "Aceita? (S/n)"
  local Msg="$1 ($(tr a-z A-Z <<< $2)/$(tr A-Z a-z <<< $3))? "
  local TamMsg=${#Msg}
  local LinhaMesg=$(($(tput lines) - 3)) # 3 linhas acima do fim da tela
  local TotCols=$(tput cols)
  local Col=$(((TotCols - TamMsg) / 2))  # Centra Msg na linha
  tput sc                                # Salva posição do cursor no corpo do script
  tput cup $LinhaMesg $Col
  read -p "$Msg" -n1 SN
  [ ! $SN ] && SN=$2                     # Se vazia coloca default em SN
  echo $SN | tr A-Z a-z                  # A saída de SN será em minúscula
  tput cup $LinhaMesg $Col; tput el      # Apaga msg da tela
  tput rc                                # Restaura cursor para posição em que estava
  return                                 # Sai da função com $? igual a zero
}


# Recebendo vários parâmetros
function MandaMsg
    {
    #  A função recebe somente um parâmetro
    #+ com a mensagem que se deseja exibir,
    #+ para não obrigar ao programador passar
    #+ a msq entre aspas, usaremos $* (todos
    #+ os parâmetro, lembra?) e não $1.
    (( $# >=1 )) || {
        echo "Você esqueceu de passar a msg"
        return 1
    }
    local Msg="$*"
    local TamMsg=${#Msg}
    local Col=$(((TotCols - TamMsg) / 2)) # Centra msg na linha
    tput sc                               # Salva a posição que o cursor está
    tput cup $LinhaMesg $Col
    read -p "$Msg" -n 1
    tput cup $LinhaMesg $Col; tput el     # Apaga msg da tela
    tput rc                               # Devolve o cursor para a
                                          # posição anterior à função
    return                                # Sai da função
    }
```

**Funções incluídas para popular arquivo**
```
#!/bin/bash
# Cadastra CDs (versao 6)
#

# Área de variáveis globais
LinhaMesg=$((`tput lines` - 3))  # Linha que msgs serão passadas para operador
TotCols=$(tput cols)             # Qtd colunas da tela para enquadrar msgs

# Área de funções
Pergunta ()
{
    #  A função recebe 3 parâmetros na seguinte ordem:
    #+ $1 - Mensagem a ser exibida na tela;
    #+ $2 - Valor a ser aceito com resposta default;
    #+ $3 - O outro valor aceito.
    #+ Supondo que $1=Aceita?, $2=s e $3=N, a linha a
    #+ seguir colocaria em Msg o valor "Aceita? (S/n)"
    local Msg="$1 ($(tr a-z A-Z <<< $2)/$(tr A-Z a-z <<< $3))? "
    local TamMsg=${#Msg}
    local LinhaMesg=$(($(tput lines) - 3))
    local TotCols=$(tput cols)
    local Col=$(((TotCols - TamMsg) / 2))  # Centra Msg na linha
    tput sc                                # Salva a posição que o cursor está
    tput cup $LinhaMesg $Col
    read -p "$Msg" -n1 SN
    [ ! $SN ] && SN=$2                     # Se vazia coloca default em SN
    echo $SN | tr A-Z a-z                  # A saída de SN será em minúscula
    tput cup $LinhaMesg $Col; tput el      # Apaga msg da tela
    tput rc                                # Restaura curso para posição em que estava
    return                                 # Sai da função
}
function MandaMsg
    {
    #  A função recebe somente um parâmetro
    #+ com a mensagem que se deseja exibir,
    #+ para não obrigar ao programador passar
    #+ a msq entre aspas, usaremos $* (todos
    #+ os parâmetro, lembra?) e não $1.
    local Msg="$*"
    local TamMsg=${#Msg}
    local Col=$(((TotCols - TamMsg) / 2)) # Centra msg na linha
    tput sc                               # Salva a posição que o cursor está
    tput cup $LinhaMesg $Col
    read -p "$Msg" -n 1
    tput cup $LinhaMesg $Col; tput el # Apaga msg da tela
    tput rc                           # Devolve o cursor para
                                      # a posição anterior à função
    return                            # Sai da função
    }

# O corpo do programa propriamente dito começa aqui
clear
echo "
                                     Inclusao de Músicas
                                     ======== == =======


                     Título do Álbum:
                                           | Este campo foi
                     Faixa:               <  criado somente para
                                           | orientar o preenchimento
                     Nome da Música:

                     Intérprete:"          # Tela montada com um único echo
while true
do
    tput cup 5 38; tput el                 # Posiciona e limpa linha
    read Album
    [ ! "$Album" ] &&                      # Operador deu <ENTER>
        {
        Pergunta "Deseja Terminar" s n
        [ $SN = "n" ] && continue          # Agora só testo a caixa baixa
        clear; exit                        # Fim da execução
        }
    grep -iq "^$Album\^" musicas 2> /dev/null &&
        {
        MandaMsg Este álbum já está cadastrado
        continue                        # Volta para ler outro álbum
        }
    Reg="$Album^"                       # $Reg receberá os dados de gravação
    oArtista=                           # Guardará artista anterior
    while true
    do
        ((Faixa++))
        tput cup 7 38
        echo $Faixa
        tput cup 9 38                   # Posiciona para ler musica
        read Musica
        [ "$Musica" ] ||                # Se o operador tiver dado <ENTER>...
            {
            Pergunta "Fim de Álbum?" s n
            [ "$SN" = n ] && continue      # Agora só testo a caixa baixa
            break                          # Sai do loop para gravar dados
            }
        tput cup 11 38                           # Posiciona para ler Artista
        [ "$oArtista" ]&& echo -n "($oArtista) " # Artista anterior é default
        read Artista
        [ "$Artista" ] && oArtista="$Artista"
        Reg="$Reg$oArtista~$Musica:"               # Montando registro
        tput cup 9 38; tput el                     # Apaga Musica da tela
        tput cup 11 38; tput el                    # Apaga Artista da tela
    done
    echo "$Reg" >> musicas                 # Grava registro no fim do arquivo
    sort musicas -o musicas                # Classifica o arquivo
done
```

-----
## Funções Externas

* Permite utilizar uma função de um determinado programa, em outro
* Traz uma cópia dessa função, sem precisa reescrevê-la
* Em shell, utiliza-se o `source`, para esse efeito

**Dicas**
* O `bash_profile` é um dos programas que são automaticamente executados no momento do login
* Deixa o ambiente preparado para o usuário `/home/$USER/.profile` 
* Por padrão, ele não tem permissões de execução
* Scripts executados `normalmente`, criam subshells e morrem (Com permissão de execução)
* Utilizando o comando `source` ou  `.` (não `./`), não são gerados subshells para executar o programa que lhe é passado como parâmetro
* `source programa`
* Com esse artifício, pode-se tratar funções como rotinas externas


**Testando o comando `source`**
```
cat script_bobo.sh
#!/bin/bash
ls
pwd
cd ..
ls
```

**Execução normal**
```
root@whoami:~# ./script_bobo.sh
10porpag.sh            arq.bkp            dia        hora             script1            teste12
189.4.80.185.config    arqs               dire       lista_frutas.sh  script_bobo.sh     teste12333
189.61.129.71.config   arquivo.txt        doa        loop             shell              teste2020-02-071539
191.186.112.10.config  B                  E          monbg.sh         sites              teste.2020-02-071540
A                      C                  emails     musicas2         sites.bkp          teste-for3.sh
aloha                  chegada.sh         er         nome             stringsed          teste-if
aniv                   conta-palavras.sh  exclui     nome2            stringsed.bkp      teste.teste
arq                    cut                exclui2    numeros          stringsed.bkp.bkp  teste.txt
arq1                   D                  F          numeros2         teste              teste-xargs
arq2                   data               frutas     nums             teste1             valores
arq3                   datas              guilherme  relapso.log      teste111           work
/home/whoami
somefile  teste2  whoami
root@whoami:~#
```
**Execução com source**
```
root@whoami:~# source script_bobo.sh
10porpag.sh            arq.bkp            dia        hora             script1            teste12
189.4.80.185.config    arqs               dire       lista_frutas.sh  script_bobo.sh     teste12333
189.61.129.71.config   arquivo.txt        doa        loop             shell              teste2020-02-071539
191.186.112.10.config  B                  E          monbg.sh         sites              teste.2020-02-071540
A                      C                  emails     musicas2         sites.bkp          teste-for3.sh
aloha                  chegada.sh         er         nome             stringsed          teste-if
aniv                   conta-palavras.sh  exclui     nome2            stringsed.bkp      teste.teste
arq                    cut                exclui2    numeros          stringsed.bkp.bkp  teste.txt
arq1                   D                  F          numeros2         teste              teste-xargs
arq2                   data               frutas     nums             teste1             valores
arq3                   datas              guilherme  relapso.log      teste111           work
/home/whoami
somefile  teste2  whoami
root@whoami:/home#
```


**Script com chamada de funções externas**
* Para chamada das funções são usados `source` e `.`, por motivos didáticos
* Prefira `source`
```
#!/bin/bash
# Cadastra CDs (versao 7)
#

# Área de variáveis globais
LinhaMesg=$((`tput lines` - 3))  # Linha que msgs serão dadas para operador
TotCols=$(tput cols)             # Qtd colunas da tela para enquadrar msgs

# O corpo do programa propriamente dito começa aqui
clear
echo "
                                     Inclusao de Músicas
                                     ======== == =======


                     Título do Álbum:
                                           | Este campo foi
                     Faixa:               <  criado somente para
                                           | orientar o preenchimento
                     Nome da Música:

                     Intérprete:"       # Tela montada com um único echo
while true
do
    tput cup 5 38; tput el              # Posiciona e limpa linha
    read Album
    [ ! "$Album" ] &&                   # Operador deu
        {
        source pergunta.func "Deseja Terminar" s n
        [ $SN = "n" ] && continue       # Agora só testo a caixa baixa
        clear; exit                     # Fim da execução
        }
    grep -iq "^$Album\^" musicas 2> /dev/null &&
        {
        . mandamsg.func Este álbum já está cadastrado
        continue                        # Volta para ler outro álbum
        }
    Reg="$Album^"                       # $Reg receberá os dados de gravação
    oArtista=                           # Guardará artista anterior
    while true
    do
        ((Faixa++))
        tput cup 7 38
        echo $Faixa
        tput cup 9 38                   # Posiciona para ler musica
        read Musica
        [ "$Musica" ] ||                # Se o operador tiver dado ...
            {
            . pergunta.func "Fim de Álbum?" s n
            [ "$SN" = n ] && continue   # Agora só testo a caixa baixa
            break                       # Sai do loop para gravar dados
            }
        tput cup 11 38                           # Posiciona para ler Artista
        [ "$oArtista" ] && echo -n "($oArtista) "  # Artista anter. é default
        read Artista
        [ "$Artista" ] && oArtista="$Artista"
        Reg="$Reg$oArtista~$Musica:"               # Montando registro
        tput cup 9 38; tput el                     # Apaga Musica da tela
        tput cup 11 38; tput el                    # Apaga Artista da tela
    done
    echo "$Reg" >> musicas                 # Grava registro no fim do arquivo
    sort musicas -o musicas                # Classifica o arquivo
done
```

**As mesmas funções em arquivos separados**

* As variáveis não são mais declaradas como local, essa diretiva só pode ser usada no corpo de funções
* O comando `return` não é mais utilizado mas poderia estar sem causar alterações na lógica
* `return` e `return 0` significam rotina bem sucedida
* Rotinas externas não podem utilizar `exit`, por estar sendo executada no mesmo shell que o script `chamador`, o exit encerraria este shell e a execução do script
* ATENÇÃO: `O SHELL QUE INTERPRETA AS ROTINAS E O MESMO QUE INTERPRETA O SCRIPT`
* Sempre utilizar no início do script e fazer as chamadas depois `source function.sh` 

```
$ cat pergunta.func
#  A função recebe 3 parâmetros na seguinte ordem:
#  $1 - Mensagem a ser dada na tela
#  $2 - Valor a ser aceito com resposta default
#  $3 - O outro valor aceito
#  Supondo que $1=Aceita?, $2=s e $3=n, a linha
#  abaixo colocaria em Msg o valor "Aceita? (S/n)"
Msg="$1 (`echo $2 | tr a-z A-Z`/`echo $3 | tr A-Z a-z`)"
TamMsg=${#Msg}
Col=$(((TotCols - TamMsg) / 2))    # Centra msg na linha
tput cup $LinhaMesg $Col
echo "$Msg"
tput cup $LinhaMesg $((Col + TamMsg + 1))
read -n1 SN
[ ! $SN ] && SN=$2                 # Se vazia coloca default em SN
echo $SN | tr A-Z a-z              # A saída de SN será em minúscula
tput cup $LinhaMesg $Col; tput el  # Apaga msg da tela

$ cat mandamsg.func
# A função recebe somente um parâmetro
# com a mensagem que se deseja exibir,
# para não obrigar ao programador passar
# a msq entre aspas, usaremos $* (todos
# os parâmetro, lembra?) e não $1.
Msg="$*"
TamMsg=${#Msg}
Col=$(((TotCols - TamMsg) / 2))    # Centra msg na linha
tput cup $LinhaMesg $Col
echo "$Msg"
read -n1
tput cup $LinhaMesg $Col; tput el  # Apaga msg da tela
```
