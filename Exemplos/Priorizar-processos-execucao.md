# Priorização de processos/execução

* Usado para priorizar uma expressão (comando)
* Para tal, pode-ser usar
   * **Crase** ` `` `
   * **Construções** ` $(cmd1..cmd2..etc)`  **recomendado**

**Exemplos:**

```
echo "Existem `who | wc -l` usuários conectados"
Existem     8 usuários conectados

echo Existem $(who | wc -l) usuários conectados
Existem 8 usuários conectados

Errado:
Atribui a variável $arqs ls -l
arqs=ls -l

Correto:
Atribui a variável $arqs o VALOR de ls -l
arqs=$(ls -l)

O shell deleta múltiplos de espaços em branco, tabs e enters
Para saída ser 'formatada', deve-se consultar o valor da variável
Usando aspas, ex:

echo $var   -> Saída não formatada
echo "$var" -> Saída formatada
```
