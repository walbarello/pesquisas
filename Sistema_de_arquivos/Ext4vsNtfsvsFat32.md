Este artigo apresenta a concepção de implementação de um sistema de arquivos compatível com Unix, o ext4, que usa duas partições de disco para armazenar seus dados. Uma partição é usada exclusivamente para informações relacionadas ao diretório, e a outra para os arquivos ordinários. O sistema de arquivos é desenvolvido como uma modificação do sistema de arquivos ext2 onipresente. O objetivo é beneficiar o:

Paralelismo no acesso às duas partições, se forem armazenados em discos com controladores separados.

Layout simplificado e melhorado que favorece padrões de acesso específicos para cada tipo de arquivo.

O sistema de arquivos ext4 é o resultado de uma tecnologia de sistema de arquivos de longa evolução que começa com o sistema de arquivos Unix concebido na década de setenta por Ken Thomson. Muitas idéias ainda são básicas (como inodes); uma descrição cronológica da evolução dos sistemas de arquivos Unix, além do interesse em si mesmo, vai lançar alguma luz sobre as escolhas que deram origem ao projeto do ext4.

### O classico sistema de arquivos Unix (UFS - Unix File System)

O protótipo do sistema de arquivos continua a ser o sistema de arquivos Unix original, projetado por Ken Thomson. A sua modularidade, limpeza e simplicidade são compensadosapenas pelo levesa, baixa eficiência. De uma forma ou de outra, os sistemas são todos "patches" para o projeto original, e eles tentam compactar algumas linhas de desempenho, sacrificando a elegância do design. Basicamente funciona da seguinte forma: "se ele funciona rápido, não importa se é feio." O modelo de sistema de arquivo no Unix é muito simples: um array de bytes simples com um tamanho muito grande máxima. A figura 1 representa a colocação dos procedimentos do sistema de arquivos do kernel entre outros serviços do kernel.

![ga1]()
File System Placement no sistema operacional

O driver (e o cache) oferecem o disco visto como uma enorme variedade de blocos. O sistema de arquivos lê e escreve tais blocos em uma única operação. Cada partição de disco tem que segurar um sistema de arquivos independente. O sistema de ficheiro utiliza os blocos de partição do seguinte modo (figura 2)

![ga2]()
Utilizando a partição pelo sistema de arquivos

O superbloco contém uma descrição dos parâmetros globais do sistema de arquivos. Alguns exemplos são: tamanho da partição, montar a partição no tempo, número de inodes, blocos livres; inodes livres, tipo de sistema de arquivos. A seguir, um número de blocos é atribuída para armazenar descrições de arquivo individuais. Cada arquivo é descrito por um inode ou informação. O número de inodes é alocado estaticamente, no momento da criação do sistema de arquivos.

Todos os atributos relevantes são mantidos nos inodes, incluindo uma representação de uma lista de blocos usados pelo arquivo, (figura 3). Os blocos indiretos precisam estar presente apenas se o tamanho do arquivo for grande o suficiente (ou seja, o ponteiro pode ser NULL). Este esquema tem muitos méritos; vamos apenas observar que arquivos com "buracos" no interior que podem existir.

![gal3]()
Representação de lista de bloqueio em Inodes

Os diretórios são, na verdade, em todos os aspectos, arquivos comuns (ou seja, com ótimos blocos de inodes, e de dados que crescem da mesma forma), mas o sistema operacional se preocupa significamente com o conteúdo do diretório. A estrutura de diretórios é bastantesimples: cada um é um conjunto de links. Basicamente, um link é uma estrutura que associa um nome (string) para um número de inode. A Figura 4 mostra a estrutura de diretórios. Cada arquivo tem que ter pelo menos um link em um diretório. Isto é verdade para os diretórios também, exceto para o diretório raiz.

![ga4]()



