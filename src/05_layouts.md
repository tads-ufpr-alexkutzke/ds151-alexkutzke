# Aula 05: Layouts básicos com Jetpack Compose
## Fundamentos de Layouts

### Componentes Principais
Os componentes básicos de layout no Compose são `Row`, `Column`, e `Box`. Esses componentes permitem arranjar elementos na vertical, horizontal, ou de forma sobreposta.

![518dbfad23ee1b05.png](https://developer.android.com/static/codelabs/jetpack-compose-basics/img/518dbfad23ee1b05.png)

A Column é um componente que alinha seus filhos verticalmente. Cada elemento filho é empilhado abaixo do anterior. É útil para criar layouts estruturados verticalmente, como listas ou seções de informações. Você pode ajustar o espaçamento entre os elementos e o alinhamento dentro da coluna para personalizar a aparência de acordo com as necessidades do seu design.

A Row é semelhante à Column, mas alinha seus filhos horizontalmente. Os elementos são posicionados lado a lado, permitindo a criação de layouts horizontais como fileiras de botões ou ícones. Assim como com Columns, você pode especificar o espaçamento e alinhamento dos elementos para controlar exatamente como eles são apresentados na tela.

O Box é um componente flexível que permite sobrepor elementos um sobre o outro. Ele funciona como um contêiner de layout onde você pode posicionar elementos em camadas. Isso é útil para criar designs complexos ou personalizados onde os elementos se sobrepõem parcialmente ou ficam centralizados dentro de uma área especificada.

```kotlin
@Composable
fun RowColumnExample() {
    Column {
        Text("Column Item 1")
        Text("Column Item 2")
        Row {
            Text("Row Item 1")
            Text("Row Item 2")
        }
    }
}
```

### Modificadores

Modifiers, ou modificadores, são ferramentas no Jetpack Compose que permitem modificar ou estilizar os componentes. Eles podem ser usados para alterar propriedades como tamanho, padding, background, e até funcionalidades de clique. Modifiers são aplicados de maneira encadeada, proporcionando uma maneira flexível e declarativa de personalizar a UI.

```kotlin
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.padding

@Composable
fun StyledText() {
    Text(
        text = "Styled with Modifiers",
        modifier = Modifier
            .background(Color.Cyan)
            .padding(16.dp)
            .width(200.dp)
            .height(50.dp)
    )
}
```

Modificadores [`fillMaxWidth`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/layout/package-summary#\\(androidx.compose.ui.Modifier\\).fillMaxSize\\(kotlin.Float\\)) e [`padding`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/layout/package-summary#\\(androidx.compose.ui.Modifier\\).padding\\(androidx.compose.ui.unit.Dp,androidx.compose.ui.unit.Dp\\)).

Modificadores podem ter sobrecargas, permitindo, por exemplo, especificar diferentes maneiras de criar padding.

### Arrangement e Alignment
O Alignment define como os elementos são alinhados dentro de um contêiner, como Row, Column, ou Box. Ele determina a posição dos elementos no eixo vertical ou horizontal (eixo principal de cada elemento), oferecendo controle preciso sobre como os componentes são posicionados tanto na direção principal quanto na direção cruzada do layout.

O Arrangement controla o espaçamento entre os elementos filhos dentro de Rows ou Columns, utilizando o eixo secundário como referência. Ele permite especificar como os elementos devem ser distribuídos, seja com espaçamento entre eles, tudo no início, centro ou fim do layout. Isso facilita a criação de interfaces esteticamente agradáveis ao controlar como o espaço extra é utilizado.

```kotlin
@Composable
fun AlignedContent() {
    Row(
        modifier = Modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.SpaceBetween,
        verticalAlignment = Alignment.CenterVertically
    ) {
        Text("Left")
        Text("Right")
    }
}
```


### Box
O `Box` pode ser utilizado para sobrepor itens e criar designs complexos onde os elementos precisam estar um em cima do outro.

```kotlin
@Composable
fun BoxExample() {
    Box(
        modifier = Modifier
            .size(100.dp)
            .background(Color.LightGray)
    ) {
        Text("Bottom", Modifier.align(Alignment.BottomStart))
        Text("Top", Modifier.align(Alignment.TopEnd))
    }
}
```

### Lazy Layouts
Utilize `LazyColumn` e `LazyRow` para listas eficientes e pagináveis, que são essenciais para tratar grandes quantidades de dados sem comprometer a performance.

```kotlin
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items

@Composable
fun SimpleLazyColumn() {
    LazyColumn {
        items(listOf("Apple", "Banana", "Cherry")) { fruit ->
            Text("Fruit: $fruit", Modifier.padding(8.dp))
        }
    }
}
```


## Customização e Temas

### Themes
A customização de temas no Jetpack Compose permite adaptar a aparência do aplicativo em nível global. Utilizando o MaterialTheme, você pode definir cores, tipografia, e formas para fornecer uma identidade visual consistente em toda a aplicação. A personalização de temas oferece uma maneira eficiente de garantir uma experiência de usuário coerente e estilizada.

Abra o arquivo `ui/theme/Theme.kt`, você verá detalhes da implementação do tema da aplicação.

`MaterialTheme` é uma função composable que reflete os princípios de estilo da [especificação do Material Design](https://m3.material.io/).

```kotlin
import androidx.compose.material.MaterialTheme
import androidx.compose.material.Surface

@Composable
fun ThemedGreeting() {
    MaterialTheme(
        colors = MaterialTheme.colors.copy(
            primary = Color.Magenta,
            primaryVariant = Color.DarkMagenta,
            secondary = Color.Cyan
        )
    ) {
        Surface {
            Text("Hello with Theme")
        }
    }
}
```

### Para o uso de Ícones


Use o composable `IconButton`, por exemplo:

```kotlin
IconButton(onClick = onClose) {
    Icon(Icons.Filled.Close, contentDescription = "Close")
}
```

Adicione a seguinte linha às dependências no seu arquivo `app/build.gradle.kts` para mais ícones:
```

 implementation("androidx.compose.material:material-icons-extended") 
```


## Práticas Avançadas

### Criando Layouts Compostos
Layouts compostos personalizados que melhor se encaixam nas necessidades específicas do design do aplicativo.

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.padding
import androidx.compose.ui.unit.dp

@Composable
fun CustomButton(content: @Composable () -> Unit) {
    Box(
        modifier = Modifier
            .padding(8.dp)
            .background(Color.Blue)
            .clickable { }
            .padding(16.dp)
    ) {
        content()
    }
}

@Composable
fun CustomButtonExample() {
    CustomButton {
        Text("Click Me!")
    }
}
```

### Interoperabilidade com Views Tradicionais
É possível utilizar `Views` dentro do Compose e vice-versa, facilitando a migração de aplicativos existentes para o novo sistema.

```kotlin
// No arquivo MainActivity.kt
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            AndroidViewExample()
        }
    }
}

