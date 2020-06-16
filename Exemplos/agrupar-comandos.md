# Agrupamento de comandos:

* **Ponto e virgula ` ; `**

   * **Agrupam comandos**
   * Ignora espaços
   * Trabalha como `enter`
   
   * **Exemplo:** `echo $var; ls -l; etc...`
 
* **Parênteses**
  
   * Invoca um **subshell** para executar comandos   

```
Obs: O subshell executou os comandos, o processo morreu e retornou para o shell que estava em execução !

root@whoami:~# pwd; (cd /etc; pwd); pwd
/home/whoami   <- saída PWD
/etc           <- subshell executou o CD, e depois o PWD
/home/whoami   <- como o subshell morreu, o PWD novamente apontou para o diretório do primeiro shell.

```

**Outros Exemplos**

```
$ echo "$var"; ls -l dir/	// Exibe o conteúdo de $var e lista o diretório dir/

## Parênteses -> Invoca um subshell para executar os comandos:
root@whoami:~# (pwd; cd work/; pwd)
Obs: O subshell executou os comandos, o processo morreu e retornou para o shell que estava em execução !

## Exemplo: 
root@whoami:~# var="Valor inicial"							// var = Valor inicial
root@whoami:~# (var="Pseudo Valor"; echo $var); echo $var	// Criando/Executando um subshell para substituir o valor de $var.
Pseudo Valor												            // O Subshel executa o conteúdo do parênteses com o valor de $var alterado.
Valor inicial												             // O Shell executa echo $var original

root@whoami:~# var=1;echo $var:1;(var=5; echo $var:5); echo $var:2
1:1
5:5
1:2

##Agrupamento com variavel:
var=$(ls -l;(cd work/; pwd))

```
