## Variáveis e parâmetros do Shell

**Tópicos**

* Variáveis do Shell
* Expansão de parâmetros
* Tratamento de Vetores


* O `bash` possui diversas variáveis que servem para dar informações ao ambiente ou alterá-lo
* Pode-se consultá-las `$ + tab`

```
Principais variáveis do Bash
Variável		Conteúdo
CDPATH			Contém os caminhos que serão pesquisados para tentar localizar um diretório especificado. Apesar desta variável ser pouco conhecida, seu uso deve ser incentivado por poupar muito trabalho, principalmente em instalações com estrutura de diretórios com muitos níveis.
HISTSIZE		Limita o número de instruções que cabem dentro do arquivo de histórico de comandos (normalmente .bash_history mas efetivamente é o que está armazenado na variável $HISTFILE). Seu valor default é 500.
HOSTNAME		O nome do host corrente (que também pode ser obtido com o comando uname -n).
LANG			Usada para determinar a língua falada no país (mais especificamente categoria do locale).
LINENO			O número da linha do script ou da função que está sendo executada, seu uso principal é para dar mensagens de erro acompanhado das variáveis $0 (nome do programa) e $FUNCNAME (nome da função em execução)
LOGNAME			Armazena o nome de login do usuário.
MAILCHECK		Especifica, em segundos, a frequência que o Shell verificará a presença de correspondências nos arquivos indicados pelas variáveis $MAILPATH ou $MAIL. O tempo padrão é 60 segundos. Uma vez este tempo expirado, o Shell fará esta verificação antes de exibir o próximo prompt primário (definido em $PS1). Se esta variável estiver sem valor ou com um valor menor ou igual a zero, a verificação de novas correspondências não será efetuada.
PATH			Caminhos que serão pesquisados para tentar localizar um arquivo especificado. Como cada script é um arquivo, caso use o diretório corrente (.) na sua variável $PATH, você não necessitará de usar o ./scrp para que scrp seja executado. Basta fazer scrp.
PIPESTATUS		É uma variável do tipo vetor (array) que contém uma lista de valores de código de retorno do último pipeline executado, isto é, um array que abriga cada um dos $? de cada instrução do último pipeline.
PROMPT_COMMAND	Se esta variável receber uma instrução, toda vez que você der um <ENTER> direto no prompt principal ($PS1), este comando será executado. É útil quando se está repetindo muito uma determinada instrução.
PS1				É o prompt principal. Aqui usamos os seus defaults: $ para usuário comum e # para root, mas é muito frequente que ele esteja customizado. Uma curiosidade é que existe até concurso de quem programa o $PS1 mais criativo. (clique para dar uma googlada).
PS2				Também chamado prompt de continuação, é aquele sinal de maior (>) que aparece após um <ENTER> sem o comando ter sido encerrado.
PWD				Possui o caminho completo ($PATH) do diretório corrente. Tem o mesmo efeito do comando pwd.
RANDOM			Cada vez que esta variável é acessada, devolve um número inteiro, que é um randômico entre 0 e 32767.
REPLY			Use esta variável para recuperar o último campo lido, caso ele não tenha nenhuma variável associada.
SECONDS			Esta variável contém a quantidade de segundos que o Shell corrente está de pé. Use-a somente para esnobar usuários daquilo que chamam de sistema operacional, mas necessita de boots frequentes. ?
TMOUT			Se tiver um valor maior do que zero, este valor será tomado como o padrão de timeout do comando read. No prompt, este valor é interpretado como o tempo de espera por uma ação antes de expirar a sessão. Supondo que a variável contenha 30, o Shell dará logout após 30 segundos de prompt sem nenhuma ação.
BASH_REMATCH	??
```

**Exemplos**

```
## Variavel nao encontrada no BASH
$ echo $CDPATH
.:..:~:/usr/local
$ pwd
/home/jneves/LM
$ cd bin
$ pwd
/usr/local/bin
```

```
# Dessa maneira, LANG é alterado apenas para o comando DATE, durante esta execução apenas
$ date
Thu Apr 14 11:54:13 BRT 2017
$ LANG=pt_BR date
Qui Abr 14 11:55:14 BRT 2017
```

```
# Usa-se [*] para todos os parâmetros do pipeline
$ who
jneves   pts/0        Apr 11 16:26 (10.2.4.144)
jneves   pts/1        Apr 12 12:04 (10.2.4.144)

$ who | grep ^botelho
$ echo ${PIPESTATUS[*]}
0 1
```

```
#Numeros randomicos de 0-100
$ echo $((RANDOM%101))
73

# De 5 a 100
$ echo $((RANDOM%96+5))
49

# De 0-10
echo $((RANDOM%11))
```

```
$ read -p "Digite S ou N: "
Digite S ou N: N
$ echo $REPLY
N
```

-----
## Expansão de Parâmetros

