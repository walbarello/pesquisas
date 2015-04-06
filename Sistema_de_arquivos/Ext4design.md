###Design e arquitetura do Ext4 File System

Este artigo apresenta a concepção de implementação de um sistema de arquivos compatível com Unix, o ext4, que usa duas partições de disco para armazenar seus dados. Uma partição é usada exclusivamente para informações relacionadas ao diretório, e a outra para os arquivos ordinários. O sistema de arquivos é desenvolvido como uma modificação do sistema de arquivos ext2 onipresente. O objetivo é beneficiar o:

* Paralelismo no acesso às duas partições, se forem armazenados em discos com controladores separados.

* Layout simplificado e melhorado que favorece padrões de acesso específicos para cada tipo de arquivo.

O sistema de arquivos ext4 é o resultado de uma tecnologia de sistema de arquivos de longa evolução que começa com o sistema de arquivos Unix concebido na década de setenta por Ken Thomson. Muitas idéias ainda são básicas (como inodes); uma descrição cronológica da evolução dos sistemas de arquivos Unix, lança alguma luz sobre as escolhas que deram origem ao projeto do ext4.

---

### O classico sistema de arquivos Unix (UFS - Unix File System)

O protótipo do sistema de arquivos continua a ser o sistema de arquivos Unix original, projetado por Ken Thomson. A sua modularidade, limpeza e simplicidade são compensados apenas pela leveza e baixa eficiência. De uma forma ou de outra, os novos sistemas são apenas "patches" do projeto original, e eles tentam compactar algumas linhas de desempenho, sacrificando a elegância do design. Basicamente funciona da seguinte forma: "se ele funciona rápido, não importa se é feio." O modelo de sistema de arquivo do Unix é muito simples: um array de bytes simples com um tamanho muito grande. A figura a baixo representa a colocação dos procedimentos do sistema de arquivos do kernel entre outros serviços do kernel.

<p align="center">
<img src ="https://github.com/lobocode/pesquisas/blob/master/Sistema_de_arquivos/ga1.png" />
</p>

***File System Placement no sistema operacional***

O driver (e o cache) oferecem o disco visto como uma enorme variedade de blocos. O sistema de arquivos lê e escreve tais blocos em uma única operação. Cada partição de disco tem que segurar um sistema de arquivos independente. O sistema de ficheiro utiliza os blocos de partição do seguinte modo (figura 2)

<p align="center">
<img src ="https://github.com/lobocode/pesquisas/blob/master/Sistema_de_arquivos/ga2.png" />
</p>

***Utilizando a partição pelo sistema de arquivos***

O superbloco contém uma descrição dos parâmetros globais do sistema de arquivos. Alguns exemplos são: tamanho da partição, montar a partição no tempo, número de inodes, blocos livres; inodes livres, tipo de sistema de arquivos. A seguir, um número de blocos é atribuída para armazenar descrições de arquivo individuais. Cada arquivo é descrito por um inode ou informação. O número de inodes é alocado estaticamente, no momento da criação do sistema de arquivos.

Todos os atributos relevantes são mantidos nos inodes, incluindo uma representação de uma lista de blocos usados pelo arquivo, (figura 3). Os blocos indiretos precisam estar presente apenas se o tamanho do arquivo for grande o suficiente (ou seja, o ponteiro pode ser NULL). Este esquema tem muitos méritos; vamos apenas observar que arquivos com "buracos" no interior que podem existir.

<p align="center">
<img src ="https://github.com/lobocode/pesquisas/blob/master/Sistema_de_arquivos/ga3.png" />
</p>

***Representação de lista de bloqueio em Inodes***

Os diretórios são, na verdade, em todos os aspectos, arquivos comuns (ou seja, com ótimos blocos de inodes, e de dados que crescem da mesma forma), mas o sistema operacional se preocupa significamente com o conteúdo do diretório. A estrutura de diretórios é bastantesimples: cada um é um conjunto de links. Basicamente, um link é uma estrutura que associa um nome (string) para um número de inode. A Figura 4 mostra a estrutura de diretórios. Cada arquivo tem que ter pelo menos um link em um diretório. Isto é verdade para os diretórios também, exceto para o diretório raiz.

