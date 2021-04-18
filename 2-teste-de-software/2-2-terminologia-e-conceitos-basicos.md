# 2.2 Terminologia e Conceitos Básicos

A IEEE tem realizado vários esforços de padronização, entre eles para padronizar a terminologia utilizada no contexto de Engenharia de Software. O padrão [ISO/IEC/IEEE 24765-2017](https://ieeexplore.ieee.org/document/8016712) diferencia os termos: **defeito** \(_fault_\) – passo, processo ou definição de dados incorreto, como por exemplo, uma instrução ou comando incorreto; **engano** \(_mistake_\) – ação humana que produz um resultado incorreto, com por exemplo, uma ação incorreta tomada pelo programador; **erro** \(_error_\) – diferença entre o valor obtido e o valor esperado, ou seja, qualquer estado intermediário incorreto ou resultado inesperado na execução do programa constitui um erro; e **falha** \(_failure_\) – produção de uma saída incorreta com relação à especificação. 

A figura abaixo ilustra o relacionamento entre os termos, sendo o defeito e o erro considerados a causa e a falha, a consequência. 

![Rela&#xE7;&#xE3;o entre os termos](../.gitbook/assets/terminologia.png)

De uma forma geral, os defeitos são classificados em: **defeitos computacionais** – o defeito provoca uma computação incorreta mas o caminho executado \(sequências de comandos\) é igual ao caminho esperado; e **defeitos de domínio** – o caminho efetivamente executado é diferente do caminho esperado, ou seja, um caminho incorreto é selecionado.

A atividade de teste é permeada por uma série de limitações \([Delamaro et al. 2016](https://www.grupogen.com.br/e-book-introducao-ao-teste-de-software)\). Em geral, os seguintes problemas são indecidíveis: dados dois programas, se eles são equivalentes; dados duas sequências de comandos \(caminhos\) de um programa, ou de programas diferentes, se eles computam a mesma função; e dado um caminho se ele é executável ou não, ou seja, se existe um dado de entrada que leva à execução desse caminho. Outra limitação fundamental é a correção coincidente – o programa pode apresentar, coincidentemente, um resultado correto para um item particular de um dado _d_ ∈ _D_, ou seja, um particular item de dado ser executado, satisfazer a um requisito de teste e não faz o software falhar.

Diz-se que um programa $$P$$com domínio de entrada $$D$$ é correto com respeito a uma especificação $$S$$se $$S(d) = P^*(d)$$para qualquer item de dado $$d \in D$$, ou seja, se o comportamento do programa está de acordo com o comportamento esperado para todos os dados de entrada. Dados dois programas $$P_1$$e $$P_2$$  , se $$P^*_1(d) = P^*_2(d)$$, para qualquer $$d \in D$$, diz-se que $$P_1$$e $$P_2$$ são **equivalentes**.

No teste de software, pressupõe-se a existência de um **oráculo** – o testador ou algum outro mecanismo – que possa determinar, para qualquer item de dado $$d \in D$$ , se  $$S(d) = P^*(d)$$, dentro de limites de tempo e esforços razoáveis. Um oráculo simplesmente decide se os valores de saída apresentados por $$P$$ são corretos em relação à especificação $$S$$ . 

Sabe-se que o **teste exaustivo** é impraticável, ou seja, testar para todos os elementos possíveis do domínio de entrada é, em geral, caro e demanda muito mais tempo do que o disponível. Ainda, deve-se salientar que não existe um procedimento de teste de propósito geral que possa ser usado para provar a correção de um programa. Apesar de não ser possível, por meio dos testes, provar que um programa está correto, os testes, se conduzidos sistemática e criteriosamente, contribuem para aumentar a confiança de que o software desempenha as funções especificadas e evidenciar algumas características mínimas do ponto de vista da qualidade do produto.

Assim, duas questões são chaves na atividade de teste: "Como os dados de teste devem ser selecionados?" e "Como decidir se um programa $$P$$ foi suficientemente testado?". **Critérios** para selecionar e avaliar conjuntos de casos de teste são fundamentais para o sucesso da atividade de teste. Tais critérios devem fornecer indicação de quais casos de teste devem ser utilizados de forma a aumentar as chances de fazer o software falhar, evidenciando a presença de defeitos no mesmo, ou quando falhas não ocorrerem, estabelecer um nível elevado de confiança na correção do programa. 

Um **caso de teste** consiste de um par ordenado $$(d, S(d))$$, no qual $$d \in D$$ é denominado de **dado de teste**, e corresponde à entrada fornecida ao programa, e $$S(d)$$é a respectiva **saída esperada** conforme a especificação. 

Dados um programa $$P$$e um conjunto de teste $$T$$, definem-se: 

* **Critério de Adequação** de Casos de Teste: predicado para avaliar $$T$$ no teste de $$P$$; e
* **Método de Seleção** de Casos de Teste: procedimento para escolher casos de teste para o teste de $$P$$. 

Em outras palavras, o papel de um critério de teste é o de gerar os chamados requisitos de testes. Tais requisitos podem ser utilizados para avaliar a adequação \(critério de adequação\) de um conjunto de teste já existente, ou os requisitos são utilizados para guiar a geração de casos de teste \(método de seleção\).



