GNU m4
------

Em 1977 no laboratório da Bell lab's Kernihan e Ritchie uniram forças para *[desenvolver o macro processador m4](http://wolfram.schneider.org/bsd/7thEdManVol2/m4/m4.pdf)* que tinha apenas 21 macros embutidos. René Seindal em 1990 lança o GNU m4 com o objetivo de eliminar limitações artificiais em muitas implementações do m4.

O GNU m4 é uma implementação do macro processador usado em todos os sistemas Unix-like e foi padronizado por sistemas de arquitetura POSIX. Normalmente, apenas uma pequena porcentagem de usuários estão cientes da sua existência. No entanto, aqueles que o encontram, muitas vezes se tornam usuários comprometidos em usa-lo. A popularidade do *[GNU autoconf](https://www.gnu.org/software/autoconf/autoconf.html)* e que requer o GNU m4 para gerar e configurar scripts, é um incentivo para muitos desenvolvedores experimenta-lo, visto que muitos programas em C dependem do autoconf. O m4 contém funções builtin para incluir arquivos com o nome executando comandos do UNIX, fazendo aritmética com inteiros, manipulação de texto de várias formas, recursividade etc... m4 pode ser usado tanto como um front-end para um compilador ou como um macro processador.

Algumas pessoas acham m4 ser bastante viciante. Eles primeiro usam m4 para problemas simples, em seguida, tomam desafios cada vez maiores, e vão aprendendo a escrever conjuntos complexos com macros m4 ao longo do caminho. Portanto, cuidado! O m4 pode ser perigoso para a saúde de programadores compulsivos. 

Em seu uso mais básico, o m4 pode ser usado para uma simples substituição de texto. Veja o exemplo a seguir:

```bash
define (AUTHOR, William Shakespeare)
Sonho de Uma Noite de Verão
por AUTHOR
```
E como saída, teremos:

```bash
Sonho de Uma Noite de Verão
por William Shakespeare
```

Embora semelhante em princípio com o pré-processador C, é uma ferramenta muito mais poderosa de uso geral. Algumas vantagens são:

* sendmail: arquivo de configuração bastante enigmática do sendmail (/etc/mail/sendmail.cf) é gerada usando m4 a partir de um arquivo de modelo que é muito mais fácil de ler e editar (/etc/mail/sendmail.mc).

* GNU Autoconf: macros em m4 são usados para produzir os scripts "Configure" que tornam o processo de instalação de pacotes de código portável entre plataformas Unix-like diferentes.

* Segurança Enhanced Linux: arquivos de política do SELinux são (no momento da escrita) processados usando m4. (Na verdade, m4 é a fonte de algumas dificuldades aqui, porque a sua flexibilidade permitem certos abusos e faz análise de uma política automatizada de difícil aplicação.)

Uma explicação melhor elaborada:
-------------------------------

Os seus argumentos usados no GNU m4, são os arquivos que ele irá ler; se nenhum for especificado, em seguida, ele lê o stdin e o texto resultante é enviado para stdout. O m4 vem com um conjunto inicial de macros embutidos, muitas vezes chamado simplesmente de " builtins ". O mais básico deles, o *define* , é usado para criar novas macros:

```bash
define (AUTHOR, W. Shakespeare)
```

Após esta definição, a palavra "AUTHOR" é reconhecida como uma macro que remete a "W. Shakespeare " incluindo seus dois argumentos de string vazia "ou espaçamentos", ou seja, ele não produz nenhuma saída para espaçamentos. No entanto, se uma linha em branco for adicionada à saída, será um problema, então você pode suprimi-lo usando o "delete to newline" do macro, isto é, o *dnl*:

```bash
define (AUTHOR, W. Shakespeare) dnl
```

Qualquer espaço em branco antes do início de um parâmetro é descartado. Assim, a definição seguinte é equivalente à anterior:

```bash
define (
     AUTHOR, W. Shakespeare) dnl
```
O comportamento de M4 pode ser confuso no início. É melhor obter uma compreensão inicial de como ele funciona para evitar problemas. Isso deve poupar tempo para descobrir o que está acontecendo quando ele não faz o que você espera. Com m4, você também pode criar condicionais ifdef, else, operações matemáticas, aritméticas, octa, hexadecimal, e operações com loops for, while, foreach e muito mais.



