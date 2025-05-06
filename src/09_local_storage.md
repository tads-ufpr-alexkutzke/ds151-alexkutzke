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

### Exemplo simples

#### Entidades

```kotlin
@Entity
data class User(
    @PrimaryKey val uid: Int,
    @ColumnInfo(name = "first_name") val firstName: String?,
    @ColumnInfo(name = "last_name") val lastName: String?
)
```

#### DAO

```kotlin
@Dao
interface UserDao {
    @Query("SELECT * FROM user")
    fun getAll(): List<User>

    @Query("SELECT * FROM user WHERE uid IN (:userIds)")
    fun loadAllByIds(userIds: IntArray): List<User>

    @Query("SELECT * FROM user WHERE first_name LIKE :first AND " +
           "last_name LIKE :last LIMIT 1")
    fun findByName(first: String, last: String): User

    @Insert
    fun insertAll(vararg users: User)

    @Delete
    fun delete(user: User)
}
```

#### Database

```kotlin
@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```


#### Uso básico

Primeiro precisamos gerar o objeto da Database:

```kotlin
val db = Room.databaseBuilder(
            applicationContext,
            AppDatabase::class.java, "database-name"
        ).build()
```

Na sequência, utilizamos a instâncias `db` para acessar os dados através das DAOs disponíveis:

```kotlin
val userDao = db.userDao()
val users: List<User> = userDao.getAll()
```

## Aplicação exemplo: MoviesApp

A versão final da aplicação pode ser acessada no seguinte repositório:<br>
<https://github.com/tads-ufpr-alexkutzke/ds151-aula-09-movies-api-room-app>

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
│   │   ├── FavoriteMovieEntity.kt
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
  - `data/local/`: arquivos relativos ao armazenamento local de dados (*Database*, *DAO* e *Entity*);
- `model`: arquivos que definem os modelos dos dados utilizados. Em geral, determinam os tipos de dados relevantes para a aplicação;
- `network/`: arquivos relacionados à requisições para APIs. No caso, arquivos de definição do `Retrofit`;
- `ui/` arquivos da interface (`Composables` e `ViewModels`);
- `utils/`: arquivos auxiliares;

