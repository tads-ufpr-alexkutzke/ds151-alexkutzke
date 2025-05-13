# Especificação Trabalho Prático

**Tema:** Desenvolvimento de uma Aplicação Android para Gestão de Tarefas

**Objetivo:** Criar uma aplicação Android que permita aos usuários gerenciar suas tarefas diárias, integrando conhecimentos de arquitetura e testes de aplicações Android.

**Descrição do Projeto:**
Os alunos devem desenvolver uma aplicação Android com as seguintes funcionalidades e requisitos:

Este trabalho poderá ser realizado em grupos de **até 4 estudantes**.

## Funcionalidades Básicas

1. **Autenticação**:
    - Autenticação com Firebase Authentication;

2. **Persistência de dados**:
    - Persistência de dados com Firebase Firestore ou Room Database;

3. **Modelo de dados**:
    - Tarefas devem ter, ao menos, as seguintes informações:
      - Título;
      - Descrição;
      - Data limite;
      - Prioridade;
      - Status;
    - Tarefas podem ter subtarefas;
    - Uma tarefa pode ser iniciada, pausada e concluída;
    - Uma tarefa pode ser concluída diretamente, sem precisar sem iniciada;

4. **Funcionalidades gerais**:
    - Permitir criação, edição e remoção de tarefas;
    - Notificar usuários sobre tarefas com data limite próxima;
    - Apresentar tempo utilizado para concluir uma tarefa;

5. **Requisito de interface:**
    - Utilizar Jetpack Compose para criação das telas.
    - O layout da aplicação e a quantidade de telas é livre.

6. **Filtro e Ordenação:**
   - Permitir filtragem por prioridade e status.
   - Ordenação por data de conclusão.

## Funcionalidades Adicionais (desejáveis, porém, opcionais)

1. **Tema Escuro/Claro:**
   - Configurar alternância entre temas escuro e claro.

2. **Pesquisa de Tarefas:**
   - Implementar barra de pesquisa para encontrar tarefas rapidamente.

## Sugestões para Telas da Aplicação

1. **Tela de Autenticação:**
   - **Login e Registro:** Usar Firebase Authentication.

2. **Tela de Dashboard:**
   - **Resumo de Tarefas:** Exibir contagem de tarefas pendentes, concluídas e totais.
   - **Listagem de Tarefas:** Mostrar as tarefas em formato de lista.

3. **Tela de Detalhes da Tarefa:**
   - **Visualizar Detalhes:** Título, descrição, data, prioridade e status.
   - **Botões de Ação:** Editar ou excluir a tarefa.

4. **Tela de Criação/Edição de Tarefa:**
   - **Entrada de Dados:** Título, descrição, data de conclusão, prioridade.
   - **Salvar/Atualizar Tarefa:** Persistir dados no Firebase Firestore ou Room Database.

5. **Tela de Notificações:**
   - **Gerenciar Notificações:** Ativar/desativar lembretes para tarefas específicas.

6. **Tela de Perfil do Usuário:**
   - **Editar Perfil:** Nome do usuário, e-mail e outros dados relevantes.
   - **Sair da Conta:** Logout da aplicação.

## Estrutura da Aplicação

- **Arquitetura:** Utilizar MVVM para uma separação clara de responsabilidades.
- **Banco de Dados:** Firebase Firestore ou Room Database para persistência.

## Entregáveis

1. **Código-Fonte:**
   - Repositório do GitLab com código bem documentado.

2. **Documentação Técnica:** (no próprio repositório)
   - Instruções para instalação, execução e detalhes sobre arquitetura utilizada.

3. **Vídeo de funcionamento**:
   - Video apresentando a aplicação em funcionamento.
   - Este vídeo ficará disponível para que todos da turma conheçam os aplicativos de outros grupos.

## Defesa do trabalho
   - Apresentação final destacando funcionalidades e processo de desenvolvimento.
   - Apenas para o professor.
   - Aplicação deve funcionar em um dispositivo físico.

## Critérios de Avaliação

A avaliação será em duas partes.

1. **Avaliação final do projeto e funcionalidade** (70% da nota):
    - **Funcionalidade Completa:** Implementação de todos os requisitos.
    - **Qualidade do Código:** Uso correto da arquitetura MVVM e boas práticas.
    - **Interface do Usuário:** Design coerente com Material Design.
    - **Documentação e código fonte:** Clareza e cobertura ampla das informações técnicas e bom gerenciamento do versionamento do código.

2. **Avaliação do processo de desenvolvimento** (30% da nota):
    - 4x checkpoints nas aulas dos dias 21/05, 28/05, 04/06 e 11/06: 

Os **checkpoints** poderão ser realizados de **duas formas**:

  - **Commits regulares** antes de cada data de checkpoint (professor irá avaliar commits realizados no repositório).
  - **Reuniões presenciais** no horário da aula;

Cada equipe **poderá decidir**, semanalmente, a forma de checkpoint que deseja.

## Repositório para o projeto

O repositório base para o projeto final é o seguinte:

<https://gitlab.com/ds151-alexkutzke/ds151-project-2025-1>

Cada equipe deve fazer apenas 1 fork do projeto acima.

Os membros da equipe devem ser adicionados pela interface do Gitlab como desenvolvedores no projeto.
O arquivo README deverá conter o nome dos integrantes do grupo.

O fork de cada equipe deverá ser realizado **antes da primeira data de checkpoint**.
