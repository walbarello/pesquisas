Make - Estrutura de projeto
--------------------

Começaremos com uma amostra básica de como montar uma estrutura básica de um projeto com **make** e vamos construir algo sobre ele continuamente. Vou chamar o nosso projeto de Jupiter e irei criar uma estrutura de diretório do projeto usando os seguintes comandos:

```bash
$ cd projects
$ mkdir -p jupiter/src
$ touch jupiter/Makefile
$ touch jupiter/src/Makefile
$ touch jupiter/src/main.c
$ cd jupiter
```

Agora temos um diretório de código fonte chamado **/src** (de source), um arquivo em C chamado **main.c** que por padrão quer dizer **principal** e geralmente todo projeto em C contém no mínimo um main.c. Além disso, temos um **Makefile** para cada um dos dois diretórios em nosso projeto. Esta é a estrutura mínima que precisamos para criar um novo projeto. 

Você deve estar se perguntando sobre a necessidade de haver um makefile para cada diretório e subdiretório. Esta estrutura se faz necessária para haver uma ligação recursiva entre um diretório e um sub-diretório. Ele simplesmente fará a ligação até **src/Makefile**, recursivamente. Isto constitui um tipo bastante comum de sistema de compilação conhecido como um sistema de construção recursiva, assim chamado porque makefiles são chamados agravés do comando **make** acessando recursivamente para dar acesso aos sub-diretórios.

> **Nota:**

> Existe um paper de Peter Miller’s, chamado  **[Recursive Make Considered Harmful](http://aegis.sourceforge.net/auug97.pdf)**  publicada há mais de 10 anos, que discute alguns dos sistemas de construção recursivos que podem causar problemas. Recomendo a leitura deste livro que vai te levar a compreender as questões que Miller apresenta. Enquanto as questões são válidas, a pura simplicidade de implementação e manutenção de um sistema de construção recursiva torna, de longe, o mais amplamente utilizado em um sistema de compilação.

**No primeiro Makefile dentro de jupiter/, crie o código a baixo:**

```bash
all clean jupiter:
		cd src && $(MAKE) $@
.PHONY: all clean
```
> **all:** É o nome das regras a serem executadas

> **clean:** Apaga os arquivos intermediários se você escrever no console ```make clean```.

 **.PHONY:** Esta regra para evitar conflitos. 

**Em src/Makefile:**

```bash
all: jupiter
jupiter: main.c
	   gcc -g -O0 -o $@ main.c
clean:
	   -rm jupiter
.PHONY: all clean
```

**Em main.c :**

```bash
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char * argv[])
{
	printf("Hello from %s!\n", argv[0]);
	return 0;
}
```

Embora existam vários outros tipos de construções em um makefile (incluindo instruções condicionais, directivas, regras de extensão, regras padrões, as variáveis de função e include statements, entre outros), para os nossos propósitos, iremos nos adequar gradativamente em vez de cobrir tudo em detalhes.

Afinal, nosso objetivo por agora, é compreender resumidamente o GNU Autotools, então abordarei apenas os aspectos necessários para cumprir este objetivo. Se você já possui conhecimento e deseja se aprofundar mais, consulte o **[GNU Make Manual](http://www.gnu.org/software/make/manual/make.html)**. 

Quem está costumado a programar com linguagens que exigem indetação, logo, não terá dificuldades em usar indentação com makefiles. Portanto, o layout padrão deverá ser parecido com isto:

```bash
var1=val1
var2=val2
...
target1 : t1_dep1 t1_dep2 ... t1_depN
<TAB>	shell-command1a
<TAB>	shell-command1b
...
target2 : t2_dep1 t2_dep2 ... t2_depN
<TAB>	shell-command2a
<TAB>	shell-command2b 
```
O utilitário **make** é uma engine de comando baseado em regras, logo, a indentação não se trata apenas de um capricho. As regras de trabalho indicam quais comandos devem ser executados e quando. Quando você usar uma linha com um caractere de tabulação, você está dizendo que, deseja executar as seguintes instruções de um shell de acordo com a regra anterior. Tudo tem uma ordem de execução e comandos que devem ser observados.

Variáveis
---------

As linhas em um makefile que contém um sinal de igual (=) são definições de variável. Variáveis em makefiles são um pouco semelhantes às variáveis do shell, mas existem algumas diferenças chaves. Na sintaxe do shell, você faria referência a uma variável dessa maneira: ${my_var}. A sintaxe para fazer referência a variáveis em um makefile é idêntica, exceto que você tem a opção de usar parênteses ou chaves: $(my_var). Use parênteses para evitar ambiguidades.