Todos os arquivos podem ser identificados pelo seu caminho, que é lista de links que devem ser percorridos para alcançar o arquivo (ou começando no diretório raiz, ou no diretório atual). Um arquivo pode ter links em muitos diretórios; um diretório tem que ter um único link para si mesmo (exceto "." e ".."), a partir de seu diretório pai.

<p align="center">
<img src ="https://github.com/lobocode/pesquisas/blob/master/Sistema_de_arquivos/ga4.png" />
</p>

***Diretório e estrutura do link***

O programa mkfs transforma uma partição "raw" em um sistema de arquivos com a criação do superbloco, inicializando inodes e acorrentar todos os blocos de dados em uma lista enorme de blocos disponíveis para um crescimento futuro. Os blocos livres são praticamente agrupados em um grande arquivo fictício, a partir do qual eles são recuperados sob demanda (quando outros arquivos ou diretórios crescem), e ao qual regressam na remoção de arquivos ou truncamento.

Observemos que todas as operações realizadas em arquivos tem que trazer os dados relevantes (por exemplo, inodes) INCORE (na RAM). A representação no interior do núcleo das estruturas de dados é mais complexa do que a estrutura no disco, porque muita informação é implícita sobre o disco em falta no interior do núcleo (por exemplo, o número de inode, o dispositivo, o tipo de sistema de arquivos).

---

### O sistema de arquivos Linux Ext2

 sistema de arquivos ext2 inspira-se fortemente sobre o legado dos sistemas FFS e VFS. Ele tem suas próprias características, no entanto:

Dá-se fragmentos de blocos; espaço é menos um problema com tamanhos de discos atuais; por outro deslocalização fragmento mão no crescimento do arquivo não é mais uma fonte de sobrecarga.

Usa grupos (cilindro), com bitmaps para inode livre e rastreamento bloco livre (Figura 6)

O sistema de arquivos ext2 inspira-se fortemente sobre o legado dos sistemas FFS e VFS. Ele tem suas próprias características, no entanto:

* Dá-se fragmentos de blocos; espaço é menos um problema com tamanhos de discos atuais; por outro deslocalização fragmento mão no crescimento do arquivo não é mais uma fonte de sobrecarga.

* Usa grupos (cilindro), com bitmaps para inode livre e rastreamento bloco livre (Figura 6);

<p align="center">
<img src ="https://github.com/lobocode/pesquisas/blob/master/Sistema_de_arquivos/ga5.png" />
</p>

* Utiliza técnicas de pré-alocação de alcançar contiguidade para blocos de arquivos; cada arquivo crescente tenta reservar um número de blocos consecutivos, que são liberados se o crescimento não é seqüencial;

* Todos os bitmaps são reduzidos a uma quadra de tamanho, por razões de eficiência de pesquisa;

* Arquivos imutável, apenas anexar-arquivos são impostas pelo kernel; ioctl() lidar com seus atributos;

* As entradas do diretório tem tamanho variável; diretório manipultion só é permitida através de chamadas de sistema especiais (por exemplo, readdir (), e não ler ());

* Bits de desmontar limpa no superbloco permitir ignorar as verificações de consistência caros em tempo de boot;

* Rápidos links simbólicos (informações armazenadas na parte reservada para inode ponteiros bloco se ele se encaixa, e não alocar um bloco);

* Alguns recursos extras estão previstas, mas ainda não foi implementado, aparentemente; compressão transparente, File Undelete.

<p align="center">
<img src ="https://github.com/lobocode/pesquisas/blob/master/Sistema_de_arquivos/ga6.png" />
</p>

***Estrutura do Grupo em ext2***

O poder da VFS é aparente em Linux em sua totalidade, como Linux suporta com a mesma facilidade até 10 diferentes sistemas, que vão desde a NFS DOS e FAT. Existem três principais estruturas de dados na camada de Linux VFS, que apontam para o arquivo de sistema em partes dependentes e independentes. Estes são:

