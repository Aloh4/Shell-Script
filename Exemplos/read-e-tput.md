# Formatando com TPUT

* Formata a exibição na tela
* Posiciona cursor, verifica qtd de linhas e colunas, apaga campos, etc
* Alguns atributos podem não funcionar, caso o modelo de terminal não conter as definições na variável `$TERM`


**Principais opções**

```
Principais Opções do Comando tput
Opções do tput		Efeito
cup 				LIN COL	CUrsor Position - Posiciona o cursor na linha LIN e coluna COL. A origem é zero
home				O mesmo que tput cup 0 0, isto é, coloca o cursor no vertice superior esquerdo da tela
bold				Coloca a tela em modo de ênfase (negrito)
rev					Coloca a tela em modo de vídeo reverso
smso				Idêntico ao anterior
smul				A partir desta instrução, os caracteres teclados aparecerão sublinhados na tela
blink				Os caracteres teclados aparecerão piscando (nem todas as definições de terminais aceitam esse)
invis				A partir desse ponto, nada do que for digitado ou mandado para a tela aparecerá no vídeo (Obs: Inseguro para ler senhas)
sgr0				Após usar um dos atributos acima, use este para restaurar a tela ao seu modo normal
reset				Limpa o terminal e restaura suas definições de acordo com o terminfo ou seja, o terminal volta ao padrão definido pela variável $TERM
lines				Devolve a quantidade de linhas da tela no momento da instrução
cols				Devolve a quantidade de colunas da tela no momento da instrução
el					Erase Line - Apaga a linha a partir da posição do cursor
ed					Erase Display - Apaga a tela a partir da posição do cursor
il N				Insert Lines - Insere N linhas a partir da posição do cursor
dl N				Delete Lines - Remove N linhas a partir da posição do cursor
ech N				Erase CHaracters - Apaga N caracteres a partir da posição do cursor
sc					Save Cursor position - Salva a posição do cursor
rc					Restore Cursor position - Coloca o cursor na posição marcada pelo último sc
flash				Faz um vídeo reverso muito rápido. Ideal para chamar a atenção sobre erros ou ocorrências interessantes (nem todas as definições de terminais aceitam essa opção)
smcup				Bate uma foto atual da tela e a salva para posterior recuperação
rmcup				Repõe na tela a foto batida com o comando tput smcup
setab N				Altera a cor de fundo (SET Background). N varia entre 0 e 7, 9 restaura cor default
setaf N				Altera a cor da fonte (SET Foreground). N varia entre 0 e 7, 9 restaura cor default
```



**Exemplos**

```
# it = initial tab
# Refer: https://www.dicas-l.com.br/download/The_GNU_code-tput-Command.pdf
tput it
8
```

**Testando opções**
```
#!/bin/bash
# Script bobo para testar
# o comando tput (versao 1)

Colunas=`tput cols`           #  Salvando quantidade colunas
Linhas=`tput lines`           #  Salvando quantidade linhas
Linha=$((Linhas / 2))         #  Qual eh a linha do meio da tela?
Coluna=$(((Colunas - 9) / 2)) #  Calculando coluna para centrar na tela
tput sc                       #  Salvando posição do cursor
tput cup $Linha $Coluna       #  Posicionando para escrever
tput rev                      #  Video reverso
echo Alô Mundo
tput sgr0                     #  Restaura video ao normal
tput rc                       #  Restaura cursor aa posição original
```


**Quantidade de caracteres de uma variável
```
$ var=Papo
$ echo ${#var}
4
$ var="Papo de Botequim"
$ echo ${#var}
16
``` 

**Parametros e TPUT**
```
#!/bin/bash
# Script bobo para testar
# o comando tput (versao 2)

Colunas=`tput cols`               #  Salvando quantidade colunas
Linhas=`tput lines`               #  Salvando quantidade linhas
Linha=$((Linhas / 2))             #  Qual eh a linha do meio da tela?
Coluna=$(((Colunas - ${#1}) / 2)) #  Centrando a mensagem na tela
tput sc                           #  Salvando posicao do cursor
tput cup $Linha $Coluna           #  Posicionando para escrever
tput rev                          #  Video reverso
echo $1							  # Poderia ser $*, para coletar todos os parametros, sem quebrar com espaço
tput sgr0                         #  Restaura video ao normal
tput rc                           #  Restaura cursor aa posição original
```

**Tirando fotografias da tela**
```
seq 2000 | xargs                         #  Sujando a tela
Lin=$(($(tput lines) / 2))               #  Calculando linha e coluna centrais da tela
Col=$(($(tput  cols) / 2))
tput cup $Lin; tput el; tput setaf 1      #  Posicionando, apagando a linha central e colorindo
echo "Em 10 segundos essa tela será fotografada e se apagará"
tput civis                               # Cursor invisível para melhorar apresentação
for ((i=10; i!=0; i--))
{
    tput cup $Lin $Col; tput el          # Posiciona no centro da tela e limpa núm anterior
    echo $i
    sleep 1
}
tput smcup                               #  Tirando uma foto da tela
clear                                    #  Poderia ter usado tput reset
tput cup $Lin
echo "Dentro de 10 segundos a tela inicial será recuperada"
for ((i=10; i>0; i--))
{
    tput cup $Lin $Col; tput el          #  Posicionou no centro da tela
    echo $i
    sleep 1
}
tput rmcup                               #  Restaurou a foto
tput cvvis;tput setaf 9                  #  Restaurou o cursor e cor
```

