# SED

*As opções abaixo podem ser usadas de maneira mista, utilizando ponto e vírgula*

**Comandos que atuam na linha inteira:**

```
sed '1 i 4' <<< 5 # Na linha 1, insira um 4
sed '1 a 6' <<< 5 # Após linha 1, insira um 6
sed '1 c outro 5' <<< 5 # Substitua 5, por "outro 5"
p   # imprim e / d - deleta / !p/!d nao imprime/nao deleta
-p  # Imprima tudo, incluindo oque foi explicitado (2x)
-!p # Imprima tudo 2x, Menos oque foi explicitado
-d  # Delete apenas oque foi explicitado
-!d # Delete tudo, menos oque foi explicitado
```
---------
# Printando linhas (p)

**Obs:**

```
,		# Vírgula delimita linhas
/ /		# Barras casam valores
```

**Exemplos**
```
seq 2 2 14 				# Sequência a partir de 2, saltando de 2 em 2 até 14

seq 5 | sed -n '3p'			# Imprimi a linha 3 

seq 2 2 14 | sed '3,5p'    		# Imprimir da 3ª a 5ª linha, será exibido o arquivo todo e as linhas 3 a 5 editadas a mais
2 4 6 6 8 8 10 10 12 14

seq 2 2 14 | sed -n '3,5p' 		# Com a opção -n 'quiet', será exibido apenas oque for ordenado
6 8 10

seq 2 2 14 | sed -n '/2/,/4/p' 		# Lista as linhas que tem nº 2 e 4 (cadeias de caracteres)
2 4 12 14				# obs: Irá listar todas as entradas com 2 e 4
seq 2 2 14 | sed -n '/^2$/,/^4$/'   	# Para tal, usa-se regex ^ e $ para delimitar limites
   
seq 5 | sed '1d;5d'			# Exemplo com ponto e vírgula
2 3 4

seq 5 | sed '1,2d;4,5d'			# Exemplo com ponto e vírgula
3

seq 5 | sed -n '/1/,/2/p;/3/,/4/p;'	# Exemplo com ponto e vírgula
1 2 3 4

* Abaixo, o exemplo nao funcionaria como esperado, 
* Pois casa a sequência entre root e sync, não apenas os 2:

cat /etc/passwd | sed -rn '/^root/,/^sync/p'							   
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync


* Linha especifica do arquivo:
root@whoami:~# cat /etc/passwd | sed -n '1p'
root:x:0:0:root:/root:/bin/bash
```

------------
# Deletando linhas (d)

* Mesmo uso do comando anterior(p), porém utilizando 'd'

**Importante !**

* Para um efeito 'reverso', pode-se usar (!) a negação do delete.
* Melhor do que a opção -n !!
* Imprimi apenas a linha encontrada !
seq 2 2 14 | sed '/2/,/4/!d'
2 4 12 14

## Ou ainda o reverso do d, utilizando -n e !p
    seq 2 2 14 | sed -n '/2/,/4/!p'

**Exemplos:**
```
* Deleta apenas a 3º linha:
seq 5 | sed '3d' 

* Deletar linhas 3 a 5
seq 2 2 14 | sed '3,5d'
2 4 12 14

* Deleta linhas que tem os nº 2 e 4
seq 2 2 14 | sed '/2/,/4/d'
6 8 10

* Deleta a última linha
* Obs: $ significa fim de linha, entre barras é REGEX !
seq 3 | sed '$d'
1 2

## Exemplo com ponto e vírgula
seq 5 | sed '1d;5d'
2 3 4

## Exemplo com ponto e vírgula
seq 5 | sed '/1/,/2/d;/3/,/4/d;'
5
```
------------------
# Inserindo/Trocando linhas (a , i , c)


* O uso da contrabarra é dependente do sistema a ser utilizado !
* Pode-ser usar um espaço 
* O Contrabarra permite inserção de espaços

```
Comando	Significado		Definição
a		append 		(acrescentar)		Anexa linha(s) após o local estipulado
i		insert 		(insere)		Insere linha(s) antes do local estipulado
c		change 		(troca)			Troca linha(s) estipulada(s)
```

**Exemplos**

