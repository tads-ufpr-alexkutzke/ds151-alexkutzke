# 07 - Integração com APIs e Consumo de dados

Nessa aula veremos como realizar requisições HTTP em uma aplicação Android com Compose.

Para isso, utilizaremos algumas bibliotecas auxiliares e trabalharemos com o conceito de `Coroutines` do Kotlin.

O material abaixo é baseado na criação de um aplicativo que acessa a API <https://moviesapi.kutzke.com.br/movies>.
Essa API simples lista os 40 filmes mais bem avaliados do IMDB.

A versão final da aplicação pode ser acessada no seguinte repositório:<br>
<https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/>

A seguir, observaremos as alterações realizadas commit a commit para compreender o funcionamento das requisições HTTP e outros recursos.

## 1. Commit: *first commit - basic app structure* - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/commit/290f5aad2a7d692ae0d899127a70e2a1b24af4dc'>Diffs 290f5aad</a>

[Repositório inicial](https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/tree/290f5aad2a7d692ae0d899127a70e2a1b24af4dc/app/src/main/java/com/example/moviesapp).

---

## 2. Commit: *api first version* - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/commit/1f445d0395cc4d2260a180dbceb4ce0a792f71e2'>Diffs 1f445d03</a>

O primeiro passo é adicionar as dependências necessárias (Navigation, ViewModel, Retrofit, okHttp):

<details>
<summary><code>build.gradle.kts</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F1f445d0395cc4d2260a180dbceb4ce0a792f71e2%2Fapp%2Fbuild.gradle.kts%23L53-64&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>
</details>


É necessários também, informar ao Android que nosso aplicativo precisa de permissão para acessar a internet. Para isso, adicione o seguinte ao `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```


Além disso, caso precise acessar uma API por HTTP, e não HTTPs, adicione o seguinte parâmetro ao bloco `application` do mesmo arquivo:

```xml
<application
    android:usesCleartextTraffic="true"
```

Vale observar, também, que o Android, por questões de segurança, não permite acesso ao `localhost`. Portanto, para acessar uma API que está na seu próprio computador, utilize o IP da rede local.

<details>
<summary><code>AndroidManifest.xml</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F1f445d0395cc4d2260a180dbceb4ce0a792f71e2%2Fapp%2Fsrc%2Fmain%2FAndroidManifest.xml%23L6-9&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MoviesApiService.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F1f445d0395cc4d2260a180dbceb4ce0a792f71e2%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fnetwork%2FMoviesApiService.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MoviesAppViewModel.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F1f445d0395cc4d2260a180dbceb4ce0a792f71e2%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesAppViewModel.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

### Sobre Coroutines

Coroutines (ou corrotinas) em Kotlin são uma utilizados para a programação de código assíncrono e concorrente de forma mais simples e eficiente. Elas permitem que realizar operações potencialmente demoradas — como acessar a internet ou ler arquivos — sem bloquear a thread principal (geralmente a de interface do usuário), tornando aplicativos mais responsivos. 

Uma coroutine é, basicamente, uma sequência de instruções que pode ser suspensa e retomada posteriormente, permitindo que outras tarefas sejam executadas enquanto ela aguarda alguma operação, como uma resposta de rede ou leitura de dados. Isso é feito de simples e estruturada, sem a complexidade tradicional de manipular múltiplas threads diretamente.

Kotlin oferece bibliotecas robustas para o uso de coroutines, com funções especiais como `suspend`, `launch`, `async` e `delay` que tornam o gerenciamento de tarefas assíncronas muito mais legível. Ao invés de callbacks encadeados ou código difícil de manter, o uso de coroutines proporciona uma sintaxe sequencial que é mais fácil de entender e depurar. Além disso, as coroutines incorporam o conceito de **concorrência estruturada**, promovendo o controle do ciclo de vida das tarefas assíncronas para evitar vazamentos de memória e garantir que todas as operações iniciadas sejam corretamente finalizadas ou canceladas quando necessário.

Considere o código a seguir:

```kotlin
import kotlinx.coroutines.*

fun main() {
    runBlocking {
        println("Weather forecast")
        delay(1000)
        println("Sunny")
    }
}
``` 

- `runBlocking()` executa um loop de eventos, que pode lidar com várias tarefas ao mesmo tempo, continuando cada tarefa de onde parou quando ela está pronta para ser retomada.

- `delay()` é na verdade uma função especial de suspensão fornecida pela biblioteca de coroutines do Kotlin.

```kotlin
import kotlinx.coroutines.*

fun main() {
    runBlocking {
        println("Weather forecast")
        printForecast()
    }
}

suspend fun printForecast() {
    delay(1000)
    println("Sunny")
}
```

- Uma função suspensa é como uma função normal, mas pode ser suspensa e retomada novamente mais tarde.
- Uma função suspensa só pode ser chamada a partir de uma coroutine ou de outra função suspensa.
- Um ponto de suspensão é o local dentro da função onde a execução pode ser suspensa.

- O \"co-\" em coroutine significa cooperativo. O código coopera para compartilhar o loop de eventos subjacente ao suspender a execução enquanto espera por algo, o que permite que outros trabalhos sejam executados nesse meio tempo.