* O superbloco de cada sistema montado;
* cada inodes \carregado "objeto (arquivos, diretório, tubulação, etc.)";
* as estruturas de arquivos, que descrevem arquivos abertos.

Cada versão no interior do núcleo de tal jeto ob tem uma estrutura com ponteiros para os manipuladores de manipulação que espe c ob jeto. A Figuraa seguir ilustra este fato para os inodes. (No oficial SunOS a Terminologia da estrutura é um vnode, e a parte do arquivo é dependente do sistema de inodo, mas ambos nomes Linux
inodes.)

<p align="center">
<img src ="https://github.com/lobocode/pesquisas/blob/master/Sistema_de_arquivos/ga7.png" />
</p>

Aqui é uma peça típica do código do kernel, na rotina dependente do sistema de arquivo de ext2 que carrega um inode:

<pre lang="c"><code>if (REGULAR(inode->mode))
inode->operations = &ext2_file_inode_operations;
	else if (DIRECTORY(inode->mode))
inode->operations = &ext2_dir_inode_operations;
	else if (SYMLINK(inode->mode))
inode->operations = &ext2_symlink_inode_operations;
else ... </code></pre>

 VFS vai chamar as operações de inode indiretamente (por exemplo inode-> Operações-> link()), sem ter que saber alguma coisa sobre a implementação.

---

### O sistema de arquivos Ext4

O sistema de arquivos ext4 é, basicamente, um refinamento do ext2, que usa duas partições simultaneamente, idealmente localizado em discos separados. Ambas as partições contém informações como: inodes, blocos diretos e indiretos, superblocks e informações de bitmap. A única diferença entre as duas partições é que todos os diretórios irá sentar-se em um deles (ambos os blocos e inodes) e arquivos comuns no outro.

Agora todos os outros tipos de arquivos (links simbólicos, pipes nomeados, arquivos especiais) são mantidos na mesma partição dos arquivos regulares, embora o seu lugar certo, obviamente, é partição dos diretórios, porque o seu padrão de uso é supostamente semelhante aos próprios diretórios . Essa alteração não deve provar dificil. Este layout permite que as operações em arquivos e diretórios a decorrer em paralelo. Os pedidos de leitura / gravação de um bloco de diretório e um bloqueio de arquivo podem ser processados simultaneamente, reduzindo a latência percebida pelo usuário.

Observemos que os pedidos pendentes simultâneos de diretório e manipulação de arquivos surgir mesmo no contexto de I / O de um único processo de usuário, por causa do write-behind e read-ahead ações do cache; isso significa que eles não são uma circunstância exótico, e estamos realmente tentando resolver um problema real.

***Basicamente duas coisas têm que ser mudados em ext2 para obter ext4:***

* Montagem e desmontagem de operações tem de operar em duas partições de uma só vez, e cruzar a validade das estruturas de dados;

* Todas as operações que lidam com carga / poupança de blocos tem que ser personalizado, para escolher uma ou outra partição, de acordo com o destino final do bloco manipulado.

Basicamente ext4 tenta fazer uso de um tipo modificado de RAID-0 técnica. RAID-0 técnicas de transformar duas partições (discos) em uma única partição \ virtual, quer pela concatenação de seus blocos, ou pelo entrelaçamento eles (blocos ou seja pares tomado de uma partição física e ímpar do outro). O RAID -0 não tem conhecimento sobre a estrutura do sistema de arquivos, e atinge \ striping "no nível do driver de dispositivo (note-se, de passagem, que o Linux possui uma academia de RAID-0, o motorista md, que no entanto não tem conexão com o nosso pro jeto)".

Nosso sistema de arquivos tenta tirar vantagem do conhecimento do conteúdo do bloco, e divide os dados em dois discos em um "nível\superior."

O sistema de arquivos ext4 é o resultado de uma longa evolução da tecnologia de sistema de arquivos, que se inicia com o sistema de arquivos Unix inicial, concebido na década de setenta por Ken Thomson. Muitas idéias ainda são básicos (como inodes); uma descrição cronológica da evolução dos sistemas de arquivos Unix, além do interesse em si mesmo, vai lançar alguma luz sobre as escolhas que deram origem ao projeto de ext4.



