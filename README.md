#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Estrutura para armazenar os dados de uma pessoa
typedef struct {
    char nome[50];
    int idade;
    char cpf[12];
} Pessoa;

// Função para adicionar um novo cadastro
Pessoa* adicionarCadastro(Pessoa* cadastros, int* tamanho, int* capacidade) {
    // Redimensionar o array, se necessário
    if (*tamanho == *capacidade) {
        *capacidade = (*capacidade == 0) ? 1 : *capacidade * 2;
        cadastros = realloc(cadastros, (*capacidade) * sizeof(Pessoa));
        if (!cadastros) {
            printf("Erro ao alocar memória!\n");
            exit(1);
        }
    }
    // Preencher os dados da nova pessoa
    printf("Digite o nome: ");
    scanf(" %[^\n]", cadastros[*tamanho].nome);
    do {
        printf("Digite a idade: ");
        scanf("%d", &cadastros[*tamanho].idade);
        if (cadastros[*tamanho].idade <= 0) {
            printf("A idade deve ser positiva!\n");
        }
    } while (cadastros[*tamanho].idade <= 0);

    do {
        printf("Digite o CPF (11 caracteres): ");
        scanf("%s", cadastros[*tamanho].cpf);
        if (strlen(cadastros[*tamanho].cpf) != 11) {
            printf("O CPF deve ter exatamente 11 caracteres!\n");
        }
    } while (strlen(cadastros[*tamanho].cpf) != 11);

    (*tamanho)++;
    return cadastros;
}

// Função para listar todos os cadastros
void listarCadastros(Pessoa* cadastros, int tamanho) {
    if (tamanho == 0) {
        printf("Nenhum cadastro encontrado.\n");
        return;
    }
    printf("\n=== Lista de Cadastros ===\n");
    for (int i = 0; i < tamanho; i++) {
        printf("Cadastro %d:\n", i + 1);
        printf("Nome: %s\n", cadastros[i].nome);
        printf("Idade: %d\n", cadastros[i].idade);
        printf("CPF: %s\n", cadastros[i].cpf);
        printf("--------------------------\n");
    }
}

// Função para deletar um cadastro pelo índice
Pessoa* deletarCadastro(Pessoa* cadastros, int* tamanho, int* capacidade, int indice) {
    if (indice < 0 || indice >= *tamanho) {
        printf("Índice inválido!\n");
        return cadastros;
    }
    for (int i = indice; i < *tamanho - 1; i++) {
        cadastros[i] = cadastros[i + 1];
    }
    (*tamanho)--;

    // Reduzir capacidade, se necessário
    if (*tamanho < *capacidade / 2) {
        *capacidade /= 2;
        cadastros = realloc(cadastros, (*capacidade) * sizeof(Pessoa));
        if (!cadastros && *tamanho > 0) {
            printf("Erro ao realocar memória!\n");
            exit(1);
        }
    }
    return cadastros;
}

int main() {
    Pessoa* cadastros = NULL; // Ponteiro para array dinâmico de cadastros
    int tamanho = 0;         // Número de cadastros atuais
    int capacidade = 0;      // Capacidade atual do array

    int opcao;
    do {
        printf("\n=== Sistema de Cadastro ===\n");
        printf("1. Adicionar Cadastro\n");
        printf("2. Listar Cadastros\n");
        printf("3. Deletar Cadastro\n");
        printf("4. Sair\n");
        printf("Escolha uma opcao: ");
        scanf("%d", &opcao);

        switch (opcao) {
            case 1:
                cadastros = adicionarCadastro(cadastros, &tamanho, &capacidade);
                break;
            case 2:
                listarCadastros(cadastros, tamanho);
                break;
            case 3: {
                int indice;
                printf("Informe o índice do cadastro a deletar: ");
                scanf("%d", &indice);
                cadastros = deletarCadastro(cadastros, &tamanho, &capacidade, indice - 1);
                break;
            }
            case 4:
                printf("Saindo...\n");
                break;
            default:
                printf("Opção inválida! Tente novamente.\n");
        }
    } while (opcao != 4);

    // Liberar a memória alocada antes de sair
    free(cadastros);

    return 0;
}
