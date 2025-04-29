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

### Configuração do Retrofit

A biblioteca Retrofit irá realizar todo o trabalho de acesso à API e conversão dos dados. Para isso, é comum colocarmos toda configuração de acesso à API (inicialização do Retrofit) em um arquivo separado. Nesse projeto, criamos o arquivo `network/MoviesApiService.kt`.


<details>
<summary><code>MoviesApiService.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F1f445d0395cc4d2260a180dbceb4ce0a792f71e2%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fnetwork%2FMoviesApiService.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

Nesse arquivo, definimos a URL base do servidor que acessaremos. No código acima, ela estava definida para meu IP local, porém, podemos atualizá-la para o servidor da API `movieisapi`:

```kotlin
private const val BASE_URL =
    "https://movieisapi.kutzke.com.br/"

```
O trecho abaixo, mostra a configuração básica do Retrofit. Nele, passamos a URL base e determinamos um `ConverterFactory`. É esse objeto que será responsável por converter a resposta em algum tipo de dado desejado. Nesse caso, `GsonConverterFactory` irá converter o formato JSON entregue pela API em um objeto acessível pelo Kotlin (nesse caso, `List<Movie>`).

```kotlin
private val retrofit = Retrofit.Builder()
    .addConverterFactory(GsonConverterFactory.create())
    .baseUrl(BASE_URL)
    .build()
```


Na sequência, se determina os endpoints acessível pela aplicação. Para isso, definimos uma interface e adicionamos uma função suspensa para cada endpoint, com os devidos marcadores de método (`@GET`) fornecidos pelo Retrofit:

 
```kotlin
interface MoviesApiService {
    @GET("/movies")
    suspend fun getMovies(): List<Movie>
}
```

Por fim, define-se um `object` que será utilizado pelo resto do App para acessar a API. Esse object contem apenas uma variável que é inicializada por `retrofit.create`.

Aqui, o uso de `object` e `lazy` tem uma razão de desempenho. Object irá garantir que apenas um objeto da classe `MoviesApi` será criado em toda aplicação. e o `lazy` determina que `retrofitService` só será inicializado quando for requisitado pela primeira vez. A inicialização do retrofit é uma operação cara, por isso deve ser feita apenas uma vez. 

```kotlin
object MoviesApi {
    val retrofitService: MoviesApiService by lazy {
        retrofit.create(MoviesApiService::class.java)
    }
}
```


### Utilizando MoviesApi no ViewModel

Embora `MoviesApi` estará disponível em qualquer lugar da aplicação, geralmente reservamos seu uso para dentro de um `ViewModel`. Desse modo, para outros componentes da aplicação, o acesso aos dados fica transparente.

<details>
<summary><code>MoviesAppViewModel.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F1f445d0395cc4d2260a180dbceb4ce0a792f71e2%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesAppViewModel.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

O código acima tem alguns pontos interessantes.

Primeiro, a definição do estado, que nesse caso, será uma lista de filmes:

```kotlin
    private var _movies = mutableStateListOf<Movie>()
    val movies: List<Movie>
        get() = _movies
```

Na sequência, o construtor do `ViewModel` faz uso de um `viewModelScope` para realizar as chamadas assíncronas à API:
 
```kotlin
    init {
        if(fake) _movies.addAll(fourMovies)
        else{
            viewModelScope.launch {
                val movies = MoviesApi.retrofitService.getMovies()
                _movies.addAll(movies)
            }
        }
    }
```

É importante salientar que, no exemplo acima, não fazemos nenhum tratamento de erro.

O `viewModelScope.launch` utilizado no construtor é algo novo. Ele determina o início de uma nova *Coroutine*, permitindo a realização de operações assíncronas e paralelas forma simples e segura (do ponto de vista de programação).


### Sobre Coroutines

**Coroutines (ou corrotinas) em Kotlin são uma utilizados para a programação de código assíncrono e concorrente de forma mais simples e eficiente.** Elas permitem que realizar operações potencialmente demoradas — como acessar a internet ou ler arquivos — sem bloquear a thread principal (geralmente a de interface do usuário), tornando aplicativos mais responsivos. 

