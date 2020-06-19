## PASTE

* Todo cut, trabalha com paste
* Cut e Paste sem delimitador, usam TAB como delimitador default
* O Paste cola 2 ou mais arquivos lado a lado, podendo ter mais de um delimitador 
* Mandará para a saída(stdout) todos os registros dos arquivos citados

Sintáxe: `paste arq1 arq2 ... arqN` 

------
**Exemplos:**

```
$ seq 1 3 9 > impar
$ seq 2 2 10 > par
$ paste impar par	// A saída será uma do lado da outra, e não em sobreposição como seria com o cat
1	2
3	4
5	6
7	8
9	10

cat impar par
1
3
5
7
9
2
```
------
## Opção -s (serial)

* Exibe uma linha, ao invez de linhas paralelas 
* Ou seja coloca lado a lado linhas de um arquivo "Oque está em pé, deita"
* Transforma linhas em colunas.

```
$ paste -s impar par	// "Deita" tudo que está em pé (exibe linhas em colunas)
1       3       5       7       9
2       4       6       8       10

```
-----
## Opção -d (delimiter)

* Defini delimitadores, como o cut
* Pode-se especificar um novo delimitador com a opção -d
* Aceita vários (separadores) delimitadores

```
$ paste par -s -d +
2+4+6+8+10

* Logo, podemos enviar a saída para ser calculada com o BC

$ paste par -s -d + | bc
30
```

## Delimitadores Paste:

```
#Aceitou mais de um separador, mas somente um após cada coluna criada pelo comando
$ ls | paste -s -d '\t\t\n'		# Poderia ser -sd'\t\t\n' ou -sd '\t\t\n'
arq1    arq2    arq3
arq4    arq5    arq6

* Paste lista arquivos lado lado,
* cat lista arquivos sequencialmente
* Por convenção, o traço - recebe os dados da entrada primária

# LS enviou a saída(stdout) para o pipe
# Pipe desviou para a entrada default (stdin) do paste
# O comando paste substitui o traço pela entrada primária (saída de ls)
$ ls | paste - - -
arq1    arq2    arq3
arq4    arq5    arq6
```

## Exemplos práticos:

```
$ cat arq1
predisposição
privilegiado
profissional

$ cat arq2
encher
mário
motor

## -d "" -> indica sem separador
$ cut -c-3 arq1 | paste -d "" - arq2
preencher
primário
promotor
```

```
$ paste impar par		// A saída será uma do lado da outra, e não em sobreposição como seria com o cat
1	2
3	4
5	6
7	8
9	10

$ paste -s impar par	// "Deita" tudo que está em pé (exibe linhas em colunas)
1       3       5       7       9
2       4       6       8       10
```

**Com delimitadores**
```
$ paste par -s -d +
2+4+6+8+10

$ ls | paste -s -d '\t\t\n'		# Poderia ser -sd'\t\t\n' ou -sd '\t\t\n'
arq1    arq2    arq3
arq4    arq5    arq6

$ ls | paste - - -		// Traço recebe a entrada primária (stdin)
arq1    arq2    arq3
arq4    arq5    arq6
```

**Definir cabeçalho**
```
$ paste impar par > numeros		# Gravando a saída em numeros
$ cat numeros
1       2
3       4
5       6
7       8
9       10

# Cat tem 2 membros, 
# A entrada default (echo, definida em -) e o arquivo numeros
# Listou-o um após o outro, não ao lado como o paste faria
echo -e 'Impares\tPares\n=======\t=====' | cat - numeros
Impares Pares
======= =====
1       2
3       4
5       6
7       8
9       10

Pode-se usar:
$ echo -e 'Impar\tPar' | cat - <(paste <(seq 1 2 9) <(seq 2 2 10))
ou...
$ echo -e 'Impar\tPar' | cat - <(paste impar par)

```
