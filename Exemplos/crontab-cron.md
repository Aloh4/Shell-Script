## Automatização de tarefas com CRON e CRONTAB

* Para cada usuário, existe um `crontab`
* Que é o local onde são gravas as diretivas que serão seguidas pelo `cron`
* O arquivo `crontab` é editado pelo comando `crontab`
* Os arquivos `crontab` dos usuários, são gravados em `/var/spool/cron/crontabs`
* Importante !!! ` https://crontab.guru/#*_*_*_*_* `


**Exemplo**
```
0 6 * * * comando
```

* Os cinco primeiros campos, representam a periodicidade de execução do comando:

```
Campo	Significado				Valores
1		Minutos					0 a 59
2		Hora					0 a 23
3		Dia do mês				1 a 31
4		Mês						1 a 12
5		Dia da semana			0 a 7, sendo que os números 0 e 7 indicam o domingo
6		Comando a ser executado	qualquer comando válido do sistema
```

**Exemplos**
```
## O comando será executado de segunda a sexta (1-5), exatamente às 20h.
0  20 * * 1-5 comando

## O comando será executado às 10h10, do dia 1º de todos os meses.
10 10 1 * * comando

## No dia 31 de dezembro, o comando será executado a cada dez minutos, o dia inteiro. 
## Sempre que um campo for preenchido com o asterisco, significa que aquele campo assumirá todos os valores possíveis.
0,10,20,30,40,50, * 31 12 * comando

## Podemos abreviar esta notação, em vez de especificar cada um dos minutos, podemos fazer da seguinte forma:
## Os dias da semana podem também ser indicados por sua abreviação: sun, mon, tue, wed, thu, fri, sat.
*/10 * 31 12 * comando

## Executado todos os minutos, de todos os dias, de todos os meses
* * * * * comando
```

## Recursos úteis 
```
Parâmetro	Descrição						Equivale a
@reboot		ocorre ao iniciar o computador	—
@yearly		ocorre 1 vez ao ano				0 0 1 1 *
@annually	o mesmo que @yearly				0 0 1 1 *
@monthly	ocorre 1 vez ao mês				0 0 1 * *
@weekly		ocorre 1 vez na semana			0 0 * * 0
@daily		Executa uma vez ao dia			0 0 * * *
@midnight	mesmo que @daily				0 0 * * *
@hourly		ocorre 1 vez a cada hora		0 * * * *
```

**Usando os parâmetros acima**
```
Exemplo, na crontab, adicionar a seguinte linha:

CMD		SCRIPT
@daily /usr/local/bin/db_backup.sh

```

**Editando o crontab**

* Obs: As linhas comentadas podem ser removidas `#`
```
crontab -e
```

**Listar conteúdo crontab**
```
crontab -l
```

* Como root, editar diretamente `crontab` dos usuários do sistema:
```
crontab -u [username] -e
```

* Enviar email com os resultados
```
# No arquivo crontab, definir:
MAILTO="admin@meuemail.com"
```

**Referências**
```
https://crontab.guru/#*_*_*_*_*
man crontab, para saber mais sobre o comando crontab
man 5 crontab, para saber mais sobre o formato do arquivo crontab
man cron, para saber mais sobre o comando cro
```