@Composable
fun AndroidViewExample() {
    AndroidView(
        factory = { context ->
            Button(context).apply {
                text = "Old View Button"
                setOnClickListener {
                    // Código do botão ao ser clicado
                }
            }
        }
    )
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


### Referências
- [Layouts em Jetpack Compose](https://developer.android.com/jetpack/compose/layouts)
- [Modificadores e Layouts no Compose](https://developer.android.com/jetpack/compose/modifiers)
- [Theming em Jetpack Compose](https://developer.android.com/jetpack/compose/themes)
- [Jetpack Compose - Animações](https://developer.android.com/jetpack/compose/animation)
- [Interoperabilidade com Compose](https://developer.android.com/jetpack/compose/integration/interoperability)

---

## Exemplo Prático -> RGB Counter

### Link do Repositório
<https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter>

### Desenvolvimento passo a passo (commits)


### Cria Counter Composable
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/5a984c9412bddfde155d7563f8e057d79499d79c)


<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F5a984c9412bddfde155d7563f8e057d79499d79c%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Estrutura básica para o contador
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/ab92dbb0b8f22eb20b7d244c8c45306056b345de)

O modificador `weight` faz o elemento preencher todo o espaço disponível, afastando os outros elementos que não têm peso, chamados de *inflexíveis*.

<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2Fab92dbb0b8f22eb20b7d244c8c45306056b345de%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Tentativa frustada de incrementar o contador
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/6408de892fa0f2a2bd6f9c2e8469f1044d65daf8)

O motivo pelo qual a mutação dessa variável não aciona recomposições é que ela não está sendo rastreada pelo Compose. Além disso, cada vez que `Greeting` é chamado, a variável será redefinida para false.

Use o `mutableStateOf`.

No entanto, você não pode simplesmente atribuir `mutableStateOf` a uma variável dentro de um composable.

Para preservar o estado entre recomposições, *lembre* o estado mutável usando `remember`.

<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F6408de892fa0f2a2bd6f9c2e8469f1044d65daf8%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Primeiro contador funcional
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/0eff3d441e7a93434f45009dcca2e8c94937fd42)

Se o estado mudar, os composables que leem esses campos serão recompostos para exibir as atualizações.

Em aplicativos Android, há um loop principal de atualização da UI assim:

![f415ca9336d83142.png](https://developer.android.com/static/codelabs/jetpack-compose-state/img/f415ca9336d83142.png)

![7d3509d136280b6c.png](https://developer.android.com/static/codelabs/jetpack-compose-state/img/7d3509d136280b6c.png)

Se a função é chamada durante a composição inicial ou em recomposições, dizemos que está presente na Composição.

Você pode inspecionar o layout do app gerado pelo Compose usando a [ferramenta Layout Inspector do Android Studio](https://developer.android.com/studio/debug/layout-inspector),

Ferramenta Layout Inspector do Android Studio navegando para Tools > Layout Inspector.
![1f8e05f6497ec35f.png](https://developer.android.com/static/codelabs/jetpack-compose-state/img/1f8e05f6497ec35f.png)


<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F0eff3d441e7a93434f45009dcca2e8c94937fd42%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Correções de layout para apresentação no emulador
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/c8d147de1799182971c410e7f8bb76317de0b8cc)


<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2Fc8d147de1799182971c410e7f8bb76317de0b8cc%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Botão de decremento
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/832e7103829b6f74867d178e50f94c92f75a75b0)

Vamos mudar nossa pré-visualização para emular a largura comum de um celular pequeno, 320dp. Adicione um parâmetro `widthDp` à anotação `@Preview`.

<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F832e7103829b6f74867d178e50f94c92f75a75b0%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Melhora layout
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/176b0c20c03542c46715986de3b268c9d02abc7f)

<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F176b0c20c03542c46715986de3b268c9d02abc7f%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Uso do by
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/1e76cae99615e6cc563cbc2e93c86edadd33aaeb)

Aqui usamos uma palavra-chave `by` ao invés do `=`. Isso é um delegate de propriedade que poupa você de digitar `.value` toda vez.

<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F1e76cae99615e6cc563cbc2e93c86edadd33aaeb%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Adiciona parâmetros de mínimo e máximo
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/caf47d8befd9d439753c0830962d755f00d5fd64)

<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2Fcaf47d8befd9d439753c0830962d755f00d5fd64%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Adiciona CounterScreen com teste de vários contadores
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/90565a84a92f21f0a7ae3c2f6c034e1563b9fc52)

