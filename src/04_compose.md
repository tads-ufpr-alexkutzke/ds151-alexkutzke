# 04_compose.md

Conteúdo:
Conceito de UI declarativa vs. UI imperativa
Configuração do ambiente para Compose
Criação dos primeiros composables
Atividade:
Desenvolvimento de um “Hello World” utilizando Compose

## Tópicos

1. Explicação sobre UI declarativa
2. Acompanhar exemplo:
  https://developer.android.com/develop/ui/compose/tutorial
3. Propor prática (criar um layout simples e pedir para os alunos tentarem reproduzir)

# Modelo Mental do Jetpack Compose

## 1. Paradigma de Programação Declarativa
- **Modelo Tradicional**: Uso de uma hierarquia de visualização em árvore.
- **Desafios**: Alterações manuais que podem levar a erros e estados ilegais.
- **Abordagem Declarativa**: Simplifica a engenharia de UI ao aplicar mudanças necessárias, evitando complexidade de atualizações manuais.

## 2. Funções Combináveis
- Funções anotadas com `@Composable` para converter dados em UI.
- Funções que não retornam nada, emitindo diretamente o estado desejado da tela.

```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = \"Hello $name\")
}
```

## 3. Recomposição


- **Modelo de UI Imperativa vs Compose**
  - No modelo imperativo, altere o estado de um widget usando setters.
  - No Compose, atualize chamando novamente a função composable com novos dados.
  - Widgets são redesenhados apenas se necessário, graças à recomposição inteligente do Compose.

- **Exemplo de Função Composable**
  - ```kotlin
    @Composable
    fun ClickCounter(clicks: Int, onClick: () -> Unit) {
        Button(onClick = onClick) {
            Text(\"I've been clicked $clicks times\")
        }
    }
    ```
  - Ao clicar no botão, a chamada atualiza `clicks` e a função `Text` é recomposta para mostrar o novo valor.

- **Eficiência na Recomputação**
  - Recomputação inteligente do Compose apenas para componentes que mudaram.
  - Isso economiza recursos computacionais e preserva a duração da bateria.
  - Ignora funções/lambdas sem parâmetros alterados, evitando recalcular desnecessariamente.

- **Cuidados com Efeitos Colaterais**
  - Evite depender de efeitos colaterais em funções composable.
  - Efeitos colaterais perigosos incluem:
    - Alterar propriedades de objetos compartilhados.
    - Atualizar elementos observáveis em ViewModel.
    - Modificar preferências compartilhadas.
  - Recomenda-se executar operações de alto custo em corrotinas em segundo plano.

- **Exemplo: Uso de SharedPreferences em Composable**
  - ```kotlin
    @Composable
    fun SharedPrefsToggle(
        text: String,
        value: Boolean,
        onValueChanged: (Boolean) -> Unit
    ) {
        Row {
            Text(text)
            Checkbox(checked = value, onCheckedChange = onValueChanged)
        }
    }
    ```
  - Atualizações de valor são geridas via callbacks e operações de fundo no ViewModel.

- **Considerações para Uso Eficiente do Compose**
  - **Recomposição Optimizada**:
    - A recomposição ignora funções/lambdas quando possível.
    - Pode ser cancelada se descoberto desnecessário.
  - **Execução e Performance**:
    - Funções composable podem ser executadas em todos os frames de uma animação.
    - São executadas em paralelo e podem ocorrer em qualquer ordem.

- **Melhores Práticas**
  - Funções composable devem ser rápidas, idempotentes e sem efeitos colaterais para garantir compatibilidade e eficiência na recomposição.

#### Recomposição no Compose: Otimizações

- **Recomposição Seletiva**
  - O Compose limita a recomposição às partes da IU que realmente precisam de atualização.
  - Permite recompor um único elemento (por exemplo, um botão) sem afetar outros elementos na árvore da IU.

- **Funções e Lambdas Composable**
  - Todas as funções e lambdas podem ser recompostas individualmente.
  - Exemplo de como a recomposição pode ignorar elementos não alterados ao renderizar uma lista:

    ```kotlin
    /**
     * Exibe uma lista de nomes que o usuário pode clicar com um cabeçalho
     */
    @Composable
    fun NamePicker(
        header: String,
        names: List<String>,
        onNameClicked: (String) -> Unit
    ) {
        Column {
            // Recompõe quando [header] muda, mas não quando [names] muda
            Text(header, style = MaterialTheme.typography.bodyLarge)
            HorizontalDivider()

            // LazyColumn é a versão do Compose para RecyclerView.
            // A lambda passada para items() é semelhante a um RecyclerView.ViewHolder.
            LazyColumn {
                items(names) { name ->
                    // Recompõe quando o [name] do item atualiza. Não recompõe quando [header] muda
                    NamePickerItem(name, onNameClicked)
                }
            }
        }
    }

    /**
     * Exibe um nome individual que o usuário pode clicar.
     */
    @Composable
    private fun NamePickerItem(name: String, onClicked: (String) -> Unit) {
        Text(name, Modifier.clickable(onClick = { onClicked(name) }))
    }
    ```

