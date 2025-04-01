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

# Pensando com Jetpack Compose
https://developer.android.com/develop/ui/compose/mental-model

## 1. Paradigma de Programação Declarativa
- **Modelo Tradicional**: Uso de uma hierarquia de visualização em árvore.
- **Desafios**: Alterações manuais que podem levar a erros e estados ilegais.
- **Abordagem Declarativa**: Simplifica a engenharia de UI ao aplicar mudanças necessárias, evitando complexidade de atualizações manuais.

## 2. Funções Composable
- Funções anotadas com `@Composable` para converter dados em UI.
- Funções que não retornam nada, emitindo diretamente o estado desejado da tela.

```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = "Hello $name")
}
```

## 3. Recomposição


- **Modelo de UI Imperativa vs Compose**
  - No modelo imperativo, altere o estado de um widget usando setters.
  - No Compose, atualize chamando novamente a função Composable com novos dados.
  - Widgets são redesenhados apenas se necessário, graças à recomposição inteligente do Compose.

- **Exemplo de Função Composable**
  - ```kotlin
    @Composable
    fun ClickCounter(clicks: Int, onClick: () -> Unit) {
        Button(onClick = onClick) {
            Text("I've been clicked $clicks times")
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
    - Pode ser cancelada se for desnecessária.
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

## Exemplo sobre execução em paralelo

```kotlin
@Composable
fun ListComposable(myList: List<String>) {
    Row(horizontalArrangement = Arrangement.SpaceBetween) {
        Column {
            for (item in myList) {
                Text("Item: $item")
            }
        }
        Text("Count: ${myList.size}")
    }
}
```
### Explicação:
- **Layout Estruturado**:
  - **`Row` com `Arrangement.SpaceBetween`**: Os elementos ficam distribuídos ao longo do espaço disponível.
  - **`Column`**: Lista os itens fornecidos.
- **Contagem de Itens**: Mostra a contagem total de itens após a coluna.

```kotlin
@Composable
fun ListWithBug(myList: List<String>) {
    var items = 0

    Row(horizontalArrangement = Arrangement.SpaceBetween) {
        Column {
            for (item in myList) {
                Card {
                    Text("Item: $item")
                    items++ // Avoid! Side-effect of the column recomposing.
                }
            }
        }
        Text("Count: $items")
    }
}
```

## Ordem de execução

- Execução pode ocorrer em qualquer ordem;
- Por exemplo, no código abaixo:
  - Se `StartScreen` seta uma variável global (um efeito colateral) e que é utilizada por, digamos, `MiddleScreen`, não há garantias se a variável estará setada quando `MiddleScreen` rodar;

```kotlin
@Composable
fun ButtonRow() {
    MyFancyNavigation {
        StartScreen()
        MiddleScreen()
        EndScreen()
    }
}
```

> [!Note]
>
> - Diferença entre componentes `Surface` e `Scaffold`:
>   - São estruturas fundamentais de layout. Algo como containers;
>   - Porém, `Scaffold` torna mais fácil o tratamento de área cobertas pelo Sistema Operacional (como barras de notificação, notch de câmera, botões de navegação, etc.);
>   - Isso é ainda mais importante após a decisão do Google de que as [aplicações devem usar `enableEdgeToEdge()`](https://developer.android.com/about/versions/15/behavior-changes-15#edge-to-edge).
>     - Isso significa que a aplicação é que deve tomar conta dessas áreas cobertas, ocupando a tela de "borda à borda";
>   - Com o `Surface` esse controle se torna bem mais complexo.


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
1. Funções de Composição
Em Compose, a UI é construída com funções chamadas funções de composição. Essas funções são anotadas com @Composable e descrevem um pedaço da UI.
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

Column: Organiza os elementos verticalmente.
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

# Tópicos para Aula sobre Jetpack Compose

## Introdução ao Jetpack Compose
- Toolkit moderno para criar interfaces nativas do Android.
- Simplifica e acelera o desenvolvimento de UI com menos código.
- Usa APIs intuitivas em Kotlin.

## Conceitos Básicos de Compose

1. **Funções de Composição (Composable Functions)**
   - Defina a UI do app programaticamente.
   - Use a anotação `@Composable` para funções de composição.
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
               Text("Hello world!")
           }
       }
   }
   ```

2. **Visualização de Funções de Composição**
   - Utilizar a anotação `@Preview` para visualizar funções no Android Studio.
   - Criação de uma função de pré-visualização para ver a composição sem parâmetros.

   ```kotlin
   import androidx.compose.runtime.Composable
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
   ```

## Desenvolvimento de Layouts
1. **Hierarquia de Elementos de UI**
   - Construir hierarquias de UI com funções de composição.
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
               contentDescription = "Contact profile picture",
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
               contentDescription = "Contact profile picture",
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
   }

   ## Mais informações

   Sobre os pacotes do Jetpack Compose
   https://developer.android.com/jetpack/androidx/releases/compose-ui
