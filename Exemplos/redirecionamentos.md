# Redirecionamentos

* O shell espera 3 direcionamentos, representados por:
   * Entrada Padrão (stdin)  - Teclado  ` <0 ` 
   * Saída Padrão (stdout)   - Tela     ` 1> `
   * Saída de erros (stderr) - Tela     ` 2> ` 	
	   

**Resumo:**

```
< ARQ ou 0< ARQ			Entrada padrão (stdin)
1> ARQ ou 1> ARQ		Entrada padrão (stdout)
2> ARQ ou 2> ARQ		Entrada padrão (stderr)

Símbolo				Efeito

CMD > ARQ		Cria o arquivo ARQ com a saída da linha de comandos CMD
CMD >> ARQ		Anexa no fim do arquivo ARQ a saída de CMD
CMD 2> ARQ		Um eventual erro produzido por CMD será desviado para ARQ
CMD < ARQ		CMD recebe o arquivo ARQ como entrada
CMD << LAB		(here document) CMD recebe os dados de todas as linhas até encontrar o label LAB
CMD <<< CAD		(here strings) CMD recebe como entrada a cadeia CAD. O mesmo que echo CAD | CMD
CMD1 | CMD2		((pipe) CMD2 recebe como entrada a saída do comando CMD1

# file descriptor - FD:

&> ARQ				Redireciona stdin e stdout para o ARQ
ARQ1 >& ARQ2		Redireciona stdin e stdout de ARQ1 para ARQ2
>& ARQ1				Redireciona stdout para ARQ1 	
ARQ1 <> ARQ2		Abre ARQ2 para leitura e gravação e associa a ele ARQ1
ARQ1 >&-			Fecha toda a stdout associada ao ARQ1 (nao compativel)
```

**Exemplos:**

```
script.sh > arq.log 2>&1	// Envia as saídas (stdout e stderr) para o arq.log
ls nome named 2>>arq2 >>arq // Envia a saída de erros para arq2, e a saída padrão para arq
script.sh 2>> log.err 1>> log.txt // Envia a saída de erros para log.err, e a saída padrão para log.txt
```
-----
# Saída padrão

* Para se especificar a saída de um programa usa-se ` > ` ou ` >> `
* Quando utilizado `cat > arq`, sendo um arq já existente, os dados do primeiro arq criado serão perdidos !
* Isso acontece porque:
   * O shell resolverá a linha digitada;
   * Vendo o redirecionamento, criará um novo arquivo
   * E finalmente, mandará a linha para a execução.
	   
-----
# Saída de erros

* Para que os erros não saiam na tela e a poluam, pode-se especificar a saída de erros;
* A lógica é a mesma da utilizada para saída padrão:
	
* Exemplos:
   * `rm /etc/teste$$ 2> /dev/null
   * Pode-se usar: `2>` ou `2>>` 
	   
``` 
ls naoexiste
No such file or directory	// Haverá erro apresentado na tela			

ls naoexiste 2> arqerros	// O erro agora, será salvo no arquivo arqerros
cat arqerros				// Pode-se fazer consulta no arquivo de logs (com erros)
```
-----
# Entrada padrão

* Usa-se o ` < `
* Serve para redirecionar a entrada de um programa, que normalmente seria recebida pelo teclado.
* `mail chefe < arquivocommailparaochefe ` mail, receberá o conteúdo de  'arquivocommailparaochefe', e não um texto digitado
* Para alguns programas, usar ex: `xargs ls < arq.txt`

# Here Document

* Representado por ` << `
* Trabalha com *labels*
* Serve para redirecionar uma *sequência* de comandos
* O Shell ignora tudo que estiver entre os labels
* Usa o conceito de prompt secundário para a execução dos demais comandos
* O escopo do comando começará na linha seguinte ao *label <<* e termina quando encontrar *label* 
	
```
ftp -ivn hostremoto << fimftp
> user $Usuário $Senha
> binary
> get arquivoremoto
fimftp


cat << FIM
> Hoje em $(date)
> A maquina está ligada desde $(uptime)
> FIM
Hoje em Sun 10 May 2020 11:47:24 PM -03
A maquina está ligada desde  23:47:26 up  2:54,  1 user,  load average: 0.00, 0.00, 0.00

```

**Exemplos:**

```
xargs ssh -T 100.127.238.186 << eof
-p 2024
eof

## Priorizou a exec de who - enviou a saida de who para a entrada do grep após encontrar o label meulabel
grep -o 'whoami' << meulabel
> $(who)
> meulabel
whoami


## Exemplo Here Document 1
grep -v [34] << teste
$(seq 5)
$(seq 3)
> teste
1
2
5
1
2

#  Exemplo Here Document 2
cat << fimcat
> Estou no dir $(pwd)
> manipulando isso:
> `ls -l`
> fimcat
Estou no dir /home/whoami
manipulando isso:
total 100
-rw-r--r-- 1 root root   133 Apr  1 09:02 aniv
-rw-r--r-- 1 root root    19 Apr 19 14:48 ARQ

```

-----
# Pipe

* O exemplos acima se referem a arquivos;
* Os próximos exemplos serão para redirecionar a saída de um comando, para entrada de outro
	
* Pipe ` | `
   * Cada redirecionamento com pipe, cria um subshell
   * Ou seja, o WC não sabe conhece a execução de LS;
   * Apenas que recebeu uma lista e precisa contá-la.

```
O comando LS passou a lista de arquivos p/ o WC que as contou com a opção -l

ls | wc -l
21
 
cat /etc/passwd | sort | lp

```
-----
# Here Strings

* Utilizado com `comando <<< CADEIA` 
* Não cria subshells
	
```
Tradicional:
echo "scale=3; 22/7" | bc

Here Strings:
bc <<< "scale=3; 22/7"

Exemplo:
**
echo "hello world" | read first second
echo $second $first
**

No exemplo, o comando echo não encontrou valores nas variáveis,
Isso porque houve redirecionamento via PIPE.
$echo executou as strings, e enviou para o comando $read que as leu normalmente
Mas o subshell morreu, e os seus valores foram perdidos:

**
read first second <<< "hello world"
echo $second $first
world hello
**

O mesmo não acontece utilizando Here Strings
Pois a string "Hello World" é enviada diretamente para a entrada do comando read.
```

**Exemplos:**

```
## Priorizou a exec de ls e who - enviou a saida de ls e who para a entrada do grep
root@whoami:~# grep -Eo 'sites|whoami' <<< $(ls;who)
sites
whoami

Comum
echo "scale=3; 22/7" | bc
3.142

Com Here strings
bc <<< "scale=3; 22/7"
3.142

```
