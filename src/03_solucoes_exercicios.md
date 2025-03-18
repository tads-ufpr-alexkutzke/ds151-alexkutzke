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
