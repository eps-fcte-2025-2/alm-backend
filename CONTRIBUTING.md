# Guia de Contribuição

Este documento é o guia central para contribuir com este projeto. Segui-lo é essencial para mantermos a qualidade do nosso código, a agilidade no desenvolvimento e um histórico de alterações limpo e compreensível.

## Como Começar (Configuração do Ambiente)

Para garantir que todos estejam trabalhando no mesmo ambiente, siga estes passos:

1.  **Clone o Repositório:**
    ```bash
    git clone alm-backend
    cd alm-backend
    ```

2.  **Instale as Dependências:**
    (Use o comando relevante para o seu projeto)
    ```bash
    pip install -r requirements.txt
    ```

## Encontrando o que Fazer

Toda nova funcionalidade ou correção de bug deve começar a partir de uma **Issue**.

1.  **Vá para a aba "Issues"** do nosso repositório no GitHub.
2.  **Procure por uma Issue** que não esteja atribuída a ninguém.
3.  **Atribua a Issue a si mesmo** (ou peça para o Tech Lead/Gerente de Projeto atribuir).
4.  Se a tarefa não estiver lá, crie uma nova Issue usando os nossos templates (Bug Report ou Feature Request).

## O Ciclo de Desenvolvimento (Nosso "Git Flow")

Nós usamos um fluxo de trabalho simples: todo trabalho é feito em *feature branches* que saem da branch principal.

**Branch Principal:** `main` (ou `master`)

### 1. Crie sua Branch

Sempre comece da versão mais atualizada da `main`.

```bash
# 1. Vá para a branch principal
git checkout main

# 2. Baixe as atualizações mais recentes
git pull origin main

# 3. Crie sua nova branch
git checkout -b [NOME_DA_BRANCH]
```
#### 1.1 Padrão de Nomenclatura de Branches

Use o seguinte padrão para nomear suas branches. Isso nos ajuda a identificar o que está acontecendo:

    Features: feature/[NOME_DA_FEATURE] (ex: feature/login-com-google)

    Bugfixes: bugfix/[DESCRICAO_DO_BUG] (ex: bugfix/validacao-de-email-quebrada)

    Hotfixes (Urgente): hotfix/[CORRECAO_URGENTE] (ex: hotfix/corrigir-crash-no-checkout)

    Outros: docs/, refactor/, chore/

### 2. Faça Commits Semânticos

Seus commits devem ser pequenos, atômicos (fazer apenas uma coisa) e seguir o padrão Conventional Commits.

Formato: tipo(escopo): descrição curta

    feat: (Nova funcionalidade)

    fix: (Correção de bug)

    docs: (Mudanças na documentação)

    style: (Formatação, ponto e vírgula, etc. - sem mudança lógica)

    refactor: (Refatoração de código que não altera o comportamento)

    test: (Adicionando ou corrigindo testes)

    chore: (Atualização de dependências, tarefas de build, etc.)

### 3. Revisão de Código (Code Review)

Nenhum código entra na main sem revisão.

Para o Autor do PR

    Marque Revisores: Adicione pelo menos 1 ou 2 membros da equipe como revisores (Reviewers).

    Aguarde a Aprovação: Não faça o "merge" do seu próprio PR.

    Receba Feedback: Se um revisor solicitar alterações, faça-as em novos commits na mesma branch e peça uma nova revisão.
