# 03_kotlin.md

Conteúdo:
Sintaxe básica: variáveis, funções, controle de fluxo
Conceitos avançados: classes, null safety e extension functions
Atividade:
Exercícios práticos de programação em Kotlin

# Aula: Introdução ao Kotlin para Desenvolvimento Android

## Objetivo

Apresentar os principais conceitos da linguagem Kotlin com foco no desenvolvimento Android, incluindo exemplos práticos e exercícios para fixar o aprendizado.

---

## 1. Introdução ao Kotlin

Kotlin é uma linguagem moderna e concisa, totalmente interoperável com Java, e é a linguagem oficial para desenvolvimento Android.

### Vantagens do Kotlin:

- Sintaxe concisa e expressiva
- Segurança contra NullPointerExceptions
- Suporte a programação funcional
- Interoperabilidade com Java
- Coroutines para programação assíncrona

**Exemplo 1: Olá, Mundo!**

```kotlin
fun main() {
    println("Olá, Mundo!")
}
```

A função `main` é o ponto de entrada do programa Kotlin. A palavra-chave `fun` indica a definição de uma função, `main` é o nome da função, e os parênteses `()` indicam que ela não recebe argumentos. O corpo da função é delimitado por `{}`.

---

## 2. Variáveis e Tipos de Dados

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

## 3. Controle de Fluxo

### Estruturas Condicionais

O `if` em Kotlin funciona de maneira semelhante a outras linguagens, podendo também ser usado como expressão.

**Exemplo 3: If/Else**

```kotlin
val idade = 18
if (idade >= 18) {
    println("Maior de idade")
} else {
    println("Menor de idade")
}
```

### When (equivalente ao switch do Java)

O `when` substitui o `switch` de forma mais concisa.

**Exemplo 4: Usando When**

```kotlin
val dia = 3
val nomeDia = when(dia) {
    1 -> "Domingo"
    2 -> "Segunda"
    3 -> "Terça"
    else -> "Outro dia"
}
println(nomeDia)
```

---

## 4. Funções

Uma função em Kotlin é declarada com a palavra-chave `fun`, seguida pelo nome, argumentos (com seus tipos) e, opcionalmente, o tipo de retorno.

**Exemplo 5: Criando uma função**

```kotlin
fun saudacao(nome: String): Unit {
    println("Olá, $nome!")
}
saudacao("Carlos")
```

O tipo `Unit` é opcional e indica que a função não retorna nenhum valor significativo (equivalente ao `void` em Java).

Para funções que retornam um valor, especificamos o tipo de retorno após os parâmetros:

```kotlin
fun soma(a: Int, b: Int): Int {
    return a + b
}
println(soma(3, 5))
```

---

## 5. Coleções (Listas e Conjuntos)

Kotlin oferece coleções imutáveis (`listOf`, `setOf`) e mutáveis (`mutableListOf`, `mutableSetOf`).

**Exemplo 6: Trabalhando com Listas**

```kotlin
val lista = listOf("Kotlin", "Java", "Swift")
println(lista[0]) // Kotlin
```

---

## 6. Mapas (Dicionários)

Os mapas armazenam pares chave-valor e podem ser imutáveis (`mapOf`) ou mutáveis (`mutableMapOf`).

**Exemplo 7: Trabalhando com Mapas**

```kotlin
val mapa = mapOf("BR" to "Brasil", "US" to "Estados Unidos")
println(mapa["BR"]) // Brasil
```

---

## 7. Orientação a Objetos em Kotlin

Kotlin suporta classes, herança e interfaces de maneira concisa.

**Exemplo 8: Criando uma classe**

```kotlin
class Pessoa(val nome: String, val idade: Int) {
    fun apresentar() {
        println("Meu nome é $nome e tenho $idade anos.")
    }
}
val pessoa = Pessoa("Alice", 30)
pessoa.apresentar()
```

A palavra-chave `class` define uma classe. Os atributos podem ser declarados diretamente no construtor primário. Métodos são funções dentro da classe.

---

## 8. Exercícios Práticos com Loops e Arrays

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
Crie um programa que conte quantas vezes um número específico aparece em um array.

**Resposta:**
```kotlin
val numeros = arrayOf(1, 2, 3, 2, 4, 2, 5)
val alvo = 2
var contador = 0
for (num in numeros) {
    if (num == alvo) {
        contador++
    }
}
println("O número $alvo aparece $contador vezes")
```

### Exercício 7:
Verifique se um array está ordenado de forma crescente.

**Resposta:**
```kotlin
val numeros = arrayOf(1, 2, 3, 4, 5)
var ordenado = true
for (i in 0 until numeros.size - 1) {
    if (numeros[i] > numeros[i + 1]) {
        ordenado = false
        break
    }
}
println("O array está ordenado? $ordenado")
```

### Exercício 8:
Gere os 10 primeiros números da sequência de Fibonacci.

**Resposta:**
```kotlin
val fibonacci = mutableListOf(0, 1)
for (i in 2 until 10) {
    fibonacci.add(fibonacci[i - 1] + fibonacci[i - 2])
}
println(fibonacci.joinToString())
```

### Exercício 9:
Crie um programa que remova todos os elementos duplicados de um array.

**Resposta:**
```kotlin
val numeros = arrayOf(1, 2, 2, 3, 4, 4, 5)
val semDuplicatas = numeros.toSet().toList()
println(semDuplicatas.joinToString())
```

### Exercício 10:
Rotacione os elementos de um array para a direita em uma posição.

**Resposta:**
```kotlin
val array = arrayOf(1, 2, 3, 4, 5)
val ultimo = array.last()
for (i in array.size - 1 downTo 1) {
    array[i] = array[i - 1]
}
array[0] = ultimo
println(array.joinToString())
```

Esses exercícios ajudam a reforçar os conceitos de loops e manipulação de arrays em Kotlin. Pratique e experimente modificar os códigos para entender melhor como funcionam! 🚀


## Referências

- Documentação oficial do Kotlin
- Guia para desenvolvimento Android com Kotlin
- Kotlinlang - Introdução à Linguagem
- JetBrains Kotlin Playground
- Repositório oficial do Kotlin no GitHub
