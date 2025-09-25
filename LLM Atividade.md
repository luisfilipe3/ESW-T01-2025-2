# Estudo Dirigido: LLMs no Aprendizado de Ruby (Homework 01)

Este documento apresenta a resolução e análise do "Homework 1: Ruby calisthenics", focando em como Modelos de Linguagem de Grande Escala (LLMs) podem ser utilizados como ferramentas de aprendizado e desenvolvimento.

-----

## 1\. Resolução dos Exercícios (Partes 01 a 06)

A seguir estão as resoluções de código para cada exercício proposto.

### **Parte 1: Fun with strings**

#### a) Palindromes

*O objetivo é escrever um método que determine se uma string é um palíndromo, ignorando caso, pontuação e caracteres não verbais, sem usar loops ou iteração*.

```ruby
def palindrome?(string)
  normalized_string = string.downcase.gsub(/\W/, '')
  normalized_string == normalized_string.reverse
end
```

#### b) Contagem de Palavras

*O objetivo é retornar um hash com a frequência de cada palavra em uma string, ignorando caso e não palavras. Iteradores são permitidos, mas `for-loops` não*.

```ruby
def count_words(string)
  words = string.downcase.scan(/\b\w+\b/)
  words.each_with_object(Hash.new(0)) { |word, hash| hash[word] += 1 }
end
```

### **Parte 2: Rock-Paper-Scissors**

#### a) Vencedor de um Jogo

*O objetivo é criar um método que recebe um jogo de dois jogadores, valida as entradas e retorna o vencedor. Em caso de empate, o primeiro jogador vence*.

```ruby
class WrongNumberOfPlayersError < StandardError; end
class NoSuchStrategyError < StandardError; end

def rps_game_winner(game)
  raise WrongNumberOfPlayersError unless game.length == 2
  valid_strategies = %w[R P S]
  player1, player2 = game

  unless valid_strategies.include?(player1[1].upcase) && valid_strategies.include?(player2[1].upcase)
    raise NoSuchStrategyError
  end

  return player1 if player1[1] == player2[1]

  case [player1[1].upcase, player2[1].upcase]
  when %w[R S], %w[S P], %w[P R]
    player1
  else
    player2
  end
end
```

#### b) Vencedor de um Torneio

*O objetivo é criar um método que recebe um torneio aninhado como um array e retorna o vencedor final*.

```ruby
def rps_tournament_winner(tournament)
  return rps_game_winner(tournament) if tournament[0][0].is_a?(String)

  left_winner = rps_tournament_winner(tournament[0])
  right_winner = rps_tournament_winner(tournament[1])
  rps_game_winner([left_winner, right_winner])
end
```

### **Parte 3: Anagrams**

*O objetivo é agrupar um array de strings em sub-arrays de anagramas, ignorando o caso*.

```ruby
def combine_anagrams(words)
  words.group_by { |word| word.downcase.chars.sort }.values
end
```

### **Parte 4: Basic OOP**

#### a) Classe `Dessert`

*Crie uma classe `Dessert` com getters/setters para nome e calorias, e métodos `healthy?` (\< 200 calorias) e `delicious?` (sempre true)*.

```ruby
class Dessert
  attr_accessor :name, :calories

  def initialize(name, calories)
    @name = name
    @calories = calories
  end

  def healthy?
    @calories < 200
  end

  def delicious?
    true
  end
end
```

#### b) Classe `JellyBean`

*Crie `JellyBean` que herda de `Dessert`, adiciona um atributo `flavor`, e modifica `delicious?` para retornar `false` se o sabor for "black licorice"*.

```ruby
class JellyBean < Dessert
  attr_accessor :flavor

  def initialize(name, calories, flavor)
    super(name, calories)
    @flavor = flavor
  end

  def delicious?
    @flavor != 'black licorice'
  end
end
```

### **Parte 5: Metaprogramming**

*Crie o método `attr_accessor_with_history` que, além da funcionalidade de `attr_accessor`, rastreia o histórico de valores de um atributo*.

