# Especificação Trabalho Prático

**Tema:** Desenvolvimento de uma Aplicação Android para Gestão de Tarefas

**Objetivo:** Criar uma aplicação Android que permita aos usuários gerenciar suas tarefas diárias, integrando conhecimentos de arquitetura e testes de aplicações Android.

**Descrição do Projeto:**
Os alunos devem desenvolver uma aplicação Android com as seguintes funcionalidades e requisitos:

## Funcionalidades Básicas

1. **Autenticação**:
    - Autenticação com Firebase Authentication;

2. **Persistência de dados**:
    - Persistência de dados com Firebase Firestore ou Room Database;

3. **Modelo de dados**:
    - Tarefas devem ter, ao menos, as seguintes informações:
      - Título;
      - Descrição;
      - Data;
      - Prioridade;
      - Status;
    - Tarefas podem ter subtarefas;

4. **Funcionalidades gerais**:
    - Permitir criação, edição e remoção de tarefas;
    - Notificar usuários sobre tarefas com data próxima;

5. **Requisito de interface:**
    - Utilizar Jetpack Compose para criação das telas.

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
   - **Editar Perfil:** Nome do usuário, e-mail (verificação de e-mail única).
   - **Sair da Conta:** Logout da aplicação.

## Estrutura da Aplicação

- **Arquitetura:** Utilizar MVVM para uma separação clara de responsabilidades.
- **Banco de Dados:** Firebase Firestore ou Room Database para persistência.

## Entregáveis

1. **Código-Fonte:**
   - Repositório do GitHub/GitLab com código bem documentado.

2. **Documentação Técnica:** (no próprio repositório)
   - Instruções para instalação, execução e detalhes sobre arquitetura.

3. **Apresentação:**
   - Apresentação final destacando funcionalidades e processo de desenvolvimento.

## Critérios de Avaliação

- **Funcionalidade Completa:** Implementação de todos os requisitos.
- **Qualidade do Código:** Uso correto da arquitetura MVVM e boas práticas.
- **Interface do Usuário:** Design coerente com Material Design.
- **Documentação e código fonte:** Clareza e cobertura ampla das informações técnicas e bom gerenciamento do versionamento do código.

- 3x Checkouts ou reuniões presenciais mensais;
- Código no gitlab (criar repositório como exercício)
- Decidir entre prova+trabalho ou apenas trabalho