* Fazem parte dos programas intrínsecos (buildtin) do shell
* Essas instruções, substituem comandos como `cut, expr, tr, sed, etc`

https://superuser.com/questions/935374/difference-between-and-in-shell-script

**Alguns Exemplos vistos**
```
${13} 	= Permite acessar um parâmetro superior a $9
${#VAR} = Devolve o tamanho de $VAR
${VAR:POS:TAM}	= Extrai de $VAR desde a pocição $POS (origem zero) com tamanho $TAM
${VAR:POS}		= Extra de $VAR a partir de POS, origem zero 

```


```
Expansão de 	parâmetros
Expressão		Resultado esperado
${VAR:-CAD}		Se $VAR não tem valor, o resultado da expressão é a cadeia CAD
${VAR:+CAD}		Se $VAR tem valor, o resultado da expressão é a cadeia CAD
${#VAR}			Tamanho de $VAR
${VAR:POS}		Extrai de $VAR a partir de POS. Origem zero
${VAR:POS:TAM}	Extrai de $VAR a partir de POS com tamanho igual a TAM. Origem zero
${VAR#PADR}		Corta a menor ocorrência de $VAR à esquerda da expressão que case com o padrão PADR, sendo este formado por metacaracteres de expansão de arquivos
${VAR##PADR}	Corta a maior ocorrência de $VAR à esquerda da expressão que case com o padrão PADR, sendo este formado por metacaracteres de expansão de arquivos
${VAR%PADR}		Corta a menor ocorrência de $VAR à direita da expressão que case com o padrão PADR, sendo este formado por metacaracteres de expansão de arquivos
${VAR%%PADR}	Corta a maior ocorrência de $VAR à direita da expressão que case com o padrão PADR, sendo este formado por metacaracteres de expansão de arquivos
${VAR/PADR/CAD}	Troca em $VAR a primeira ocorrência de PADR por CAD. O padrão PADR pode ser formado por metacaracteres de expansão de arquivos
${VAR//PADR/CAD}	Troca em $VAR todas as ocorrências de PADR por CAD. O padrão PADR pode ser formado por metacaracteres de expansão de arquivos
${VAR/#PADR/CAD}	Se PADR combina com o início de $VAR, então é trocado por CAD. O padrão PADR pode ser formado por metacaracteres de expansão de arquivos
${VAR/%PADR/CAD}	Se PADR combina com o fim de $VAR, então é trocado por CAD. O padrão PADR pode ser formado por metacaracteres de expansão de arquivos
${!VAR}			Lista valor da variável apontada por $VAR (indireção)
${VAR^}			Coloca a primeira letra de $VAR em maiúscula
${VAR^^}		Coloca todas as letras de $VAR em maiúscula
${VAR,}			Coloca a primeira letra de $VAR em minúscula
${VAR,,}		Coloca todas as letras de $VAR em minúscula
${VAR~}			Troca a caixa da primeira letra de $VAR
${VAR~~}		Inverte a caixa de todas as letras de $VAR

${array[POS]} == Significa ARRAY !
```



**Resposta default para programas**

* Caso o operador não digite nada, poderia-se setar um valor default na variável, neste caso S
```
SN=${SN:-S}
```


**Exemplo oposto ao anterior**
* A cadeia só é exibida qdo $var2 tem um valor
```
$ Var1=10; unset Var2; echo Var1 tem 10${Var2:+ e Var2 tem $Var2}
Var1 tem 10
$ Var1=10; Var2=; echo Var1 tem 10${Var2:+ e Var2 tem $Var2}
Var1 tem 10
$ Var1=10; Var2=20; echo Var1 tem 10${Var2:+ e Var2 tem $Var2}
Var1 tem 10 e Var2 tem 20
```

**Tamanho da cadeia**
```
$ cadeia=0123
$ echo ${#cadeia}
4
```

**Extrair posição especifica**
```
$ cadeia=0123
$ echo ${#cadeia}
4
```

**Extrair do fim ao principio**
```
$ TimeBom=Flamengo
$ echo ${TimeBom: -5}
mengo
$ echo ${TimeBom:(-5)}
mengo
```

**Utilizando metacaracteres de expansão de arquivos**
```
# Suprimiu tudo a esquerda da primeira ocorrencia da cadeia *' '
# Ou seja, tudo até o primeiro espaço em branco, poderia ser -> *a
$ cadeia="Casa da Mãe Joana"
$ echo ${cadeia#*' '}
da Mãe Joana
$ echo Morada ${cadeia#*' '}
Morada da Mãe Joana

# Suprimir tudo a esquerda da última ocorrencia de uma cadeia
$ echo ${cadeia##*' '}
Joana
$ echo Vou levar um papo com a ${cadeia##*' '}
Vou levar um papo com a Joana

```