```
## Obs para esssa opção, deve ser usado o argumento -e, ponto e virgula não funcionaria
Exemplo correto:
seq 5 | sed -e '/1/,/2/c\#' -e '/3/,/4/c\##'
# ## 5
Exemplo errado:
seq 5 | sed '1,3a\##; 4i\!!'
1 ##; 4i!! 2
Intervalo Misto OK
seq 5 | sed -e '/1/,/2/d;/3/,/4/a\%%%'
3 %%% 4 %%% 5

## Obs: Para todos os exemplos abaixo, o uso de intervalos de cadeias é válido !
* Exemplo:
* ` seq 2 2 14 | sed '/2/,/4/a\###'  `

* Adiciona linha após a linha X
seq 5 | sed '3a\AAA'  
1 2 3 AAA 4 5

* Adiciona linha antes da linha X
seq 5 | sed '3i\AAA' 
1 2 AAA 3 4 5

* Substitui a linha X	   
seq 5 | sed '3c\AAA'
1 2 AAA 4 5

* Editando múltiplas linhas:
seq 5 | sed '2,4a\###'
1 2 ### 3 ### 4 ### 5

seq 5 | sed '2,4i\###'
1 ### 2 ### 3 ### 4 5

seq 5 | sed '2,4c\###'
1 ### 5

* Inserindo múltiplas linhas
* Pode ser feito com prompt de continuação ou notações \n \t , etc.

seq 5 | sed '3c\###\n###'
1 2 ### ### 4 5

seq 5 | sed '3c\
> ###\
> ###'
1 2 ### ### 4 5

* Inserir linha no final do arquivo:
seq 5 | sed '$a\6'
1 2 3 4 5 6 
```
-----
# Substituir cadeias (s)

* Troca as ocorrências(cadeias de caracteres) de uma *REGEX* por um novo valor especificado
* Atua em todas as linhas do texto
* `sed 's/REGEX/ALTERAÇÃO/' ARQ`
* `sed -r 's/REGEX/ALTERAÇÃO/' ARQ` - *Extended Regex*

* Com o comand substitute (s), podemos escolher qual ocorrencia será substituida
sed 's/REGEX/Texto/2' <- substitui na segunda ocorrencia
sed 's/REGEX/Texto/3' <- substitui na terceira ocorrencia

* Caso usar a ocorrencia + a opçao global (g), será substituido a partir da ocorrencia X
sed 's/REGEX/Texto/3g' <- substitui da terceira ocorrencia pra frente !
	
**Exemplo de Sintaxe**

```
# sed 's/So/Ana/' <<< Solista
Analista

s			Comando propriamente dito
/../../		Delimitadores
So			Expressão Regular que será pesquisada
Ana			Texto da substituição

* SED substitute c/ a opção I, ignore case-sensitive -- o recomendado é -I,  !
Pois temos...
sed 'i\'      -> insert
sed -i  ' '   -> edit in file
sed 's/ / /I' -> Ignore case-sensitive

sed 's/unix/Linux/I' <<< "Gosto de Unix"
-> Gosto de Linux

```
	
**Exemplos:**

```
## Alterar somente a primeira ocorrência
sed 's/a/X/' <<< abracadabra
Xbracadabra

## Alterar ocorrência específica
sed 's/a/X/5' <<< abracadabra
abracadabrX

## Alterar todas as ocorrências (flag G = Global)
sed 's/a/X/g' <<< abracadabra
XbrXcXdXbrX

## Trocar todas as linhas, a partir da segunda
sed 's/a/X/2g' <<< abracadabra
abrXcXdXbrX
```

-----------
#Dicas sobre Delimitadores

* A barra ` \ ` não são obrigatórias como delimitadores
* O `sed` entenderá como delimitador, qualquer caractere após o `s`
* A contrabarra `\` pode:
   * Escapar a barra `/` com `\/`
   * Escapar o próprio contrabarra com `\\`

* Obs: Todos os exemplos abaixo podem ser feitos com REGEX
* Usando listas ao invés de escapes:
sed -r 's|[\]|/|g' <<< '\teste\'


```
## Trocar barra por contrabarra: dir Linux -> Windows
* Utilizou a contrabarra \ para escapar a barra /
* Utilizou a contrabarra \ para escapar a contrabarra \\ 

sed 's/\//\\/g' <<< /usr/local/bin/prg.teste
\usr\local\bin\prg.teste

Detalhado: sed 's/ \/ / \\ /g' <<< /usr/local/bin/prg.teste

#Trocar contrabarra, por barra:
sed -r 's/\\/\//g' <<< '\teste\'
/teste/

ou...

sed -r 's|\\|/|g' <<< '\teste\'
/teste/


## Trocar diretório, ex: /usr/local/bin/ por /usr/local/var/
sed 's/\/usr\/local\/bin/\/teste1\/teste2\/teste3/' arqs
/teste1/teste2/teste3
 
Detalhado: s/ \/usr \/local \/bin / \/teste1 \/teste2 \/teste3