```ruby
class Class
  def attr_accessor_with_history(attr_name)
    attr_name = attr_name.to_s
    attr_reader attr_name
    attr_reader "#{attr_name}_history"

    class_eval %Q{
      def #{attr_name}=(value)
        @#{attr_name}_history ||= [nil]
        @#{attr_name}_history << value
        @#{attr_name} = value
      end
    }
  end
end
```

### **Parte 6: Advanced OOP, Open Classes and Duck Typing**

#### a) Conversão de Moeda

*Estenda a classe `Numeric` para permitir conversões como `5.dollars.in(:euros)`*.

```ruby
class Numeric
  @@currencies = { 'dollar' => 1.0, 'yen' => 0.013, 'euro' => 1.292, 'rupee' => 0.019 }

  def method_missing(method_id)
    singular_currency = method_id.to_s.gsub(/s$/, '')
    if @@currencies.has_key?(singular_currency)
      self * @@currencies[singular_currency]
    else
      super
    end
  end

  def in(currency)
    singular_currency = currency.to_s.gsub(/s$/, '')
    self / @@currencies[singular_currency]
  end
end
```

#### b) Palíndromo em `String`

*Adapte a solução de palíndromo para que funcione como `"foo".palindrome?`*.

```ruby
class String
  def palindrome?
    normalized_str = self.downcase.gsub(/\W/, '')
    normalized_str == normalized_str.reverse
  end
end
```

#### c) Palíndromo em `Enumerable`

*Generalize a solução de palíndromo para funcionar em Enumerables, como `[1,2,3,2,1].palindrome?`*.

```ruby
module Enumerable
  def palindrome?
    self.to_a == self.to_a.reverse
  end
end
```

-----

## 2\. Explicação das Estruturas e Algoritmos

Aqui detalhamos a lógica por trás de cada solução.

  * **Parte 1a (Palindrome):** O algoritmo normaliza a string de entrada convertendo-a para minúsculas e removendo todos os caracteres não-alfanuméricos com a expressão regular `\W`. Em seguida, compara a string resultante com sua versão invertida. É uma abordagem funcional e declarativa.

  * **Parte 1b (Contagem de Palavras):** A string é primeiramente "limpa" e dividida em um array de palavras usando `scan` com a regex `\b\w+\b`, que captura sequências de caracteres de palavra. O método `each_with_object(Hash.new(0))` é usado para iterar sobre o array e construir um hash. O `Hash.new(0)` garante que, se uma chave ainda não existir, seu valor inicial seja 0, permitindo o incremento `+= 1` sem verificações adicionais.

  * **Parte 2a (RPS Game):** O algoritmo primeiro valida as entradas, garantindo que haja exatamente dois jogadores e que as estratégias sejam válidas ("R", "P" ou "S"), lançando exceções caso contrário. A lógica do vencedor é implementada com um `case statement` que verifica todas as combinações vitoriosas para o primeiro jogador. Se nenhuma dessas combinações corresponder (e não for um empate), o segundo jogador vence.

  * **Parte 2b (RPS Tournament):** A solução usa **recursão**. O **caso base** da recursão é um único jogo (identificado por `is_a?(String)` no nome do jogador). Nesse caso, a função `rps_game_winner` é chamada. O **passo recursivo** ocorre quando um elemento do torneio é outro torneio; a função se chama para a parte esquerda e direita do array, e os vencedores resultantes jogam entre si.

  * **Parte 3 (Anagrams):** A chave do algoritmo é que anagramas, quando normalizados (convertidos para minúsculas e com as letras ordenadas), são idênticos. O método `group_by` do Ruby é usado para agrupar as palavras com base nessa chave normalizada. Por fim, `.values` extrai apenas os agrupamentos de palavras do hash resultante.

  * **Parte 4 (OOP):** A solução implementa conceitos básicos de OOP. A classe `Dessert` usa `attr_accessor` para gerar getters/setters. A classe `JellyBean` usa herança (`< Dessert`) para reutilizar o comportamento de `Dessert`. O método `delicious?` em `JellyBean` **sobrescreve** o método da classe pai para implementar uma lógica específica, e `super` é usado no construtor para chamar o inicializador da classe pai.

  * **Parte 5 (Metaprogramming):** A solução usa metaprogramação para adicionar um método à própria classe `Class`, tornando-o disponível para todas as definições de classe. O método `class_eval` executa uma string de código no contexto da classe, permitindo a definição dinâmica de um método setter (`#{attr_name}=`). Dentro deste método, o operador `||=` inicializa o array de histórico na primeira vez que o setter é chamado para um objeto.

  * **Parte 6 (Open Classes & Duck Typing):**

      * **a) Moeda:** Usa "monkey patching" para abrir e modificar a classe `Numeric`. `method_missing` intercepta chamadas a métodos inexistentes (como `.dollars`), tratando-os como conversões de moeda.
      * **b) Palíndromo em `String`:** Modifica a classe `String` para adicionar o método `palindrome?` diretamente a ela.
      * **c) Palíndromo em `Enumerable`:** Adiciona o método `palindrome?` ao módulo `Enumerable`. Qualquer classe que inclua este módulo (como `Array` e `Range`) ganha automaticamente essa funcionalidade, um exemplo clássico de **duck typing**.

