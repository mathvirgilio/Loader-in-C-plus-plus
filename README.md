# SB_TP2 - Carregador de Programas em Memória

## 📋 Descrição

Este projeto implementa um **carregador de programas em memória** (loader) que simula o processo de alocação de um programa em blocos de memória disponíveis. O programa é desenvolvido em **C** e **Assembly IA-32**, demonstrando a integração entre linguagens de alto e baixo nível.

O carregador recebe o tamanho de um programa e uma lista de blocos de memória disponíveis (cada bloco com seu endereço inicial e tamanho), e então aloca o programa nesses blocos, imprimindo os endereços de memória onde cada parte do programa foi carregada.

## 🏗️ Estrutura do Projeto

```
Loader-in-C-plus-plus/
├── carregador.c      # Programa principal em C (interface e validação)
├── procedimentos.s   # Lógica de carregamento em Assembly IA-32
├── procedimentos.o   # Objeto compilado do Assembly
├── carregador        # Executável final
└── README.md         # Este arquivo
```

## 🔧 Requisitos

Para compilar e executar este projeto, você precisará de:

- **NASM** (Netwide Assembler) - para compilar o código Assembly
- **GCC** com suporte para arquitetura 32-bit
- **Bibliotecas de desenvolvimento 32-bit** (no Linux)

### Instalação das Dependências (Linux)

```bash
sudo apt install nasm gcc-multilib libc6-dev-i386
```

## 🚀 Compilação

O projeto é compilado em duas etapas:

### 1. Compilar o código Assembly (IA-32)

```bash
nasm -f elf32 procedimentos.s -o procedimentos.o
```

Este comando:
- `-f elf32`: Especifica o formato de saída como ELF 32-bit
- `procedimentos.s`: Arquivo fonte em Assembly
- `-o procedimentos.o`: Arquivo objeto de saída

### 2. Compilar o código C e vincular com o objeto Assembly

```bash
gcc -m32 -g -o carregador carregador.c procedimentos.o
```

Este comando:
- `-m32`: Compila para arquitetura 32-bit
- `-g`: Inclui informações de debug
- `-o carregador`: Nome do executável final
- `carregador.c`: Código fonte em C
- `procedimentos.o`: Objeto Assembly compilado

## 📖 Uso

### Formato da Linha de Comando

```bash
./carregador <tam_prog> <início_bloco1> <tamanho_bloco1> [<início_bloco2> <tamanho_bloco2> ...]
```

### Parâmetros

- **`tam_prog`**: Tamanho total do programa a ser carregado em memória
- **`início_blocoN`**: Endereço inicial do bloco de memória N
- **`tamanho_blocoN`**: Tamanho do bloco de memória N

**Observações:**
- Os blocos de memória devem ser fornecidos em pares (início, tamanho)
- O número máximo de blocos é 8 (16 parâmetros no total, incluindo o tamanho do programa)
- O número mínimo de parâmetros é 3 (tam_prog + 1 bloco completo)

### Exemplos de Execução

#### Exemplo 1: Programa pequeno em um único bloco
```bash
./carregador 125 100 500
```
Carrega um programa de 125 unidades em um bloco que começa no endereço 100 e tem tamanho 500.

#### Exemplo 2: Programa médio em múltiplos blocos
```bash
./carregador 125 100 500 4000 300
```
Carrega um programa de 125 unidades distribuído entre:
- Bloco 1: início em 100, tamanho 500
- Bloco 2: início em 4000, tamanho 300

#### Exemplo 3: Programa grande em vários blocos
```bash
./carregador 125 100 500 4000 300 20000
```
Carrega um programa de 125 unidades em três blocos de memória.

#### Exemplo 4: Programa muito grande
```bash
./carregador 125 100 500 4000 300 20000 125 30000 345
```
Carrega um programa de 125 unidades distribuído em quatro blocos de memória.

## 🔍 Como Funciona

1. **Validação (C)**: O programa em C valida os parâmetros de entrada, verificando:
   - Quantidade mínima e máxima de parâmetros
   - Se os blocos estão em pares (início, tamanho)

2. **Carregamento (Assembly)**: A função `procedimentos` em Assembly:
   - Recebe o tamanho do programa e a lista de blocos
   - Itera pelos blocos disponíveis
   - Aloca o programa sequencialmente nos blocos
   - Imprime o endereço inicial e final de cada parte alocada
   - Para quando todo o programa foi carregado ou não há mais blocos disponíveis

3. **Saída**: O programa imprime os endereços de memória onde cada parte do programa foi carregada, um por linha.

## 🐛 Debug

Para depurar o programa, você pode usar `gdb`:

```bash
gdb ./carregador
```

Como o código foi compilado com a flag `-g`, você terá informações de debug disponíveis.

## 📝 Notas Técnicas

- O código Assembly usa chamadas de sistema Linux (`int 0x80`) para escrita na saída padrão
- A conversão de números para strings é feita manualmente em Assembly
- O programa suporta até 8 blocos de memória (definido no array `blocos[8]` em C)