##Usando outros delimitadores
* Pode-se usar qualquer delimitador
* Preferencialmente, que não será usado nos argumentos do comando

sed 's|/|\\|g' <<< /usr/local/bin
\usr\local\bin

# Quando usado apenas delimitadores de pesquisa (e não substituição)
# É necessário 'escapar' o primeiro delimitador:
sed '\|^\/|!d' <<< /teste/teste
Detalhado: sed '\| ^\/ |!d'

Outros...
## Exibir registros que comecem com /
sed '/^\//!d' arqs

```

------------
# Opção -i (edit files 'in place')
**OBS: Edita o arquivo original !**

```
#Lê o arquivo arq e manda a saída para arq2
sed 's/A/#/' arq > arq2

# Cria uma cópia do arq (arq.bkp) e edita arq (original)
sed -i.bkp 's/A/#/' arq

cat arq -> #BCDE (original editado)
cat arq.bkp -> ABCDE (cópia antes da edição)


# Altera arq diretamente !
sed -i 's/A/#/' arq
```

**Exemplo para deletar entradas de arquivo, usando outro arquivo:**

```
cat stringsed
AAA
BBB
CCC

sed -i.bkp 's|^|/^| ; s|$|/d|' stringsed

cat stringsed
/^AAA/d
/^BBB/d
/^CCC/d

cat stringsed.bkp
AAA
BBB
CCC

sed -i.bkp -f stringsed stringsed.bkp

ls -l stri*
stringsed
stringsed.bkp
stringsed.bkp.bkp

cat stringsed
/^AAA/d
/^BBB/d
/^CCC/d

cat stringsed.bkp
Vazio
cat stringsed.bkp.bkp
AAA
BBB
CCC
```

-------
# Opção -f (file--script)

**Utiliza um arquivo pré-existente para realizar sua operação.** 

**Exemplos** 
```
#Criar um arquivo com entradas a serem deletadas:
cat exclui:
postflix .. gdm.. root

# Preparar as entradas do arquivo:
sed -i 's|^|/^|;s|$|:/d|'
cat exclui:
/^postfix:/d ... /^gdm:/d ... /^root:/d

Obs:
#Utilizar ; para acrescentar dois comandos ao sed
#No sed como no Shell o ponto e vírgula ( ;) serve para colocar dois ou mais comandos na mesma linha)
sed -i 's|^|/^|;s|$|:/d|'
Detalhado: sed -i 's|^|/^| ; s|$|:/d|'

#Utilizar o arquivo criado com sed
sed -f exclui /etc/passwd -> Os usuários do arquivo exclui, não existirão na saída

#Para edição permanente, utilizar a opção -in
sed -i -f exclui /etc/passwd
ou...
sed -i.bkp -f exclui /etc/passwd

#Simplicado:

cat >> exclui2
s/A/#/

sed -f exclui2 <<< ABCDE
#BCDE
```
--------
# Opção -s

* **Edita vários arquivos de uma vez**

**Exemplos**
```
#Deleta a terceira da linha dos arquivos:
sed -s '3d' arq1 arq2 arq3

#Print a terceira da linha dos arquivos:
sed -s '3!d' arq1 arq2 arq3
```
------------
# Opção (e)

* **Editar entradas distintas no mesmo arquivo (igual o uso de `;`)**

**Exemplos**
```
# Com a opção -e
seq 3 | sed -e '1 i\0' -e '$ a\4'
0 1 2 3 4

# Modo original com pipe (cria subshell)
seq 3 | sed '1i\0' | sed '4a\4' // Inseriu 0 no início, 3 virou a 'quarta linha'
0 1 2 3 4

# Com Regex
seq 3 | sed '1 i\0' | sed '$ a\4'
```
--------------
# Substituir caracteres (y)

* Semelhate a opção `s`

* `s` Pesquisa por cadeias = **REGEX**
* `y` Pesquisa caractere e o substitui  

* Troca um a um dos caracteres da ENTRADA pela SAÍDA
* A quantidade de caracteres deve ser igual !
* Sintaxe: `sed 'y/ENTRADA/SAIDA' `

sed 'y/aeiou/12345/' <<< inconstitucionalissimamente
3nc4nst3t5c34n1l3ss3m1m2nt2

```
Criação de nomes

ABCEGIJOPQSTXZ
48(361|0?957*2

IZEASGTBQO
1234567890

OIZEASGTBQ
0123456789

sed 'y/ABCEGIJOPQSTXZ/48(361|0?957*2/' <<< BLACKHORSE
8L4(KH0R53

```
