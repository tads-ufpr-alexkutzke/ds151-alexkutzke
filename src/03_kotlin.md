# Aula 03: Introdução ao Kotlin para Desenvolvimento Android

## Introdução ao Kotlin

Kotlin é uma linguagem moderna e concisa, totalmente interoperável com Java, e é a linguagem oficial para desenvolvimento Android.

### Vantagens do Kotlin:

- Sintaxe concisa e expressiva;
- Segurança contra NullPointerExceptions;
- Suporte a programação funcional;
- Interoperabilidade com Java;
- Coroutines para programação assíncrona;

**Exemplo 1: Olá, Mundo!**

```kotlin
fun main() {
    println("Olá, Mundo!")
}
```
A função `main` é o ponto de entrada do programa Kotlin. A palavra-chave `fun` indica a definição de uma função, `main` é o nome da função, e os parênteses `()` indicam que ela não recebe argumentos. O corpo da função é delimitado por `{}`.

---

## Variáveis e Tipos de Dados

Em Kotlin, variáveis podem ser declaradas de duas formas principais:

- `val` (imutável, equivalente a `final` em Java)
- `var` (mutável, pode ter seu valor alterado posteriormente)

**Exemplo 2: Declarando variáveis**

```kotlin
val nome: String = "João"
var idade: Int = 25
idade = 26 // Permitido pois "idade" é mutável
```

A sintaxe segue o formato `val/var nome: Tipo = valor`. O tipo pode ser inferido automaticamente pelo compilador.

---

## Controle de Fluxo

O controle de fluxo em Kotlin é essencial para definir a lógica de execução dos programas. Ele permite que o código tome decisões e repita ações com base em condições específicas. As principais estruturas de controle de fluxo em Kotlin incluem:

### Estruturas Condicionais

#### if / else

##### Sintaxe:
```kotlin
if (condicao) {
    // Código executado se a condição for verdadeira
} else {
    // Código executado se a condição for falsa
}
```

##### Exemplo:
```kotlin
val numero = 10
if (numero > 0) {
    println("O número é positivo")
} else {
    println("O número é negativo ou zero")
}
```

O `if` também pode ser usado como expressão retornando um valor:
```kotlin
val resultado = if (numero % 2 == 0) "Par" else "Ímpar"
println(resultado)
```

#### when
O `when` substitui o `switch` de outras linguagens e permite comparar um valor com múltiplas condições.

##### Sintaxe:
```kotlin
when (variavel) {
    valor1 -> { // Código }
    valor2 -> { // Código }
    else -> { // Código }
}
```

##### Exemplo:
```kotlin
val dia = 3
val nomeDoDia = when (dia) {
    1 -> "Domingo"
    2 -> "Segunda-feira"
    3 -> "Terça-feira"
    4 -> "Quarta-feira"
    5 -> "Quinta-feira"
    6 -> "Sexta-feira"
    7 -> "Sábado"
    else -> "Dia inválido"
}
println(nomeDoDia)
```

O `when` também pode ser usado com expressões mais complexas:
```kotlin
val idade = 25
when {
    idade < 12 -> println("Criança")
    idade in 12..17 -> println("Adolescente")
    else -> println("Adulto")
}
```

### Estruturas de Repetição

#### while

##### Sintaxe:
```kotlin
while (condicao) {
    // Código executado repetidamente
}
```

##### Exemplo:
```kotlin
var contador = 1
while (contador <= 5) {
    println("Contador: $contador")
    contador++
}
```

#### do-while
O `do-while` executa pelo menos uma vez, pois a verificação da condição ocorre após a execução do bloco.

##### Sintaxe:
```kotlin
do {
    // Código executado ao menos uma vez
} while (condicao)
```

##### Exemplo:
```kotlin
var numero = 0
do {
    println("Número: $numero")
    numero++
} while (numero < 3)
```

#### for
O `for` é usado para iterar sobre intervalos, listas e outras coleções.

##### Sintaxe:
```kotlin
for (item in colecao) {
    // Código executado para cada item
}
```

##### Exemplo com intervalos:
```kotlin
for (i in 1..5) {
    println("Número: $i")
}
```

##### Exemplo com listas:
```kotlin
val frutas = listOf("Maçã", "Banana", "Laranja")
for (fruta in frutas) {
    println(fruta)
}
```

