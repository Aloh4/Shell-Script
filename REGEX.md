# REGEX

* Método formal para se especificar um padrão de texto.
* É composta por metacaracteres ** ? + * ^ $ \b [] {} () | . **

## Tabelas Aurélio Jargas !
https://www.dropbox.com/s/ntbw8a278fucgm2/expressoes-regulares-3-tabelas.pdf?dl=0

### Testes interativos:
https://www.piazinho.com.br/ed5/exemplos.html#3

---
# Dicas

* Não complique, nem sempre uma **ER** menor, será mais fácil de entender
* Use **Circunflexo**
* Evista **Lista Negada**
* Evite **Curinga**
* Seja **específico**
   * O que você quer casar ?
   * Em qual quantidade ?
   * Em qual contexto ou posição ?
* Não seja afobado, **seja nijna**

---
**Representantes**

* Representam caracteres:

``` 
.	  = Casa com qualquer caractere.
[] 	= Casa apenas com oque está dentro dela.
[^ ]   = Casa com tudo, menos com oque está dentro dela.
``` 

* Dentro das listas, podemos usar intervalos e classes POSIX.

```
[A-Za-z0-9]  = Lista de A-Z maiúsculo, a-z minúsculo e números
[[:xdigit:]] = Classe POSIX  de números hexadecimais
[[alnum]]    = Classe POSIX de A-Z maiúsculo, a-z minúsculo e números 
```

---
**Quantificadores**

* Indicam o número de repetição da entidade anterior:

``` 
?    = O opcional é..opcional, casa ou não o caractere anterior
*    = Casa em qualquer quantidade o caractere anterior...0...1...infinitas vezes
+    = Casa ao menos 1 vez o caractere anterior, de 1 a infinitas vezes	
{}   = Casa de N..a M o caractere anterior, {1,3} neste caso de 1 a 3 vezes...
```
* As chaves podem simular outros quantificadores:
 
```
{0,1} = equivale ao opcional ?
{0, } = Equivale ao asterisco
{1, } = Equivale ao +
```
---
**Âncoras**

* Marcam um ponto da linha a ser casado

```
^	= Indica início de linha '^[Cc]ase com sorvete'...'^[0-9]' -> case com 0-9'
$	= Indica fim de linha 'Case com sorvet[es]$'...'[0-9]$' -> case com 0-9'
\b   = Indica bordas no texto \b...\b
```
---
**Outros**

* Metacaracteres adicionais

```
\        = Escape, tira o "poder" do metacaractere ' \. ' ...torna o ponto literal.
|        = "ou" , Usado para decisões
( )      = Define um "bloco" na expressão, pode-se expandir com quantificadores.
\1..\9   = Retrovisores são usados em conjuntos com os grupos
           Os grupos (guardam) informações que podem ser acessadas pelos retrovisores \1.
```
---
# Dicas - REGEX
---

**Ponto**

* O ponto casa com qualquer coisa
* O ponto casa com o ponto
* O ponto é um curinga para casar um caractere

```
n.o - casa com - nao, não, neo, nuo ... 
```
------------------------------------------------------
**Lista**

* A lista casa com quem ela conhece e tem suas próprias regras
* Casa apenas 1 caractere por lista !
* Dentro da lista, todo mundo é normal
* Dentro da lista, traço indica intervalo
* Um **-** literal, deve ser o último item da lista
* Um **]** literal, deve ser o primeiro item da lista
* Os intervalos respeitam a tabela ASCII (Não usar A-z)
* **[:classes POSIX:]** incluem acentuação **[A-Z]** não.

```
n[aã]o - casa com - nao, não.
[A-Z]   - casa com letras de A-Z maiúsculas.
[0-9]   - casa com números.
[:alpha:] - casa letras maiúsculas e minúsculas com ou sem acento.
[:alnum:] - casa letras maiúsculas e minúsculas com ou sem acento e números.

```

------------------------------------------------------
**Lista Negada**

* Uma lista negada segue todas as regras de uma lista normal
* Um **^** literal não deve ser o primeiro item da lista
* **[:classes POSIX:]** podem ser negadas
* A lista negada sempre deve casar algo.

```
[^A-Z] - casa tudo, menos letras maiúsculas
[^[:digit:]] - casa tudo, menos números
grep -Eo [:;.!?][^ ] - casa pontuação, sem espaço em branco na sequência

o = only-matching = print only the matched parts. 
Each such match goes in a separated line.
```

------------------------------------------------------
**? Opcional ?**

* O opcional, é opcional
* O Opcional é útil para procurar palavras no singular e plural
* Podemos tornar opcionais caracteres e metacaracteres

```
fala[r!]? = casa com fala, falar, fala! 

**Obs:** Usando  'grep -Eo' percebe-se que o match se dá apenas nos caracteres da lista.
Os demais elementos casados se dão por ele ser um "quantificador" guloso.

```
------------------------------------------------------
** Asterístico * **

* O asterisco repete em qualquer quantidade
* Quantificadores são gulosos
* O curinga ** .* ** é o tudo e o nada, qualquer coisa !

```
7*0 = casa com 0, 70,770, 7770...
fala[r!]* = casa com fala, falar, fala!, falar!, falarrr, fala!!!, falarrr!!! ....
```
------------------------------------------------------
**+ Mais +**