** Não exibe o `path` completo do programa
```
# Aqui, apagou-se tudo a esquerda com casamento */, até a ultima barra
# 0 representa o caminho (nome) do programa -> $0
echo Uso: ${0##*/} texto da mensagem de erro 
```

**Suprimir a direita da ocorrencia de uma cadeia
```
# è o mesmo uso do #, porém inverso
$ echo $cadeia
Casa da Mãe Joana
$ echo ${cadeia%' '*}
Casa da Mãe
$ echo ${cadeia%%' '*}
Casa
```

**Trocar a primeira ocorrencia de uma cadeia por outro
```
$ echo $cadeia
Casa da Mãe Joana
$ echo ${cadeia/Mãe/Vovó}
Casa da Vovó Joana
$ echo ${cadeia/Mãe /}
Casa da Joana

#Utilizando metacaracteres
# cuidado ! Metacaracteres são gulosos !
$ echo $cadeia
Casa da Mãe Joana
$ echo ${cadeia/*a /Residência }
Residência Mãe Joana

$ echo ${cadeia/*sa/Residência}
Residência da Mãe Joana
$ echo ${cadeia/????/Lar}
Lar da Mãe Joana

#Troca todas as ocorrencias por outra
$ echo ${cadeia//a/ha}
Chasha dha Mãe Johanha
```

**Remover espaços em branco em nomes de arquivos
```
#!/bin/bash
#  Renomeia arquivos com espaços nem branco
#+ no nome, trocando-os por sublinhado (_).
Erro=0
for Arq in *' '*      # Expande para todos os arquivos com espaço em branco no nome
do
[ -f ${Arq// /_} ] && {
    echo $Arq não foi renomeado
    Erro=1
    continue
    }
mv "$Arq" "${Arq// /_}"
done 2> /dev/null     #  Caso não exista arquivo com brancos o for dá erro
exit $Erro

#validação extra
# [[ -f *' '* ]] Se existir mais de um arquivo nessa categoria, seria gerado erro pois o test testa 1 parametro por vez
ls *' '* > /dev/null 2> /dev/null || echo Não há arquivo com espaço no nome.
```


**Troca cadeia no inicio e fim de uma variavel
```
$ echo $Passaro
quero quero
$ echo "Como diz o sulista - "${Passaro/#quero/não}
Como diz o sulista - não quero

$ echo "Como diz o nordestino - "${Passaro/%quero/não}
Como diz o nordestino - quero não
```

**Listar o valor de uma variável apontada por outra
```
$ a=b
$ b=5
$ e
```

**Mudando caixa (alta/baixa)
```
$ Nome="botelho"
$ echo ${Nome^}	        # 1º letra em maiúscula
Botelho
$ echo ${Nome^^}	# Todas as letras em maiúsculas
BOTELHO
$ Nome="botelho carvalho"
$ echo ${Nome^}	        # 1º letra em maiúscula
Botelho carvalho
$ Coisa="AAAbbb cccDDD"
$ echo ${Coisa~}	# Troca case da 1º letra
aAAbbb CccDDD
$ echo ${Coisa~~}	# Troca case de todas as letras
aaaBBB CCCddd

read -p "Deseja continuar (S/n)? " # S maiúsculo (default) / Faltou n1?
[[ ${REPLY^} == N ]] && exit
```

**Passa nome de arquivos para minusculo**
```
#!/bin/bash
#  Se o nome do arquivo tiver pelo menos uma
#+ letra maiúscula, troca-a para minúscula

for Arq in *[A-Z]*	#  Arquivos com pelo menos 1 maiúscula
do
if  [ -f "${Arq,,}" ]	#  Arq em minúsculas já existe?
then
    echo ${Arq,,} já existe
else
mv "$Arq" "${Arq,,}"
fi
done
```

**Dica**
```
A ordem dos caracteres em expansão do tipo [...] é determinada pela variável LC_COLLATE
E a sequencia default é diferente da normal utilizada
Para isso, necessário fazer `export LC_COLLATE=C`

Pesquisar sobre LC_ALL e LC_COLLATE
```

-----
## Vetores / Arrays


* Método para se tratar diversas informações (normalmente do mesmo tipo), sob um único nome
* Por exemplo vetor:cervejas contém vários tipos de cervejas ,etc
* Para acessar seus dados, precisaríamos de um índice

`nomedovedor[indice]`
`vetor[0] , vetor[1] ...etc`

**OBS**
```
${array[POS]} == Significa ARRAY !
```

* Existem quatro formas de se declarar um vetor:

* Desta maneira, se o vetor já existir, seu dados serão perdidos
* Vetor denso
* `vet=(EL0 EL1 ... ELn)` se iniciado como `vetor=(), será iniciado vazio` 

* Cria o elemento de índice `N` do vetor `vet` com valor `VAL`, se não existe, vet será criado
* Vetor esparso
* `vet[N]=VAL` , `Veiculos=([2]=jegue [5]=cavalo [9]=patinete)`

