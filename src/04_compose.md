# 04_compose.md

Conteúdo:
Conceito de UI declarativa vs. UI imperativa
Configuração do ambiente para Compose
Criação dos primeiros composables
Atividade:
Desenvolvimento de um “Hello World” utilizando Compose

> [!Note]
>
> - Diferença entre componentes `Surface` e `Scaffold`:
>   - São estruturas fundamentais de layout. Algo como containers;
>   - Porém, `Scaffold` torna mais fácil o tratamento de área cobertas pelo Sistema Operacional (como barras de notificação, notch de câmera, botões de navegação, etc.);
>   - Isso é ainda mais importante após a decisão do Google de que as [aplicações devem usar `enableEdgeToEdge()`](https://developer.android.com/about/versions/15/behavior-changes-15#edge-to-edge).
>     - Isso significa que a aplicação é que deve tomar conta dessas áreas cobertas, ocupando a tela de "borda à borda";
>   - Com o `Surface` esse controle se torna bem mais complexo.

```Kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            BasicsCodelabTheme {
                Scaffold( modifier = Modifier.fillMaxSize() ) { innerPadding ->
                    Greeting(
                        name = "Android",
                        modifier = Modifier.padding(innerPadding)
                    )
                }
            }
        }
    }
}

@Composable
fun Greeting(name: String, modifier: Modifier = Modifier) {
    Text(
        text = "Hello $name!",
        modifier = modifier
    )
}

@Preview(showBackground = true)
@Composable
fun GreetingPreview() {
    BasicsCodelabTheme {
        Greeting("Android")
    }
}
```
Jetpack Compose: A Nova Era do Desenvolvimento de Interfaces Android
Introdução
Jetpack Compose é um toolkit moderno do Android para construir interfaces de usuário (UI) de forma declarativa. Diferente do sistema tradicional baseado em XML, Compose permite descrever a UI como uma função que transforma dados em elementos visuais.
Benefícios do Jetpack Compose:

Código Mais Simples e Conciso: Reduz drasticamente a quantidade de código necessária para criar interfaces.
Reutilização de Componentes: Facilita a criação de componentes reutilizáveis e personalizados.
Atualizações Dinâmicas: Recompõe apenas as partes da UI que precisam ser atualizadas, melhorando a performance.
Compatibilidade com Kotlin: Integra-se perfeitamente com a linguagem Kotlin, aproveitando seus recursos modernos.
Animações Simplificadas: Oferece APIs poderosas para criar animações complexas com facilidade.

Conceitos Fundamentais
1. Funções Componíveis
Em Compose, a UI é construída com funções chamadas funções componíveis. Essas funções são anotadas com @Composable e descrevem um pedaço da UI.
@Composable
fun Greeting(name: String) {
    Text(text = "Olá, $name!")
}

2. Estado (State)
O estado é qualquer dado que pode mudar e influenciar a UI. Em Compose, usamos remember e mutableStateOf para armazenar e observar o estado.
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    Button(onClick = { count++ }) {
        Text(text = "Contador: $count")
    }
}

3. Modificadores (Modifiers)
Modificadores são usados para decorar ou adicionar comportamento aos elementos da UI. Eles são encadeáveis e permitem configurar propriedades como tamanho, padding, cor, etc.
Text(
    text = "Olá, Compose!",
    modifier = Modifier
        .padding(16.dp)
        .background(Color.LightGray)
)

4. Layouts
Compose oferece vários layouts para organizar elementos na tela. Alguns dos mais comuns incluem:



*   Column: Organiza os elementos verticalmente.




Row: Organiza os elementos horizontalmente.
Box: Permite sobrepor elementos.

@Composable
fun MyLayout() {
    Column {
        Text(text = "Elemento 1")
        Text(text = "Elemento 2")
    }
}

Exemplos Práticos
Exemplo 1: Lista Simples
Criar uma lista de itens usando LazyColumn.
@Composable
fun SimpleList() {
    val items = listOf("Item 1", "Item 2", "Item 3")
    LazyColumn {
        items(items) { item ->
            Text(text = item, modifier = Modifier.padding(8.dp))
        }
    }
}

Exemplo 2: Formulário de Login
Implementar um formulário de login com campos de texto e um botão.
@Composable
fun LoginForm() {
    var username by remember { mutableStateOf("") }
    var password by remember { mutableStateOf("") }

    Column(
        modifier = Modifier.padding(16.dp),
        verticalArrangement = Arrangement.spacedBy(8.dp)
    ) {
        TextField(
            value = username,
            onValueChange = { username = it },
            label = { Text("Usuário") }
        )
        TextField(
            value = password,
            onValueChange = { password = it },
            label = { Text("Senha") },
            visualTransformation = PasswordVisualTransformation()
        )
        Button(onClick = { /* Lógica de login aqui */ }) {
            Text("Entrar")
        }
    }
}

Exemplo 3: Exibindo uma Imagem
Mostrar uma imagem a partir de um URL usando AsyncImage.
import coil.compose.AsyncImage

@Composable
fun ShowImage() {
    val imageUrl = "https://example.com/image.jpg"
    AsyncImage(
        model = imageUrl,
        contentDescription = "Imagem Descrição",
        modifier = Modifier.size(200.dp)
    )
}

Para utilizar o AsyncImage, é necessário adicionar a dependência da biblioteca Coil no build.gradle:
dependencies {
    implementation("io.coil-kt:coil-compose:2.5.0")
}

Exercícios
Exercício 1: Criar um Perfil de Usuário
Crie uma tela que exiba informações de um perfil de usuário, como nome, foto e descrição. Utilize Column, Row, Image e Text para estruturar a UI.
Exercício 2: Lista de Tarefas
Implemente uma lista de tarefas onde o usuário pode adicionar novas tarefas e marcá-las como concluídas. Use LazyColumn, TextField, Button e Checkbox.
Exercício 3: Contador com Cores Dinâmicas
Crie um contador que muda de cor a cada incremento. Utilize remember, mutableStateOf, Button e Color.
Exercício 4: Calculadora Simples
Desenvolva uma calculadora simples com botões para os números e operações básicas (+, -, *, /). Utilize Column, Row e Button para organizar os elementos.
Dicas Adicionais

Explore a Documentação: A documentação oficial do Jetpack Compose é uma excelente fonte de informações e exemplos.
Use o Preview: O Android Studio oferece previews em tempo real para visualizar as mudanças na UI sem precisar executar o aplicativo.
Experimente: A melhor forma de aprender é praticar e experimentar com diferentes componentes e layouts.
Comunidade: Participe de fóruns e grupos de discussão para tirar dúvidas e compartilhar conhecimento.

Conclusão
Jetpack Compose representa um avanço significativo no desenvolvimento de interfaces Android, oferecendo uma forma mais eficiente e intuitiva de criar UIs. Com este material, seus alunos terão uma base sólida para começar a explorar e dominar essa tecnologia.