- **Escopos de Recomputação**
  - Elementos podem ser recompostos individualmente sem necessidade de executar pais ou irmãos.
  - Alteração no `header` permite pular para a execução da `Column`, enquanto ignora `LazyColumn` se `names` não mudou.

- **Cuidados com Efeitos Colaterais**
  - Execução de funções composable não deve ter efeitos colaterais.
  - Efeitos colaterais devem ser acionados a partir de callbacks, garantindo que a recomposição não altere o estado de forma indesejada.


## 4. Recomposição Otimista
- Permite cancelamento e reinício da recomposição com novos parâmetros.
- Necessário garantir funções idempotentes e sem efeitos colaterais para manter a integridade.

## 5. Recomendações para Desenvolvimento
- Utilizar funções rápidas e sem efeitos colaterais.
- Evitar operações caras durante recomposição.
- Mover trabalhos intensivos para outras threads e utilizar estados mutáveis ou LiveData para gerenciar dados.

## Exemplo 1: Função Simples Combinável

```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = \"Hello $name\")
}
```
### Explicação:
- **Função Combinável (`@Composable`)**: A anotação `@Composable` indica que esta função pode ser usada para emitir elementos de UI.
- **Parâmetro de Entrada**: Recebe uma `String` chamada `name`.
- **Renderização de Texto**: Utiliza a função `Text` para exibir a saudação com o nome fornecido.
- **Retorno**: Esta função não retorna nada explicitamente, pois seu objetivo é descrever a UI no estilo declarativo.

---

## Exemplo 2: NamePicker com LazyColumn

```kotlin
@Composable
fun NamePicker(
    header: String,
    names: List<String>,
    onNameClicked: (String) -> Unit
) {
    Column {
        Text(header, style = MaterialTheme.typography.bodyLarge)
        HorizontalDivider()

        LazyColumn {
            items(names) { name ->
                NamePickerItem(name, onNameClicked)
            }
        }
    }
}

@Composable
private fun NamePickerItem(name: String, onClicked: (String) -> Unit) {
    Text(name, Modifier.clickable(onClick = { onClicked(name) }))
}
```
### Explicação:
- **Componentes Visuais**:
  - **`Column`**: Organiza seus filhos verticalmente.
  - **`Text`**: Exibe o cabeçalho com um estilo definido.
  - **`HorizontalDivider`**: Adiciona uma linha divisória horizontal.
- **`LazyColumn`**: Semelhante a um `RecyclerView` em Android, utilizado para listas longas de elementos de maneira otimizada.
- **Recomposição Eficiente**: A função ignora recomposições desnecessárias. Se o `header` mudar, apenas ele é recomposto, não afetando a lista.
- **Interatividade**: `NamePickerItem` é clicável, ativando a função `onNameClicked` quando um nome é selecionado.

---

## Exemplo 3: SharedPrefsToggle

```kotlin
@Composable
fun SharedPrefsToggle(
    text: String,
    value: Boolean,
    onValueChanged: (Boolean) -> Unit
) {
    Row {
        Text(text)
        Checkbox(checked = value, onCheckedChange = onValueChanged)
    }
}
```
### Explicação:
- **Estrutura de UI**:
  - **`Row`**: Organiza items horizontalmente.
  - **`Text`**: Mostra o texto associado à preferência.
  - **`Checkbox`**: Controla o estado (checado ou não-checado) com base no valor booleano fornecido.
- **Interação com o Usuário**: O checkbox permite ao usuário alternar o valor, acionando `onValueChanged`.

---

## Exemplo 4: ListComposable

```kotlin
@Composable
fun ListComposable(myList: List<String>) {
    Row(horizontalArrangement = Arrangement.SpaceBetween) {
        Column {
            for (item in myList) {
                Text(\"Item: $item\")
            }
        }
        Text(\"Count: ${myList.size}\")
    }
}
```
### Explicação:
- **Layout Estruturado**:
  - **`Row` com `Arrangement.SpaceBetween`**: Os elementos ficam distribuídos ao longo do espaço disponível.
  - **`Column`**: Lista os itens fornecidos.
