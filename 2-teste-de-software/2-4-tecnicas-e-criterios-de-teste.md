# 2.4 Técnicas e Critérios de Teste

Para se conduzir e avaliar a qualidade da atividade de teste têm-se as técnicas de teste funcional, estrutural e baseada em defeitos \([Delamaro et al. 2016](https://www.grupogen.com.br/e-book-introducao-ao-teste-de-software)\). Tais técnicas diferenciam-se pela origem da informação utilizada na avaliação e construção dos conjuntos de teste.

* Na **técnica funcional**, os critérios e requisitos de teste são estabelecidos a partir da função de especificação do software; 
* Na **técnica estrutural**, os critérios e requisitos são derivados essencialmente a partir das características de uma particular implementação em teste; e
* Na **técnica baseada em defeitos**, os critérios e requisitos de teste são oriundos do conhecimento sobre defeitos típicos cometidos no processo de desenvolvimento de software.

A figura a seguir ilustra o relacionamento entre técnica, critério, requisito e caso de teste.

![Relacionamentos entre Conceitos de Teste](../.gitbook/assets/relacionamento-conceitos.png)

Independentemente de qual técnica determinado critério pertence, o papel de todo e qualquer critério de teste é derivar requisitos de teste que de forma direta ou indireta, particionam o domínio de entrada do produto em teste, forçando o testador a selecionar ao menos um elemento de cada subdomínio pra poder satisfazer o critério em questão. Desse modo, critérios mais rigorosos particionam mais o domínio de entrada do que critérios menos rigorosos. As figuras abaixo, adaptada de [Pezzè e Young \(2007\)](https://www.amazon.com/exec/obidos/ASIN/0471455938/acmorg-20) ilustram a segmentação ocacionada por diferentes critérios de teste supostamente dos menos rigorosos \(a\) para os mais rigorosos \(e\).

| ![](../.gitbook/assets/dominio-01.png)  | ![](../.gitbook/assets/dominio-02.png)  |
| :---: | :---: |
| \(a\) | \(b\) |
| ![](../.gitbook/assets/dominio-03.png)  | ![](../.gitbook/assets/dominio-04.png)  |
| \(c\) | \(d\) |

![\(e\)](../.gitbook/assets/dominio-05.png)

Neste texto será dado ênfase à técnica de teste funcional e alguns de seus critérios. Para ilustrar a aplicação dos critérios de teste será utilizada a especificação de um programa simples, parte de um compilador, denominado `Identifier`. A especificação do referido programa é dada abaixo:

```text
###############################################################################
# Especificação Identifier.py
# ---------------------------
#
# O programa deve determinar se um identificador é ou não valido em 
# ’Silly Pascal’ (uma estranha variante do Pascal).
#
# Um identificador válido deve começar com uma letra e conter apenas letras 
# ou dígitos. Caracteres acentuados não são válidos.
#
# Além disso, deve ter no mínimo 1 e no máximo 6 caracteres de comprimento.
###############################################################################
```

## Técnica Funcional

O teste funcional também é conhecido como teste caixa preta pelo fato de tratar o software como uma caixa cujo conteúdo é desconhecido e da qual só é possível visualizar o lado externo, ou seja, os dados de entrada fornecidos e as respostas produzidas como saída \([Delamaro et al. 2016](https://www.grupogen.com.br/e-book-introducao-ao-teste-de-software)\). Na técnica de teste funcional são verificadas as funções do sistema sem se preocupar com os detalhes de implementação. 

O teste funcional envolve dois passos principais: identificar as funções que o software deve realizar e criar casos de teste capazes de checar se essas funções são realizadas pelo software \([Delamaro et al. 2016](https://www.grupogen.com.br/e-book-introducao-ao-teste-de-software)\). As funções que o software deve possuir são identificadas a partir de sua especificação. Assim, uma especificação bem elaborada e de acordo com os requisitos do usuário é essencial para esse tipo de teste. 

Dois dos critérios funcionais mais conhecidos são:

* **Particionamento em Classes de Equivalência**: a partir das condições de entrada de dados identificadas na especificação, divide-se o domínio de entrada de um programa em classes de equivalência válidas e inválidas. Em seguida seleciona-se o menor número possível de casos de teste, baseando-se na hipótese que um elemento de uma dada classe seria representativo da classe toda, sendo que para cada uma das classes inválidas deve ser gerado um caso de teste distinto. O uso de particionamento permite examinar os requisitos mais sistematicamente e restringir o número de casos de teste existentes. Alguns autores também consideram o domínio de saída do programa para estabelecer as classes de equivalência \([Copeland, 2004](https://dl.acm.org/doi/10.5555/975083)\).
* **Análise do Valor Limite**: é um complemento ao critério Particionamento em Classes de Equivalência, sendo que os limites associados às condições de entrada são exercitados de forma mais rigorosa; ao invés de se selecionar qualquer elemento de uma classe, os casos de teste são escolhidos nas fronteiras das classes, pois há indícios que nesses pontos há uma maior concentração de defeitos. O espaço de saída do programa também é particionado e são exigidos casos de teste que produzam resultados nos limites dessas classes de saída.

Um dos problemas relacionado aos critérios funcionais é que muitas vezes a especificação do programa é feita de modo descritivo e não formal. Dessa maneira, os requisitos de teste derivados de tais especificações são também, de certa forma, imprecisos e informais. Como consequência, tem-se dificuldade em automatizar a aplicação de tais critérios, que ficam, em geral, restritos à aplicação manual. Por outro lado, para a aplicação desses critérios é essencial apenas que se identifiquem as entradas, a função a ser computada e a saída do programa, o que os tornam aplicáveis praticamente em todas fases de teste \(unidade, integração e sistema\). No caso do TDD, essa é a técnica de teste mais utilizada pois é sempre a partir do entendimento da especificação do que será construído que os testes são desenvolvidos \([Percival, 2017](http://www.obeythetestinggoat.com/pages/book.html)\).

### Critério Particionamento em Classe de Equivalência

A título de ilustração, considerando a especofocação do programa `Identifier` e o critério Particionamento em Classes de Equivalência, são identificadas na tabela abaixo as condições de entrada e classes de equivalência válidas e inválidas segundo a interpretação dos autores.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Restri&#xE7;&#xF5;es de Entrada</th>
      <th style="text-align:center">Classe V&#xE1;lida</th>
      <th style="text-align:center">Classe Inv&#xE1;lida</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Tamanho do identificador</td>
      <td style="text-align:center">
        <p></p>
        <p>(1)</p>
      </td>
      <td style="text-align:center">
        <p></p>
        <p>(2) (3)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Primeiro caractere &#xE9; uma letra</td>
      <td style="text-align:center">
        <p>Sim</p>
        <p>(4)</p>
      </td>
      <td style="text-align:center">
        <p>N&#xE3;o</p>
        <p>(5)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Demais caracteres s&#xE3;o letras ou d&#xED;gitos</td>
      <td style="text-align:center">
        <p>Sim</p>
        <p>(6)</p>
      </td>
      <td style="text-align:center">
        <p>N&#xE3;o</p>
        <p>(7)</p>
      </td>
    </tr>
  </tbody>
</table>

Como foi mencionado anteriormente, ao aplicar um critério de teste considerando determinada fonte de informação, são gerados os requisitos de teste demandados pelo referido critério. No caso do critério Particionamento em Classe de Equivalência, esses requisitos correspondem a partições, como as exibidas na tabela acima. Observa-se que uma vez estabalecidos os requisitos, todo e qualquer conjunto de teste que satisfaça o critério terá ao menos cinco casos de teste: um para cobrir as classes válidas e outros quatro, um para cada classe inválida. Os números abaixo das partições servem para identificá-las.

Por exemplo, dada a tabela acima, um possível caso de teste para cobrir as partições válidas seria: `("a1", Válido)`. Observa-se que esse teste é capaz de satisfazer as partições \(1\), \(4\) e \(6\) pois tem tamanho 2, inicia com letra e contém apenas mais um dígito após a primeira letra.

O segundo caso de teste, capaz de cobrir a partição inválida \(2\) é o `("", Inválido)`, ou seja, a cadeia vazia.

As demais partições \(3\), \(4\) e \(5\) poderiam ser cobertas, por exemplo, com os casos de teste `("2B3", Inválido)`, `("Z-12", Inválido)`, `("A1b2C3d", Inválido)`, respectivamente.

Desse modo, têm-se o conjunto de teste $$T_0 = $$ {`("a1", Válido)`, `("", Inválido)`, `("2B3", Inválido)`, `("Z-12", Inválido)`, `("A1b2C3d", Inválido)` } como sendo um conjunto adequado para o critério Particionamento em Classe de Equivalência, considerando as partições definidas.

Observe que no caso do critério Particionamento em Classe de Equivalência, todo e qualquer valor dentro da partição é considerado um representante da classe de equivalência e pode ser selecionado para dar origem ao caso de teste. No critério de teste a seguir, a diferença é apenas essa, ou seja, quem pode ser utilizado para compor o caso de teste.

### Critério Análise do Valor Limite

De acordo com [Myers et al. \(2011\)](https://www.wiley.com/en-gb/The+Art+of+Software+Testing,+3rd+Edition-p-9781118133156), a experiência mostra que casos de teste que exploram condições limites têm uma maior probabilidade de encontrar erros. Tais condições correspondem a valores que estão exatamente ou imediatamente acima ou abaixo dos limitantes das classes de equivalência.

Assim, esse critério é usado em conjunto com o Particionamento de Equivalência, mas ao invés dos dados de teste serem escolhidos aleatoriamente, eles devem ser selecionados de forma que o limitante de cada classe de equivalência seja explorado. Segundo [Myers et al. \(2011\)](https://www.wiley.com/en-gb/The+Art+of+Software+Testing,+3rd+Edition-p-9781118133156), além da escolha seletiva dos dados de teste, o outro ponto que difere este critério do Particionamento de Equivalência, é a observação do domínio de saída, diferentemente de outros autores, que já fazem essa consideração no próprio critério de Particionamento de Equivalência.

Embora não existam diretrizes bem definidas que levem à determinação dos dados de teste, [Myers et al. \(2011\)](https://www.wiley.com/en-gb/The+Art+of+Software+Testing,+3rd+Edition-p-9781118133156) sugerem que as seguintes recomendações sejam seguidas:

1. se a condição de entrada especifica um intervalo de valores, devem ser definidos dados de teste para os limites desse intervalo e dados de teste imediatamente subsequentes, que explorem as classes inválidas vizinhas desse intervalo. Por exemplo, se uma classe válida estiver no intervalo $$[1..100]$$, os seguintes dados de teste devem ser derivados: 0, 1, 2, 99, 100 e 101. Ou seja, o limite, e um elemento abaixo \(inválido\) e um acima \(válido\) do limite.
2. se a condição de entrada especifica uma quantidade de valores, por exemplo, de 1 a 255 valores, devem ser definidos dados de teste com nenhum valor de entrada, somente um valor, dois valores, 254 valores, 255 valores e 256 valores de entrada.
3. usar a diretriz i\) para as condições de saída.
4. usar a diretriz ii\) para as condições de saída.
5. se a entrada ou saída for um conjunto ordenado, deve ser dada maior atenção ao primeiro e último elemento desse conjunto;
6. usar a intuição para definir outras condições limites.

Considerando as partições definidas para o programa Identifier, a definição do que é letra ou dígito poderia ser mais detalhada, facilitando a identificação dos limites das classes de equivalência. Por exemplo, a tabela abaixo ilustra esse detalhamento. No caso, assume-se que há uma tabela de codficação de caracteres de modo que é possível determinar qual o caracter imediatamente anterior a "a", por exemplo. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">Restri&#xE7;&#xF5;es de Entrada</th>
      <th style="text-align:center">Classe V&#xE1;lida</th>
      <th style="text-align:center">Classe Inv&#xE1;lida</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Tamanho do identificador</td>
      <td style="text-align:center">
        <p></p>
        <p>(1)</p>
      </td>
      <td style="text-align:center">
        <p></p>
        <p>(2) (3)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Primeiro caractere &#xE9; uma letra</td>
      <td style="text-align:center">
        <p></p>
        <p>(4)</p>
      </td>
      <td style="text-align:center">
        <p></p>
        <p>(5)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Demais caracteres s&#xE3;o letras ou d&#xED;gitos</td>
      <td style="text-align:center">
        <p></p>
        <p>(6)</p>
      </td>
      <td style="text-align:center">
        <p></p>
        <p>(7)</p>
      </td>
    </tr>
  </tbody>
</table>

Para facilitar a geração dos casos de teste considere utilizar a [Tabela ASCII](https://python-reference.readthedocs.io/en/latest/docs/str/ASCII.html) para identificação dos símbolos válidos e inválidos de cada classe. Como pode ser observado, o critério Análise do Valor Limite pode ser considerado mais rigoroso pois exige bem mais casos de testes, e também satisfaz os requisitos demandados pelo critério Particionamento em Classe de Equivalência.



