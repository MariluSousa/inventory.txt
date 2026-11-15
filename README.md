#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main() {
    int opcao;
    char continuar = 's';

    do {
        printf("================================\n");
        printf("   Controle de Estoque\n");
        printf("================================\n");
        printf("Selecione uma opção:\n");
        printf("1. Adicionar Item\n");
        printf("2. Remover Item\n");
        printf("3. Listar Estoque\n");
        printf("4. Sair\n");
        printf("Opção: ");
        
        if (scanf("%d", &opcao) != 1) {
            while (getchar() != '\n');
            printf("Opção inválida! Tente novamente.\n\n");
            continue;
        }

        if (opcao == 1) {
            char nome[100];
            int qtd;

            printf("Digite o nome do item: ");
            scanf(" %[^\n]", nome);
            printf("Digite a quantidade: ");
            scanf("%d", &qtd);

            FILE *f = fopen("inventory.txt", "a");
            fprintf(f, "%s\n%d\n", nome, qtd);
            fclose(f);

            printf("Item adicionado com sucesso!\n");

        } else if (opcao == 2) {
            char busca[100];
            int qtdRemover;

            printf("Digite o nome do item: ");
            scanf(" %[^\n]", busca);
            printf("Digite a quantidade a ser removida: ");
            scanf("%d", &qtdRemover);

            FILE *f = fopen("inventory.txt", "r");
            if (f == NULL) {
                printf("Item não encontrado.\n");
            } else {
                FILE *temp = fopen("temp.txt", "w");
                char nome[100];
                int qtd;
                int achou = 0;

                while (fscanf(f, " %[^\n]", nome) == 1) {
                    fscanf(f, "%d", &qtd);

                    if (strcmp(nome, busca) == 0) {
                        achou = 1;
                        if (qtdRemover > qtd) {
                            printf("Estoque insuficiente. Quantidade disponível: %d\n", qtd);
                            fprintf(temp, "%s\n%d\n", nome, qtd);
                        } else {
                            qtd -= qtdRemover;
                            if (qtd > 0) {
                                fprintf(temp, "%s\n%d\n", nome, qtd);
                                printf("Quantidade atualizada com sucesso!\n");
                            } else {
                                printf("Item removido do estoque!\n");
                            }
                        }
                    } else {
                        fprintf(temp, "%s\n%d\n", nome, qtd);
                    }
                }

                fclose(f);
                fclose(temp);

                if (!achou) {
                    printf("Item não encontrado.\n");
                    remove("temp.txt");
                } else {
                    remove("inventory.txt");
                    rename("temp.txt", "inventory.txt");
                }
            }

        } else if (opcao == 3) {
            FILE *f = fopen("inventory.txt", "r");
            if (f == NULL) {
                printf("O estoque está vazio.\n");
            } else {
                char nome[100];
                int qtd;
                int vazio = 1;

                printf("=============================\n");
                printf("        Estoque Atual\n");
                printf("=============================\n");

                while (fscanf(f, " %[^\n]", nome) == 1) {
                    fscanf(f, "%d", &qtd);
                    vazio = 0;
                    printf("Nome: %s\n", nome);
                    printf("Quantidade: %d\n\n", qtd);
                }

                fclose(f);

                if (vazio) {
                    printf("O estoque está vazio.\n");
                }
            }

        } else if (opcao == 4) {
            printf("Obrigado por usar o Controle de Estoques! Até a próxima.\n");
            break;
        } else {
            printf("Opção inválida! Tente novamente.\n\n");
            continue;
        }

        printf("Deseja realizar outra operação? (s/n): ");
        scanf(" %c", &continuar);

        if (continuar != 's' && continuar != 'S') {
            printf("Obrigado por usar o Controle de Estoques! Até a próxima.\n");
            break;
        }
        printf("\n");

    } while (1);

    return 0;
}
