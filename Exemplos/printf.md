## Formatando a saída com printf

**Sintaxe**
```
printf [-v VAR] FMT [ARG...]
```

Onde:

* FMT - é uma cadeia de caracteres que contem 3 tipos de objeto:

	* 1 - caracteres simples;
	* 2 - caracteres para especificação de formato;
	* 3 - sequência de escape no padrão da linguagem C.

* ARG - é a cadeia a ser impressa sob o controle do formato FMT.
* VAR - é o nome da variável que armazenará a saída do comando, caso a opção -v seja usada


## Os caracteres nesta especificação, são precedidos por `%`

```
Caracteres para especificação de formato
Letra	O argumento (ARG) será impresso como
c	Simples caractere
d	Número no sistema decimal
e	Notação científica exponencial
f	Número com ponto decimal (float)
g	O menor entre os formatos %e e %f com supressão dos zeros não significativos
o	Número no sistema octal
s	Cadeia de caracteres
x	Número no sistema hexadecimal [0-9A-Z]
%	Imprime um %. Não existe nenhuma conversão
```

## Os caracteres nesta especificação, são precedidos por `\`
```
Sequencias de escape no padrão da linguagem C
Sequência	Efeito
a	Soa o beep
b	Volta uma posição (backspace)
f	Salta para a próxima página lógica (form feed)
n	Salta para o início da linha seguinte (line feed)
r	Volta para o início da linha corrente (carriage return)
t	Avança para a próxima marca de tabulação
```

**Exemplos**
```
$ printf "%c" "1 caracter"
1$               # Errado! Só listou 1 caractere e não saltou linha ao final
$ printf "%c\n" "1 caracter"
1                # Saltou linha mas ainda não listou a cadeia inteira
$ printf "%c caractere\n" 1
1 caractere      # Esta é a forma correta o %c recebeu o 1
$ a=2
$ printf "%c caracteres\n" $a
2 caracteres     # O %c recebeu o valor da variável $a
$ printf "%10c caracteres\n" $a
2 caracteres
$ printf "%10c\n" $a caracteres
         2
         c

printf "%s caracteres\n" 1 2 3
1 caracteres
2 caracteres
3 caracteres

		 
```

* Um número seguido do sinal `%` significa o tamanho que a cadeia terá, após a execução do comando

**Exemplos 2**
```
$ printf "%d\n" 32
32
$ printf "%10d\n" 32
        32              # Preenche com 8 brancos à esquerda e não com zeros
$ printf "%04d\n" 32
0032                    # 04 após % significa 4 dígitos com zeros à esquerda
$ printf "%e\n" $(echo "scale=2 ; 100/6" | bc)
1.666000e+01            # O default do %e é 6 decimais
$ printf "%.2e\n" `echo "scale=2 ; 100/6" | bc`
1.67e+01                # O .2 especificou duas decimais
$ printf "%f\n" 32.3
32.300000               # O default do %f é 6 decimais
$ printf "%.2f\n" 32.3
32.30                   # O .2 especificou duas decimais
$ printf "%.3f\n" `echo "scale=2 ; 100/6" | bc`
33.330                  # O bc devolveu 2 decimais. o printf colocou 0 à direita
```


* O BC, sempre usará um ponto como separador de decimais
* Em alguns sistemas, o printf só reconhece a vírgula 
* Para evitar erros, use printf com a cadeia `LC_ALL=C`
* Ambos os comandos serão executados no local default (LC é derivado de locale, ALL é tudo e C é o padrão)

**Exemplos**
```
$ printf "%e\n" $(echo "scale=2 ; 100/6" | bc)    
bash: printf: 16.66: número inválido    
0,000000e+00    
$ LC_ALL=C printf "%e\n" $(echo "scale=2 ; 100/6" | bc)    
1.666000e+01
```

**Continuação dos exemplos**
```
$ printf "%o\n" 10
12                      # Converteu o 10 para octal
$ printf "%03o\n" 27
033                     # Assim a conversão fica com mais jeito de octal, né?
$ printf "%s\n" Peteleca
Peteleca
$ printf "%15s\n" Peteleca
       Peteleca         # Peteleca + brancos à esquerda com total de 15 caracteres    
$ printf "%-15sNeves\n" Peteleca
Peteleca       Neves    # O menos (-) encheu com brancos à direita de Peteleca
$ printf "%.3s\n" Peteleca
Pet                     # 3 trunca as 3 primeiras
$ printf "%10.3sa\n" Peteleca

       Peta             # Pet com 10 caracteres concatenado com a (após o s)

$ printf "EXEMPLO %x\n" 45232
EXEMPLO b0b0            # Transformou para hexa mas os zeros não combinam
$ printf "EXEMPLO %X\n" 45232
EXEMPLO B0B0            # Assim disfarçou melhor (repare o X maiúsculo)
$ printf "%X %XL%X\n" 49354 192 10
C0CA C0LA
```


**Exemplos Práticos**
```
#Print enviou 20 espaços em branco ao TR
#TR alterou todos os espaços por - 
printf "%20s\n" | tr ' ' -
--------------------

# O mesmo efeito para todo o terminal
 printf "%$(tput cols)s" ' ' | tr ' ' -
------------------------------------------------- .....


```
