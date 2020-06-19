## XARGS

* Constrói lista de parâmetros
* Passa a execução para outros programas 
* Combina o ARG INI c/ os argumentos recebidos da entrada padrão
* Executa-os (CMD) uma ou mais vezes

**Sintáxe**

`xargs [CMD [ARG INI]]`

## Características

* Quem passa os parâmetro de execução ao comando é o xargs, não o shell
* Tem controle do ambiente e dos paramêtros a serem passados sem causar estouro de pilha (Too many arguments)
* Sempre tentará executar o comando `CMD` com o maior nº de argumentos possível

**Exemplo**

```
1º Característica // Ele executa, não o shell

ls arq | rm			-> Shell não deixará executar
ls arq | xargs rm	-> xargs 'executa' rm

alias rm="rm -i"	-> Torna rm interativo	
ls arq | xargs rm	-> xargs executou rm novamente sem a interpretação de seu alias por não ser executado pelo Shell

* `rm` passou a atuar como parâmetro `xargs`
* `xargs` recebeu via pipe a saída de `ls`
* enviou para execução de `rm`

2º Característica // Manda o maior nº de parâmetros possível

seq 10 | xargs
1 2 3 4 5 6 7 8 9 10

* Por padrão xargs assume 'echo', caso não seja especificado comando

```

**Exemplo de execução**

```
## Script para executar find, xargs e grep por argumentos
# Toda lista de find é enviada para xargs -> grep

cat grepr

# Grep recursivo
# Pesquisa a cadeia de caracteres definida em $2 a partir do diretorio $1
# xargs passa a maior lista possível de find para grep executar (Passa a maior qtd de parâmetros possível e suficiente)
# Para não causar estouro de pilha
# Exemplo:  find . -type f | xargs -t grep REGEXP ARQ1 ARQ2 ARN
find $1 -type f -print | xargs grep -l "$2"

# Criação alternativa com exec
# Para cada entrada de find, será executado um grep, execução mais lenta
# Exemplo:  grep REGEXP ARQ1; grep REGEXP ARQ2; grep REGEXP ARQ3; grep REGEXP ARQ4
find $1 -type f -exec grep -l "$2" {} \;

Execução: grepr <DIR> <CADEIA-BUSCA>

```
-----
## Opção -i e -I

* Inserir a entrada primária, em qualquer lugar da linha de comandos

* opção -i (replace string)
* Troca o alvo `{ }` pela cadeia recebida em stdin, pipe, etc

```
# Encontra arquivos ordinários, com nome "*.txt"
# Envia a listagem para xargs
# Os alvos { } são substituidos pela saída de find, 
# O comand 'bash' irá printar com echo e remover com rm
find . -type f -name "*.txt" | xargs -i bash -c "echo removendo {}; rm {}"

# Obs, a opção -i está caindo em desuso, sendo utilizado a opção -I
OK	find . -type f -name "*.txt" | xargs -I{} bash -c "echo removendo {}; rm {}"
OK	find . -type f -name "*.txt" | xargs --replace bash -c "echo removendo {}; rm {}"

# É possível setar outro target que não o { }
# Abaixo utilizando [ , [] e x

find . -type f -name "*.txt" | xargs -I[ bash -c "echo removendo [; rm ["
find . -type f -name "*.txt" | xargs -I[] bash -c "echo removendo []; rm []"
find . -type f -name "*.txt" | xargs -Ix bash -c "echo removendo x; rm x"

```
-----
## Opção -n (max-args)

* Exibe um argumento por vez, ou N vezes na mesma linha

```
ls | xargs > arq.ls		// Enviou a saída de LS para arq.ls
xargs -n1 < arq.ls		// Exibiu 1 argumento por linha
arq1
arq2
arq3
arq.ls

xargs -n 2 < arq.ls		// Exibe 2 argumentos por linha
arq1 arq2
arq3 arq.ls

# Gerar loop, sem instruções de loop !
# Manda a saída de seq para xargs uma vez de cada vez !
# Salva no arquixo linhas.txt
seq 5 | xargs -n1 echo linha > linhas.txt
linha 1
linha 2
linha 3
linha 4
linha 5
```
-----
## Opção -l ou -L (max-lines)

* A opção -n pega a entrada primária, não linhas, 
* Para se pegar 'linhas' , usa-se a opção -L2
```
xargs -L2 < linhas.txt
linha 1 linha 2
linha 3 linha 4
linha 5
```
-----
## Opção -p (interactive)
```
xargs -p -L1 < linhas.txt
echo linha 1 ?...y
linha 1
echo linha 2 ?...y
linha 2
```
-----
## Opção -t

* Exibe instruções montadas, antes de ser executadas !
```
-i		Substitui um par de chaves ({}) pelas cadeias recebidas
-nNum	Manda o maior número de parâmetros recebidos, até o máximo de Num para o comando a ser executado
-LNum	Manda o maior número de linhas recebidas, até o máximo de Num para o comando a ser executado
-p		Mostra a linha de comando montada e pergunta se deseja executá-la
-t		Mostra a linha de comando que foi montada
```
-----

