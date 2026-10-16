# Desafio Prático – Diagnóstico de Arquitetura: O Gargalo da Memória Virtual

## Parte A – O Fenômeno da Paginação e o Gargalo do Disco

### Page Fault

A memória virtual permite que um processo trabalhe com um espaço de memória maior do que a quantidade de RAM disponível fisicamente. Para isso, o Sistema Operacional mantém na memória principal as páginas que estão sendo utilizadas e pode deixar outras no armazenamento secundário.

Um **Page Fault**, ou falta de página, acontece quando um processo tenta acessar uma página que naquele momento não está carregada na memória RAM. Quando isso ocorre, a execução é interrompida temporariamente para que o Sistema Operacional localize a página necessária e faça seu carregamento para a memória.

Caso não exista um espaço livre na RAM, pode ser necessário retirar outra página para abrir espaço. Depois que a página necessária é carregada e as informações da tabela de páginas são atualizadas, o processo pode continuar sua execução.

O problema é que buscar uma página no disco é muito mais demorado do que acessar uma informação que já está na memória RAM. Por isso, quando as faltas de página acontecem com muita frequência, o desempenho do sistema começa a cair.
