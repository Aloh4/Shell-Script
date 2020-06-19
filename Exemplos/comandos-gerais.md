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

* O modo correto é `> arq

```

## Exemplos expansão de arquivos:
```
cp caminho/completo/do/arquivo{,.bkp}
mkdir /usr/local/{bin,etc,games,include,lib,sbin,share,src}
```

## Copie o arq1 para o arq.bkp:
```
root@whoami:~/shell/sequencias# cp arq{1,.bkp}
```

## Exemplo saída:
```
root@whoami:~/shell/sequencias# echo arq{1,.bkp}
arq1 arq.bkp 
```