<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F90565a84a92f21f0a7ae3c2f6c034e1563b9fc52%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Monta o layout de RGBCounterScreen
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/f0ef233d46acab8a9749767c703a6fad617bb1b5)


<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2Ff0ef233d46acab8a9749767c703a6fad617bb1b5%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Inícia o State Hoisting do Counter
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/d447cd1338a854a31df9e449439b4325223d707e)

Em funções Composable, o estado que é lido ou modificado por várias funções deve existir em um ancestral comum — esse processo é chamado de State Hoisting, ou elevação de estado.

Como passamos eventos para cima? Passando callbacks para baixo. Callbacks são funções passadas como argumentos para outras funções e executadas quando o evento ocorre.

O padrão geral para elevação de estado no Jetpack Compose é substituir a variável de estado por dois parâmetros:
 - Valor: T - o valor atual a ser exibido;
 - OnValueChange: (T) -> Unit - um evento que solicita a mudança de valor com um novo valor T;

-  Propriedades importantes:
    - Fonte única de verdade: ao mover o estado em vez de duplicá-lo, estamos garantindo que exista apenas uma fonte única de verdade. Isso ajuda a evitar bugs.
    - Compartilhável: Estado elevado pode ser compartilhado com múltiplos composables.
    - Interceptável: Chamadores dos composables sem estado podem decidir ignorar ou modificar eventos antes de mudar o estado.
    - Desacoplado: O estado para uma função composable sem estado pode ser armazenado em qualquer lugar. Por exemplo, em um ViewModel.


<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2Fd447cd1338a854a31df9e449439b4325223d707e%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Exemplo de uso do novo Stateless Counter
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/1a0aa2aafd75bbfddc142eaca56c6c5fce8b4861)


> [!NOTE]
> No Compose, você não esconde elementos da UI. Em vez disso, você simplesmente não os adiciona à composição, então eles não são incluídos na árvore de UI que o Compose gera. Você faz isso com simples lógica condicional em Kotlin.

<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F1a0aa2aafd75bbfddc142eaca56c6c5fce8b4861%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Adicionar os limites ao formato Stateless
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/c400cf4bca8babbb5932ea585c27a52945ebc112)


<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2Fc400cf4bca8babbb5932ea585c27a52945ebc112%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Versão funcional da RGBCounterScreen
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/26bc0d54afa2fbaa1c573987d8613c09c75c43a6)


<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F26bc0d54afa2fbaa1c573987d8613c09c75c43a6%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Adiciona parâmetro step ao contador
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/808ca638b0be3e6673a5f51aed6b3243da68bf2c)


<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F808ca638b0be3e6673a5f51aed6b3243da68bf2c%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Adiciona parâmetro text ao contador
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/c5f59d6ee29cfa2e84ad85af926a55b6e8224ac5)


<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2Fc5f59d6ee29cfa2e84ad85af926a55b6e8224ac5%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Ideia simples para diminuir repetição de código
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/e3f7e4df1ea64ed8489891bc6cd724371de529d9)

<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2Fe3f7e4df1ea64ed8489891bc6cd724371de529d9%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---


### Atualiza mainactivity
[Diffs do commit](https://github.com/tads-ufpr-alexkutzke/ds151-aula-05-counter/commit/2b9f3852f3c25b75aee2ac3fe6430043e1037505)


<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-05-counter%2Fblob%2F2b9f3852f3c25b75aee2ac3fe6430043e1037505%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Faula05counter%2Fui%2FCounter%2FCounter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

---

> [!IMPORTANT]
> Se você executar o aplicativo em um dispositivo, clicar nos botões e então girar, a tela será mostrada novamente. A função `remember` funciona apenas enquanto o composable for mantido na Composição. Quando você gira, toda a atividade é reiniciada, então todo o estado é perdido. Isso também acontece com qualquer mudança de configuração e com a morte do processo.

Na próxima aula veremos como resolver esse problema.
