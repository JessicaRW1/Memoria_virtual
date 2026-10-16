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
