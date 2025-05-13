# 10 - Arquitetura de Aplicações Android e Outros Conceitos Importantes

## Considerações Iniciais

- **Limitações de Recursos:** Dispositivos móveis podem encerrar processos de aplicativos para liberar recursos.
- **Arquitetura de Aplicativos:** Define os limites e responsabilidades de cada parte do aplicativo.

## Princípios Fundamentais

### Separação de Responsabilidades

- Evitar concentrar todo o código em uma Activity ou Fragment.

### Interface Guiada por Modelos de Dados

- A UI deve ser baseada em modelos de dados, preferencialmente persistentes.
- Benefícios: Aplicativos mais testáveis e robustos.

### Fonte Única de Verdade

- Designar uma única fonte de verdade (SSOT) para cada tipo de dado.
- Centraliza mudanças, protege dados contra alterações indevidas e facilita o rastreamento de bugs.

### Fluxo de Dados Unidirecional

- Utiliza-se o padrão de Fluxo de Dados Unidirecional (UDF) onde o estado flui em uma direção e eventos na direção oposta.
  
## Arquitetura Recomendada

### Camadas do Aplicativo

1. **Camada de UI:** Exibe os dados na tela.
2. **Camada de Dados:** Contém a lógica de negócios e expõe os dados.
   
![Arquitetura Típica](https://developer.android.com/static/topic/libraries/architecture/images/mad-arch-overview.png)

### Técnicas Modernas

- Arquitetura reativa e em camadas.
- Fluxo de Dados Unidirecional (UDF).
- Uso de Coroutines e Flows.
- Injeção de dependência.

## Camadas Detalhadas

### Camada de UI

- **Elementos de UI:** Renderizam os dados.
- **Portadores de Estado:** Mantêm e gerenciam os dados do UI (exemplo: ViewModel).

![Camada de UI](https://developer.android.com/static/topic/libraries/architecture/images/mad-arch-overview-ui.png)

### Camada de Dados

- **Repositórios:** Expõem e centralizam dados, resolvem conflitos entre fontes e contêm lógica de negócios.

![Camada de Dados](https://developer.android.com/static/topic/libraries/architecture/images/mad-arch-overview-data.png)

### Camada de Domínio (Opcional)

- **Função:** Encapsula lógica de negócios complexa que pode, por exemplo, ser reutilizada por vários ViewModels.
  
![Camada de Domínio](https://developer.android.com/static/topic/libraries/architecture/images/mad-arch-overview-domain.png)

## Injeção de Dependência

- **Injeção de Dependência (DI):** Permite definir dependências sem construi-las diretamente. Durante a execução, outra classe é responsável por prover essas dependências.
- **Service Locator:** Padrão que fornece um registro para obter dependências. Ou seja, durante a execução, classes podem consultar o registro para obter suas dependências.

A biblioteca [Hilt](https://developer.android.com/training/dependency-injection/hilt-android) é indicada para o uso de DI em projetos Android:
  - [Exemplo de aplicação no aplicativo Movies App](https://github.com/tads-ufpr-alexkutzke/ds151-aula-09-movies-api-room-app/compare/main...hilt-implementation)

## Melhores Práticas Gerais

- Não armazene dados em componentes de aplicativos.
- Reduza dependências em classes Android (seus componentes devem ser os únicos a dependerem de Apis do Android, como o `Context`).
- Crie limites bem definidos entre módulos.
- Minimize a exposição de cada módulo.
- Testabilidade e segurança de tipos.

## Benefícios da Arquitetura

- **Manutenção e Qualidade:** Melhora a robustez geral do aplicativo.
- **Escalabilidade:** Facilita a contribuição de múltiplas pessoas e equipes.
- **Onboarding:** Consistência simplifica a adaptação de novos integrantes.
- **Testabilidade:** Arquitetura favorece tipos simples e testáveis.

---

## Recomendações para Arquitetura de Aplicações Android

<https://developer.android.com/topic/architecture/recommendations>

---

## Outros conteúdos

### Distribuição de Aplicativos

- **APKs Otimizados:** No Google Play, servidores geram APKs otimizados contendo apenas recursos necessários para o dispositivo que solicita a instalação.
  
### Sistema Operacional Android

- **Sistema Multiusuário:** Android é um sistema multiusuário baseado em Linux; cada app é um usuário diferente.
- **Permissões de Arquivos:** Cada app tem permissões para acessar apenas seus próprios arquivos.
- **Processo Isolado:** Cada app executa em seu próprio processo Linux e máquina virtual, garantindo isolamento.
- **Princípio do Menor Privilégio:** Apps acessam apenas componentes necessários para sua execução.

### Permissões

- **Solicitação de Permissões:** Apps podem solicitar acesso a dados do dispositivo como localização e câmera, mas o usuário deve conceder explicitamente essas permissões. [Mais informações sobre permissões](https://developer.android.com/training/permissions).

![Fluxo de alto nível para utilização de permissões no Android.](https://developer.android.com/static/images/training/permissions/workflow-overview.svg)

### Tipos de Componentes de Aplicativos

1. **Activities**
   - Ponto de entrada para interação com o usuário, representando uma tela.
   - Diferentes apps podem iniciar activities se permitido.

2. **Services**
   - Executa operações em segundo plano sem interface de usuário.
   - Ex.: Tocar música, buscar dados na rede.

3. **Broadcast Receivers**
   - Permite receber eventos do sistema e reagir a eles.
   - Ex.: Notificação de eventos futuros.

4. **Content Providers**
   - Gerencia dados entre aplicativos.
   - Por exemplo o sistema Android expõe um Content Provider que gerencia informações de contato do usuário. Qualquer Aplicação com permissões suficientes pode gerenciar essas informações;
   - Acesso permitido a apps com as permissões corretas.

### Interação entre Aplicativos

- **Início de Componentes de Outros Apps:** Apps podem iniciar componentes de outros apps sem incorporar código de terceiros.
- **Intents:** Mensagens assíncronas que ativam três dos quatro tipos de componentes (activities, services, receivers):
  - Por exemplo, uma aplicação pode enviar uma `Intent` para o aplicativo nativo de câmera para capturar uma foto.
  - Nesse caso, não é necessário implementar um acesso à câmera. Basta utilizar a tela do aplicativo nativo.
  - Esse relacionamento pode ser obtido com qualquer aplicativo que exponha `Intents` para acesso aos seus recursos.
- **Manifesto do Aplicativo:** Arquivo `AndroidManifest.xml` declara componentes e permissões de uso.


[Exemplo de `Intent` para compartilhar um filme na Aplicação Movies App](https://github.com/tads-ufpr-alexkutzke/ds151-aula-09-movies-api-room-app/compare/hilt-implementation...intent).

### Declaração de Componentes

- **Elementos no Manifesto:**
  - [`<activity>`](https://developer.android.com/guide/topics/manifest/activity-element) para activities
  - [`<service>`](https://developer.android.com/guide/topics/manifest/service-element) para services
  - [`<receiver>`](https://developer.android.com/guide/topics/manifest/receiver-element) para broadcast receivers
  - [`<provider>`](https://developer.android.com/guide/topics/manifest/provider-element) para content providers

- **Declaração de Câmera:**
  - Apps declaram recursos de hardware, como câmera, no arquivo `AndroidManifest.xml`.
  - Permite especificar se o recurso é necessário ou opcional.
