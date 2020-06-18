# Priorização de processos/execução

* Usado para priorizar uma expressão (comando)
* Para tal, pode-se usar
   * **Crase** ` `` `
   * **Construções** ` $(cmd1..cmd2..etc)`  **recomendado**

**Exemplos:**

```
echo "Existem `who | wc -l` usuários conectados"
Existem     8 usuários conectados

echo Existem $(who | wc -l) usuários conectados
Existem 8 usuários conectados

```

**Priorização de comando, $var recebe como valor a saída do comando ls -l e não a string, ls -l**

```
Errado:
Atribui a variável $arqs, ls -l
arqs=ls -l

Correto:
Atribui a variável $arqs o VALOR de ls -l
arqs=$(ls -l)
ou..
arqs=`ls -l`

A $var protegida por aspas interpreta o sinal da variável $, com essa proteção, a saída sairá formatada pois o shell não verá espaços em branco.
Se o vesse, trocaria todos os multipls (Espaços, enters, TABS) por apenas 1, tornando a saída não formatada.

## Exemplo:
$ var=$(ls -l)

Formatado:
echo "$var"
total 92 
-rw-r--r-- 1 root root 133 Apr 1 09:02 aniv 
-rw-r--r-- 1 root root 18 Apr 1 09:02 datas 
......

Sem formatar:

echo $var
total 84 -rw-r--r-- 1 root root 0 Apr 27 21:35 aloha -rw-r--r-- 1 
root root 133 Apr 1 09:02 aniv -rw-r--r-- 1 root root 15 May 11 00:41 arq -rw-r--r-- 1 root root 6 Apr 27 11:20 arq1 -rw-r--r-- 1 root root 54 May 10 21:24
```