Uma coroutine é, basicamente, **uma sequência de instruções que pode ser suspensa e retomada posteriormente**, permitindo que outras tarefas sejam executadas enquanto ela aguarda alguma operação, como uma resposta de rede ou leitura de dados. Isso é feito de simples e estruturada, sem a complexidade tradicional de manipular múltiplas threads diretamente.

Kotlin oferece bibliotecas robustas para o uso de coroutines, com **funções especiais como `suspend`, `launch`, `async` e `delay`** que tornam o gerenciamento de tarefas assíncronas muito mais legível. Ao invés de callbacks encadeados ou código difícil de manter, o uso de coroutines proporciona uma sintaxe sequencial que é mais fácil de entender e depurar. Além disso, as coroutines incorporam o conceito de **concorrência estruturada**, promovendo o controle do ciclo de vida das tarefas assíncronas para evitar vazamentos de memória e garantir que todas as operações iniciadas sejam corretamente finalizadas ou canceladas quando necessário.

Considere o código a seguir (exemplos retirados de <https://developer.android.com/codelabs/basic-android-kotlin-compose-coroutines-kotlin-playground>):

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

- O Android fornece suporte a escopo de coroutines em entidades que têm um ciclo de vida bem definido, como `Activity` (`lifecycleScope`) e `ViewModel` (`viewModelScope`).
- Coroutines iniciadas dentro desses escopos obedecerão ao ciclo de vida da entidade correspondente, como `Activity` ou `ViewModel`.

> [!IMPORTANT]
> Aplicações Android geralmente possuem uma "Main thread" que é responsável pela atualização e renderização da tela. Sem o uso de coroutines, como `viewModelScope`, a tela ficaria "travada" até que processamentos assíncronos ou mais longos terminassem.

---

## 3. Commit: *MovieDetails with api request* - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/commit/a08300309e6d51dfabcb3a21f9d4904bb4104066'>Diffs a0830030</a>

Nesse commit, atualizamos a tela `MovieDetailsScreen` para acessar os dados de um filme por meio de um novo endpoint: `/movies/{movieId}`.


### Novo endpoint com parâmetro

Adicionamos as informações para o novo endpoint, com o detalhe de que ele espera um `movieId` como parâmetro:

```kotlin
    @GET("/movies/{id}")
    suspend fun getMovie(@Path("id") id:Int): Movie
 ``` 

<details>
<summary><code>MoviesApiService.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2Fa08300309e6d51dfabcb3a21f9d4904bb4104066%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fnetwork%2FMoviesApiService.kt%23L17-L23&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>
</details>

### Atualização do MoviesAppViewModel

Devemos, também, atualizar o `MoviesAppViewModel` para que ele acesse esse novo endpoint.

Dois detalhes importantes estão nessa atualização.

Em primeiro lugar, definimos um novo estado `movieDetails`, do tipo `StateFlow<Movie?>`. `StateFlow` são uma forma comum de armazenar estados mais complexos como classes. `Flow` é um conceito do kotlin e tem relação com o paradigma *Produtor-Coletor*. Mas, para nós, no momento, será apenas uma forma conveniente de armazenar estados.

```kotlin
private var _movieDetails = MutableStateFlow<Movie?>(null)
val movieDetails: StateFlow<Movie?> = _movieDetails
```


Em segundo lugar, alteramos a função `getMovie`, para que ela utilize o novo endpoint. Aqui adicionamos uma chamada para `delay` apenas para que fique claro que um processamento assíncrono está ocorrendo.

Vale notar a necessidade de utilizar `.value` para acessar o valor do estado quando trabalhamos com `StateFlow`.

```kotlin
fun getMovie(movieId:Int) {
    viewModelScope.launch {
        delay(2000)
        val movie = MoviesApi.retrofitService.getMovie(movieId)
        _movieDetails.value = movie
    }
}
```

Uma pequena alteração foi feita para permitir o uso de Previews, uma vez que o acesso a API só ocorre na execução pelo emulador e dispositivo físico:

```kotlin
init {
    if(fake) _movies.addAll(fourMovies)
    else{
```


<details>
<summary><code>MoviesAppViewModel.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2Fa08300309e6d51dfabcb3a21f9d4904bb4104066%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesAppViewModel.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

### Uso do novo endpoint

Para recuperar o estado do `moviesAppViewModel`, utilizamos o método `collectAsState`, uma vez que estamos utilizando um `StateFlow`.

```kotlin
val movie = moviesAppViewModel.movieDetails.collectAsState()
```

Agora, utilizamos um `LaunchedEffect` para invocar a função `getMovie` do `moviesAppViewModel`.

Um `LaunchedEffect` é um recurso utilizado em Composables para lidar com efeitos colaterais. Se for necessário executar algum código que não segue a mesma linha de execução do composable, devemos separá-lo. Por exemplo, um código assíncrono ou que leva muito tempo, são efeitos colaterais.

Nesse caso, o bloco `LaunchedEffect` será executado de forma segura nos momentos necessários. Esse bloco será reexecutado sempre que um dos seus parâmetros tiver seu valor alterado (no caso, `movieId`). Ou seja, ele não é executado sempre que uma recomposição ocorre.

```kotlin
LaunchedEffect(movieId) {
    moviesAppViewModel.getMovie(movieId)
}
```

Basta, agora, renderizar o composable `MovieDetailsScreen` quando o estado `movie` não for nulo:

```kotlin
if(movie.value == null) Text("Carregando ...")
else{
    movie.value?.let {
        MovieDetailsScreen(
            movie = it,
            onGoBackClick = {
                navController.navigate("movies")
            }
        )
    }
}
```

<details>
<summary><code>MoviesApp.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2Fa08300309e6d51dfabcb3a21f9d4904bb4104066%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2FMoviesApp.kt%23L48-L75&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

## 4. Commit *MovieDetailsScreen handles api* - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/commit/42a1de57c25761c754983c2089080247e629958d'>Diffs 42a1de57</a>

Um detalhe importante da última atualização é o fato de que o componente `MoviesApp` está cuidando da chamada para a API. Isso gera o inconveniente de utilizarmos `LaunchedEffect` dentro de um `composable` do `NavHost`. Esses componentes de navegação devem ser simples e tratar apenas de lógicas de navegação. 

Nesse caso, portanto, podemos passar o tratamento de chamadas API para o componente que necessita dessas informações, `MovieDetailsScreen`.

> [!NOTE]
> Para essa alteração, o componente `MovieDetailsScreen` precisará acessar `moviesAppViewModel`. A decisão de passar `viewModels` para componentes deve ser considerada com cuidado. Ela pode complexificar o código e tornar a testagem mais difícil. Porém, nesse caso, `moviesAppViewModel` será acessado apenas pelo componente `MovieDetailsScreen` e não por seus componentes internos.

### Simplificando ` MoviesApp`

No componente de navegação `MoviesApp` as alterações são simples. Basta passar toda a lógica de API para o componente `MovieDetailsScreen` que receberá, agora o `movieId`, apenas.


```kotlin
val movieId:Int? = backStackEntry.arguments?.getInt("movieId")

if(movieId == null) Text("Carregando ...")
else{
    movieId.let {
        MovieDetailsScreen(
            movieId = it,
            onGoBackClick = {
                navController.popBackStack()
            }
        )
    }
```


<details>
<summary><code>MoviesApp.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F42a1de57c25761c754983c2089080247e629958d%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2FMoviesApp.kt%23L25-L72&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

### Acessando API no componente `MovieDetailsScreen`

Algumas alterações são necessárias para que a tela `MovieDetailsScreen` possa lidar com requisições de API.

Inicialmente, ela precisa ter acesso ao viewModel, uma vez que todas as chamadas para API são controladas por `moviesAppViewModel`. Portanto, adicionamos um parâmetro para a tela que recebe, por padrão o valor de `viewModel()`.

```kotlin
@Composable
fun MovieDetailsScreen(
    movieId: Int,
    moviesAppViewModel: MoviesAppViewModel = viewModel(),
    onGoBackClick: () -> Unit = {},
){
```

O restante do composable é, basicamente, o que estava em `MoviesApp` anteriormente:

```kotlin
@Composable
fun MovieDetailsScreen(

    // ...
    val movie = moviesAppViewModel.movieDetails.collectAsState()

    LaunchedEffect(movieId) {
        moviesAppViewModel.getMovie(movieId)
    }

    if(movie.value == null) Text("Carregando ...")
    movie.value?.let{ movie ->
        MovieDetailsScreen(movie = movie)
    }
}
```

Um outro detalhe interessante é a criação de um outro componente com o mesmo nome, mas com uma assinatura de parâmetros diferente:

```kotlin
@Composable
fun MovieDetailsScreen(
    movie: Movie = fourMovies[0],
    onGoBackClick: () -> Unit = {},
){
    MovieItem(movie = movie)
}
```

Isso permite a criação mais simples de um Preview, uma vez que, como sabemos, não podem realizar requisições HTTP ou operações assíncronas.

```kotlin
@Preview
@Composable
fun MovieDetailsScreenPreview(){
    MoviesAppTheme {
       MovieDetailsScreen(
           movie = fourMovies[0]
       )
    }
}
```

Assim, criamos um preview para a instância de `MovieDetailsScreen` que não necessita da lógica de API.


<details>
<summary><code>MovieDetailsScreen.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F42a1de57c25761c754983c2089080247e629958d%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMovieDetailsScreen.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

## 5. Commit: *Exception handling and UiState* - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/commit/68567bdd7e9e01b2e90816dc95f58d1e1a4f7273'>Diffs 68567bdd</a>

Para finalizar o desenvolvimento dessa aplicação inicial, vamos adicionar tratamento de exceções para que problemas de acesso à API não causem respostas inesperadas ao usuário.

### Adicionando mais estados para tratamento de exceções

Quando realizamos requisições a uma API, podemos interpretar que a interface pode ter três estados principais (cada um com seus dados específicos): sucesso, erro e carregando.

Portanto, podemos alterar os estados armazenados em `MoviesAppViewModel` para que atendam essa nova organização.

Existem muitas formas de fazer isso. Uma delas é definindo uma `sealed interface`:

```kotlin
sealed interface MoviesScreenUiState {
    class Success(val movies: List<Movie>): MoviesScreenUiState
    object Error: MoviesScreenUiState
    object Loading: MoviesScreenUiState
}

sealed interface MovieDetailsScreenUiState {
    class Success(val movie: Movie ) : MovieDetailsScreenUiState
    object Error : MovieDetailsScreenUiState
    object Loading : MovieDetailsScreenUiState
}
```

Aqui, embora estejamos no mesmo viewModel, definimos uma interface para o estado de cada tela. Isso não é sempre necessário, mas, no caso da aplicação aqui apresentada, é uma solução mais simples.

Como os itens `Error` e `Loading` não possuem dados, eles podem ser declarados como `object` e não `class`.

Agora, o `MoviesAppViewModel` pode utilizar essas interfaces para gerar novos estados:

```kotlin
class MoviesAppViewModel(val fake: Boolean = false): ViewModel() {

    var moviesScreenUiState: MoviesScreenUiState by mutableStateOf(MoviesScreenUiState.Loading)
    var movieDetailsScreenUiState: MovieDetailsScreenUiState by mutableStateOf(MovieDetailsScreenUiState.Loading)

```

Inicializamos os estamos para cada tela, por padrão, como `.Loading`.

A lógica de carregamento da lista de filmes pode adicionar um bloco `try` `catch` para o tratamento de qualquer erro no acesso à API:

```kotlin
    init {
        getMovies()
    }

    private fun getMovies(){
        viewModelScope.launch {
            moviesScreenUiState = try {
               val movies = MoviesApi.retrofitService.getMovies()
                MoviesScreenUiState.Success(movies = movies)
            }
            catch(e: IOException){
                MoviesScreenUiState.Error
            }
        }
    }
```

Perceba que, dependendo do caso, atribuímos o valor diferente para `moviesScreenUiState`. É como se os valores `Success`, `Error` e `Loading` fossem passados como informação adicional ao objeto do estado.

A função `getMovie` tem um comportamento muito parecido:

```kotlin

    fun getMovie(movieId:Int) {
        movieDetailsScreenUiState = MovieDetailsScreenUiState.Loading
        viewModelScope.launch {
            movieDetailsScreenUiState = try{
                delay(2000)
                val movie = MoviesApi.retrofitService.getMovie(movieId)
                MovieDetailsScreenUiState.Success(movie = movie)
            }
            catch(e: IOException) {
                MovieDetailsScreenUiState.Error
            }
        }
    }
```

Um detalhe especial é que, a cada chamada de `getMovie` reinicializamos o estado da tela com `.Loading` para que um novo carregamento seja representado ao usuário.

<details>
<summary><code>MoviesAppViewModel.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesAppViewModel.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>


### Utilizando o novo estado nas telas

Os componentes de tela, `MoviesScreen` e `MovieDetailsScreen` precisam, apenas, utilizar esses novos estados.

Para simplificar, optamos a passagem desses estados como parâmetro para cada tela.

O desenho da tela pode ser realizado com um bloco `when`, para cada tipo possível de valor de `MoviesScreenUiState`:
```kotlin

fun MoviesScreen(
    moviesScreenUiState: MoviesScreenUiState,
    onGoToMovieDetailsClick: (movieId:Int) -> Unit = {},
){
    when(moviesScreenUiState){
        is MoviesScreenUiState.Success -> {
            MoviesList(
                movies = moviesScreenUiState.movies,
                onMovieClick = onGoToMovieDetailsClick
            )
        }
        is MoviesScreenUiState.Loading -> LoadingScreen(modifier = Modifier.fillMaxSize())
        is MoviesScreenUiState.Error -> ErrorScreen( modifier = Modifier.fillMaxSize())
    }
}
```

Para a tela `MovieDetailsScreen` as alterações são bastante semelhantes:

```kotlin
@Composable
fun MovieDetailsScreen(
    movieId: Int,
    moviesAppViewModel: MoviesAppViewModel = viewModel(),
    movieDetailsScreenUiState: MovieDetailsScreenUiState,
    onGoBackClick: () -> Unit = {},
){
    when(movieDetailsScreenUiState){
        is MovieDetailsScreenUiState.Success -> {
            MovieDetailsScreen(movie = movieDetailsScreenUiState.movie)
        }
        is MovieDetailsScreenUiState.Loading -> LoadingScreen(modifier = Modifier.fillMaxSize())
        is MovieDetailsScreenUiState.Error -> ErrorScreen( modifier = Modifier.fillMaxSize())
    }

    LaunchedEffect(movieId) {
        moviesAppViewModel.getMovie(movieId)
    }
}
```

Aqui, ainda precisamos do acesso ao `moviesAppViewModel` pois é necessário realizar a chamada à `getMovie`.

<details>
<summary><code>MoviesScreen.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesScreen.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

<details>
<summary><code>MovieDetailsScreen.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMovieDetailsScreen.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

### Ajustes em `MoviesApp`

Por fim, alguns pequenos ajustes são necessários no componente de navegação `MoviesApp`.

```kotlin
composable("movies") {
    MoviesScreen(
        moviesScreenUiState = moviesAppViewModel.moviesScreenUiState,
        onGoToMovieDetailsClick = { movieId ->
            navController.navigate("movieDetails/$movieId")
        }
    )
}
```

Adicionamos o parâmetro `moviesScreenUiState` e passamos o valor atual presente em `moviesAppViewModel`.

Para `MovieDetailsScreen`, as alterações são semelhantes:

```kotlin
composable(
    route="movieDetails/{movieId}",
    arguments = listOf(
        navArgument ("movieId") {
            defaultValue = 0
            type = NavType.IntType
        }
    )
) { backStackEntry ->
    val movieId:Int? = backStackEntry.arguments?.getInt("movieId")

    movieId?.let {
        MovieDetailsScreen(
            movieId = it,
            moviesAppViewModel = moviesAppViewModel,
            movieDetailsScreenUiState = moviesAppViewModel.movieDetailsScreenUiState,
            onGoBackClick = {
                navController.popBackStack()
            }
        )
    }
}
```

<details>
<summary><code>MoviesApp.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-08-movies-api-app%2Fblob%2F68567bdd7e9e01b2e90816dc95f58d1e1a4f7273%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2FMoviesApp.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