> [!IMPORTANT]
> Nesse projeto, utilizamos uma paradigma de repositórios para abstrair todo o acesso aos dados da aplicação. Por essa razão, uma
> certa quantidade de código *boilerplate* precisou ser adicionada (vide os arquivos de *providers*).
> Esse efeito pode ser sensivelmente diminuído através do uso de Injeção de Dependências (DI - *Dependencies Injection*). No desenvolvimento
> Android, isso geralmente é realizado com o auxílio da biblioteca [Hilt](https://developer.android.com/training/dependency-injection/hilt-android?hl=pt-br).

### Dependências

Algumas dependências são necessárias para o uso do Room (`app/build.gradle.kts`):

```kotlin
plugins {
    // ...
    id("com.google.devtools.ksp")
}

dependencies {

    // ...

    val room_version = "2.7.1"

    implementation("androidx.room:room-runtime:$room_version")

    ksp("androidx.room:room-compiler:$room_version")

    // ...

}
```

Atenção ao plugin `ksp` adicionado. Além disso, esse plugin deve ser adicionado, também, ao arquivo `build.gradle.kts` do projeto:

```kotlin
// Top-level build file where you can add configuration options common to all sub-projects/modules.
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
    alias(libs.plugins.kotlin.compose) apply false
    id("com.google.devtools.ksp") version "2.0.21-1.0.27" apply false
}
```

### Model

Antes de abordarmos a implementação do acesso aos dados locais, devemos conhecer quais são os tipos de dados utilizados na lógica da aplicação

#### Movie.kt

Define a classe Movie:

```kotlin
class Movie(
    val id: Int,
    val title: String,
    val cast: List<String>,
    val director: String,
    val synopsis: String,
    val posterUrl: String,
) {
}
```

<details>
<summary><code>Movie.kt</code></summary>

```kotlin
package com.example.moviesapp.model

class Movie(
    val id: Int,
    val title: String,
    val cast: List<String>,
    val director: String,
    val synopsis: String,
    val posterUrl: String,
) {
}

val fourMovies: List<Movie> = listOf(
    Movie(
        id = 1,
        title = "Um Sonho de Liberdade",
        cast = listOf("Tim Robbins", "Morgan Freeman", "William Sadler"),
        director = "Frank Darabont",
        synopsis = "Acusado injustamente de assassinato, Andy Dufresne encontra esperança e redenção na prisão de Shawshank.",
        posterUrl = "https://image.tmdb.org/t/p/w500/q6y0Go1tsGEsmtFryDOJo3dEmqu.jpg",
    ),
    Movie(
        id = 2,
        title = "O Poderoso Chefão",
        cast = listOf("Marlon Brando", "Al Pacino", "James Caan"),
        director = "Francis Ford Coppola",
        synopsis = "A trajetória da família mafiosa Corleone e os desafios enfrentados no submundo do crime.",
        posterUrl = "https://image.tmdb.org/t/p/w500/d4KNaTrltq6bpkFS01pYtyXa09m.jpg",
    ),
    Movie(
        id = 3,
        title = "O Poderoso Chefão II",
        cast = listOf("Al Pacino", "Robert De Niro", "Robert Duvall"),
        director = "Francis Ford Coppola",
        synopsis = "Expande a saga dos Corleone, explorando passado e presente da família mafiosa.",
        posterUrl = "https://image.tmdb.org/t/p/w500/amvmeQWheahG3StKwIE1f7jRnkZ.jpg",
    ),
    Movie(
        id = 4,
        title = "Batman: O Cavaleiro das Trevas",
        cast = listOf("Christian Bale", "Heath Ledger", "Aaron Eckhart"),
        director = "Christopher Nolan",
        synopsis = "O Coringa ameaça destruir Gotham, e Batman precisa lidar com caos e sacrifícios pessoais.",
        posterUrl = "https://image.tmdb.org/t/p/w500/qJ2tW6WMUDux911r6m7haRef0WH.jpg",
    ),
)

```

</details>

#### Review.kt

Define a classe Review:

```kotlin
class Review(
    val id: Int,
    val movieId: Int,
    val author: String,
    val reviewText: String,
    val rating: Int,
) {
}
```

<details>
<summary><code>Review.kt</code></summary>

```kotlin
package com.example.moviesapp.model

class Review(
    val id: Int,
    val movieId: Int,
    val author: String,
    val reviewText: String,
    val rating: Int,
) {
}

val fourReviews: List<Review> = listOf(
    Review(
        id = 1,
        movieId = 1,
        author = "João Silva",
        reviewText = "Uma história inspiradora sobre esperança, amizade e redenção. Atuações impecáveis de Tim Robbins e Morgan Freeman.",
        rating = 10
    ),
    Review(
        id = 2,
        movieId = 1,
        author = "Ana Souza",
        reviewText = "O filme emociona do começo ao fim, com uma narrativa envolvente e um final perfeito.",
        rating = 9
    ),
        Review(
        id = 3,
        movieId = 1,
        author = "Carlos Mendes",
        reviewText = "Roteiro cativante e personagens profundos. Um clássico absoluto do cinema.",
        rating = 10
    ),
)
```

</details>

### Data

Analisaremos os arquivos presentes na pasta `data/` do projeto. São, portanto, responsáveis por toda definição, acesso e abstração dos dados utilizados na aplicação.

#### Data/Local

Os arquivos presentes em `data/local` tem a função de definir e implementar o acesso aos dados locais, por meio do uso do *Room*.

##### FavoriteMovieEntity.kt

Este arquivo define a *Entity* que representa um filme favorito na base de dados local:

```kotlin
@Entity(tableName = "favorite_movies")
@TypeConverters(CastConverter::class)
data class FavoriteMovieEntity(
    @PrimaryKey val id: Int,
    val title: String,
    val cast: List<String>,
    val director: String,
    val synopsis: String,
    val posterUrl: String
)
```

<details>
<summary><code>FavoriteMovieEntity.kt</code></summary>

```kotlin
package com.example.moviesapp.data.local

import androidx.room.Entity
import androidx.room.PrimaryKey
import androidx.room.TypeConverters

@Entity(tableName = "favorite_movies")
@TypeConverters(CastConverter::class)
data class FavoriteMovieEntity(
    @PrimaryKey val id: Int,
    val title: String,
    val cast: List<String>,
    val director: String,
    val synopsis: String,
    val posterUrl: String
)
```

</details>

##### FavoriteMovieDao.kt

Agora temos a definição do DAO, responsável pelo acesso às entidades `FavoriteMovieEntity` na base de dados local:

```kotlin
@Dao
interface FavoriteMovieDao {
    @Query("SELECT * FROM favorite_movies")
    suspend fun getAll(): List<FavoriteMovieEntity>

    @Query("SELECT * FROM favorite_movies WHERE id = :movieId")
    suspend fun getById(movieId: Int): FavoriteMovieEntity?

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(movie: FavoriteMovieEntity)

    @Delete
    suspend fun delete(movie: FavoriteMovieEntity)
}
```



<details>
<summary><code>FavoriteMovieDao.kt</code></summary>

```kotlin
package com.example.moviesapp.data.local

import androidx.room.*

@Dao
interface FavoriteMovieDao {
    @Query("SELECT * FROM favorite_movies")
    suspend fun getAll(): List<FavoriteMovieEntity>

    @Query("SELECT * FROM favorite_movies WHERE id = :movieId")
    suspend fun getById(movieId: Int): FavoriteMovieEntity?

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(movie: FavoriteMovieEntity)

    @Delete
    suspend fun delete(movie: FavoriteMovieEntity)
}

```

</details>

##### FavoriteMoviesDatabase

Por fim, temos a definição da própria base de dados local, com `FavoriteMoviesDatabase`:

```kotlin
@Database(entities = [FavoriteMovieEntity::class], version = 1)
@TypeConverters(CastConverter::class)
abstract class FavoriteMoviesDatabase : RoomDatabase() {
    abstract fun favoriteMovieDao(): FavoriteMovieDao

    companion object {
        @Volatile private var INSTANCE: FavoriteMoviesDatabase? = null

        fun getInstance(context: Context): FavoriteMoviesDatabase {
            return INSTANCE ?: synchronized(this) {
                INSTANCE ?: Room.databaseBuilder(
                    context.applicationContext,
                    FavoriteMoviesDatabase::class.java,
                    "favorites.db"
                ).build().also { INSTANCE = it }
            }
        }
    }
}
```

Aqui vale notar como a estrutura de definição de um Database está mais complexa do que no exemplo inicial deste material.
Isso se deve ao fato de que o código acima garante que apenas um objeto de `FavoriteMoviesDatabase` seja instanciado para toda
a aplicação, não importando quantas vezes `getInstance` seja chamado.

Perceba, porém, que o código central ainda é uma simples chamada para `Room.databaseBuilder`:

```kotlin
                INSTANCE ?: Room.databaseBuilder(
                    context.applicationContext,
                    FavoriteMoviesDatabase::class.java,
                    "favorites.db"
                ).build().also { INSTANCE = it }
```


<details>
<summary><code>FavoriteMoviesDatabase.kt</code></summary>

```kotlin
package com.example.moviesapp.data.local

import android.content.Context
import androidx.room.Database
import androidx.room.Room
import androidx.room.RoomDatabase
import androidx.room.TypeConverters

@Database(entities = [FavoriteMovieEntity::class], version = 1)
@TypeConverters(CastConverter::class)
abstract class FavoriteMoviesDatabase : RoomDatabase() {
    abstract fun favoriteMovieDao(): FavoriteMovieDao

    companion object {
        @Volatile private var INSTANCE: FavoriteMoviesDatabase? = null

        fun getInstance(context: Context): FavoriteMoviesDatabase {
            return INSTANCE ?: synchronized(this) {
                INSTANCE ?: Room.databaseBuilder(
                    context.applicationContext,
                    FavoriteMoviesDatabase::class.java,
                    "favorites.db"
                ).build().also { INSTANCE = it }
            }
        }
    }
}
```

</details>

##### CastConverter.kt

Ainda na pasta `data/local` temos um arquivo utilitário, responsável por realizar uma simples conversão de dados. Essa conversão é necessária pois, um dos campos de `Movie` é a lista `cast: List<String>`. Assim, convertemos essa lista para uma String que poderá ser serializada no momento de recuperação desse dado:

```kotlin
class CastConverter {
    @TypeConverter
    fun fromList(list: List<String>): String = list.joinToString(separator = ";")
    @TypeConverter
    fun toList(str: String): List<String> =
        if (str.isEmpty()) emptyList() else str.split(";")
}
```


<details>
<summary><code>CastConverter.kt</code></summary>

```kotlin
package com.example.moviesapp.data.local
 
import androidx.room.TypeConverter
 
class CastConverter {
    @TypeConverter
    fun fromList(list: List<String>): String = list.joinToString(separator = ";")
    @TypeConverter
    fun toList(str: String): List<String> =
        if (str.isEmpty()) emptyList() else str.split(";")
}
```

</details>

### Data - Arquivos de repositories e providers

O restante da pasta `data/` define nossos *repositories* e *providers*.

Um repository é uma camada de abstração responsável por gerenciar o acesso a diferentes fontes de dados, como banco de dados local (Room), serviços de rede (API), ou arquivos. Ele centraliza a lógica de obtenção, armazenamento e atualização de dados, oferecendo uma interface única para as demais partes do app (por exemplo, ViewModels). O uso do repository facilita a manutenção, os testes e a reutilização do código.

Um provider, por sua vez, é uma classe responsável por criar e/ou fornecer uma única instância de um objeto, geralmente utilizando o padrão *Singleton*. Por exemplo, um provider pode ser usado para produzir uma instância única do repository ao longo de toda a aplicação, garantindo que todos os componentes utilizem o mesmo objeto compartilhado. Essa abordagem facilita o gerenciamento do ciclo de vida das dependências e promove a reutilização e consistência no acesso aos recursos.

No caso da aplicação exemplo, temos repositories e providers para acesso remoto (API) e acesso local (Room).

##### Repository local

```kotlin
class LocalFavoriteMoviesRepository(private val dao: FavoriteMovieDao) {
    suspend fun getAllFavorites(): List<FavoriteMovieEntity> = dao.getAll()
    suspend fun getFavorite(movieId: Int): FavoriteMovieEntity? = dao.getById(movieId)
    suspend fun isFavorite(movieId: Int): Boolean = dao.getById(movieId) != null
    suspend fun addFavorite(movie: FavoriteMovieEntity) = dao.insert(movie)
    suspend fun removeFavorite(movie: FavoriteMovieEntity) = dao.delete(movie)
}
```


<details>
<summary><code>LocalFavoriteMoviesRepository.kt</code></summary>

```kotlin
package com.example.moviesapp.data

import com.example.moviesapp.data.local.FavoriteMovieEntity
import com.example.moviesapp.data.local.FavoriteMovieDao

class LocalFavoriteMoviesRepository(private val dao: FavoriteMovieDao) {
    suspend fun getAllFavorites(): List<FavoriteMovieEntity> = dao.getAll()
    suspend fun getFavorite(movieId: Int): FavoriteMovieEntity? = dao.getById(movieId)
    suspend fun isFavorite(movieId: Int): Boolean = dao.getById(movieId) != null
    suspend fun addFavorite(movie: FavoriteMovieEntity) = dao.insert(movie)
    suspend fun removeFavorite(movie: FavoriteMovieEntity) = dao.delete(movie)
}
```

</details>

##### Provider local

```kotlin
object LocalFavoriteMoviesRepositoryProvider {

    @Volatile private var instance: LocalFavoriteMoviesRepository? = null

    fun getRepository(context: Context): LocalFavoriteMoviesRepository {
        return instance ?: synchronized(this) {
            instance ?: LocalFavoriteMoviesRepository(
                FavoriteMoviesDatabase.getInstance(context).favoriteMovieDao()
            ).also { instance = it }
        }
    }
}

```

<details>
<summary><code>LocalFavoriteMoviesRepositoryProvider.kt</code></summary>

```kotlin
package com.example.moviesapp.data

import android.content.Context
import com.example.moviesapp.data.local.FavoriteMoviesDatabase

object LocalFavoriteMoviesRepositoryProvider {

    @Volatile private var instance: LocalFavoriteMoviesRepository? = null

    fun getRepository(context: Context): LocalFavoriteMoviesRepository {
        return instance ?: synchronized(this) {
            instance ?: LocalFavoriteMoviesRepository(
                FavoriteMoviesDatabase.getInstance(context).favoriteMovieDao()
            ).also { instance = it }
        }
    }
}
```

</details>

##### Repository remoto

```kotlin
class RemoteMoviesRepository(
    private val apiService: MoviesApiService
) {
    suspend fun getMovies(): List<Movie> {
        return apiService.getMovies()
    }
 
    suspend fun getMovie(id: Int): Movie {
        return apiService.getMovie(id)
    }
 
    suspend fun getReviews(movieId: Int): List<Review> {
        return apiService.getReviews(movieId)
    }
}
```

<details>
<summary><code>RemoteMoviesRepository.kt</code></summary>

```kotlin
package com.example.moviesapp.data
 
import com.example.moviesapp.network.MoviesApiService
import com.example.moviesapp.model.Movie
import com.example.moviesapp.model.Review
 
class RemoteMoviesRepository(
    private val apiService: MoviesApiService
) {
    suspend fun getMovies(): List<Movie> {
        return apiService.getMovies()
    }
 
    suspend fun getMovie(id: Int): Movie {
        return apiService.getMovie(id)
    }
 
    suspend fun getReviews(movieId: Int): List<Review> {
        return apiService.getReviews(movieId)
    }
}
```

</details>

##### Provider remoto

```kotlin
object RemoteMoviesRepositoryProvider {
    val repository: RemoteMoviesRepository by lazy {
        RemoteMoviesRepository(MoviesApi.retrofitService)
    }
}
```

<details>
<summary><code>RemoteMoviesRepositoryProvider.kt</code></summary>

```kotlin
package com.example.moviesapp.data
 
import com.example.moviesapp.network.MoviesApi
 
object RemoteMoviesRepositoryProvider {
    val repository: RemoteMoviesRepository by lazy {
        RemoteMoviesRepository(MoviesApi.retrofitService)
    }
}
```

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
