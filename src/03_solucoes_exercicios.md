# Exercícios Práticos com Loops e Arrays (Respostas)

A seguir, uma lista de exercícios progressivos envolvendo loops e arrays em Kotlin.

### Exercício 1:
Crie um programa que imprima os números de 1 a 10 usando um loop `for`.

**Resposta:**
```kotlin
for (i in 1..10) {
    println(i)
}
```

### Exercício 2:
Crie um programa que imprima todos os números pares de 1 a 20 usando um loop `for`.

**Resposta:**
```kotlin
for (i in 1..20) {
    if (i % 2 == 0) {
        println(i)
    }
}
```

### Exercício 3:
Crie um programa que calcule a soma dos números de 1 a 100 usando um loop `while`.

**Resposta:**
```kotlin
var soma = 0
var i = 1
while (i <= 100) {
    soma += i
    i++
}
println("Soma: $soma")
```

### Exercício 4:
Dado um array de inteiros, encontre o maior número presente no array.

**Resposta:**
```kotlin
val numeros = arrayOf(3, 7, 2, 9, 5)
var maior = numeros[0]
for (num in numeros) {
    if (num > maior) {
        maior = num
    }
}
println("Maior número: $maior")
```

### Exercício 5:
Inverta os elementos de um array sem usar funções prontas.

**Resposta:**
```kotlin
val array = arrayOf(1, 2, 3, 4, 5)
val tamanho = array.size
for (i in 0 until tamanho / 2) {
    val temp = array[i]
    array[i] = array[tamanho - 1 - i]
    array[tamanho - 1 - i] = temp
}
println(array.joinToString())
```

### Exercício 6:
Crie um programa que conte quantas vezes uma string aparece em outra.

**Resposta:**
```kotlin
fun main() {
    val texto = "Kotlin é uma linguagem incrível. Kotlin é simples e poderosa. Eu amo Kotlin!"
    val substring = "Kotlin"
    
    val count = contarOcorrencias(texto, substring)
    println("A string '$substring' aparece $count vezes no texto.")
}

fun contarOcorrencias(texto: String, palavra: String): Int {
    var count = 0
    var index = texto.indexOf(palavra)

    while (index != -1) {
        count++
        index = texto.indexOf(palavra, index + palavra.length)
    }
    
    return count
}
```

### Exercício 7:
Verifique se um array está ordenado de forma crescente.
Se não estiver, informe o usuário e ordene o array.

**Resposta:**
```kotlin
fun main() {
    val numeros = arrayOf(3, 1, 4, 1, 5, 9, 2)  // Exemplo de array não ordenado

    if (isOrdenadoCrescente(numeros)) {
        println("O array já está ordenado de forma crescente.")
    } else {
        println("O array não está ordenado. Ordenando...")
        val numerosOrdenados = numeros.sortedArray()
        println("Array ordenado: ${numerosOrdenados.joinToString(", ")}")
    }
}

fun isOrdenadoCrescente(array: Array<Int>): Boolean {
    for (i in 0 until array.lastIndex) {
        if (array[i] > array[i + 1]) {
            return false
        }
    }
    return true
}
```

### Exercício 8:
Gere os 30 primeiros números da sequência de Fibonacci e armazene-os em uma lista.
Imprima apenas os valores pares.

**Resposta:**
```kotlin
fun main() {
    val fibonacci = mutableListOf<Int>(0, 1)  // Iniciando a sequência com os dois primeiros números padrão
    val limit = 30

    // Gerar os 100 primeiros números da sequência de Fibonacci
    for (i in 2 until limit) {
        val nextValue = fibonacci[i - 1] + fibonacci[i - 2]
        fibonacci.add(nextValue)
    }

    // Filtrar e imprimir somente os números pares
    println("Valores pares da sequência de Fibonacci:")
    fibonacci.filter { it % 2 == 0 }.forEach { println(it) }
}
```

### Exercício 9:
Crie uma classe Livro com os atributos título, autor e ano. 
Crie uma lista de livros. 
Filtre e imprima apenas os livros publicados após o ano 2000.

**Resposta:**

```kotlin
data class Livro(val titulo: String, val autor: String, val ano: Int)

fun main() {
    val livros = listOf(
        Livro("2001: A Space Odyssey", "Arthur C. Clarke", 1968),
        Livro("The Road", "Cormac McCarthy", 2006),
        Livro("The Circle", "Dave Eggers", 2013)
    )
    val recentes = livros.filter { it.ano > 2000 }
    for (livro in recentes) {
        println("Título: ${livro.titulo}, Autor: ${livro.autor}, Ano: ${livro.ano}")
    }
}
```

### Exercício 10:
Implemente uma classe Biblioteca que gerencie uma coleção de livros. A classe deve ter métodos para adicionar livros, remover livros por título e listar todos os livros ordenados por ano de publicação.

**Resposta:**
```kotlin
data class Livro(val titulo: String, val autor: String, val ano: Int)

class Biblioteca {
    private val livros = mutableListOf<Livro>()

    fun adicionarLivro(livro: Livro) {
        livros.add(livro)
    }

    fun removerLivro(titulo: String) {
        livros.removeIf { it.titulo == titulo }
    }

    fun listarLivros() {
        livros.sortedBy { it.ano }.forEach { 
            println("${it.titulo}, ${it.autor}, ${it.ano}")
        }
    }
}

fun main() {
    val biblioteca = Biblioteca()
    biblioteca.adicionarLivro(Livro("The Road", "Cormac McCarthy", 2006))
    biblioteca.adicionarLivro(Livro("The Circle", "Dave Eggers", 2013))
    biblioteca.adicionarLivro(Livro("Dune", "Frank Herbert", 1965))

    biblioteca.listarLivros()
}

```