**Script para ver as combinações de cores de fonte e fundo**
**`tput setaf` `tput setab`**
* Com o `tput bold` ativo, tem-se 7 cores adicionais

```
#!/bin/bash
for ((b=0; b<=7; b++))
{
    tput setab  9; tput setaf  9; echo -n "|"
    for ((f=0; f<=7; f++))
    {
        tput setab $b; tput setaf $f; echo -n " b=$b f=$f "
        tput setab  9; tput setaf  9; echo -n "|"
    }
    echo
}
tput setab 9; tput setaf 9
```

```
Cores do terminal
Símbolo	Cor
0		Preto
1		Azul
2		Verde
3		Ciano
4		Vermelho
5		Magenta
6		Amarelo
7		Branco
9		Volta a cor defaul
```


-----
# O comando READ

* O comando read, recebe uma lista separada por espaços em branco
* Coloca cada item da lista, em uma variável
* Se a quantidade de variáveis for menor que a quantidade de itens
* A última variável recebe o restante

**Exemplo**

```
$ read var1 var2 var3
Curso de Shell   # Entrada do read digitada na tela
$ echo $var1
Curso
$ echo $var2
de
$ echo $var3
Shell
$ read var1 var2
Curso de Shell
$ echo $var1
Curso
$ echo $var2
de Shell
```


* Esse comportamento pode ser modificado pela variável `$IFS (Inter Field Separador)`
```
$ oIFS="$IFS"
$ IFS=:                # Salvando o IFS. Como estamos no prompt, temos de restaurá-lo
$ read var1 var2 var3
Curso de Shell         # Entrada do read digitada na tela
$ echo $var1
Curso de Shell
$ echo $var2
$ echo $var3
$ read var1 var2 var3
Curso:de:Shell         # Entrada do read digitada na tela
$ echo $var1
Curso
$ echo $var2
de
$ echo $var3
Shell
$ IFS="$oIFS"          # Restaurando o valor original do IFS
```

* Alguns comandos de shell, aceitam que algumas variáveis tenham um valor atribuído que perdure
* somente ao longo do tempo de execução do comando 

**Exemplo**
```
$IFS+read
e
$LANG+date
```

**Exemplo `$LANG+date`**
```
$ echo $LANG                    # Variável que define o idioma
pt_BR.UTF-8                     # Português do Brasil com acentuação
$ date
Qua Abr  5 17:06:03 BRT 2017    # Data em Português
$ LANG=en_EN date               # LANG teve seu valor alterado (Perceba o espaço em branco !)
                                # para Inglês para executar o comando date
Wed Apr  5 17:06:31 BRT 2017    # Comando date gerou data em Inglês
$ echo $LANG
pt_BR.UTF-8                     # Ao fim do comando, valor volta ao original
```


**Exemplo IFS**

* Here string redirecionou a saída de grep para read
* Leu todos os campos de uma vez


```
oIFS="$IFS"
IFS=: read lname lixo uid gid coment home shell <<< $(grep root /etc/passwd) #(Perceba o espaço em branco !)
echo -e "$lname\n$uid\n"
root
0

echo -e "$lname\n$uid\n$lixo\n$uid\n$gid\n$coment\n$home\n$shell"
root
0
x
0
0
root
/root
/bin/bash

IFS=$oIFS
```


**Opções READ**
```
Opções do comando read no Bash
Opção		Ação
-p PROMPT	Escreve o PROMPT na tela e fica aguardando você digitar o dado
-n NUM		Lê até NUM caracteres
-t SEG		Espera SEG segundos para que a leitura seja concluída
-s			O que está sendo teclado não aparece na tela (silent)
```

**Exemplos**

**Lê o campo "Matricula" de maneira convencional**
```
echo -n "Matricula: "; read Mat #Opção -n para não saltar linha
Matricula: 12345
echo $Mat
12345
```

**Simplicado com `-p`**
```
read -p "Matricula: " Mat
Matricula: 12345
echo $Mat
12345
```

**Lendo uma quantia de caracteres**

* Obs: o `read` não tem newline por default como o `echo`
```
$ read -n5 -p"CEP: " Num ; read -n3 -p- Compl
CEP: 12345-678$
$ echo $Num
12345
$ echo $Compl
678
```

