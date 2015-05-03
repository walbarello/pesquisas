GNU m4
------

Em 1977 no laboratório da Bell lab's Kernihan e Ritchie uniram forças para **[desenvolver o macro processador m4](http://wolfram.schneider.org/bsd/7thEdManVol2/m4/m4.pdf)** que tinha apenas 21 macros embutidos. René Seindal em 1990 lança o GNU m4 com o objetivo de eliminar limitações artificiais em muitas implementações do m4.

O GNU m4 é uma implementação do macro processador usado em todos os sistemas Unix-like e foi padronizado por sistemas de arquitetura POSIX. Normalmente, apenas uma pequena porcentagem de usuários estão cientes da sua existência. No entanto, aqueles que o encontram, muitas vezes se tornam usuários comprometidos em usa-lo. A popularidade do **[GNU autoconf](https://www.gnu.org/software/autoconf/autoconf.html)** e que requer o GNU m4 para gerar e configurar scripts, é um incentivo para muitos desenvolvedores experimenta-lo, visto que muitos programas em C dependem do autoconf. O m4 contém funções builtin para incluir arquivos com o nome executando comandos do UNIX, fazendo aritmética com inteiros, manipulação de texto de várias formas, recursividade etc... m4 pode ser usado tanto como um front-end para um compilador ou como um macro processador.

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

Após esta definição, a palavra "AUTHOR" é reconhecida como uma macro que remete a "W. Shakespeare " incluindo seus dois argumentos de string vazia "ou espaçamentos", ou seja, ele não produz nenhuma saída para espaçamentos. No entanto, se uma linha em branco for adicionada à saída, será um problema, então você pode suprimi-lo usando o "delete to newline" do macro, isto é, o **dnl**:

```bash
define (AUTHOR, W. Shakespeare) dnl
```

Qualquer espaço em branco antes do início de um parâmetro é descartado. Assim, a definição seguinte é equivalente à anterior:

```bash
define (
     AUTHOR, W. Shakespeare) dnl
```
O comportamento de M4 pode ser confuso no início. É melhor obter uma compreensão inicial de como ele funciona para evitar problemas. Isso deve poupar tempo para descobrir o que está acontecendo quando ele não faz o que você espera. Com m4, você também pode criar condicionais ifdef, else, operações matemáticas, aritméticas, octa, hexadecimal, e operações com loops for, while, foreach e muito mais. Vejamos com o clássico hello world por exemplo:

```bash
define(`yoo',`Hello World!')
I say this: yoo
```

Coloque o código a cima em um arquivo chamado my_first_m4_program e execute-o com o m4:

```bash
m4 my_first_m4_program

A saída será:
I say this: Hello World!
```

Podemos também redirecionar a saída para um arquivo simplesmente acrescentando o "> filename".Vejamos:

```bash
m4 my_first_m4_program > test_file
cat test_file

I say this: Hello World!
```

Um macro simples substitui apenas uma parte do texto sobre a entrada. Embora este seja um mecanismo simples de tratar o poderoso m4. O texto literal é colocado entre os delimitadores de texto (padrão: 'e'), as variáveis não. A declaração pode ser dividida em várias linhas:

```bash
define(`yoo',
`Hello World!'
)
I say this: yoo')
```

Isso resultará em algumas linhas vazias na saída do programa, no entanto. Também é possível deixar a segunda parte da declaração em várias linhas:

```bash
define(`htmlheader',
`
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
<head>
  <title>my_title</title>
  <meta http-equiv="Content-Type" content=
  "text/html; charset=utf-8" />
</head>
```

Uma definição de M4 pode ser reforçada com uma lista de parâmetros:

```bash
define(`my_value',`$1_file')
my_value(`test')`)
```

Este último comando (my_value('test') retornará o test_file como saída. Já o primeiro parâmetro é abordado como $1 e o segundo $2. Instruções condicionais aumenta a utilidade de nossos scripts. Esta é a sintaxe:

```bash
ifelse(`first_text',`second_text',`true_action',`false_action')
```

* ifelse: o comando m4
* first_text: Este é o primeiro parâmetro
* second_text: Este é o segundo parâmetro
* true_action: esta é a saída, se o primeiro parâmetro e o segundo parâmetro são iguais
* false_action: esta é a saída, se o primeiro parâmetro e o segundo parâmetro não são iguais

Um outro exemplo de uso real:

```bash
ifelse(my_filename,`index.html',`Home',`<a href="/index.html" title="To index page">Home</a>')'`)
```
Esta é uma parte de uma macro em m4 que cria um menu em um página da web. Se a página atual tiver o nome "index.html" (que é destinado para variável my_filename), em seguida, a saída é uma linha com apenas a palavra "Home", caso contrário, a saída será hiperlink para a página inicial. As macros também podem ser aninhadas. Isto significa que uma macro usa a saída de outra macro para modificar outra. Quando combinado com instruções condicionais, resulta em um mecanismo muito forte. Exemplo:

```bash
define(`my_menu',`<li>ifelse(filename,$1,`<span class="selected">$1_menu</span>',`<a href="$1_file" title="$1_title">$1_menu</a>')</li>')
```

Incluindo arquivos de outros módulos em outros diretórios e subdiretórios:

```bash
include(`pagedefinitions')
include(`webmenudefinitions')
include(`xhtmldefinitions')
build_htmlheader(`current_webpage')
insert_content(`current_webpage')
build_htmlfooter(`current_webpage')
```


É muito fácil introduzir um monte de espaço em branco na saída das  macros em m4. O primeiro passo para reduzir o espaço em branco gerado é o uso do comando dnl ( entenda dnl da seguinte forma: "excluir tudo daqui em diante até a nova linha"). O próximo passo é o uso do comando **divert** entenda divert da seguinte maneira (divert: "redireciona a saída para um fluxo diferente"). Quando emitirmos o comando divert(-1) a saída é enviada para transmitir -1, que é um fluxo não-existente (como o /dev/null). Exemplo:

```bash
divert(-1)
define(`my_macro1',
    `some_macro_expansion'
)
define(`my_macro2',
    `another_macro_expansion'
)
divert
```

Pode parecer um pouco estranho usar um pré-processador como m4 para gerar o código. Ter que desenvolver scripts para gerar o código. Isto significa que você precisa Depurar esses scripts também. Mas em algum momento, a compensação pode ser positiva. Depende muito da situação, da necessidade.

Material:
--------

** [GNU m4 documentation](http://www.gnu.org/software/m4/manual/m4.html) **