- **Contagem de Itens**: Mostra a contagem total de itens após a coluna.

---

## Exemplo 5: ListWithBug

```kotlin
@Composable
fun ListWithBug(myList: List<String>) {
    var items = 0

    Row(horizontalArrangement = Arrangement.SpaceBetween) {
        Column {
            for (item in myList) {
                Card {
                    Text(\"Item: $item\")
                    items++ // Evitar! Efeito colateral da recomposição da coluna.
                }




---

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


## Tutorial do site do Android


Jetpack Compose is a modern toolkit for building native Android UI. Jetpack Compose simplifies and accelerates UI development on Android with less code, powerful tools, and intuitive Kotlin APIs.

In this tutorial, you'll build a simple UI component with declarative functions. You won't be editing any XML layouts or using the Layout Editor. Instead, you will call composable functions to define what elements you want, and the Compose compiler will do the rest.

Full Preview
Tutorial
Learning the basics of Compose
1: Composable functions
2: Layouts
3: Material Design
4: Lists and animations
Lesson 1: Composable functions
Jetpack Compose is built around composable functions. These functions let you define your app's UI programmatically by describing how it should look and providing data dependencies, rather than focusing on the process of the UI's construction (initializing an element, attaching it to a parent, etc.). To create a composable function, just add the @Composable annotation to the function name.


Add a text element
To begin, download the most recent version of Android Studio, and create an app by selecting New Project, and under the Phone and Tablet category, select Empty Activity. Name your app ComposeTutorial and click Finish. The default template already contains some Compose elements, but in this tutorial you will build it up step by step.

First, display a “Hello world!” text by adding a text element inside the onCreate method. You do this by defining a content block, and calling the Text composable function. The setContent block defines the activity's layout where composable functions are called. Composable functions can only be called from other composable functions.

Jetpack Compose uses a Kotlin compiler plugin to transform these composable functions into the app's UI elements. For example, the Text composable function that is defined by the Compose UI library displays a text label on the screen.

Define a composable function
To make a function composable, add the @Composable annotation. To try this out, define a MessageCard function which is passed a name, and uses it to configure the text element.

// ...
import androidx.compose.runtime.Composable

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MessageCard("Android")
        }
    }
}

@Composable
fun MessageCard(name: String) {
    Text(text = "Hello $name!")
}


Preview your function in Android Studio
The @Preview annotation lets you preview your composable functions within Android Studio without having to build and install the app to an Android device or emulator. The annotation must be used on a composable function that does not take in parameters. For this reason, you can't preview the MessageCard function directly. Instead, make a second function named PreviewMessageCard, which calls MessageCard with an appropriate parameter. Add the @Preview annotation before @Composable.

// ...
import androidx.compose.ui.tooling.preview.Preview

@Composable
fun MessageCard(name: String) {
    Text(text = "Hello $name!")
}

@Preview
@Composable
fun PreviewMessageCard() {
    MessageCard("Android")
}

Rebuild your project. The app itself doesn't change, since the new PreviewMessageCard function isn't called anywhere, but Android Studio adds a preview window which you can expand by clicking on the split (design/code) view. This window shows a preview of the UI elements created by composable functions marked with the @Preview annotation. To update the previews at any time, click the refresh button at the top of the preview window.


import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.material3.Text

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            Text("Hello world!")
        }
    }
}
  

Lesson 2: Layouts
UI elements are hierarchical, with elements contained in other elements. In Compose, you build a UI hierarchy by calling composable functions from other composable functions.


Add multiple texts
So far you’ve built your first composable function and preview! To discover more Jetpack Compose capabilities, you’re going to build a simple messaging screen containing a list of messages that can be expanded with some animations.

Start by making the message composable richer by displaying the name of its author and a message content. You need to first change the composable parameter to accept a Message object instead of a String, and add another Text composable inside the MessageCard composable. Make sure to update the preview as well.

// ...

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MessageCard(Message("Android", "Jetpack Compose"))
        }
    }
}

data class Message(val author: String, val body: String)

@Composable
fun MessageCard(msg: Message) {
    Text(text = msg.author)
    Text(text = msg.body)
}

@Preview
@Composable
fun PreviewMessageCard() {
    MessageCard(
        msg = Message("Lexi", "Hey, take a look at Jetpack Compose, it's great!")
    )
}


This code creates two text elements inside the content view. However, since you haven't provided any information about how to arrange them, the text elements are drawn on top of each other, making the text unreadable.

Using a Column
The Column function lets you arrange elements vertically. Add Column to the MessageCard function.
You can use Row to arrange items horizontally and Box to stack elements.

// ...
import androidx.compose.foundation.layout.Column

@Composable
fun MessageCard(msg: Message) {
    Column {
        Text(text = msg.author)
        Text(text = msg.body)
    }
}

Add an image element
Enrich your message card by adding a profile picture of the sender. Use the Resource Manager to import an image from your photo library or use this one. Add a Row composable to have a well structured design and an Image composable inside it.

// ...
import androidx.compose.foundation.Image
import androidx.compose.foundation.layout.Row
import androidx.compose.ui.res.painterResource

@Composable
fun MessageCard(msg: Message) {
    Row {
        Image(
            painter = painterResource(R.drawable.profile_picture),
            contentDescription = "Contact profile picture",
        )
    
       Column {
            Text(text = msg.author)
            Text(text = msg.body)
        }
  
    }
  
}

Configure your layout
Your message layout has the right structure but its elements aren't well spaced and the image is too big! To decorate or configure a composable, Compose uses modifiers. They allow you to change the composable's size, layout, appearance or add high-level interactions, such as making an element clickable. You can chain them to create richer composables. You'll use some of them to improve the layout.

// ...
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.unit.dp

@Composable
fun MessageCard(msg: Message) {
    // Add padding around our message
    Row(modifier = Modifier.padding(all = 8.dp)) {
        Image(
            painter = painterResource(R.drawable.profile_picture),
            contentDescription = "Contact profile picture",
            modifier = Modifier
                // Set image size to 40 dp
                .size(40.dp)
                // Clip image to be shaped as a circle
                .clip(CircleShape)
        )

        // Add a horizontal space between the image and the column
        Spacer(modifier = Modifier.width(8.dp))

        Column {
            Text(text = msg.author)
            // Add a vertical space between the author and message texts
            Spacer(modifier = Modifier.height(4.dp))
            Text(text = msg.body)
        }
    }
}

Lesson 3: Material Design
Compose is built to support Material Design principles. Many of its UI elements implement Material Design out of the box. In this lesson, you'll style your app with Material Design widgets.


Use Material Design
Your message design now has a layout, but it doesn't look great yet.
Jetpack Compose provides an implementation of Material Design 3 and its UI elements out of the box. You'll improve the appearance of our MessageCard composable using Material Design styling.

To start, wrap the MessageCard function with the Material theme created in your project, ComposeTutorialTheme, as well as a Surface. Do it both in the @Preview and in the setContent function. Doing so will allow your composables to inherit styles as defined in your app's theme ensuring consistency across your app.

Material Design is built around three pillars: Color, Typography, and Shape. You will add them one by one.

Note: the Empty Compose Activity template generates a default theme for your project that allows you to customize MaterialTheme. If you named your project anything different from ComposeTutorial, you can find your custom theme in the Theme.kt file in the ui.theme subpackage.

// ...

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            ComposeTutorialTheme {
                Surface(modifier = Modifier.fillMaxSize()) {
                    MessageCard(Message("Android", "Jetpack Compose"))
                }
            }
        }
    }
}

@Preview
@Composable
fun PreviewMessageCard() {
    ComposeTutorialTheme {
        Surface {
            MessageCard(
                msg = Message("Lexi", "Take a look at Jetpack Compose, it's great!")
            )
        }
    }
}


Color
Use MaterialTheme.colorScheme to style with colors from the wrapped theme. You can use these values from the theme anywhere a color is needed. This example uses dynamic theming colors (defined by device preferences). You can set dynamicColor to false in the MaterialTheme.kt file to change this.

// ...
import androidx.compose.foundation.border
import androidx.compose.material3.MaterialTheme

@Composable
fun MessageCard(msg: Message) {
   Row(modifier = Modifier.padding(all = 8.dp)) {
       Image(
           painter = painterResource(R.drawable.profile_picture),
           contentDescription = null,
           modifier = Modifier
               .size(40.dp)
               .clip(CircleShape)
               .border(1.5.dp, MaterialTheme.colorScheme.primary, CircleShape)
       )

       Spacer(modifier = Modifier.width(8.dp))

       Column {
           Text(
               text = msg.author,
               color = MaterialTheme.colorScheme.secondary
           )

           Spacer(modifier = Modifier.height(4.dp))
           Text(text = msg.body)
       }
   }
}

Style the title and add a border to the image.

Typography
Material Typography styles are available in the MaterialTheme, just add them to the Text composables.

// ...

@Composable
fun MessageCard(msg: Message) {
   Row(modifier = Modifier.padding(all = 8.dp)) {
       Image(
           painter = painterResource(R.drawable.profile_picture),
           contentDescription = null,
           modifier = Modifier
               .size(40.dp)
               .clip(CircleShape)
               .border(1.5.dp, MaterialTheme.colorScheme.primary, CircleShape)
       )
       Spacer(modifier = Modifier.width(8.dp))

       Column {
           Text(
               text = msg.author,
               color = MaterialTheme.colorScheme.secondary,
               style = MaterialTheme.typography.titleSmall
           )

           Spacer(modifier = Modifier.height(4.dp))

           Text(
               text = msg.body,
               style = MaterialTheme.typography.bodyMedium
           )
       }
   }
}



Shape
With Shapeyou can add the final touches. First, wrap the message body text around a Surface composable. Doing so allows customizing the message body's shape and elevation. Padding is also added to the message for a better layout.

// ...
import androidx.compose.material3.Surface

@Composable
fun MessageCard(msg: Message) {
   Row(modifier = Modifier.padding(all = 8.dp)) {
       Image(
           painter = painterResource(R.drawable.profile_picture),
           contentDescription = null,
           modifier = Modifier
               .size(40.dp)
               .clip(CircleShape)
               .border(1.5.dp, MaterialTheme.colorScheme.primary, CircleShape)
       )
       Spacer(modifier = Modifier.width(8.dp))

       Column {
           Text(
               text = msg.author,
               color = MaterialTheme.colorScheme.secondary,
               style = MaterialTheme.typography.titleSmall
           )

           Spacer(modifier = Modifier.height(4.dp))

           Surface(shape = MaterialTheme.shapes.medium, shadowElevation = 1.dp) {
               Text(
                   text = msg.body,
                   modifier = Modifier.padding(all = 4.dp),
                   style = MaterialTheme.typography.bodyMedium
               )
           }
       }
   }
}



Enable dark theme
Dark theme (or night mode) can be enabled to avoid a bright display especially at night, or simply to save the device battery. Thanks to the Material Design support, Jetpack Compose can handle the dark theme by default. Having used Material Design colors, text and backgrounds will automatically adapt to the dark background.

// ...
import android.content.res.Configuration

@Preview(name = "Light Mode")
@Preview(
    uiMode = Configuration.UI_MODE_NIGHT_YES,
    showBackground = true,
    name = "Dark Mode"
)
@Composable
fun PreviewMessageCard() {
   ComposeTutorialTheme {
    Surface {
      MessageCard(
        msg = Message("Lexi", "Hey, take a look at Jetpack Compose, it's great!")
      )
    }
   }
}


You can create multiple previews in your file as separate functions, or add multiple annotations to the same function.

Add a new preview annotation and enable night mode.

Color choices for the light and dark themes are defined in the IDE-generated Theme.kt file.

So far, you've created a message UI element that displays an image and two texts with different styles, and it looks good both in light and dark themes!


  
hide preview
Lesson 4: Lists and animations
Lists and animations are everywhere in apps. In this lesson, you will learn how Compose makes it easy to create lists and fun to add animations.


Create a list of messages
A chat with one message feels a bit lonely, so we are going to change the conversation to have more than one message. You'll need to create a Conversation function that will show multiple messages. For this use case, use Compose’s LazyColumn and LazyRow. These composables render only the elements that are visible on screen, so they are designed to be very efficient for long lists.

// ...
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items

@Composable
fun Conversation(messages: List<Message>) {
    LazyColumn {
        items(messages) { message ->
            MessageCard(message)
        }
    }
}

@Preview
@Composable
fun PreviewConversation() {
    ComposeTutorialTheme {
        Conversation(SampleData.conversationSample)
    }
}



In this code snippet, you can see that LazyColumn has an items child. It takes a List as a parameter and its lambda receives a parameter we’ve named message (we could have named it whatever we want) which is an instance of Message. In short, this lambda is called for each item of the provided List. Copy the sample dataset into your project to help bootstrap the conversation quickly.

Animate messages while expanding
The conversation is getting more interesting. It's time to play with animations! You will add the ability to expand a message to show a longer one, animating both the content size and the background color. To store this local UI state, you need to keep track of whether a message has been expanded or not. To keep track of this state change, you have to use the functions remember and mutableStateOf.

// ...
import androidx.compose.foundation.clickable
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.setValue

class MainActivity : ComponentActivity() {
   override fun onCreate(savedInstanceState: Bundle?) {
       super.onCreate(savedInstanceState)
       setContent {
           ComposeTutorialTheme {
               Conversation(SampleData.conversationSample)
           }
       }
   }
}

@Composable
fun MessageCard(msg: Message) {
    Row(modifier = Modifier.padding(all = 8.dp)) {
        Image(
            painter = painterResource(R.drawable.profile_picture),
            contentDescription = null,
            modifier = Modifier
                .size(40.dp)
                .clip(CircleShape)
                .border(1.5.dp, MaterialTheme.colorScheme.primary, CircleShape)
        )
        Spacer(modifier = Modifier.width(8.dp))

        // We keep track if the message is expanded or not in this
        // variable
        var isExpanded by remember { mutableStateOf(false) }

        // We toggle the isExpanded variable when we click on this Column
        Column(modifier = Modifier.clickable { isExpanded = !isExpanded }) {
            Text(
                text = msg.author,
                color = MaterialTheme.colorScheme.secondary,
                style = MaterialTheme.typography.titleSmall
            )

            Spacer(modifier = Modifier.height(4.dp))

            Surface(
                shape = MaterialTheme.shapes.medium,
                shadowElevation = 1.dp,
            ) {
                Text(
                    text = msg.body,
                    modifier = Modifier.padding(all = 4.dp),
                    // If the message is expanded, we display all its content
                    // otherwise we only display the first line
                    maxLines = if (isExpanded) Int.MAX_VALUE else 1,
                    style = MaterialTheme.typography.bodyMedium
                )
            }
        }
    }
}



Composable functions can store local state in memory by using remember, and track changes to the value passed to mutableStateOf. Composables (and their children) using this state will get redrawn automatically when the value is updated. This is called recomposition.

By using Compose’s state APIs like remember and mutableStateOf, any changes to state automatically update the UI.

Note: You need to add the following imports to correctly use Kotlin's delegated property syntax (the by keyword). Alt+Enter or Option+Enter adds them for you.
import androidx.compose.runtime.getValue import androidx.compose.runtime.setValue

Now you can change the background of the message content based on isExpanded when we click on a message. You will use the clickable modifier to handle click events on the composable. Instead of just toggling the background color of the Surface, you will animate the background color by gradually modifying its value from MaterialTheme.colorScheme.surface to MaterialTheme.colorScheme.primary and vice versa. To do so, you will use the animateColorAsState function. Lastly, you will use the animateContentSize modifier to animate the message container size smoothly:

// ...
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.animateContentSize

@Composable
fun MessageCard(msg: Message) {
    Row(modifier = Modifier.padding(all = 8.dp)) {
        Image(
            painter = painterResource(R.drawable.profile_picture),
            contentDescription = null,
            modifier = Modifier
                .size(40.dp)
                .clip(CircleShape)
                .border(1.5.dp, MaterialTheme.colorScheme.secondary, CircleShape)
        )
        Spacer(modifier = Modifier.width(8.dp))

        // We keep track if the message is expanded or not in this
        // variable
        var isExpanded by remember { mutableStateOf(false) }
        // surfaceColor will be updated gradually from one color to the other
        val surfaceColor by animateColorAsState(
            if (isExpanded) MaterialTheme.colorScheme.primary else MaterialTheme.colorScheme.surface,
        )

        // We toggle the isExpanded variable when we click on this Column
        Column(modifier = Modifier.clickable { isExpanded = !isExpanded }) {
            Text(
                text = msg.author,
                color = MaterialTheme.colorScheme.secondary,
                style = MaterialTheme.typography.titleSmall
            )

            Spacer(modifier = Modifier.height(4.dp))

            Surface(
                shape = MaterialTheme.shapes.medium,
                shadowElevation = 1.dp,
                // surfaceColor color will be changing gradually from primary to surface
                color = surfaceColor,
                // animateContentSize will change the Surface size gradually
                modifier = Modifier.animateContentSize().padding(1.dp)
            ) {
                Text(
                    text = msg.body,
                    modifier = Modifier.padding(all = 4.dp),
                    // If the message is expanded, we display all its content
                    // otherwise we only display the first line
                    maxLines = if (isExpanded) Int.MAX_VALUE else 1,
                    style = MaterialTheme.typography.bodyMedium
                )
            }
        }
    }
}


Next steps
Congratulations, you’ve finished the Compose tutorial! You’ve built a simple chat screen efficiently showing a list of expandable & animated messages containing an image and texts, designed using Material Design principles with a dark theme included and previews—all in under 100 lines of code!

Here’s what you've learned so far:

Defining composable functions
Adding different elements in your composable
Structuring your UI component using layout composables
Extending composables by using modifiers
Creating an efficient list
Keeping track of state and modifying it
Adding user interaction on a composable
Animating messages while expanding them
If you want to dig deeper on some of these steps, explore the resources below.


# Tópicos para Aula sobre Jetpack Compose

## Introdução ao Jetpack Compose
- Toolkit moderno para criar interfaces nativas do Android.
- Simplifica e acelera o desenvolvimento de UI com menos código.
- Usa APIs intuitivas em Kotlin.

## Conceitos Básicos de Compose

1. **Funções Componíveis (Composable Functions)**
   - Defina a UI do app programaticamente.
   - Use a anotação `@Composable` para funções componíveis.
   - Exemplo: Adicionar um elemento de texto com a função `Text`.

   ```kotlin
   import android.os.Bundle
   import androidx.activity.ComponentActivity
   import androidx.activity.compose.setContent
   import androidx.compose.material3.Text

   class MainActivity : ComponentActivity() {
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContent {
               Text(\"Hello world!\")
           }
       }
   }
   ```

2. **Visualização de Funções Componíveis**
   - Utilizar a anotação `@Preview` para visualizar funções no Android Studio.
   - Criação de uma função de pré-visualização para ver a composição sem parâmetros.

   ```kotlin
   import androidx.compose.runtime.Composable
   import androidx.compose.ui.tooling.preview.Preview

   @Composable
   fun MessageCard(name: String) {
       Text(text = \"Hello $name!\")
   }

   @Preview
   @Composable
   fun PreviewMessageCard() {
       MessageCard(\"Android\")
   }
   ```

## Desenvolvimento de Layouts
1. **Hierarquia de Elementos de UI**
   - Construir hierarquias de UI com funções componíveis.
   - Exemplo: Usar `Column` para dispor elementos verticalmente.

   ```kotlin
   import androidx.compose.foundation.layout.Column

   @Composable
   fun MessageCard(msg: Message) {
       Column {
           Text(text = msg.author)
           Text(text = msg.body)
       }
   }
   ```

2. **Adicionar Elementos de Texto e Imagem**
   - Exemplo de código para adicionar múltiplos textos e imagens.
   - Uso de `Row` e `Image` para estruturar elementos.

   ```kotlin
   import androidx.compose.foundation.Image
   import androidx.compose.foundation.layout.Row
   import androidx.compose.ui.res.painterResource

   @Composable
   fun MessageCard(msg: Message) {
       Row {
           Image(
               painter = painterResource(R.drawable.profile_picture),
               contentDescription = \"Contact profile picture\",
           )
           Column {
               Text(text = msg.author)
               Text(text = msg.body)
           }
       }
   }
   ```

3. **Configuração do Layout**
   - Uso de modificadores para alterar tamanho, layout e aparência.
   - Exemplo de código com modificadores como `padding`, `size` e `clip`.

   ```kotlin
   import androidx.compose.foundation.border
   import androidx.compose.foundation.layout.Spacer
   import androidx.compose.foundation.layout.height
   import androidx.compose.foundation.layout.padding
   import androidx.compose.foundation.layout.size
   import androidx.compose.foundation.layout.width
   import androidx.compose.foundation.shape.CircleShape
   import androidx.compose.ui.Modifier
   import androidx.compose.ui.draw.clip
   import androidx.compose.ui.unit.dp

   @Composable
   fun MessageCard(msg: Message) {
       Row(modifier = Modifier.padding(all = 8.dp)) {
           Image(
               painter = painterResource(R.drawable.profile_picture),
               contentDescription = \"Contact profile picture\",
               modifier = Modifier
                   .size(40.dp)
                   .clip(CircleShape)
           )
           Spacer(modifier = Modifier.width(8.dp))
           Column {
               Text(text = msg.author)
               Spacer(modifier = Modifier.height(4.dp))
               Text(text = msg.body)
           }
       }
   }
   ```

## Design com Material Design

1. **Uso do Design Material**
   - Implementação de Material Design 3.
   - Uso de `Surface` e tema `ComposeTutorialTheme` para consistência de estilo.

   ```kotlin
   class MainActivity : ComponentActivity() {
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContent {
               ComposeTutorialTheme {
                   Surface(modifier = Modifier.fillMaxSize()) {
                       MessageCard(Message(\"Android\", \"Jetpack Compose\"))
                   }
               }
           }
       }
   }

   @Preview
   @Composable
   fun PreviewMessageCard() {
       ComposeTutorialTheme {
           Surface {
               MessageCard(
                   msg = Message(\"Lexi\", \"Take a look at Jetpack Compose, it's great!\")
               )
           }
       }
   }
   ```

2. **Aplicando Estilos com `MaterialTheme`**
   - Cores: Uso de `MaterialTheme.colorScheme`.
   - Tipografia: Uso de `MaterialTheme.typography`.
   - Formas: Ajustes de bordas e elevação de elementos.

   ```kotlin
   import androidx.compose.foundation.border
   import androidx.compose.material3.MaterialTheme

   @Composable
   fun MessageCard(msg: Message) {
       Row(modifier = Modifier.padding(all = 8.dp)) {
           Image(
               painter = painterResource(R.drawable.profile_picture),
               contentDescription = null,
               modifier = Modifier
                   .size(40.dp)
                   .clip(CircleShape)
                   .border(1.5.dp, MaterialTheme.colorScheme.primary, CircleShape)
           )

           Spacer(modifier = Modifier.width(8.dp))

           Column {
               Text(
                   text = msg.author,
                   color = MaterialTheme.colorScheme.secondary,
                   style = MaterialTheme.typography.titleSmall
               )

               Spacer(modifier = Modifier.height(4.dp))
               Text(
                   text = msg.body,
                   style = MaterialTheme.typography.bodyMedium
               )
           }
       }
   }
   ```

## Trabalhando com Temas

1. **Habilitação do Tema Escuro**
   - Suporte nativo a tema escuro com adaptação de cores automáticas.
   - Uso de múltiplas anotações de pré-visualização para ver temas claro e escuro.

   ```kotlin
   import android.content.res.Configuration

   @Preview(name = \"Light Mode\")
   @Preview(
       uiMode = Configuration.UI_MODE_NIGHT_YES,
       showBackground = true,
       name = \"Dark Mode\"
   )
   @Composable
   fun PreviewMessageCard() {
       ComposeTutorialTheme {
           Surface {
               MessageCard(
                   msg = Message(\"Lexi\", \"Hey, take a look at Jetpack Compose, it's great!\")
               )
           }
       }
   }
   ```

## Listas e Animações

1. **Criação de Listas de Mensagens**
   - Uso de `LazyColumn` e `LazyRow` para eficiência.
   - Exemplo de código para criar uma função `Conversation` exibindo múltiplas mensagens.

   ```kotlin
   import androidx.compose.foundation.lazy.LazyColumn
   import androidx.compose.foundation.lazy.items

   @Composable
   fun Conversation(messages: List<Message>) {
       LazyColumn {
           items(messages) { message ->
               MessageCard(message)
           }
       }
   }

   @Preview
   @Composable
   fun PreviewConversation() {
       ComposeTutorialTheme {
           Conversation(SampleData.conversationSample)
       }
   }
   ```

2. **Animação de Mensagens**
   - Uso de funções `remember` e `mutableStateOf` para gerenciar estado local.
   - Exemplo de código para animar cor e tamanho de mensagens ao expandir.

   ```kotlin
   import androidx.compose.foundation.clickable
   import androidx.compose.runtime.getValue
   import androidx.compose.runtime.mutableStateOf
   import androidx.compose.runtime.remember
   import androidx.compose.runtime.setValue
   import androidx.compose.animation.animateColorAsState
   import androidx.compose.animation.animateContentSize

   @Composable
   fun MessageCard(msg: Message) {
       Row(modifier = Modifier.padding(all = 8.dp)) {
           Image(
               painter = painterResource(R.drawable.profile_picture),
               contentDescription = null,
               modifier = Modifier
                   .size(40.dp)
                   .clip(CircleShape)
                   .border(1.5.dp, MaterialTheme.colorScheme.secondary, CircleShape)
           )
           Spacer(modifier = Modifier.width(8.dp))

           var isExpanded by remember { mutableStateOf(false) }
           val surfaceColor by animateColorAsState(
               if (isExpanded) MaterialTheme.colorScheme.primary else MaterialTheme.colorScheme.surface,
           )

           Column(modifier = Modifier.clickable { isExpanded = !isExpanded }) {
               Text(
                   text = msg.author,
                   color = MaterialTheme.colorScheme.secondary,
                   style = MaterialTheme.typography.titleSmall
               )

               Spacer(modifier = Modifier.height(4.dp))

               Surface(
                   shape = MaterialTheme.shapes.medium,
                   shadowElevation = 1.dp,
                   color = surfaceColor,
                   modifier = Modifier.animateContentSize().padding(1.dp)
               ) {
                   Text(
                       text = msg.body,
                       modifier = Modifier.padding(all = 4.dp),
                       maxLines = if (isExpanded) Int.MAX_VALUE else 1,
                       style = MaterialTheme.typography.bodyMedium
                   )
               }
           }
       }
