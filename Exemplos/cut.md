## CUT

* Corta um determinado pedaço de um arquivo
* Possui duas maneiras distintas de uso

**Opcões adicionais cut**
```
--complement // Lista tudo, menos o campo definido
--output-delimiter CAD  // Especifica a cadeia CAD para ser o novo delimitador na saida do comando
```

**Exemplo**
```
seq -s: 5 | cut -f3 -d:
3

seq -s: 5 | cut -f3 -d: --complement
1:2:4:5

seq -s: 5 | cut -f3 -d: --complement --output-delimiter $'\t'
1       2       4       5
```

-----
# Opção -c (characters)

* Útil para arquivos de tamanho fixo
* PosINI = Posição inicial
* PosFim = Posição final

```
Sintáxe: `cut -cPosINI-PosFim [arquivo]` 
Sintáxe: `INI1-FIM1, ... , INIn-FIMn` 
Sintáxe: `INI1,INI2,INI3-FIM1` 
Sintáxe: `-FIM1, ...` 
Sintáxe: `INI1-, ...` 
```

```
## Exibe da 1º a 3º posição:
cut -c1-3 numeros
123
098

## Se PosINI for omitida, será exibido até a posição final:
cut -c-5 numeros		// Exibe da primeira posição até a 5º
12345
09876

## Se PosFIM for omitida, será exibido tudo a partir da posição inicial até a última posição:
cut -c5- numeros			// Exibe da 5º posição até a posição final
567890
654321

##Pode-se definir posições especificas:
cut -c5 numeros				// Exibe a 5º posição
cut -c3,5,8 numeros 		// Exibe a 3º,5º e 8º posição
cut -c3,5,8- numeros		// Exibe a 3º,5º,8º até a posição final
```
-----
## Opção -f (fields)

* Usa o conceito de fields
* Corta o campo escolhido, usando delimitadores (-d)
* Aceita apenas um (separador)delimitador
* Se o campo delimitado não existir, nada será exibido
* Se não for especificado delimitador, será utilizado o primeiro TAB como tal.
* Pode-se escolher intervalos, ou campos especificos !

**Exemplos com Delimitadores:**

```
## Exibe as colunas de 1 a 3 
ls -l | cut -f1-3 -d" "

## Exibe as colunas 1 e 3
ls -l | cut -f1,3 -d" "

cat musicas
album 1^Artista1~Musica1:Artista2~Musica2 

album 1^							// Campo 1^
Artista1~Musica1:Artista2~Musica2	// Campo 2^

album 1^Artista1~				// Campo 1~
Musica1:Artista2~				// Campo 2~
Musica2								  // Campo 3~

album 1^Artista1~Musica1:		// Campo 1:
Artista2~Musica2 					// Campo 2:

```
-----
## Opção -s (only-delimited)

* Caso um campo não possua o delimitador especificado, nada será exibido.

**Sem opção -s (todos os arquivos serão exibidos)**
```
# ls | cut -f2 -d.
arq1 py sh arq2 py sh
```

**Com opção -s (serão exibidos apenas os arquivos que possuem o delimitador)**
```
# ls | cut -sf2 -d.
py sh py sh
```


**Exemplos**
```
## Delimitador ^

$ cut -f1 -d^ musicas
album 1

$ cut -f2 -d^ musicas
Artista1~Musica1:Artista2~Musica2

## Delimitador ~

$ cut -f1 -d~ musicas
album 1^Artista1

$ cut -f2 -d~ musicas
Musica1:Artista2

$ cut -f3 -d~ musicas
Musica2

## Delimitador :
$ cut -f1 -d: musicas
album 1^Artista1~Musica1

$ cut -f2 -d: musicas
Artista2~Musica2

## "Cortando" a saída do CUT com outro CUT
# Obteve a saída Artista2~Musica2 e cortou com o primeiro delimitador de ~
$ cut -f2 -d: musicas | cut -f1 -d~
Artista2

Ou...
# Obteve a saída album 1^Artista1~ e cortou com o segundo delimitador de ^
$ cut -f1 -d~ musicas | cut -f2 -d^
Artista1

# Sem uso de delimitador, o primeiro TAB se torna o delimitador:
$ cat musicas
album 1^Artista1~Musica1:Artista2~      Musica2

$ cut -f1 musicas
album 1^Artista1~Musica1:Artista2~

$ cut -f2 musicas
Musica2

```


