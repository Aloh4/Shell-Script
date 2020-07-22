## Dica:

```
A execução de arquivos usando ./ acontece pois, o diretório atual
não está na variável que possui todos os diretórios pesquisados para localizarmos arquivos ($PATH)

A solução é:

echo 'PATH:$PATH:.' >> ~/.bashrc
source ~/.bashrc

1 - Insere na última linha do arquivo .bashrc o PATH:$PATH:. (agrega o diretório corrente a esta variável)
2 - Executa o arquivo com source para a alteração entrar em vigor
```

## CAT ARQ | grep ou etc.. ` É ERRADO ` !!!
## Correto
```
grep...arq
wc...arq
cut...arq
```

**Validar tempo de execução**
```
time CMD #builtin
which CMD #nao builtin
```

**O comando exit**
```
O exit manda o codigo de retorno do programa, por isso, pode assumir qualquer valor.
```

**Finalizar opcoes recebidas por um programa**
```
--  >> Significa fim das opcoes de um programa
grep -o -- "A" arq
```

## Usando echo corretamente
```
echo ..
echo ..
echo ..
é errado !

faça:
echo "
		Linha1:
		Linha2:
		etc... "
```

## Modo inteligente e rápido para criar arquivo:

```
$ > temp

**Obs: CAT / TOUCH não criam arquivos !**

* O modo correto é > arq

```

## O mesmo com eval
```
## Criar vários arquivos com eval !
Errado:		> arq{1..4}
Correto: 	eval \>arq{1..4}\; # Poderia ser sem \;

teste:
echo \>arq{1..4}\; ## O echo exibe a 'segunda' passada do eval 
```

## Exemplos expansão de arquivos:
```
cp caminho/completo/do/arquivo{,.bkp}
mkdir /usr/local/{bin,etc,games,include,lib,sbin,share,src}
```

## Copie o arq1 para o arq.bkp:
```
root@whoami:~/shell/sequencias# cp arq{1,.bkp}

## Exemplo saída:
root@whoami:~/shell/sequencias# echo arq{1,.bkp}
arq1 arq.bkp 
```
## Exemplos tail:
```
$ seq 10 | tail -5    # Lista as 5 últimas
$ seq 10 | tail -n 4    # Lista as 4 últimas
$ seq 10 | tail -n +3    # Lista as últimas a partir da 3ª
```

**tipos de echo**
```
Sem -n
echo "Escreva: "; read var
Escreva:


Com -n (nao salta linha)
root@whoami:~# echo -n "Escreva: "; read var
Escreva:
```

**Enxergar caracteres especiais**
```
cat -et # -e representa enter($), -t representa TAB (^I)
```

## script parafuso

```
## Uso sleep 15&
## Com o numero do process em background, utilizar
## parafuso.sh PID 

#!/bin/bash
tput civis
trap "tput cnorm; exit" 0 2 3 15
echo -ne "\t\t"
while kill -0 $1 2>&-
do
	for i in \| \/ \- \\ \| \/ \-
	do
		echo -en "\b$i"
		sleep 0.2
	done
done

```

**Uso SEQ**

```
# Sintaxe:
seq [-OPCOES] PRIMEIRO [INCREMENTO] [ULTIMO] 

# Pode-se usar numeros negativos

# Informando só PRIMEIRO
$ seq 3
1
2
3

# Informando PRIMEIRO e ULTIMO 
$ seq 2 4
2
3
4

# Informando PRIMEIRO, INCREMENTO e ULTIMO
$ seq 0 3 9
0
3
6

# Suas opções mais usadas são a -w que preenche com zeros à esquerda listando todos com o tamanho de ULTIMO
$ seq -w 0 20 100
000
020
040
060
080
100

# E a opção -s [SEP] que informa o separador SEP, cujo valor padrão é o <ENTER>
$ seq -w -s. 0 2 20?   # Usando o ponto (.) como separador
00.02.04.06.08.10.12.14.16.18.20

```