##### Exemplo com índice:
```kotlin
val nomes = listOf("Ana", "Bruno", "Carlos")
for ((indice, nome) in nomes.withIndex()) {
    println("$indice: $nome")
}
```

#### Controle de Loop: break e continue

- `break`: interrompe o loop completamente.
- `continue`: pula para a próxima iteração do loop.

##### Exemplo:
```kotlin
for (i in 1..10) {
    if (i == 5) break
    println(i)
}
```
Saída:
```
1
2
3
4
```

```kotlin
for (i in 1..10) {
    if (i % 2 == 0) continue
    println(i)
}
```
Saída:
```
1
3
5
7
9
```

---

## Funções em Kotlin

As funções são blocos de código reutilizáveis que executam uma tarefa específica. No Kotlin, as funções podem ter parâmetros, retornar valores e até serem funções de alta ordem.

### Declaração de Funções

A palavra-chave `fun` é usada para declarar uma função em Kotlin.

#### Sintaxe básica:
```kotlin
fun nomeDaFuncao(parametros): TipoDeRetorno {
    // Corpo da função
    return valor
}
```

#### Exemplo:
```kotlin
fun soma(a: Int, b: Int): Int {
    return a + b
}

val resultado = soma(3, 5)
println(resultado) // Saída: 8
```

### Funções com Retorno Unit

Se uma função não retorna um valor, seu tipo de retorno é `Unit` (equivalente a `void` em outras linguagens). O `Unit` pode ser omitido.

```kotlin
fun imprimirMensagem(mensagem: String): Unit {
    println(mensagem)
}

imprimirMensagem("Olá, Kotlin!")
```

### Funções de Uma Linha (Expressões)

Se a função consiste em apenas uma expressão, podemos usar a sintaxe simplificada:

```kotlin
fun multiplicar(a: Int, b: Int) = a * b

println(multiplicar(4, 3)) // Saída: 12
```

### Parâmetros com Valores Padrão

Podemos definir valores padrão para parâmetros:

```kotlin
fun saudar(nome: String = "Visitante") {
    println("Olá, $nome!")
}

saudar() // Saída: Olá, Visitante!
saudar("Carlos") // Saída: Olá, Carlos!
```

### Parâmetros Nomeados

Podemos chamar funções especificando os nomes dos parâmetros para maior clareza:

```kotlin
fun formatarTexto(texto: String, repetir: Int = 1, maiusculo: Boolean = false) {
    val resultado = if (maiusculo) texto.uppercase() else texto
    repeat(repetir) { println(resultado) }
}

formatarTexto(repetir = 3, texto = "Oi", maiusculo = true)
```

### Funções de Extensão

No Kotlin, podemos adicionar novas funções a classes existentes sem modificá-las, usando funções de extensão.

```kotlin
fun String.reverter(): String {
    return this.reversed()
}

println("Kotlin".reverter()) // Saída: niltoK
```

### Funções Lambda (Funções Anônimas)

Kotlin permite definir funções anônimas (lambdas) que podem ser atribuídas a variáveis ou passadas como argumentos.

```kotlin
val soma = { a: Int, b: Int -> a + b }
println(soma(5, 7)) // Saída: 12
```

### Funções de Alta Ordem

Funções que recebem outras funções como parâmetro ou retornam funções são chamadas de alta ordem.

```kotlin
fun operacao(a: Int, b: Int, funcao: (Int, Int) -> Int): Int {
    return funcao(a, b)
}

val resultadoSoma = operacao(10, 20, ::soma)
println(resultadoSoma) // Saída: 30
```

No exemplo acima, usamos `::soma` para referenciar diretamente a função `soma`. O operador `::` é usado para obter uma referência de função, permitindo que a função seja passada como argumento sem ser executada imediatamente.

### Trailing Lambdas

Quando o último (ou único) parâmetro de uma função é uma função lambda, podemos movê-la para fora dos parênteses, tornando o código mais legível. Esse recurso é chamado de *trailing lambda*.

#### Exemplo sem trailing lambda:
```kotlin
fun executar(acao: () -> Unit) {
    acao()
}

executar({ println("Executando ação!") })
```

#### Exemplo com trailing lambda:
```kotlin
executar {
    println("Executando ação!")
}
```

Esse estilo é amplamente utilizado em bibliotecas Kotlin, como nas funções `forEach` de listas:

```kotlin
val numeros = listOf(1, 2, 3, 4)
numeros.forEach {
    println(it)
}
```

---

## Coleções em Kotlin

As coleções em Kotlin são estruturas de dados que armazenam múltiplos elementos. Existem três tipos principais de coleções:

- **List**: Uma coleção ordenada de elementos.
- **Set**: Uma coleção de elementos únicos.
- **Map**: Uma coleção de pares chave-valor.

### Listas (List)

Uma `List` é uma coleção ordenada de elementos, que pode ser mutável ou imutável.

#### Lista imutável (List)
```kotlin
val listaImutavel = listOf("Maçã", "Banana", "Laranja")
println(listaImutavel[0]) // Saída: Maçã
```

#### Lista mutável (MutableList)
```kotlin
val listaMutavel = mutableListOf("Maçã", "Banana")
listaMutavel.add("Laranja")
println(listaMutavel) // Saída: [Maçã, Banana, Laranja]
```

#### Operações comuns com List
```kotlin
val numeros = listOf(1, 2, 3, 4, 5)
println(numeros.size) // Obtém o tamanho da lista
println(numeros.contains(3)) // Verifica se um elemento existe
println(numeros.first()) // Primeiro elemento
println(numeros.last()) // Último elemento
```

### Conjuntos (Set)

Os conjuntos (`Set`) armazenam elementos únicos e não garantem uma ordem específica.

#### Set imutável
```kotlin
val setImutavel = setOf(1, 2, 3, 3)
println(setImutavel) // Saída: [1, 2, 3]
```

#### Set mutável
```kotlin
val setMutavel = mutableSetOf(1, 2, 3)
setMutavel.add(4)
setMutavel.add(2) // Elemento duplicado não é adicionado
println(setMutavel) // Saída: [1, 2, 3, 4]
```

### Mapas (Map)

Os mapas (`Map`) armazenam pares chave-valor.

#### Map imutável
```kotlin
val mapaImutavel = mapOf("nome" to "Carlos", "idade" to 30)
println(mapaImutavel["nome"]) // Saída: Carlos
```

#### Map mutável
```kotlin
val mapaMutavel = mutableMapOf("nome" to "Ana")
mapaMutavel["idade"] = 25
println(mapaMutavel) // Saída: {nome=Ana, idade=25}
```

#### Operações com Map
```kotlin
val mapa = mapOf(1 to "um", 2 to "dois", 3 to "três")
println(mapa.keys) // Obtém as chaves
println(mapa.values) // Obtém os valores
println(mapa.containsKey(2)) // Verifica se a chave existe
```

### Iteração sobre Coleções

#### Iterando com `for`
```kotlin
val lista = listOf("A", "B", "C")
for (item in lista) {
    println(item)
}
```

#### Usando `forEach`
```kotlin
lista.forEach { println(it) }
```

### Filtragem e Transformação

#### Filtragem (`filter`)
```kotlin
val numeros = listOf(1, 2, 3, 4, 5)
val pares = numeros.filter { it % 2 == 0 }
println(pares) // Saída: [2, 4]
```

#### Transformação (`map`)
```kotlin
val dobrado = numeros.map { it * 2 }
println(dobrado) // Saída: [2, 4, 6, 8, 10]
```

---

## Orientação a Objetos em Kotlin

A Orientação a Objetos (OO) é um paradigma de programação que organiza o código em torno de objetos. No Kotlin, podemos trabalhar com classes, herança, encapsulamento, polimorfismo e interfaces de forma simples e intuitiva.

###  Classes e Objetos

Uma classe é um modelo para criar objetos. Em Kotlin, usamos a palavra-chave `class` para definir classes.

### Exemplo de classe e objeto:
```kotlin
class Pessoa(val nome: String, var idade: Int) {
    fun saudacao() {
        println("Olá, meu nome é $nome e tenho $idade anos.")
    }
}

val pessoa = Pessoa("Carlos", 30)
pessoa.saudacao() // Saída: Olá, meu nome é Carlos e tenho 30 anos.
```

###  Construtores

Podemos definir construtores primários diretamente na declaração da classe e construtores secundários dentro do corpo da classe.

#### Construtor primário:
```kotlin
class Carro(val marca: String, val modelo: String)

val carro = Carro("Toyota", "Corolla")
println(carro.marca) // Saída: Toyota
```

