## Funcionalidades Grep

* *Global Regular Expression Print*
* Pesquisa ocorrências de cadeia de caracteres na entrada definida
* Em alguns ambientes, pode ser necessário utilizar:
   * `fgrep` -> fast, usa ou não **REGEX**, atalho `grep -f`
   * `grep`  -> normal, não permite **REGEX**
   * `egrep` -> REGEX extendido, permite **REGEX**, atalho `grep -E`

**Uso da opção `grep -E` , quando houver:**

```
Ponto de interrogação	?
Sinal de adição	+
Parênteses	()
Chaves	{}
Barra Vertical	|
``` 
**Principais parâmetros:**

```
Opção -E	Extended regex

Opção -l	Só mostra o nome dos arquivos nos quais houve casamento
Opção -c	Somente a quantidade de linhas que casarem com uma expressão regular.
Opção -n	Precede cada linha com seu número relativo dentro de arquivo.

Opção -v	Lista tudo, menos as linhas nas quais houve casamento
Opção -i	A pesquisa ignora caixa alta e caixa baixa
Opção -o	Mostra somente o texto que casou

Opção -w	Só casa com palavras inteiras e completas
Opção -f	Indica o arquivo que contém as Expressões Regulares que serão pesquisadas

```

**Complementares**


## Pegar linha especifica do arquivo somente com grep
grep -E '^3\b' <<< $(grep -n '.*' /etc/passwd)
3:bin:x:2:2:bin:/bin:/usr/sbin/nologin

1244  grep -Eo '^[A-Za-z]+:x:([0-9]+):\1'  /etc/passwd | grep -o '^[A-Za-z]+'
1245  grep -Eo '^[A-Za-z]+:x:([0-9]+):\1'  /etc/passwd | grep -o '^[A-Za-z]*'

#grep -f usus /etc/passwd // -> Pesquisa em /etc/passm utilizando a cadeia de caracteres de usus

# ls -l | grep ^d| grep -o '[^ ]*$' <- apenas os nomes dos subdiretorios do diretório corrente

##Apenas nomes de users com GUID:UID iguais
root@whoami:~# grep -Eo '^[A-Za-z]+' <<< $(grep -Eo '^[A-Za-z]+:x:([0-9]+):\1' /etc/passwd)

Para procurar as linhas de um arquivo que têm exatamente 20 caracteres, posso fazer:
grep -E '^.{20}$' arquivo

ou sem a opção -E, colocando uma contrabarra antes de cada caractere que configura Expressão Regular avançada:
grep '^.\{20\}$' arquivo 

## Precisam da opção -E
Ponto de interrogação	?
Sinal de adição	+
Parênteses	()
Chaves	{}
Barra Vertical	|
