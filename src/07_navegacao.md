# 07 - Introdução à navegação no Jetpack Compose

Navegação é o processo de mover o usuário entre diferentes telas (ou funcionalidades) do App.

Com Jetpack Compose, isso é feito através de componentes baseados na ideia de UI declarativa. Ou seja, ainda utilizaremos `Composables`.

A navegação com Jetpack Compose tem alguns pontos interessantes:
- Facilita a organização, controle e o fluxo entre telas em apps criados com Compose.
- Reduz acoplamento e gerencia automaticamente a pilha de telas (*back stack*).

## Conceitos Básicos

- **`NavHost`**: Contêiner que exibe as telas (destinos) conforme o fluxo de navegação.
- **`NavController`**: Controlador responsável por gerenciar comandos de navegação (ir para próxima tela, voltar, etc).
- **`NavGraph`**: Também chamado de *Composable Destinations*, define as trocas de telas possíveis na aplicação.

> [!IMPORTANT]
> Cada tela navegável é uma função @Composable.

Abordaremos cada um desses conceitos a seguir. Mas antes, é necessário configurar o projeto.

### Configurando a Navegação

Basta adicionarmos uma dependência ao projeto para que as funções de navegação estejam disponíveis.

```kotlin
dependencies {
    val nav_version = "2.8.9"

    implementation("androidx.navigation:navigation-compose:$nav_version")
}
```

### Uma aplicação simples com navegação

Link para o repositório com o código completo:
<https://github.com/tads-ufpr-alexkutzke/ds151-aula-07-movies-app/tree/main>

A aplicação proposta é bastante simples e terá apenas duas telas.

Em primeiro lugar, criaremos dois Composables para representar as telas da aplicação.

```kotlin
@Composable
fun MoviesScreen(onGoToMovieDetailsClick: () -> Unit = {}){
    Column(
        modifier = Modifier
            .fillMaxSize(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text(text="Tela de Filmes")
        ElevatedButton(
            onClick = onGoToMovieDetailsClick
        ) {
            Text("Vai para tela de detalhes")
        }
    }
}

@Preview
@Composable
fun MoviesScreenPreview() {
    MoviesAppTheme{
        MoviesScreen()
    }
}

@Composable
fun MovieDetailsScreen(){
    Column(
        modifier = Modifier
            .fillMaxSize(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text(text= "Tela de detalhes")
    }
}

@Preview
@Composable
fun MovieDetailsPreview(){
    MoviesAppTheme{
        MovieDetailsScreen()
    }
}
```
Os composables `MoviesScreen` e `MovieDetailsScreen` não tem nada de diferente dos composables vistos nas últimas aulas.

#### O componente MoviesApp

Agora criaremos um componente que irá representar a aplicação como um todo e será responsável por abrigar todos os dados referentes à navegação.

A criação desse componente não é obrigatória, mas é um padrão comum de desenvolvimento com o Jetpack Compose.

```kotlin
@Composable
fun MoviesApp(
    navController: NavHostController = rememberNavController()
){
    NavHost(
        navController = navController,
        startDestination = "movies",
    ){
        composable("movies"){
            MoviesScreen()
        }
        composable("movieDetails"){
            MovieDetailsScreen()
        }
    }

}

@Preview
@Composable
fun MoviesAppPreview(){
   MoviesAppTheme{}
       MoviesApp()
   }
}
```

No código acima, já temos os 3 componentes da navegação: 

- `NavHost`: define um componente de navegação:
  - `navController`: variável vem de `rememberNavController()`;
  - `startDestination`: define a tela inicial para a navegação;
- `composable` ou `NavGraph`: cada `composable` define uma **rota**, ou tela, possível para a navegação:
  - Uma rota pode ser entendida como uma url;
  - Existem várias formas de nomear uma rota: a mais simples é apenas com uma string contendo um nome único;
  - A seguir veremos outras formas;

No exemplo, temos duas rotas: `"movies"` e `"movieDetails"`. Cada uma renderiza uma das telas criadas.

Já podemos atualizar a `MainActivity` para que utilize nosso novo componente;

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            MoviesAppTheme{}
                MoviesApp()
            }
        }
    }
}

@Composable
fun MoviesApp(
    navController: NavHostController = rememberNavController(),
){
    Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
        NavHost(
            modifier = Modifier.padding(innerPadding),
            navController = navController,
            startDestination = "movies",
        ) {
            composable("movies") {
                MoviesScreen()
            }
            composable("movieDetails") {
                MovieDetailsScreen()
            }
        }
    }
}
```

No exemplo, já aproveitamos para passar o `Scaffold` também para dentro do componente `MoviesApp`.

#### Navegando para a segunda tela

Para trocarmos de tela, é necessário utilizarmos o método `navigate` do componente `navController`.

A tela `MoviesScreen` já possui um evento para definir o comportamento do botão para trocar de tela.

Portanto, basta atribuir esse evento:

```kotlin
            composable("movies") {
                MoviesScreen(
                    onGoToMovieDetailsClick = {
                        navController.navigate("movieDetails")
                    })
            }
