# Resumo

**Linux é um sistema multiusuário**

* **Shell** = Primeiro programa *ganhado* no linux.

* Cada usuário tem o seu próprio shell, interpondo-se entre o linux.
* O **Shell** interpreta os comandos e os envia para o linux !

**Tipos de shell mais usados**

* Bourne Shell **(sh)**
* Korn Shell **(ksh)**
* Born Again Shell **(bash)**
* C Shell **(csh)**


**Curiosidades**

* Divisão do arquivo **/etc/passwd**
    * Cada linha possui 7 campos, divididos por **:**

```
user:validate-pass:user-identifier-UID:group-identifier-GUID:comments:path-directory:starter-program
```	

* É possível definir *shutdown* em starter-program, e toda vez que o user **X** logar-se, a máquina seria desligada.

-------
# Teoria

**Camadas do Linux**

```
Hardware -> Kernel -> Programas e Comandos -> Shell
```

* O **shell** avalia as linhas digitadas em pedaços, separados por espaços/brancos
* O *Branco/Espaço* é um caractere reservado !
* O **shell** identifica os caracteres reservados entre **Atribuição** e **Interpretação** 

* Se o **shell** encontra, dois campos separados por um sinal de **=**, ele identifica como sendo uma **atribuição**
   * Fazendo assim, a atribuição de um valor a uma variável 

   * **Atribuição**
      * ** var=1000**
	  * O **shell** identificou uma atribuição e colocou **1000** na variável **var**


* Caso o **shell** identifique um campo separado por espaço, ele identifica como sendo um comando e realizará uma **interpretação**.
* O primeiro pedaço será o nome do programa e os demais trechos, seus parâmetros.
* O shell então verifica a existência do programa e suas permissões.
* Em seguida, ele avalia parâmetros, redirecionamentos e variáveis.

   * **Interpretação**
      * ** ls -l **
      * O **shell** identificou um espaço em branco e **interpretou** como sendo um parâmetro para o programa ** LS **	  

* Uma vez que o Shell descobriu tratar-se de um comando, ele irá cumprir as seguintes tarefas, em sua ordem de execução:

**1 - Resolução de redirecionamentos;**

* **Entrada (stdin)   < **
* **Saída   (stdout)  > **
* **Erros   (stderr)  2> **

**2 - Substituição das variáveis pelos seus valores;**

* Definir variável: ** var=1000 **
* Remover o valor da variável: ** unset var**

* Nomes válidos para variáveis:

```
Válidos		Não válidos

Var			1Var
Var1		var:
_VAR_1		123

```

**3 - Expansão de chaves e substituição de metacaracteres;**

  * ** Expansão de chaves **
  * Na expansão de chaves ** { } ** não existem metacaracteres !
  * O conteúdo das chaves se expandem, como em uma **REGEX** para textos (arquivos) já definidos/existentes
  * Elas atuam de duas maneiras:
  
     * Formando sequências
	 * Expandindo textos
	 * **{INICIO...FIM}  {INICIO,FIM} {INICIO...FIM..INTERVALO}**

* A expansão de chaves casa **Prefixo e Sufixo**
* As cadeis formadas, são separadas por espaços em branco
* Se um componente entre ** , ** estiver vazio *sem elemento* após a vírgula, não haverá nada entre **Prefixo e Sufixo**


* OBS: Os intervalos não precisam ser exatos ! *
  
** Exemplos **

```
echo {3..15}, echo {15..3},  echo {c..s}, echo {Z..a} 

echo {0010..0019}    # Preenchendo com 2 zeros à esquerda
0010 0011 0012 0013 0014 0015 0016 0017 0018 0019 

echo {-5..19..3}    # Incrementando de 3 em 3 (Passo 3)
-5 -2 1 4 7 10 13 16 19 

echo Pe{itu,la,ga,}da

echo caminho/completo/do/arquivo{,.bkp}
caminho/completo/do/arquivo caminho/completo/do/arquivo.bkp 

cp caminho/completo/do/arquivo{,.bkp}
mkdir /usr/local/{bin,etc,games,include,lib,sbin,share,src}

echo prg{1..3}.{sh,c,py}

```

  
  * ** Substituição de metacaracteres **
  
  * Na substituição de metacaracteres, os caracteres coringas se expandem para casar com nomes de arquivos;
  * Se algum metacaractere for encontrado, ele será substituído pelos seus possíveis valores !
  * ** * , ? , [ ] , [! ] ou [^ ] **  <- para REGEX ( grep )
  
** Exemplos **

```
Exemplo * = ARQ* *ARQ *ARQ* -> Casa vários
Exemplo ? = AR?  ?RQ   A?? -> Casa apenas um
Exemplo [ ] = [A-Za-z0-9] [1-13] <- Errado !, casa o intervalo 1 a 1 e 3, não casará 13! 
Exemplo [! ]/[^ ] = [!A-Za-z0-9] <- Casa tudo !, menos o conteúdo da lista (perigoso)

```

**4 - Resolução de aliases;**

   * Abrevição de comandos
   * ** #alias  #unalias**
   * Pode-se definir uma cadeia de comandos com ** ( ; ) **


*Finalmente* 

**5 - Verifica se o comando existe e se está com a execução permitida;** <br>
**6 - Finalmente manda a linha interpretada para a execução.**