-----

## 3\. Implementações Alternativas e Análise Comparativa

Discutimos aqui abordagens alternativas para resolver os mesmos problemas.

  * **Parte 1 (Strings): Paradigma Funcional vs. Imperativo**

      * **Alternativa:** Para ambos os exercícios, uma abordagem imperativa usando loops (`while` ou `each` com lógica manual) poderia ser usada.
      * **Vantagens:** O controle manual pode, em teoria, permitir micro-otimizações de memória ou performance.
      * **Desvantagens:** O código se torna mais verboso, complexo e menos "idiomático" em Ruby. As soluções funcionais com `gsub`, `scan`, e `group_by` são mais declarativas, legíveis e concisas.

  * **Parte 2 (RPS): `case` Statement vs. Estrutura de Dados**

      * **Alternativa:** Em vez de um `case statement`, as regras do jogo poderiam ser armazenadas em um Hash, como `WINNING_MOVES = { 'R' => 'S', ... }`. A lógica de vitória seria verificar se `WINNING_MOVES[player1_move] == player2_move`.
      * **Vantagens:** Desacopla as regras (dados) da lógica de execução. Torna o código mais fácil de ler e estender se novas jogadas forem adicionadas.
      * **Desvantagens:** Para um conjunto fixo e pequeno de regras como o RPS clássico, a diferença é mínima e pode ser considerada uma complexidade desnecessária por alguns.

  * **Parte 4 (OOP): Herança vs. Mixins**

      * **Alternativa:** A característica `delicious?` poderia ser implementada em um módulo (`module Delicious`) e incluída na classe `Dessert` com `include Delicious`.
      * **Vantagens:** Mixins são ideais para compartilhar comportamento entre classes que não têm uma relação de herança direta (is-a). Promove a composição em vez da herança.
      * **Desvantagens:** Para este problema, onde `JellyBean` é claramente um tipo de `Dessert`, a herança simples é a ferramenta mais direta e apropriada. Usar um mixin aqui seria um exagero.

  * **Parte 5 (Metaprogramming): `class_eval` vs. `define_method`**

      * **Alternativa:** Usar `define_method` com um bloco em vez de `class_eval` com uma string.
      * **Vantagens:** `define_method` é geralmente considerado mais seguro, pois evita os riscos de injeção de código associados à avaliação de strings. O código dentro do bloco também se beneficia de melhor suporte de ferramentas (linting, highlighting).
      * **Desvantagens:** A manipulação de variáveis de instância dentro do bloco `define_method` pode ser mais verbosa, exigindo `instance_variable_set` e `instance_variable_get`.

  * **Parte 6 (Open Classes): Monkey Patching vs. Módulos de Utilitários**

      * **Alternativa:** Em vez de abrir classes nativas (`Numeric`, `String`), criar um módulo de utilitários (ex: `module StringUtils`) com métodos de classe (ex: `StringUtils.palindrome?(str)`).
      * **Vantagens:** Evita a poluição de namespaces globais e o risco de conflitos com outras bibliotecas, tornando o código mais seguro e manutenível em projetos grandes.
      * **Desvantagens:** Perde-se a sintaxe fluida e elegante (`"foo".palindrome?`) que torna o código em Ruby tão expressivo.