```kotlin
import kotlinx.coroutines.*

fun main() {
    runBlocking {
        println("Weather forecast")
        launch {
            printForecast()
        }
        launch {
            printTemperature()
        }
    }
}

suspend fun printForecast() {
    delay(1000)
    println("Sunny")
}

suspend fun printTemperature() {
    delay(1000)
    println("30\u00b0C")
}
```

- A função `launch()` da biblioteca de coroutines inicia uma nova coroutine.
- Coroutines em Kotlin seguem um conceito chave chamado [concorrência estruturada](https://kotlinlang.org/docs/coroutines-basics.html#structured-concurrency), onde o seu **código é sequencial por padrão** e coopera com um loop de eventos subjacente, a menos que você peça explicitamente para executar concorrentemente (por exemplo, usando `launch()`).

```kotlin
import kotlinx.coroutines.*

fun main() {
    runBlocking {
        println("Weather forecast")
        val forecast: Deferred<String> = async {
            getForecast()
        }
        val temperature: Deferred<String> = async {
            getTemperature()
        }
        println("${forecast.await()} ${temperature.await()}")
        println("Have a good day!")
    }
}

suspend fun getForecast(): String {
    delay(1000)
    return "Sunny"
}

suspend fun getTemperature(): String {
    delay(1000)
    return "30\u00b0C"
}
```

- A função `async()` é utilizada quando é importante determinarmos o momento em que a coroutine termina e precisa de um valor de retorno dela.
- A função `async()` retorna um objeto do tipo `Deferred`, que funciona como uma promessa de que o resultado estará disponível quando estiver pronto. Você pode acessar o resultado no objeto `Deferred` usando `await()`.

```kotlin
suspend fun getWeatherReport() = coroutineScope {
    val forecast = async { getForecast() }
    val temperature = async { getTemperature() }
    "${forecast.await()} ${temperature.await()}"
}
```

- `coroutineScope{}` cria um escopo local.
- As coroutines iniciadas dentro deste escopo são agrupadas juntas dentro deste escopo, o que tem implicações para **cancelamento e exceções**.
- `coroutineScope()` só retornará quando todo o seu trabalho, incluindo quaisquer coroutines iniciadas, for concluído.
- Com `coroutineScope()`, mesmo que a função execute internamente trabalhos de forma concorrente, para quem chama parece uma operação síncrona, porque `coroutineScope` não retorna até todo o trabalho estar completo.

- `launch()` e `async()` são [funções de extensão](https://kotlinlang.org/docs/extensions.html) em `CoroutineScope`. Chame `launch()` ou `async()` no escopo para criar uma nova coroutine dentro desse escopo.

- Um `CoroutineScope` está atrelado a um ciclo de vida, que define quanto tempo as coroutines dentro desse escopo viverão. Se um escopo for cancelado, seu job também é cancelado, propagando o cancelamento para seus filhos. Se um job filho falha com exceção, os outros jobs filhos são cancelados, o job pai é cancelado e a exceção é relançada para o chamador.

- O Android fornece suporte a escopo de corrotines em entidades que têm um ciclo de vida bem definido, como `Activity` (`lifecycleScope`) e `ViewModel` (`viewModelScope`).
- Coroutines iniciadas dentro desses escopos obedecerão ao ciclo de vida da entidade correspondente, como `Activity` ou `ViewModel`.


---

## 3. Commit: *MovieDetails with api request* - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/commit/a08300309e6d51dfabcb3a21f9d4904bb4104066'>Diffs a0830030</a>


<details>
<summary><code>gradle.xml</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2Fa08300309e6d51dfabcb3a21f9d4904bb4104066%2F.idea%2Fgradle.xml&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MoviesApiService.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2Fa08300309e6d51dfabcb3a21f9d4904bb4104066%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fnetwork%2FMoviesApiService.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MoviesApp.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2Fa08300309e6d51dfabcb3a21f9d4904bb4104066%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2FMoviesApp.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MoviesAppViewModel.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2Fa08300309e6d51dfabcb3a21f9d4904bb4104066%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesAppViewModel.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

## 4. Commit *MovieDetailsScreen handles api* - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/commit/42a1de57c25761c754983c2089080247e629958d'>Diffs 42a1de57</a>

<details>
<summary><code>MoviesApp.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F42a1de57c25761c754983c2089080247e629958d%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2FMoviesApp.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MovieDetailsScreen.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F42a1de57c25761c754983c2089080247e629958d%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMovieDetailsScreen.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

## 5. Commit: *Exception handling and UiState* - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/commit/68567bdd7e9e01b2e90816dc95f58d1e1a4f7273'>Diffs 68567bdd</a>

<details>
<summary><code>MoviesApp.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2FMoviesApp.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>ApiComposables.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FApiComposables.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MovieDetailsScreen.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMovieDetailsScreen.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MoviesAppViewModel.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesAppViewModel.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MoviesScreen.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesScreen.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>ic_connection_error.xml</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fres%2Fdrawable%2Fic_connection_error.xml&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>loading_img.xml</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fres%2Fdrawable%2Floading_img.xml&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