**Lendo com timeout**
```
# Neste caso, o || foi executado pois read retornou algo diferente de 0
# A variavel $Nome permanece vazia, até que seja digitado enter

read -t2 -p "Digite seu nome completo: " Nome || echo -e "\nEta moleza!"
Digite seu nome completo: teste
Eta moleza!
```

**Ler dados, sem ser exibido na tela**
```
# Neste caso, foi esquecido de colocar a variável que armazenará os dados
# A variavel `$REPLY` do bash, possui a última cadeia lida
# A falta de new line, fez o prompt permanecer na mesma linha
# Não significa a falta do enter, mas devido a opção -s, que faz não aparecer nada, nem `ENTER`

$ read -sp "Senha: "
Senha: 

echo $REPLY
segredo :)
```

------
# Lendo arquivos com read

* Ao ler um arquivo com permissão de leitura
* O `read` só será mal sucedido quando alcança o `EOF`
* Sabendo disso, pode-se ler arquivos de duas maneiras:

**1 - Redirecionando a entrada do arquivo para o bloco do while**
* É mais rápido
* Não necessita de um subshell para assisti-lo
* Em um bloco de instruções grande, o redirecionamento fica pouco visível o que por vezes prejudica a vizualização do código;
```
while read Linha
do
	echo $Linha
done < arquivo
###
 while read Linha
> do
> echo $Linha
> done < arq
A
B
C
```

**2 -  Redirecionando a saída de um cat para o while**
* Como o nome do arquivo está antes do while, é mais fácil a vizualização do código
* O Pipe chama um subshell para interpretá-lo, tornando o processo mais lento, pesado e por vezes problemático
```
cat arquivo |
while read Linha
do
    echo $Linha
done
###
cat arq |
> while read linha
> do
> echo $linha
> done
A
B
C
```

**Exemplos**

* Problema com pipe
```
cat frutas
abacate
maça
morango
pera
tangerina
uva
------

cat lista_frutas.sh
#!/bin/bash
# Exemplo de read passando arquivo por pipe
cat frutas |
        while read fruta
        do
                echo $((++ContaFruta)) $fruta # Incrementa o contador e lista-o com cada fruta
        done
echo Meu arquivo tem :$ContaFruta: frutas cadastradas
------

./lista_frutas.sh
1 abacate
2 maça
3 morango
4 pera
5 tangerina
6 uva
Meu arquivo tem :: frutas cadastradas


# Como foi utilizado subshell, ao final de execução o contador zerou(morreu)
```

* Exemplo com redirecionamento `<`
```
cat lista_frutas.sh
#!/bin/bash
# Exemplo de read passando arquivo por pipe
while read fruta
do
        echo $((++ContaFruta)) $fruta # Incrementa o contador e lista-o com cada fruta
done < frutas
echo Meu arquivo tem :$ContaFruta: frutas cadastradas

-------

./lista_frutas.sh
1 abacate
2 maça
3 morango
4 pera
5 tangerina
6 uva
Meu arquivo tem :6: frutas cadastradas
```

**Dica**
* Listar um arquivo e, a cada dez registros
* O operador precise digitar alguma tecla para continuar (verificar)
```
#!/bin/bash
#  Prg de teste para escrever
#+ 10 linhas e parar para ler
#  Versão 2

while read Num
do
    let ContLin++           # Contando...
    echo -n "$Num "         # -n para nao saltar linha
    ((ContLin % 10 == 0)) && read < /dev/tty     # Explicitando que a entrada é pelo teclado
done < numeros
```

**Exemplo Formatado**
```
#!/bin/bash
#  Prg de teste para escrever
#+ 10 linhas e parar para ler
# A contagem agora é feita por tput lines
# Ou seja, se a tela tem 25 linhas, listará 22 registros, etc
clear
while read Num
do
    let ContLin++                           # Contando...
    echo "$Num"
    ((ContLin % (`tput lines` - 3) == 0)) &&
        {
        read -n1 -p"Tecle Algo " < /dev/tty # para ler qq caractere
        clear                               # limpa a tela apos leitura
        }
done < numeros
```

**Exemplo TPUT - receber dados do teclado**
```
#!/bin/bash
#
clear
echo "
        Programa:
        Versão:
        Arquivos:"

tput cup 1 25; read prg
tput cup 2 25; read vrs

while true
do
        tput cup 3 25; tput el; read arq
        [[ -z $arq ]] && break
done
```



**Cortando sem usar cut**

```
# Lista UserID e Username
# Leu os 4 primeiros campos, mas só listou os desejados separados por : (com a alteração da variável $IFS)
# Usou a variavel lixo, para alocar o conteúdo que nao quer
while IFS=: read Uname Lixo Uid Lixo
do
    echo $((++Cont)) $Uid $Uname
done < /etc/passwd
```

**Outro Exemplo**
```
cat arq1
A:B:C:D:E
while IFS=: read var1 var2 var3 var4 var5; do echo $var1 $var3; done < arq1
A C

ou...
while IFS=: read var1 lixo var3 lixo lixo; do echo $var1 $var3; done < arq1
A C

```
