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

