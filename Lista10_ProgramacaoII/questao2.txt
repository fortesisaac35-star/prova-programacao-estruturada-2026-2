#include <stdio.h>
#include <string.h>

#define MAX 40

typedef struct {
    int codigo;
    char descricao[100];
    double valor;
    int estoque;
} Produto;

int localizar(const Produto produtos[], int total, int codigo) {
    for (int i = 0; i < total; i++)
        if (produtos[i].codigo == codigo) return i;
    return -1;
}

void cadastrar(Produto produtos[], int *total) {
    if (*total >= MAX) { puts("Cadastro cheio."); return; }
    Produto p;
    printf("Codigo: "); scanf("%d", &p.codigo);
    if (localizar(produtos, *total, p.codigo) != -1) { puts("Codigo ja cadastrado."); return; }
    printf("Descricao: "); scanf(" %99[^\n]", p.descricao);
    printf("Valor unitario: "); scanf("%lf", &p.valor);
    printf("Quantidade em estoque: "); scanf("%d", &p.estoque);
    if (p.valor < 0 || p.estoque < 0) { puts("Valores invalidos."); return; }
    produtos[(*total)++] = p;
    puts("Produto cadastrado.");
}

void alterarValor(Produto produtos[], int total, int codigo) {
    int i = localizar(produtos, total, codigo);
    if (i < 0) { puts("Produto nao encontrado."); return; }
    double novo;
    printf("Novo valor: "); scanf("%lf", &novo);
    if (novo < 0) { puts("Valor invalido."); return; }
    produtos[i].valor = novo;
    puts("Valor atualizado.");
}

double consultarValor(const Produto produtos[], int total, int codigo) {
    int i = localizar(produtos, total, codigo);
    return i < 0 ? -1 : produtos[i].valor;
}

int consultarEstoque(const Produto produtos[], int total, int codigo) {
    int i = localizar(produtos, total, codigo);
    return i < 0 ? -1 : produtos[i].estoque;
}

void vender(Produto produtos[], int total, int codigo, int quantidade) {
    int i = localizar(produtos, total, codigo);
    if (i < 0) { puts("Produto nao encontrado."); return; }
    if (quantidade <= 0) { puts("Quantidade invalida."); return; }
    if (produtos[i].estoque == 0) { puts("Estoque zerado. Venda impossivel."); return; }
    if (quantidade > produtos[i].estoque) {
        char resposta;
        printf("Estoque insuficiente (%d). Deseja comprar toda a quantidade disponivel? (s/n): ", produtos[i].estoque);
        scanf(" %c", &resposta);
        if (resposta != 's' && resposta != 'S') { puts("Venda cancelada."); return; }
        quantidade = produtos[i].estoque;
    }
    produtos[i].estoque -= quantidade;
    printf("Venda realizada. Total a pagar: R$ %.2f\n", quantidade * produtos[i].valor);
}

void atualizarEstoque(Produto produtos[], int total, int codigo) {
    int i = localizar(produtos, total, codigo);
    if (i < 0) { puts("Produto nao encontrado."); return; }
    int quantidade;
    printf("Nova quantidade em estoque: "); scanf("%d", &quantidade);
    if (quantidade < 0) { puts("Quantidade invalida."); return; }
    produtos[i].estoque = quantidade;
    puts("Estoque atualizado.");
}

void listarTodos(const Produto produtos[], int total) {
    if (!total) { puts("Nenhum produto cadastrado."); return; }
    for (int i = 0; i < total; i++)
        printf("Codigo: %d | Descricao: %s\n", produtos[i].codigo, produtos[i].descricao);
}

void listarZerados(const Produto produtos[], int total) {
    int encontrou = 0;
    for (int i = 0; i < total; i++)
        if (produtos[i].estoque == 0) {
            printf("Codigo: %d | Descricao: %s\n", produtos[i].codigo, produtos[i].descricao);
            encontrou = 1;
        }
    if (!encontrou) puts("Nenhum produto com estoque zero.");
}

int main(void) {
    Produto produtos[MAX];
    int total = 0, opcao, codigo, quantidade;
    do {
        puts("\n=== PAPELARIA ESCOLAR ===");
        puts("1 Cadastrar produto\n2 Alterar valor\n3 Consultar valor\n4 Consultar estoque");
        puts("5 Vender\n6 Atualizar estoque\n7 Listar produtos\n8 Listar estoque zero\n0 Sair");
        printf("Opcao: ");
        if (scanf("%d", &opcao) != 1) break;
        if (opcao >= 2 && opcao <= 6) {
            printf("Codigo do produto: ");
            if (scanf("%d", &codigo) != 1) break;
        }
        switch (opcao) {
            case 1: cadastrar(produtos, &total); break;
            case 2: alterarValor(produtos, total, codigo); break;
            case 3: {
                double valor = consultarValor(produtos, total, codigo);
                if (valor < 0) puts("Produto nao encontrado.");
                else printf("Valor unitario: R$ %.2f\n", valor);
                break;
            }
            case 4: {
                int estoque = consultarEstoque(produtos, total, codigo);
                if (estoque < 0) puts("Produto nao encontrado.");
                else printf("Quantidade em estoque: %d\n", estoque);
                break;
            }
            case 5:
                printf("Quantidade desejada: "); scanf("%d", &quantidade);
                vender(produtos, total, codigo, quantidade);
                break;
            case 6: atualizarEstoque(produtos, total, codigo); break;
            case 7: listarTodos(produtos, total); break;
            case 8: listarZerados(produtos, total); break;
            case 0: puts("Encerrando programa."); break;
            default: puts("Opcao invalida.");
        }
    } while (opcao != 0);
    return 0;
}
