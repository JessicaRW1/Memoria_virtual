# Desafio Prático – Diagnóstico de Arquitetura: O Gargalo da Memória Virtual

## Parte A – O Fenômeno da Paginação e o Gargalo do Disco

### Page Fault

A memória virtual permite que um processo trabalhe com um espaço de memória maior do que a quantidade de RAM disponível fisicamente. Para isso, o Sistema Operacional mantém na memória principal as páginas que estão sendo utilizadas e pode deixar outras no armazenamento secundário.

Um **Page Fault**, ou falta de página, acontece quando um processo tenta acessar uma página que naquele momento não está carregada na memória RAM. Quando isso ocorre, a execução é interrompida temporariamente para que o Sistema Operacional localize a página necessária e faça seu carregamento para a memória.

Caso não exista um espaço livre na RAM, pode ser necessário retirar outra página para abrir espaço. Depois que a página necessária é carregada e as informações da tabela de páginas são atualizadas, o processo pode continuar sua execução.

O problema é que buscar uma página no disco é muito mais demorado do que acessar uma informação que já está na memória RAM. Por isso, quando as faltas de página acontecem com muita frequência, o desempenho do sistema começa a cair.

### Paginação excessiva e Thrashing

O cenário apresentado no servidor da TechBuy é característico de **Thrashing**, que acontece quando o sistema possui memória física insuficiente para manter as páginas necessárias aos processos que estão ativos.

Com pouca RAM disponível, uma página é retirada para dar espaço a outra. Pouco depois, aquela página pode ser necessária novamente e precisa voltar para a memória. Esse processo começa a se repetir muitas vezes, aumentando bastante as operações de leitura e escrita no disco.

Isso ajuda a explicar por que o disco do servidor chega a 100% de utilização enquanto a CPU permanece com pouco uso. Os processos passam boa parte do tempo esperando que suas páginas sejam carregadas do disco, em vez de realmente executar instruções no processador.

Quando um processo fica aguardando uma operação de paginação, o Sistema Operacional pode colocar outro processo para executar. Porém, se vários deles também precisarem de páginas que não estão na RAM, eles acabam entrando no mesmo ciclo de espera. Com isso, aumentam as trocas entre processos, mas pouco trabalho útil é realizado pela CPU.

No caso da TechBuy, portanto, o principal gargalo não está no processamento das regras de negócio, mas na movimentação excessiva de páginas entre a memória RAM e o disco.

## Parte B – O Papel e a Eficiência da TLB

### Funcionamento da TLB

A **TLB (Translation Lookaside Buffer)** funciona como uma pequena memória de alta velocidade utilizada para guardar traduções recentes entre endereços virtuais e endereços físicos.

Quando um programa utiliza um endereço virtual, esse endereço precisa ser traduzido para descobrir em qual região da memória física está o dado correspondente. Sem a TLB, seria necessário consultar constantemente a tabela de páginas armazenada na memória.

A TLB mantém algumas dessas traduções mais utilizadas próximas da MMU. Quando a tradução procurada está nela, o acesso é mais rápido e é chamado de **TLB hit**.

Ela é implementada em hardware junto à MMU porque a tradução de endereços acontece o tempo todo durante a execução dos programas. Se fosse necessário realizar uma consulta comum à memória RAM para cada tradução, seriam necessários acessos extras à memória e o desempenho seria prejudicado.

### TLB Miss e troca de contexto

Um **TLB miss** ocorre quando a tradução procurada não está armazenada na TLB. Nesse caso, é necessário consultar a tabela de páginas para encontrar a informação correspondente, o que aumenta o tempo necessário para acessar a memória.

É importante diferenciar um TLB miss de um Page Fault. Um TLB miss não significa necessariamente que a página está fora da RAM. A tradução pode apenas não estar registrada naquele momento na TLB. Se a página estiver na memória, a tradução é encontrada na tabela de páginas e pode ser adicionada à TLB. Se a página também não estiver na RAM, então ocorre uma falta de página e será necessário buscá-la no armazenamento secundário.

As trocas de contexto também interferem nesse funcionamento. Cada processo possui seu próprio espaço de endereçamento, então as traduções utilizadas por um processo não podem simplesmente ser usadas pelo próximo.

Em uma situação mais simples, quando o Sistema Operacional troca de processo, as entradas da TLB relacionadas ao processo anterior precisam ser invalidadas. Com isso, o novo processo pode apresentar mais TLB misses nos primeiros acessos, até que a TLB seja preenchida novamente com suas traduções.

Por isso, uma quantidade muito alta de trocas de contexto também pode aumentar o custo de acesso à memória e contribuir para a queda de desempenho do servidor.

## Parte C – Síntese e Solução

### Aumento da memória RAM

Dobrar a quantidade de memória RAM do servidor tende a reduzir o problema observado na TechBuy.

Com mais memória física disponível, um número maior de páginas pode permanecer carregado na RAM ao mesmo tempo. Dessa forma, diminui a necessidade de retirar páginas da memória para colocá-las no disco e carregá-las novamente pouco tempo depois.

A consequência é uma redução na quantidade de Page Faults provocados pela falta de espaço e também uma queda no uso do disco relacionado à paginação. Como os processos passam menos tempo esperando operações de entrada e saída, a CPU consegue permanecer mais tempo executando trabalho útil.

Isso não significa que os Page Faults deixarão de existir completamente. Eles ainda podem acontecer, por exemplo, quando uma página é acessada pela primeira vez ou quando a quantidade de memória necessária pelos processos volta a ultrapassar a RAM disponível.

No entanto, se a causa do Thrashing no servidor realmente for a falta de memória física, aumentar a quantidade de RAM permite manter uma parte maior do conjunto de páginas utilizadas pelos processos na memória principal. Nesse cenário, a tendência é reduzir bastante a paginação excessiva e, consequentemente, o uso do disco.

### Instrumento Visual de Síntese
## Mapa Mental

<img width="760" height="527" alt="image" src="https://github.com/user-attachments/assets/289fdd28-5f16-4d97-b509-7cf30f2ea23f" />



