# Remoção de significado

* É possível inibir a interpretação do shell para determinada síntaxe
* Utilizando: apóstrofo ' ' , \ , ou " "
* O shell irá remover o caractere especial, e não irá interpretar seu conteúdo !

* **Apóstrofos**
   * Não deixam o shell interpretar nada !
* **Contrabarra** 
  * Remove/inibe o significado de apenas do caractere seguinte.
* **Aspas** 
   * Shell consegue interpretar ** `$ cifrão` ` ``crase` ` \ contrabarra `**
   
**Exemplos:**

```
echo "Here's how we can use single ' and double \" quotes within double quotes"

rm \*    -> Deleta o arquivo '*' e não todo diretório 
echo '*' -> Exibe o arquivo *
echo \*  -> Exibe o arquivo *
echo "existem `who | wc -l` usuarios"

var=1
echo \$var		-> Saída: $var
echo '\$var'	-> Saída: \$var
echo "\$var"	-> Saída: $var
echo "$var"		-> Saída: 1
```
