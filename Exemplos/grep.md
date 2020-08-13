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
Opção -q        quiet, não exibe nada na saída padrão (apenas altera o valor de $?)
Opção -r        recursivo, procura em todos os arquivos abaixo de um diretório

Opção -v	Lista tudo, menos as linhas nas quais houve casamento
Opção -i	A pesquisa ignora caixa alta e caixa baixa
Opção -o	Mostra somente o texto que casou

Opção -w	Só casa com palavras inteiras e completas
Opção -f	Indica o arquivo que contém as Expressões Regulares que serão pesquisadas

```

**Complementares**


**Removendo entradas:**

```
grep -v "$*" musicas > /tmp/mus$$
mv -f /tmp/mus$$ musicas

1 - Utilizou a opção -v para inverter a busca do grep
2 - Com isso, o shell devolveu tudo, menos o padrão de busca (fazendo a função 'delete')
3 - Enviou para um arquivo temporário em /tmp/mus$$
4 - Substitiu o arquivo original pelo criado em /tmp/mus$$, gerando uma nova listagem (com um elemento deletado)

```

**Pegar linha especifica do arquivo somente com grep**
```
grep -E '^3\b' <<< $(grep -n '.*' /etc/passwd)
3:bin:x:2:2:bin:/bin:/usr/sbin/nologin
```

**Pesquisa em /etc/passm utilizando a cadeia de caracteres do arquivo usus**
```
#grep -f usus /etc/passwd
# ls -l | grep ^d| grep -o '[^ ]*$' <- apenas os nomes dos subdiretorios do diretório corrente
```

##Apenas nomes de users com GUID:UID iguais
```
root@whoami:~# grep -Eo '^[A-Za-z]+' <<< $(grep -Eo '^[A-Za-z]+:x:([0-9]+):\1' /etc/passwd)
```

**Procurar as linhas de um arquivo que têm exatamente 20 caracteres, posso fazer:**
```
grep -E '^.{20}$' arquivo

ou sem a opção -E, colocando uma contrabarra antes de cada caractere que configura Expressão Regular avançada:
grep '^.\{20\}$' arquivo 
```

## Precisam da opção -E
```
Ponto de interrogação	?
Sinal de adição	+
Parênteses	()
Chaves	{}
Barra Vertical	|
```
