## FIND

* Realiza buscas recursivas
* Utiliza nomes e outras características

##Sintáxe:

```
find CAMINHO EXPRESSAO ACAO `` 
find CAMINHO EXPR1 EXPR2 ... EXPRn ACAO `` 

* Operador AND
find CAMINHO EXPR1 -a EXPR2 -a ... -a EXPRn  ACAO `` 

Operador OR
*find CAMINHO EXPR1 -o EXPR2 -o ... -o EXPRn ACAO `` 

Parâmetros
CAM		Caminhos de diretório a partir do qual irá procurar pelos arquivos ("a partir" porque é recursivo e, por isso, sempre tentará entrar pelos subdiretórios "pendurados" neste);
EXPR	Define quais critérios de pesquisa. Pode ser uma combinação entre vários tipos de procura;
ACAO	Define que ação executar com os arquivos que atenderem aos critérios de pesquisa definidos por EXPR.
```

## Principais critérios de pesquisa:

```
Opção		Resultado
-name		Procura arquivos que tenham o nome especificado. Aqui podem ser usados metacaracteres ou caracteres curingas, porém estes caracteres deverão estar entre aspas, apóstrofos ou imediatamente precedidos por uma contrabarra isso porque quem tem de expandir os coringas é o find. Se fosse o Shell que os expandisse, isto seria feito somente com relação ao diretório corrente, o que jogaria por terra a característica recursiva do find;
-user		Procura arquivos que tenham usuário como dono;
-group		Procura arquivos que tenham grupo como grupo dono;
-type c		Procura por arquivos que tenham o tipo c, correspondente à letra do tipo do arquivo. Os tipos aceitos estão na tabela a seguir:
```

## Valores de c na opção acima	Tipo de arquivo procurado:

```
b								Arquivo especial acessado a bloco
c								Arquivo especial acessado a caractere
d								Diretório
p								Named pipe (FIFO)
f								Arquivo normal
l								Link simbólico
s								Socket
-size ±n[UNID]					A opção -size procura por arquivos que usam mais (+n) de n UNIDades UNID de espaço ou a menos (-n) de n UNIDades UNID de espaço.

* Valores de UNID na opção acima	Valor
b									Bloco de 512 bytes (valor default)
c									Caracteres
k									Kilobytes (1024 bytes)
w									Palavras (2 bytes)
-atime ±d							Procura por arquivos que foram acessados há mais (+d) de d dias ou a menos (-d) de d dias;
-ctime ±d							Procura por arquivos cujo status mudou há mais (+d) de d dias ou a menos (-d) de d dias;
-mtime ±d							Procura por arquivos cujos dados foram modificados há mais (+d) de d dias ou a menos (-d) de d dias;
```

## Principais ações

```
Opção			Resultado
-print			Esta opção faz com que os arquivos encontrados sejam exibidos na tela. Esta é a opção default no Linux. Nos outros sabores Unix que conheço, se nenhuma ação for especificada, ocorrerá um erro;
-exec CMD {} \;	Executa o comando CMD. O escopo de comando é considerado encerrado quando um ponto-e-vírgula (;) é encontrado. A cadeia {} é substituída pelo nome de cada arquivo que satisfaz ao critério de pesquisa e a linha assim formada é executada. Assim como foi dito para a opção -name, o ponto-e-vírgula (;) deve ser precedido por uma contrabarra (\), ou deve estar entre aspas ou apóstrofos;
-ok CMD {} \;	O mesmo que o anterior porém pergunta se pode executar a instrução CMD sobre cada arquivo que atende ao critério de pesquisa;
-printf FMT		A opção -printf permite que se escolha os campos que serão listados e formata a saída de acordo com o especificado em FMT.

```

## Formatação de saída

