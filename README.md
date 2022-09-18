# Produto-cartesiano-manual-

**Produto cartesiano**

é uma operação que faz o "Cruzamento" de todos os registros entre duas tabelas.



## Entendendo Outer Joins SQL – Parte 1 (Produto Cartesiano)

  

Participo de algumas listas de discussão sobre PHP e percebi que muitos desenvolvedores têm dúvidas a respeito de comandos SQL, não sabem ou não utilizam de forma correta os outer joins.

Por isso, decidi fazer um post sobre esse assunto explicando como utilizar o *left outer join, right outer join, natural join, cross join ou inner join* (mais utilizado).

### Para que servem os outer joins?

Servem para fazer as junções entre duas ou mais tabelas, cruzando informações, combinando registros, testando desigualdades.

### Por que cruzar dados?

Quando pensamos em armazenamento de dados, não podemos sair criando tabelas sem uma avaliação e sem a criação de um modelo ER (Entidade Relacionamento), e uma das vantagens de pensar no modelo é evitar a repetição de dados. E como fazer isso? Distribuindo-os em mais de uma tabela de forma que possamos categorizar as repetições.

Se você olhar o modelo abaixo, existe uma tabela de programadores e uma de empresas, note que as duas estão relacionadas. Guardo na tabela programadores somente um código que identifica a empresa em que o programador trabalha. Fiz isso em virtude da possibilidade de ter vários programadores relacionados a mesma empresa, assim evito ter que escrever várias vezes o nome da empresa (texto ocupa mais espaço que número) e evito que a mesma empresa seja escrita de maneiras diferentes, dificultando assim, por exemplo, uma busca. Porém, agora para recuperar esses dados será necessário utilizar junções.

