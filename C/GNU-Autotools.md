Estrutura de projeto
--------------------

Começaremos com uma amostra básica de um projeto e vamos construir algo sobre ele continuamente. Vamos chamar o nosso projeto de Jupiter e irei criar uma estrutura de diretório do projeto usando os seguintes comandos:

```bash
$ cd projects
$ mkdir -p jupiter/src
$ touch jupiter/Makefile
$ touch jupiter/src/Makefile
$ touch jupiter/src/main.c
$ cd jupiter
```

Agora temos um diretório de código fonte chamado **/src** (de source), um arquivo em C chamado **main.c**, um **Makefile** para cada um dos dois diretórios em nosso projeto. Esta é a estrutura mínima que precisamos para criar um novo projeto. Vamos começar adicionando suporte para construção da função **clean** em nosso projeto. O makefile fará muito pouco neste ponto; Apesar disso, sua existência se faz importante visto que é necessário haver uma ligação recursiva entre um diretório e um sub-diretório.Ele simplesmente fará a ligação até **src/Makefile**, recursivamente. Isto constitui um tipo bastante comum de sistema de compilação, conhecido como um sistema de construção recursiva, assim chamado porque makefiles são chamados agravés do comando **make** acessando recursivamente para dar acesso aos sub-diretórios.

> **Nota:**

> Existe um paper de Peter Miller’s, chamado  **[Recursive Make Considered Harmful](http://aegis.sourceforge.net/auug97.pdf)**  publicada há mais de 10 anos, que discute alguns dos sistemas de construção recursivos que podem causar problemas. Recomendo a leitura deste livro que vai te levar a compreender as questões que Miller apresenta. Enquanto as questões são válidas, a pura simplicidade de implementação e manutenção de um sistema de construção recursiva torna, de longe, o mais amplamente utilizado em um sistema de compilação.

**Em Makefile, crie o código a baixo:**

```bash
all clean jupiter:
		cd src && $(MAKE) $@
.PHONY: all clean
```
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

Embora existam vários outros tipos de construções em um makefile (incluindo instruções condicionais, directivas, regras de extensão, as regras padrão, as variáveis da função e include statements, entre outros), para os nossos propósitos, irei me adequar levemente a conforme necessário em vez de cobrir tudo em detalhes. Isto não significa que eles são sem importância, no entanto — pelo contrário, eles são muito úteis se você vai escrever seu próprio sistema de compilação à mão. No entanto, nosso objetivo por agora, é compreender resumidamente o GNU Autotools, então abordarei apenas os aspectos necessários para cumprir este objetivo. Se você já possui conhecimento e deseja se aprofundar mais, consulte o **[GNU Make Manual](http://www.gnu.org/software/make/manual/make.html)**. 

Comandos de tabulação são necessários nos arquivos makefile, isto é, indentação. Além do que, admite-se que o código fica mais organizado e legível. Esta é a herança de sistemas baseados em Unix. Portanto, o layout padrão será sempre parecido com isto:

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
O utilitário make é uma engine de comando baseado em regras, e as regras de trabalho indicam quais comandos devem ser executados e quando. Quando você usar uma linha com um caractere de tabulação, você está dizendo que, quer executar as seguintes instruções de um shell de acordo com a regra anterior. Tudo tem uma ordem de execução e comandos que devem ser observados.

Variáveis
---------

As linhas em um makefile que contém um sinal de igual (=) são definições de variável. Variáveis em makefiles são um pouco semelhantes às variáveis do shell, mas existem algumas diferenças chaves. Na sintaxe do shell, você faria referência a uma variável dessa maneira: ${my_var}. A sintaxe para fazer referência a variáveis em um makefile é idêntica, exceto que você tem a opção de usar parênteses ou chaves: $(my_var). Use parênteses para evitar ambiguidades.