#### Construtor secundário:
```kotlin
class Animal {
    var nome: String
    var especie: String
    
    constructor(nome: String, especie: String) {
        this.nome = nome
        this.especie = especie
    }
}

val cachorro = Animal("Rex", "Cachorro")
println(cachorro.nome) // Saída: Rex
```

###  Herança

Kotlin permite herança entre classes usando `open` para permitir que uma classe seja estendida.

```kotlin
open class Animal(val nome: String) {
    open fun fazerSom() {
        println("Som genérico")
    }
}

class Cachorro(nome: String) : Animal(nome) {
    override fun fazerSom() {
        println("Au Au")
    }
}

val cachorro = Cachorro("Bolt")
cachorro.fazerSom() // Saída: Au Au
```

###  Modificadores de Visibilidade

Kotlin oferece quatro modificadores de visibilidade:
- `public` (padrão) – acessível de qualquer lugar.
- `private` – acessível apenas dentro da classe.
- `protected` – acessível dentro da classe e subclasses.
- `internal` – acessível dentro do mesmo módulo.

```kotlin
class ContaBancaria(private val saldo: Double) {
    fun exibirSaldo() {
        println("Saldo: $saldo")
    }
}

val conta = ContaBancaria(1000.0)
conta.exibirSaldo() // Saída: Saldo: 1000.0
```

###  Classes Abstratas

Classes abstratas não podem ser instanciadas diretamente e servem como modelo para subclasses.

```kotlin
abstract class SerVivo(val nome: String) {
    abstract fun mover()
}

class Peixe(nome: String) : SerVivo(nome) {
    override fun mover() {
        println("O peixe está nadando")
    }
}

val peixe = Peixe("Nemo")
peixe.mover() // Saída: O peixe está nadando
```

###  Interfaces

Interfaces definem comportamentos que podem ser implementados por várias classes.

```kotlin
interface Nadador {
    fun nadar() {
        println("Estou nadando!")
    }
}

class Golfinho : Nadador

val golfinho = Golfinho()
golfinho.nadar() // Saída: Estou nadando!
```

### Data Classes

As `data class` são usadas para armazenar dados de forma eficiente, gerando automaticamente métodos como `toString()`, `equals()`, e `copy()`.

```kotlin
data class Usuario(val nome: String, val idade: Int)

val usuario1 = Usuario("Ana", 25)
val usuario2 = usuario1.copy(idade = 30)

println(usuario1) // Saída: Usuario(nome=Ana, idade=25)
println(usuario2) // Saída: Usuario(nome=Ana, idade=30)
```

---

## Exercícios Práticos com Loops e Arrays

A seguir, uma lista de exercícios progressivos envolvendo loops e arrays em Kotlin.

### Exercício 1:
Crie um programa que imprima os números de 1 a 10 usando um loop `for`.

### Exercício 2:
Crie um programa que imprima todos os números pares de 1 a 20 usando um loop `for`.


### Exercício 3:
Crie um programa que calcule a soma dos números de 1 a 100 usando um loop `while`.


### Exercício 4:
Dado um array de inteiros, encontre o maior número presente no array.


### Exercício 5:
Inverta os elementos de um array sem usar funções prontas.

### Exercício 6:
Crie um programa que conte quantas vezes uma string aparece em outra.

### Exercício 7:
Verifique se um array está ordenado de forma crescente. 
Se não estiver, informe o usuário e ordene o array.

### Exercício 8:
Gere os 30 primeiros números da sequência de Fibonacci e armazene-os em uma lista.
Imprima apenas os valores pares.

### Exercício 9:
Crie uma classe Livro com os atributos título, autor e ano. 
Crie uma lista de livros. 
Filtre e imprima apenas os livros publicados após o ano 2000.


### Exercício 10:
Implemente uma classe Biblioteca que gerencie uma coleção de livros. A classe deve ter métodos para adicionar livros, remover livros por título e listar todos os livros ordenados por ano de publicação.


## Referências

- [Documentação oficial do Kotlin](https://kotlinlang.org/docs/home.html)
- [Guia para desenvolvimento Android com Kotlin](https://developer.android.com/kotlin)
- [Kotlinlang - Introdução à Linguagem](https://kotlinlang.org/docs/getting-started.html)
- [JetBrains Kotlin Playground](https://play.kotlinlang.org/)
- [Repositório oficial do Kotlin no GitHub](https://github.com/JetBrains/kotlin)
