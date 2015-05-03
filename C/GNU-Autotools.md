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

> Existe um paper de Peter Miller’s, chamado  **[Recursive Make Considered Harmful](http://miller.emu.id.au/ pmiller/books/rmch/)**  publicada há mais de 10 anos, que discute alguns dos sistemas de construção recursivos que podem causar problemas. Recomendo a leitura deste livro que vai te levar a compreender as questões que Miller apresenta. Enquanto as questões são válidas, a pura simplicidade de implementação e manutenção de um sistema de construção recursiva torna, de longe, o mais amplamente utilizado em um sistema de compilação.