```
Caractere	Significado
%f			Nome do arquivo (caminho completo não aparece)
%F			Indica a qual tipo de file system o arquivo pertence
%g			Grupo ao qual o arquivo pertence
%G			Grupo ao qual o arquivo pertence (GID - Numérico)
%h			Caminho completo do arquivo (tudo menos o nome)
%i			Número do inode do arquivo (em decimal)
%m			Permissão do arquivo (em octal)
%p			Nome do arquivo
%s			Tamanho do arquivo
%u			Nome de usuário (username) do dono do arquivo
%U			Número do usuário (UID) do dono do arquivo

## Datas e horas

%a	Data do último acesso
%c	Data de criação
%t	Data de alteração

## Formatando datas e horas:
Utilizar os itens da tabela anterior em maiúsculo

-printf '%AHH%AMM%ASS \n'
-printf '%Ax %AHH%AMM %Ap \n'
05/20/2020 00H49M AM

## Tabela de formatação de tempo
Caractere	Significado
H			Hora (00..23)
I			Hora (01..12)
k			Hora (0..23)
l			Hora (1..12)
M			Minuto (00..59)
p			AM or PM
r			Horário de 12 horas (hh:mm:ss) seguido de AM ou PM
S			Segundos (00 ... 61)
T			Horário de 24-horas (hh:mm:ss)
Z			Fuso horário (na Cidade Maravilhosa BRST)

## Tabela de formatação de datas
Caractere	Significado
a			Dia da semana abreviado (Dom...Sab)
A			Dia da semana por extenso (Domingo...Sábado)
b			Nome do mês abreviado (Jan...Dez)
B			Dia do mês por extenso (Janeiro...Dezembro)
c			Data e hora completa (Fri Dec 23 15:21:41 2005)
d			Dia do mês (01...31)
D			Data no formato mm/dd/aa
h			Idêntico a b
j			Dia sequêncial do ano (001...366)
m			Mês (01...12)
U			Semana sequêncial do ano. Domingo como 1º dia da semana (00...53)
w			Dia sequêncial da semana (0..6)
W			Semana sequêncial do ano. Segunda-feira como 1º dia da semana (00...53)
x			Representação da data no formato do país (definido por $LC_ALL)
y			Ano com 2 dígitos (00...99)
Y			Ano com 4 dígitos

```

** Exemplos práticos FIND: **

```
## Procurar discos
find / -type b

## Arquivos modificados a mais de um dia
find / -mtime 1

##Procura no diretório atual
find . -name 'ARQ*'

## Lista todos os diretórios do root sob /usr/local
find /usr/local -type d -user root

## A opção -a torna o 'AND' explicito 
find /usr/local -type d -a -user root 

## Lista arquivos regulares do usuário ou do root, no dir corrente
find . -type f -user $LOGNAME -o -user root 

## REGEX com find
find . -name 'aloha' -exec grep -E '^[Ff]' {} \;

## Pergunta a execução do comando // Corrigir comando
find . -name 'aloha' -ok -exec grep -E '^[Ff]' {} \;

## Remove arquivos com mais de um mega e acessado a mais de 60 dias
find . -type f -size +1000000c -atime +60 -exec rm {} \;

-type f				Todos os arquivos regulares (normais)
-size +1000000c		Tamanho maior do que 1000000 de caracteres (+1000000c)
-atime +60			Último acesso há mais de 60 (+60) dias.
```

## Backup com find
```
find . -atime -1 -size +100c -exec cp {}{,.bkp} \;

## Procura arquivos no diretório atual
# Com último acesso a mais de 30 dias OU
# Com mais de 100 caracteres
# Executa copia para ARQ1.bkp ARQ2.bkp ...etc
# Gera um backup.tar.gz para os arquivos com .bkp
find . -atime +30 -o -size +100c -exec cp {}{,.bkp} \; ; tar -zcvf backup.tar.gz *.bkp
Saída:
cp: -r not specified; omitting directory '.'
aloha.bkp
arq.bkp
teste.bkp
```

## Exibir informações adicionais com printf
```
find . -name "teste" -printf '%t %p %g \n'
saida: Wed May 20 00:49:47.5546851480 2020 ./teste root
```
## Formatar saida com printf
```
find . -name "teste" -printf '%AA %AHH%AMM %Ap \n'
Wednesday 00H49M AM

find . -name "teste" -printf '%Ax %AHH%AMM %Ap \n'
05/20/2020 00H49M AM
```

## Listar arquivos por ordem de tamanho
```
find . -name ".b*" -printf '%s\t%p\n' | sort -n   # \t é um <TAB>
```

## Listar arquivos por data e hora de ultima alteração
```
find . -name ".b*" -printf '%TY-%Tm-%Td %TH:%TM:%TS %p\n' | sort
```

