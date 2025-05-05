# 09 - Persistência de dados local com Room


## Room

O Room é uma **biblioteca de persistência de dados** do Android que facilita o armazenamento e o gerenciamento de dados locais **em bancos de dados SQLite**. 

Ele fornece uma **camada de abstração** sobre o SQLite, permitindo que os desenvolvedores definam entidades, consultem e manipulem dados utilizando objetos e métodos Java/Kotlin de forma simples, segura e eficiente. 

Com o Room, é possível manter o **código mais organizado, evitar erros comuns de SQL** e aproveitar recursos como verificação de consultas em tempo de compilação e integração direta com o ciclo de vida do aplicativo.


### Componentes Principais do Room

O Room possui três componentes principais:

- Classe de banco de dados: Gerencia o banco de dados e é o principal ponto de acesso para os dados persistidos do aplicativo.
- Entidades de dados: Representam as tabelas do banco de dados do app.
- DAOs (Data Access Objects): Fornecem métodos para consultar, atualizar, inserir e deletar dados no banco.

A classe de banco de dados fornece instâncias dos DAOs, que permitem ao app acessar e manipular os dados como objetos correspondentes às entidades definidas. Dessa forma, o app pode recuperar, inserir ou atualizar informações nas tabelas do banco com facilidade.

![Arquitetura Room](https://developer.android.com/static/images/training/data-storage/room_architecture.png)

## Aplicação exemplo: MoviesApp

A versão final da aplicação pode ser acessada no seguinte repositório:<br>
<https://github.com/tads-ufpr-alexkutzke/ds151-aula-08-movies-api-app/>

A aplicação exemplo segue o desenvolvimento da última aula, com a adição de armazenamento local com Room.
O objetivo da aplicação é que o usuário possa salvar localmente os dados dos filmes marcados como favoritos. Esses dados devem ser acessíveis mesmo sem acesso à internet.

A organização dos arquivos nessa aplicação foi melhorada. Agora temos arquivos separados em pastas que indicam suas funções. A estrutura é a seguinte:

```
app/src/main/java/com/example/moviesapp/
├── AppContextHolder.kt
├── Application.kt
├── data
│   ├── local
│   │   ├── CastConverter.kt
│   │   ├── FavoriteMovieDao.kt
│   │   ├── FavoriteMovieEntry.kt
│   │   └── FavoriteMoviesDatabase.kt
│   ├── LocalFavoriteMoviesRepository.kt
│   ├── LocalFavoriteMoviesRepositoryProvider.kt
│   ├── RemoteMoviesRepository.kt
│   └── RemoteMoviesRepositoryProvider.kt
├── MainActivity.kt
├── model
│   ├── Movie.kt
│   └── Review.kt
├── network
│   └── MoviesApiService.kt
├── ui
│   ├── moviesapp
│   │   ├── ApiComposables.kt
│   │   ├── MovieDetailsScreen.kt
│   │   ├── MovieDetailsViewModel.kt
│   │   ├── MovieItem.kt
│   │   ├── MoviesList.kt
│   │   ├── MoviesListViewModel.kt
│   │   └── MoviesScreen.kt
│   ├── MoviesApp.kt
│   └── theme
│       ├── Color.kt
│       ├── Theme.kt
│       └── Type.kt
└── utils
    └── MapperExtensions.kt
```

O conteúdo de cada pasta é o seguinte:

- `data/`: arquivos relativos a repositórios de dados, sejam locais ou remotos. Aqui armazenamos os itens conhecidos como Repositórios;
  - `data/local/`: arquivos relativos ao armazenamento local de dados (*Database*, *DAO* e *Entry*);
- `model`: arquivos que definem os modelos dos dados utilizados. Em geral, determinam os tipos de dados relevantes para a aplicação;
- `network/`: arquivos relacionados à requisições para APIs. No caso, arquivos de definição do `Retrofit`;
- `ui/` arquivos da interface (`Composables` e `ViewModels`);
- `utils/`: arquivos auxiliares;

## Data
<details>
<summary><code>LocalFavoriteMoviesRepository.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fdata%2FLocalFavoriteMoviesRepository.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>LocalFavoriteMoviesRepositoryProvider.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fdata%2FLocalFavoriteMoviesRepositoryProvider.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>RemoteMoviesRepository.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fdata%2FRemoteMoviesRepository.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>RemoteMoviesRepositoryProvider.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fdata%2FRemoteMoviesRepositoryProvider.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

### Data/Local
<details>
<summary><code>FavoriteMovieEntry.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fdata%2Flocal%2FFavoriteMovieEntry.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>FavoriteMovieDao.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fdata%2Flocal%2FFavoriteMovieDao.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

<details>
<summary><code>CastConverter.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fdata%2Flocal%2FCastConverter.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>FavoriteMoviesDatabase.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fdata%2Flocal%2FFavoriteMoviesDatabase.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

## Model

<details>
<summary><code>Movie.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fmodel%2FMovie.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>Review.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fmodel%2FReview.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

## Network

<details>
<summary><code>MoviesApiService.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fnetwork%2FMoviesApiService.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

## Utils

<details>
<summary><code>MapperExtensions.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Futils%2FMapperExtensions.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

## Ui

<details>
<summary><code>MoviesApp.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2FMoviesApp.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

### ui/moviesapp

<details>
<summary><code>MoviesScreen.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesScreen.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MoviesListViewModel.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesListViewModel.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MovieDetailsScreen.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMovieDetailsScreen.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MovieDetailsViewModel.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMovieDetailsViewModel.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MoviesList.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMoviesList.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>MovieItem.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FMovieItem.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>ApiComposables.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2Fui%2Fmoviesapp%2FApiComposables.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>

## App

<details>
<summary><code>MainActivity.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2FMainActivity.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>Application.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2FApplication.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
<details>
<summary><code>AppContextHolder.kt</code></summary>
<iframe frameborder="0" scrolling="yes" style="width:100%; height:478px;" allow="clipboard-write" src="https://emgithub.com/iframe.html?target=https%3A%2F%2Fgithub.com%2Ftads-ufpr-alexkutzke%2Fds151-aula-09-movies-api-room-app%2Fblob%2F4fc1caa0c4c59250f7cc888d67dc1fe27b9fbe5b%2Fapp%2Fsrc%2Fmain%2Fjava%2Fcom%2Fexample%2Fmoviesapp%2FAppContextHolder.kt&style=default&type=code&showBorder=on&showLineNumbers=on&showFileMeta=on&showFullPath=on&showCopy=on"></iframe>

</details>
