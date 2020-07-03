## Dica:

```
A execução de arquivos usando ./ acontece pois, o diretório atual
não está na variável que possui todos os diretórios pesquisados para localizarmos arquivos ($PATH)

A solução é:

echo 'PATH:$PATH:.' >> ~/.bashrc
source ~/.bashrc

1 - Insere na última linha do arquivo .bashrc o PATH:$PATH:. (agrega o diretório corrente a esta variável)
2 - Executa o arquivo com source para a alteração entrar em vigor
```

## CAT ARQ | grep ou etc.. ` É ERRADO ` !!!
## Correto
```
grep...arq
wc...arq
cut...arq
```

## Modo inteligente e rápido para criar arquivo:

```
$ > temp

**Obs: CAT / TOUCH não criam arquivos !**

* O modo correto é > arq

```

## Exemplos expansão de arquivos:
```
cp caminho/completo/do/arquivo{,.bkp}
mkdir /usr/local/{bin,etc,games,include,lib,sbin,share,src}
```

## Copie o arq1 para o arq.bkp:
```
root@whoami:~/shell/sequencias# cp arq{1,.bkp}

## Exemplo saída:
root@whoami:~/shell/sequencias# echo arq{1,.bkp}
arq1 arq.bkp 
```
## Exemplos tail:
```
$ seq 10 | tail -5    # Lista as 5 últimas
$ seq 10 | tail -n 4    # Lista as 4 últimas
$ seq 10 | tail -n +3    # Lista as últimas a partir da 3ª
```

**tipos de echo**
```
Sem -n
echo "Escreva: "; read var
Escreva:


Com -n (nao salta linha)
root@whoami:~# echo -n "Escreva: "; read var
Escreva:
```

**Enxergar caracteres especiais**
```
cat -et # -e representa enter($), -t representa TAB (^I)
```

