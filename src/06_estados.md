# 06_estados.md

Conteúdo:
Conceitos de estado e recomposição
Uso de remember, mutableStateOf e state hoisting
Tratamento de eventos de clique e interações
Atividade:
Implementação de um contador interativo

## Código exemplo: Jogo da Velha

<https://github.com/tads-ufpr-alexkutzke/ds151-example-tictactoe>


- Problema com LazyColumn e estados:
  - Se elemento sai da tela, ele perde o estado:
  - Solução simples: `rememberSaveable`

- `MutableList`s observáveis:
  - Utilizar `MutableStateListOf` ou `toMutableStateList()`

- Problemas com o `rememberSaveable`:
  - If you try to use `rememberSaveable()` to store the list in `WellnessScreen`, you'll get a runtime exception:

  - This error tells you that you need to provide a [custom saver](https://developer.android.com/jetpack/compose/state#restore-ui-state). However, you shouldn't be using `rememberSaveable` to store large amounts of data or complex data structures that require lengthy serialization or deserialization.

  - Similar rules apply when working with Activity's [`onSaveInstanceState`](https://developer.android.com/reference/android/app/Activity#onSaveInstanceState\(android.os.Bundle\)); you can find more information in the [Save UI states documentation](https://developer.android.com/topic/libraries/architecture/saving-states#onsaveinstancestate). If you want to do this, you need an alternative storing mechanism. You can learn more about different [options for preserving UI state](https://developer.android.com/topic/libraries/architecture/saving-states#options) in the documentation.

## Desenvolvimento da aplicação MyTasks

A seguir está o passo a passo do desenvolvimento da aplicação [MyTasks](https://github.com/tads-ufpr-alexkutzke/ds151-aula-06-mytasks/)

<https://github.com/tads-ufpr-alexkutzke/ds151-aula-06-mytasks/>

### 1. Criação do componente TaskItem

O componente `TaskItem` exibe uma Tarefa.

Ele foi planejado para ser do tipo **stateless**, ou seja, seu estado será manipulado pelo seu antecessor.

#### Commit: **TaskItem Layout** - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-06-mytasks/commit/17b16e81a0e69f4fa2c87636bb4a0132cec0a93a'>Diffs 17b16e81</a>

<details>
<summary><code>TaskItem.kt</code></summary>
<br>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2F17b16e81a0e69f4fa2c87636bb4a0132cec0a93a%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FTaskItem.kt%23L26-86&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

### 2. Criação do componente TaskList

O componente `TaskItem` exibe uma lista de tarefas, ou seja, uma lista de `TaskItem`.

Pontos importantes:
  - Parâmetro `key`: para que a `LazyColumn` mantenha um controle mais preciso de quais elementos foram atualizados, é importante informar uma forma de identificar cada elemento unicamente:
    - Aqui usamos um `id` criado de forma randômica para cada `Task`: `val id: UUID = UUID.randomUUID()`

#### Commit: **TaskList** - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-06-mytasks/commit/1b615fd406870b04e65bcaa05bf0fec817425ea3'>Diffs 1b615fd4</a>

<details>
<summary><code>TaskList.kt</code></summary>
<br>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2F1b615fd406870b04e65bcaa05bf0fec817425ea3%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FTaskList.kt%23L10-46&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

#### Commit: **NewTaskControl and MyTasks** - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-06-mytasks/commit/a39ac3052175e11c5a84bc03c89f80fe02551df7'>Diffs a39ac305</a>





<details>
<summary><code>MyTasks.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2Fa39ac3052175e11c5a84bc03c89f80fe02551df7%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FMyTasks.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>NewTaskControl.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2Fa39ac3052175e11c5a84bc03c89f80fe02551df7%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FNewTaskControl.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

#### Commit: **First states** - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-06-mytasks/commit/bb906d1cb3a5c3641d11b3fb3a7d701cd4a7c257'>Diffs bb906d1c</a>





<details>
<summary><code>MainActivity.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2Fbb906d1cb3a5c3641d11b3fb3a7d701cd4a7c257%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2FMainActivity.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MyTasks.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2Fbb906d1cb3a5c3641d11b3fb3a7d701cd4a7c257%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FMyTasks.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>TaskList.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2Fbb906d1cb3a5c3641d11b3fb3a7d701cd4a7c257%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FTaskList.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

#### Commit: **Events and states working** - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-06-mytasks/commit/07479b859a9aacfa231ecb1b0255798b2759c951'>Diffs 07479b85</a>





<details>
<summary><code>MyTasks.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2F07479b859a9aacfa231ecb1b0255798b2759c951%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FMyTasks.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>TaskItem.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2F07479b859a9aacfa231ecb1b0255798b2759c951%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FTaskItem.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>TaskList.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2F07479b859a9aacfa231ecb1b0255798b2759c951%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FTaskList.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

#### Commit: **Sort tasks** - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-06-mytasks/commit/26f4dc530a8f60ebcf543eae044bf41c9091327d'>Diffs 26f4dc53</a>





<details>
<summary><code>MyTasks.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2F26f4dc530a8f60ebcf543eae044bf41c9091327d%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FMyTasks.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

**Readme** - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-06-mytasks/commit/ce6303dab475642fae177d2bbf679cecae374f21'>Diffs ce6303da</a>





<details>
<summary><code>README.md</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2Fce6303dab475642fae177d2bbf679cecae374f21%2FREADME.md&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

---

### Utilizando `ViewModel` para armazenar estados

#### Commit: **Implements MyTasksViewModel** - <a href='https://github.com/tads-ufpr-alexkutzke/ds151-aula-06-mytasks/commit/c3d0f24b1a9395952265d218b6461e1a65aed8dc'>Diffs c3d0f24b</a>

Existem dois tipos de lógica:
  - Logica de UI ou Comportamento: *como exibir* alterações no estado;
  - Lógica de Negócios ou Domínio: *o que fazer* quando o estado é alterado;

A Lógica de Domínio é, geralmente, armazenada em outras camadas da aplicação (por exemplo, camada de dados, como veremos a seguir).

[ViewModels](https://developer.android.com/topic/libraries/architecture/viewmodel) permitem que `composables` e outros componentes de UI acessem a lógica de domínio armazenadas em uma camada de dados da aplicação:
  - `ViewModels` sobrevivem a mais eventos do que `composables`:
    - Seguem o ciclo de vida de seu antecessor, por exemplo, da `Activity`;
    - Inclusive a trocas de telas, se assim for necessário;

É uma boa prática manter as Lógicas de UI e de Domínio separadas: mover para uma `ViewModel`;

Nesse commit, criamos uma nova `ViewModel`, chamada `MyTasksViewModel`:

<details>
<summary><code>MyTasksViewModel.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2Fc3d0f24b1a9395952265d218b6461e1a65aed8dc%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FMyTasksViewModel.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

Para que o projeto compile corretamente, é necessário adicionar uma nova dependência ao arquivo `app/build.gradle.kts`:
j

```kotlin
implementation("androidx.lifecycle:lifecycle-viewmodel-compose:{latest_version}")

// ou (depende da versão do android studio)

implementation(libs.androidx.lifecycle.viewmodel.compose)
```

<details>
<summary><code>build.gradle.kts</code></summary>
<br>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2Fc3d0f24b1a9395952265d218b6461e1a65aed8dc%2Fapp%2Fbuild.gradle.kts%23L42-60&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

##### Utilizando o ViewModel criado

Podemos acessar o `MyTasksViewModel` em qualquer `composable`, chamando [`viewModel()`](https://developer.android.com/reference/kotlin/androidx/lifecycle/viewmodel/compose/package-ummary#viewModel\(androidx.lifecycle.ViewModelStoreOwner,kotlin.String,androidx.lifecycle.ViewModelProvider.Factory,androidx.lifecycle.viewmodel.CreationExtras\)).:
- `viewModel()` retorna um `ViewModel` existente ou cria um novo no escopo atual;

No componente `MyTasks` adicionamos um parâmetro que chamado `myTasksViewModel`  que recebe como valor default o resultado de `viewModel()`;

<details>
<summary><code>MyTasks.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-06-mytasks%2Fblob%2Fc3d0f24b1a9395952265d218b6461e1a65aed8dc%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmytasks%2Fui%2Fmytasks%2FMyTasks.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

`myTasksViewModel` poderá ser permitirá ao composable acessar e manipular os dados desse ViewModel;

O ViewModel criado será mantido enquanto o seu escopo estiver vivo. Nesse caso, enquanto a Activity que possui `MyTasks` estiver viva;

---