* O mais repete em qualquer quantidade, pelo menos uma vez
* O mais é igual o asteristico, só mais exigente.

```
7+0 = casa com 70,770, 7770...
fala[r!]+ = casa com falar, fala!, falar!, falarrr, fala!!!, falarrr!! ....
```

------------------------------------------------------
** Chaves {n,m} **

* Chaves são precisas
* Pode-se especificar um número exato, mínimo, máximo ou faixa numérica de repetição
* As chaves podem simuar os metacaracteres ** ?  *  + **

```
[0-9]{1,3}    = Case uma lista de até 3 dígitos

^.{20,50}$ 
ou ^ {20,50}$ = Case uma linha em branco de 20 à 50 espaços

[A-Za-z]{3}   = Case uma lista de até 3 literais

{0,1} = Igual opcional
{0, } = Igual ao asterisco
{1, } = Igual ao mais
```
------------------------------------------------------
** ^Circunflexo^**


* Encontra palavras no início de linha
* Só é especial no início da ER

```
^[0-9]		= Case um número de 0-9 no início de linha
^[^A-Za-z]	= Case tudo, menos uma linha iniciada com letra

```
------------------------------------------------------
** $Cifrão$ **

* Procura palavras no fim da linha
* Só é especial no final da ER

```
[0-9]$		= Case um número de 0-9 no fim de linha
[^A-Za-z]$	= Case tudo, menos uma linha que termina com letra

```
------------------------------------------------------
** \b Borda \B **

* Marca limites de uma palavra
* Engloba letras, números e sublinhado
* A borda é útil para casar palavras exatas, não parciais
* \B indica não borda !

```
dia     = dia, diafragma, melodia, radial, bom-dia
\bdia   = dia, diafragma, bom-dia
dia\b   = dia, melodia, bom-dia
\bdia\b = dia, bom-dia
\Bdia   = melodia, radial
dia\B   = diafragma, radial
\Bdia\B = radial

```
------------------------------------------------------
** | ou | **

* O escape escapa um metacaractere, tirando seu 'poder'
* ** \\* ** é o mesmo que **[*]** = ' Asteristico literal'
* O escape escapa o escape ** \\\\ **

```
\.	= Torna o ponto literal
\\	= Torna uma contrabarra literal
\*é igual a [*] = Tornam o asterístico literal.
\. \? \{ \^ \+	 = Torna metacaracteres em literais
[0-9]\.[0-9]{3}\.[0-9]{3}-[0-9] = Procura um RG qualquer n.nnn.nnn-n

```
------------------------------------------------------
** | ou | **

* O OU indica alternativas
* Lista para um caractere, ou para vários
* O grupo multiplica o poder do OU

```
gato|pato|rato  = OU gato OU pato OU rato
gpr[ato]		= Igual o exemplo acima, melhor opção para 1 caractere
http|ftp		= OU http OU FTP

``` 
------------------------------------------------------
** ( ..grupo ..) **

* Grupos servem para agrupar decisões em blocos 
* Grupos podem conter grupos
* Grupos são quantificáveis

```
(ha)+			= casa com ha,haha,hahaha...
(\.[0-9]{3})	= casa com .1.2.3 ...
(super|hiper)?mercado		= OU supermercado OU hipermercado ...
((su|hi)per)?mercado		= OU supermercado OU hipermercado OU mercado
(mini|(su|hi)per)?mercado	= OU mercado OU supermercado OU hipermercado OU minimercado... 

```
------------------------------------------------------
** \1... Retrovisores \9 **

* O retrovisor só funciona se usado com o grupo
* O retrovisor, serve para procurar palavras repetidas
* Numeram-se os retrovisores, contando os grupos da **ESQUERDA** para **DIREITA**
* Existem no máximo 9 retrovisores por ER

```
(lenta)(mente) é \2 \1	   = lentamente é mente lenta
((band)eira)nte	 \1 \2a   = bandeirante bandeira banda
((((a)b)c)d) \1 \2 \3 \4     = abcd, abc, ab, a 		

((((grupo quatro)grupo três)grupo dois) grupo um)
```
------------------------------------------------------
** Classes Posix **

```
[:upper:] [A-Z] Letras maiúsculas
[:lower:] [a-z] Letras minúsculas
[:alpha:] [A-Za-z] Maiúsculas e minúsculas
[:alnum:] [A-Za-z0-9] Letras e números
[:digit:] [0-9] Números
[:xdigit:] [0-9A-Fa-f] Números hexadecimais
[:punct:] [.,!?:...] Caracteres de pontuação
[:blank:] [ \t] Espaço em branco e TAB
[:space:] [ \t\n\r\f\v] Caracteres brancos
[:cntrl:] Caracteres de controle
[:graph:] [^ \t\n\r\f\v] Caracteres imprimíveis
[:print:] [^\t\n\r\f\v] Imprimíveis e o espaço
```
** Remendos **

```
[[:lower:]] [a-zà-ü]
[[:upper:]] [A-ZÀ-Ü]
[[:alpha:]] [A-Za-zÀ-ü]
[[:alnum:]] [A-Za-zÀ-ü0-9]
```