[![modelo](https://s.profissionaisti.com.br/wp-content/uploads/2009/06/modelo.jpg)](https://s.profissionaisti.com.br/wp-content/uploads/2009/06/modelo.jpg)

| ![empresas](https://s.profissionaisti.com.br/wp-content/uploads/2009/06/empresas.jpg) | ![programadores_linguagens](https://s.profissionaisti.com.br/wp-content/uploads/2009/06/programadores_linguagens.jpg) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![programador](https://s.profissionaisti.com.br/wp-content/uploads/2009/06/programador1.jpg) | ![linguagens](https://s.profissionaisti.com.br/wp-content/uploads/2009/06/linguagens.jpg) |

### Cruzando Dados

**1 – Produto Cartesiano**

Esse não é bem um tipo de junção, é conhecido como conexão cruzada, ou seja, resulta no cruzamento de cada linha de uma tabela com todas as linhas de outra tabela.

SELECT p.nome, l.nome FROM programadores p CROSS JOIN linguagens l;

![cross](https://s.profissionaisti.com.br/wp-content/uploads/2009/06/cross.jpg)

O resultado desta consulta gera 36 linhas de combinações. Obs.: Esse tipo de junção não é muito utilizado.

Para obter o mesmo resultado podemos utilizar outras duas sintaxes: retirar o CROSS JOIN e inserir uma vírgula (,) ou usar o INNER JOIN sem a condição de comparação (ON). O resultado será o mesmo.

SELECT p.nome, l.nome FROM programadores p, linguagens l;

SELECT p.nome, l.nome FROM programadores p INNER JOIN linguagens l;



## Entendendo Outer Joins SQL – Parte 2 (Conexões Internas – INNER JOIN)

  

**2 – Conexões Internas**

A conexão interna inicialmente faz a mesma coisa que a [conexão cruzada](https://www.profissionaisti.com.br/2009/06/entendendo-outer-joins-sql-parte-1-produto-cartesiano), porém aplica restrições que podem ser de igualdade ou desigualdade, isso faz com que algumas linhas sejam eliminadas do resultado.

**Vamos lá então, quem faz parte das conexões internas?** INNER JOIN, NATURAL JOIN e STRAIGHT_JOIN.

***Obs.:** O modelo de dados utilizado neste post encontra-se em [Entendendo Outer Joins SQL – Parte 1 (Produto Cartesiano)](https://www.profissionaisti.com.br/2009/06/entendendo-outer-joins-sql-parte-1-produto-cartesiano)*

Digamos que eu necessite fazer uma busca de todos os programadores e suas respectivas empresas, como resolvo?

SELECT * FROM programadores p INNER JOIN empresas e ON e.id_empresa = p.id_empresa;

Na consulta acima, fiz uma junção entre as duas tabelas que guardam as informações que preciso e fiz uma restrição (ON) comparando a chave da empresa existente nas duas tabelas.

A figura abaixo mostra o comportamento do banco, o qual relaciona cada linha de uma tabela com todos os registros da outra tabela, trazendo como resultado somente as linhas onde a chave da empresa é idêntica (linhas em vermelho).

![inner_comofunciona](https://s.profissionaisti.com.br/wp-content/uploads/2009/06/inner_comofunciona.jpg)

Essa restrição (ON) não impede que eu utilize as outras opções da sintaxe do SELECT, por exemplo, o WHERE. Inclusive eu obteria o mesmo resultado com a consulta abaixo:

SELECT * FROM programadores p, empresas e WHERE e.id_empresa = p.id_empresa;



Vamos para uma segunda situação, na qual preciso de uma lista com todos os programadores que programam em pelo menos uma linguagem e saber quais são estas. Como mostra o modelo, temos um relacionamento n:m que originou a tabela programadores_linguagens, como resolvo?

SELECT p.nome, l.nome
FROM programadores p
INNER JOIN programadores_linguagens pl
ON pl.id_programador = p.id_programador
INNER JOIN linguagens l
ON l.id_linguagens = pl.id_linguagens;

![inner2](https://s.profissionaisti.com.br/wp-content/uploads/2009/06/inner2.jpg)

Repare que os programadores Pinter e Gabriel não apareceram no resultado da consulta, isso porque ambos não estão relacionados à nenhuma linguagem de programação. Mas, e se fosse necessário a presença deles na listagem? Você resolveria utilizando conexões externas (item 3).

O NATURAL JOIN e o STRAIGHT_JOIN fazem exatamente a mesma coisa que o INNER JOIN em questão de resultado, porém, com suas particularidades:

**NATURAL JOIN:** com ele você não precisa identificar quais colunas serão comparadas, pois ele fará a comparação entre campos com mesmo nome.

SELECT * FROM programadores NATURAL JOIN empresas;

**STRAIGHT_JOIN:** faz com que a tabela a esquerda seja lida primeiro, isso é utilizado quando o otimizador do JOIN coloca as tabelas em ordem errada. Isto é muito pouco utilizado.

SELECT * FROM programadores p STRAIGHT_JOIN empresas e ON e.id_empresa = p.id_empresa;

**Obs.:
1** **–** Posso substituir o **ON** por **USING** quando o nome nas duas tabelas for idêntico.

Ex.: SELECT * FROM programadores p INNER JOIN empresas e USING(id_empresa);

**2 –** O uso do **INNER** é opcional.

Ex.: SELECT * FROM programadores p JOIN empresas e USING (id_empresa);



## Entendendo Outer Joins SQL – Parte 3 (Conexões Externas LEFT, RIGHT JOIN)

  

**3 – Conexões Externas**

As conexões externas servem para efetuar junções entre tabelas sem que necessariamente exista entre elas uma combinação exata. O **LEFT** e o **RIGHT OUTER JOIN** são os componentes desse tipo de conexão. Não é obrigado o uso do termo OUTER, se você encontrar apenas LEFT JOIN, por exemplo, funcionará da mesma maneira.

O OUTER JOIN pode ser utilizado quando você quiser retornar uma lista de todos os programadores, mesmo que estes não estejam relacionados a nenhuma linguagem de programação. A diferença do LEFT para o RIGHT está apenas na identificação de qual tabela da junção irá retornar todos os dados. O mais comum é o LEFT, pois normalmente colocamos a tabela mais importante primeiro. Inclusive a ordem das tabelas e das cláusulas de restrições na consulta, em alguns bancos, altera o desempenho.

**Obs.:** O modelo de dados utilizado neste post encontra-se em [Entendendo Outer Joins SQL – Parte 1 (Produto Cartesiano)](https://www.profissionaisti.com.br/2009/06/entendendo-outer-joins-sql-parte-1-produto-cartesiano)

No exemplo abaixo quero uma lista com todos os programadores e suas empresas independente se eles estão relacionados a uma empresa ou não.

SELECT * FROM programadores p LEFT JOIN empresas e ON e.id_empresa = p.id_empresa;

Já neste exemplo, eu quero a lista de todas as empresas e seus programadores, independente se a empresa tem ou não programadores.

SELECT * FROM programadores p RIGHT JOIN empresas e ON e.id_empresa = p.id_empresa;

O banco vai relacionar cada linha de uma tabela com todas as linhas da outra tabela, trazendo as linhas que atendem a comparação existente na consulta (ON) e para as empresas que não tem nem um programador ele irá preencher com NULL os campos, como é o caso da “Empresa 5”.

![right](https://s.profissionaisti.com.br/wp-content/uploads/2009/06/right.jpg)