* Criar o vetor `vet` vazio, se já existir, será mantido inalterado
* `declare -a vet`

* Cria um vetor associativo, cujo os indices não são numéricos (versão 4.0 do Bash)
* `declare -A vet`

**Para consultar o conteúdo do vetor, usa-se `${vet[NN]}`**

**Exemplos**
```
$ Familia[10]=Silvina
$ Familia[22]=Juliana
$ Familia[40]=Paula
$ Familia[51]=Julio
$ echo ${Familia[22]}
Juliana
$ echo ${Familia[18]}  # Não existe
$ echo ${Familia[40]}
Paula
```

**Notação sintática do BASH (Não funciona em UNIX**
```
$ Frutas=(abacaxi banana laranja tangerina)
$ echo ${Frutas[1]}
banana
```

**Também é possível acessar os índices com expressões aritméticas**
```
$ echo ${Frutas[10-6]}
fruta do conde
$ echo ${Frutas[10/2]}
fruta pão
$ echo ${Frutas[2*2]}
fruta do conde
$ echo ${Frutas[0*3]}
abacaxi
```

**Vetores com read -a**

```
read -a Animais <<< "cachorro gato cavalo"	# Usando Here Strings

$ for i in 0 1 2
> do
>     echo ${Animais[$i]}
> done
cachorro
gato
cavalo

ou...
for i in $(seq 0 2); do echo ${animas[$i]}; done
```

**Vetores com leitura de arquivos**
```
$ cat nums
1 2 3
2 4 6
3 6 9
4 8 12
5 10 15

$ while read -a vet
> do
>     echo -e ${vet[0]}:${vet[1]}:${vet[2]}
> done < nums
1:2:3
2:4:6
3:6:9
4:8:12
5:10:15
```

------
## Manipulando vetores

* Pode-se utilizar os mesmos conceitos de passagem de parêmetros para acessar os índices dos vetores
* ` * .. @ `

```
echo ${letras[*]}
a b c d
echo ${letras[@]}
a b c d
```

*Acessando todos os elementos*
```
# Com aspas, o vetor não será partido em listagem \/
$ for fruta in "${Frutas[@]}"
> do
>     echo $fruta
> done
```

**Outro Exemplo**

```
Animais=([2]="Mico Leão" [5]="Galinha d'Angola" [8]="Gato Pardo")

$ echo ${!Animais[*]}
2 5 8
$ echo ${!Animais[@]}
2 5 8

$ for Ind in ${!Animais[@]}	# Ind recebe cada um dos índices
> do
> echo ${Animais[Ind]}
> done
Mico Leão
Galinha d''Angola
Gato Pardo
```

**Exibir a quantidade de elementos de um vetor**
```
$ echo ${#Frutas[*]}
6

$ echo ${#Frutas[@]}
6
```


**Transferindo valores de vetor**
```
$ Vetor=("${Frutas[@]}")
$ echo "${Vetor[4]}"
fruta do conde
$ echo "${Vetor[5]}"
fruta pão
```

**Listando ranges**
```
## Exemplo com variável
var=1234567890
echo ${var:1:3}
234

echo ${var:3}
4567890

##Exemplo com vetores
echo ${Frutas[@]:1:3}   # A partir do 2º elemento, listar 3 elementos
banana laranja tangerina
echo ${Frutas[@]:4}     # A partir do 5º caractere até o fim
fruta do conde fruta pão
```

**Exemplos**
```
$ Frase=(Alshançar o shéu é sensashional. Um sushesso\!)
$ echo ${Frase[*]//sh/c}
Alcançar o céu é sensacional. Um sucesso!
```

## Vetores associativos
* Seus índices são alfabéticos
* Antes de inserir seus valores, é necessário declará-lo

```
$ declare -A Animais	# Obrigatório para vetor associativo
$ Animais[cavalo]=doméstico
$ Animais[zebra]=selvagem
$ Animais[gato]=doméstico
$ Animais[tigre]=selvagem

##Exemplo errado
Animais = ([cavalo]=doméstico [zebra]=selvagem [gato]=domestico [tigre]=selvagem)
$ echo ${Animais[@]}
domestico selvagem doméstico selvagem
$ echo ${!Animais[@]}
gato zebra cavalo tigre
```

## Lendo arquivo para um vetor (mapfile)

* Joga um arquivo de texto inteiro para dentro de um vetor 
```
$ cat frutas
abacate
maçã
morango
pera
tangerina
uva

$ mapfile vet < frutas  # Mandando frutas para vetor vet
$ echo ${vet[@]}        # Listando todos elementos de vet
abacate maçã morango pera tangerina uva

# O mesmo resultado seria obtido com
$ vet=($(cat frutas))
```




