-----

## 4\. Análise do Processo com LLM e Feedback Final

### Análise de Tempo e Esforço

O uso de um LLM no processo de resolução deste homework altera fundamentalmente a distribuição de tempo e esforço.

  * **Entender:** **Esforço Reduzido.** O tempo gasto para entender conceitos complexos (como a estrutura de dados aninhada do torneio ou o funcionamento de `class_eval`) é drasticamente diminuído. Perguntas diretas ao LLM fornecem explicações e exemplos que seriam mais demorados de encontrar na documentação.

  * **Desenvolver:** **Esforço Reduzido.** A geração do código inicial é quase instantânea. O esforço muda de "digitar o código" para "formular o prompt correto" e "guiar o LLM" para a solução desejada. Isso acelera massivamente a fase de prototipagem.

  * **Analisar Criteriosamente:** **Esforço Aumentado e Mais Crítico.** Esta se torna a etapa mais importante. O tempo economizado nas fases anteriores *deve* ser reinvestido aqui. A análise crítica envolve questionar o LLM sobre suas escolhas ("Por que usar `group_by`?"), pedir alternativas ("Mostre-me uma versão imperativa"), e validar a correção do código para casos extremos. O esforço é menos mecânico e mais estratégico.

### Conclusão da IA

A utilização de um LLM para resolver os exercícios do Homework 01 foi uma experiência extremamente positiva, revelando seu potencial como uma ferramenta de "par programador" e tutor.

**Pontos Positivos:**

  * **Aceleração do Aprendizado:** O LLM desmistifica conceitos avançados, fornecendo explicações claras e sob demanda, o que é ideal para aprender tópicos como metaprogramação.
  * **Foco em Design:** Ao abstrair a escrita de código repetitivo, o LLM permite que o desenvolvedor se concentre em pensar sobre o design do algoritmo e comparar diferentes abordagens.
  * **Exploração Facilitada:** A capacidade de pedir e analisar rapidamente implementações alternativas (funcional vs. imperativo, herança vs. mixin) é uma forma poderosa de aprofundar o entendimento da linguagem e de paradigmas de programação.

**Riscos e Mitigação:**

  * O principal risco é o **aprendizado superficial**, onde o usuário apenas copia e cola a solução sem entendê-la. A mitigação para isso é uma abordagem ativa: tratar o LLM não como uma fonte de respostas, mas como um parceiro de diálogo, questionando, pedindo justificativas e forçando a exploração de alternativas.

### Conclusão
Por um lado é bom ter a IA ajudando com os códigos base por salvar muito tempo de pensar em quais códigos base usar e usar este tempo em outras atividades, por outro eu tenho medo de confiar em IA em algumas coisas, pois os códigos fornecidos podem ser um plágio de alguém que detém os direitos, nem sempre em códigos mais complexos a IA é confiável em sua resposta, quanto mais elaborado o código e difícil a tarefa, mais alta a chance de ter uma respostas que não funciona corretamente, além de que mesmo que a IA saiba "ensinar" algumas coisas, mesmo as faculdades e escolas tem um certo atraso sobre ensinar de forma eficiente, a IA é limitada ainda enquanto o ser humano tende a ter mais liberdade para criação de materiais e formas de ensinar. A forma de evolução é ter um uso adequado e curioso com a ferramenta.