```

Dessa forma, o botão já realizar a troca de telas.

Um ponto interessante, é que o `NavHost` já irá cuidar de toda a pilha de telas automaticamente. Por exemplo, ao rodar a aplicação no emulador, o botão de `Voltar` da interface do smartphone já é capaz de retornar à tela anterior.

Porém, também é possível adicionarmos um botão na tela `movieDetails` para realizarmos a volta para a tela anterior.

> [!IMPORTANT]
> É importante que a lógica de navegação não seja compartilhada entre os componentes. Ou seja, os componentes filhos (telas e outros) devem apenas utilizar funções definidas no escopo do `NavHost`. Por exemplo, não é uma boa prática, passar a variável `navController` como parâmetro para outros componentes.

Primeiro, adicionamos o botão:

```kotlin
@Composable
fun MovieDetailsScreen(
    onGoBackClick: () -> Unit = {}
){
    Column(
        modifier = Modifier
            .fillMaxSize(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally,
    ) {
        Text(text= "Tela de detalhes")
        ElevatedButton(
            onClick = onGoBackClick
        ) {
            Text("Voltar")
        }
    }
}
```

Agora, basta atribuir o evento corretamente no componente `MoviesApp`:

```kotlin
            composable("movieDetails") {
                MovieDetailsScreen(
                    onGoBackClick = {
                        navController.navigate("movies")
                    }
                )
            }
```
#### Passando argumentos para telas

Ao trocarmos de tela, por vezes, é interessante que argumentos sejam passados. Por exemplo, ao trocar para a tela de detalhes de um filme, talvez seja interessante informarmos o `id` do filme a ser mostrado na tela seguinte.

Para realizar a passagem desses argumentos, pode alterar um pouco a rota:

```kotlin
            composable("movies") {
                MoviesScreen(
                    onGoToMovieDetailsClick = {
                        navController.navigate("movieDetails/10")
                    })
            }
            composable("movieDetails/{movieId}") { backStackEntry ->
                val movieId:String = backStackEntry.arguments?.getString("movieId") ?: ""
                MovieDetailsScreen(
                    movieId = movieId,
                    onGoBackClick = {
                        navController.navigate("movies")
                    }
                )
            }

// ...

            @Composable
fun MovieDetailsScreen(
    movieId: String = "",
    onGoBackClick: () -> Unit = {}
){

// ...

        Text(text= "Tela de detalhes")
        Text(text= "MovieId: $movieId")
// ...
}
```

É preciso prestar um pouco de atenção aos tipos de variáveis. Como um determinado argumento pode ser nulo, a variável que o recebe deve ser `nullable`, ou uma checagem deve ser realizada antes (operador `?:`).

##### Definindo tipos de argumentos

É possível, ainda, definir mais detalhes sobre os argumentos passados. Para isso, utilizaremos o parâmetro `argumentos` e o componente `navArgument`:

```kotlin
            composable(
                route="movieDetails/{movieId}",
                arguments = listOf(
                    navArgument("movieId") {
                        defaultValue = 0
                        type = NavType.IntType
                    }
                )
            ) { backStackEntry ->
                val movieId:Int? = backStackEntry.arguments?.getInt("movieId")

                movieId?.let{
                    MovieDetailsScreen(
                        movieId = it,
                        onGoBackClick = {
                            navController.navigate("movies")
                        }
                    )

                }
            }
}
// ...

@Composable
fun MovieDetailsScreen(
    movieId: Int = -1,
    onGoBackClick: () -> Unit = {}
){

// ...

        Text(text= "Tela de detalhes")
        Text(text= "MovieId: $movieId")

// ...
}
```

#### Outras formas de retornar na navegação


### Navegação aninhada
Referência: <https://medium.com/@KaushalVasava/navigation-in-jetpack-compose-full-guide-beginner-to-advanced-950c1133740>

Destinações podem ser agrupadas em um grafo mais complexo, ou aninhado:

![](https://miro.medium.com/v2/resize:fit:640/format:webp/1*Xhpe8-nz8GhH0CylOhUpCw.png)

Para isso, agrupe rotas relacionadas em um componente `navigation`:
```kotlin
NavHost(navController, startDestination = "home") {
    ...
    // Navigating to the graph via its route ('login') automatically
    // navigates to the graph's start destination - 'username'
    // therefore encapsulating the graph's internal routing logic
    navigation(startDestination = "username", route = "login") {
        composable("username") { ... }
        composable("password") { ... }
        composable("registration") { ... }
    }
    ...
}
```
