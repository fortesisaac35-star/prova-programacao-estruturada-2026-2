#include <stdio.h>
#include <string.h>

#define MAX_CONTAS 100

typedef struct {
    int numero;
    char nome[100];
    char cpf[15];
    char telefone[25];
    double saldo;
} Conta;

typedef struct {
    Conta contas[MAX_CONTAS];
    int total;
} Cadastro;

/* Retorna -1 para cadastro vazio, -2 para conta inexistente. */
int buscar(const Cadastro *cadastro, int numero) {
    if (cadastro->total == 0) return -1;
    for (int i = 0; i < cadastro->total; i++) {
        if (cadastro->contas[i].numero == numero) return i;
    }
    return -2;
}

void cadastrar(Cadastro *cadastro, int numero) {
    if (cadastro->total >= MAX_CONTAS) {
        puts("Cadastro cheio (limite de 100 contas).");
        return;
    }
    if (buscar(cadastro, numero) >= 0) {
        puts("Numero de conta repetido. Cadastro nao realizado.");
        return;
    }
    Conta nova = {0};
    nova.numero = numero;
    printf("Nome do titular: ");
    scanf(" %99[^\n]", nova.nome);
    printf("CPF: ");
    scanf(" %14s", nova.cpf);
    printf("Telefone: ");
    scanf(" %24s", nova.telefone);
    printf("Saldo inicial: ");
    if (scanf("%lf", &nova.saldo) != 1 || nova.saldo < 0) {
        puts("Saldo inicial invalido. Cadastro cancelado.");
        return;
    }
    cadastro->contas[cadastro->total++] = nova;
    puts("Conta cadastrada com sucesso.");
}

void consultarSaldo(const Cadastro *cadastro, int numero) {
    int pos = buscar(cadastro, numero);
    if (pos < 0) {
        puts("Conta nao cadastrada.");
        return;
    }
    printf("Saldo da conta %d: R$ %.2f\n", numero, cadastro->contas[pos].saldo);
}

void depositar(Cadastro *cadastro, int numero) {
    int pos = buscar(cadastro, numero);
    if (pos < 0) {
        puts("Conta nao cadastrada.");
        return;
    }
    double valor;
    printf("Valor do deposito: ");
    if (scanf("%lf", &valor) != 1 || valor <= 0) {
        puts("Valor invalido.");
        return;
    }
    cadastro->contas[pos].saldo += valor;
    printf("Deposito realizado. Novo saldo: R$ %.2f\n", cadastro->contas[pos].saldo);
}

void sacar(Cadastro *cadastro, int numero) {
    int pos = buscar(cadastro, numero);
    if (pos < 0) {
        puts("Conta nao cadastrada.");
        return;
    }
    double valor;
    printf("Valor do saque: ");
    if (scanf("%lf", &valor) != 1 || valor <= 0) {
        puts("Valor invalido.");
        return;
    }
    if (valor > cadastro->contas[pos].saldo) {
        puts("Saldo insuficiente.");
        return;
    }
    cadastro->contas[pos].saldo -= valor;
    printf("Saque realizado. Novo saldo: R$ %.2f\n", cadastro->contas[pos].saldo);
}

void listarContas(const Cadastro *cadastro) {
    if (cadastro->total == 0) {
        puts("Nenhuma conta cadastrada.");
        return;
    }
    for (int i = 0; i < cadastro->total; i++) {
        const Conta *conta = &cadastro->contas[i];
        printf("Conta: %d | Titular: %s | Telefone: %s\n",
               conta->numero, conta->nome, conta->telefone);
    }
}

int main(void) {
    Cadastro corrente = {0};
    Cadastro poupanca = {0};
    int tipo, opcao, numero;
    Cadastro *selecionado;

    do {
        puts("\n=== BANCO DINHEIRO CERTO ===");
        puts("1 - Conta corrente");
        puts("2 - Conta poupanca");
        puts("0 - Encerrar");
        printf("Escolha o cadastro: ");
        if (scanf("%d", &tipo) != 1) return 1;
        if (tipo == 0) break;
        if (tipo != 1 && tipo != 2) {
            puts("Opcao invalida.");
            continue;
        }
        selecionado = (tipo == 1) ? &corrente : &poupanca;
        do {
            printf("\n--- %s ---\n", tipo == 1 ? "CONTA CORRENTE" : "CONTA POUPANCA");
            puts("1 - Cadastrar conta");
            puts("2 - Consultar saldo");
            puts("3 - Depositar");
            puts("4 - Sacar");
            puts("5 - Listar contas");
            puts("0 - Voltar");
            printf("Opcao: ");
            if (scanf("%d", &opcao) != 1) return 1;
            if (opcao >= 1 && opcao <= 4) {
                printf("Numero da conta: ");
                if (scanf("%d", &numero) != 1) return 1;
            }
            switch (opcao) {
                case 1: cadastrar(selecionado, numero); break;
                case 2: consultarSaldo(selecionado, numero); break;
                case 3: depositar(selecionado, numero); break;
                case 4: sacar(selecionado, numero); break;
                case 5: listarContas(selecionado); break;
                case 0: break;
                default: puts("Opcao invalida.");
            }
        } while (opcao != 0);
    } while (tipo != 0);
    puts("Programa encerrado.");
    return 0;
}
