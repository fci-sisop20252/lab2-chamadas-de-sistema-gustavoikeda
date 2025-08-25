# Relatório do Laboratório 2 - Chamadas de Sistema

---

## Exercício 1a - Observação printf() vs 1b - write()

### Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: __9__ syscalls
- ex1b_write: ___7__ syscalls

**2. Por que há diferença entre printf() e write()?**

```
Existe diferenças, pois eles trabalham em niveis diferentes, o printf() em nivel mais alto sendo menos custoso e quando a performance nao for critica, ja o write() trabalha em um baixo nivel sendo uma chamada de sistema, resultando em um custo maior porém com comportamento previsivel, ideal para quando a performance for critica.
```

**3. Qual implementação você acha que é mais eficiente? Por quê?**

```
Printf() é mais eficiente, pois ele economiza chamadas de sistemas, assim fazendo o programa ser menos custoso e mais rapido.
```

---

## Exercício 2 - Leitura de Arquivo

### Resultados da execução:
- File descriptor: ___3__
- Bytes lidos: ___127__

### Comando strace:
```bash
strace -e open,read,close ./ex2_leitura
```

### Análise

**1. Por que o file descriptor não foi 0, 1 ou 2?**

```
O file descriptor nao foi 0, 1 ou 2 porque estes três primeiros decritores ja estavam ocupados desde o inicio do programa
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
O arquivo foi lido corretamente, pois a funcao open() retorna o menor numero nao negativo inteiro que identifica o arquivo aberto na tabela de descritores do processo.
```

---

## Exercício 3 - Contador com Loop

### Resultados (BUFFER_SIZE = 64):
- Linhas: ___24__ (esperado: 25)
- Caracteres: __1299___
- Chamadas read(): __21___
- Tempo: __0.000127___ segundos

### Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |       82        | 0.000147  |
| 64          |       21        | 0.000127  |
| 256         |       6         | 0.000065  |
| 1024        |       2         | 0.000063  |

### Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Quanto maior o tamanho do buffer, menor é a quantidade de chamadas.
```

**2. Como você detecta o fim do arquivo?**

```
O fim do arquivo é detectado quando o read() retorna o valor 0, indicando que o offset do arquivo esta ou passou do fim do arquivo.
```

---

## Exercício 4 - Cópia de Arquivo

### Resultados:
- Bytes copiados: __1364___
- Operações: ___6__
- Tempo: __0.000174___ segundos
- Throughput: __7655.35___ KB/s

### Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [X] Idênticos [ ] Diferentes

### Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Devemos verificar se os bytes_escritos são iguais ao bytes lidos, pois assim podemos confirmar se todo o arquivo foi lido e copiado corretamente.
```

**2. Que flags são essenciais no open() do destino?**

```
As flags essenciais no open() do destino são O_WRONLY , O_CREAT e O_TRUNC
```

---

## Análise Geral

### Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
AS syscalls demonstram a transição entre usuario e kernel por meio do strace que retorna o numero de bytes.
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
Os files descriptors sao essenciais para identificar o estado do processo e sua conexão com recursos de entrada e saida.
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
Quanto maior o buffer melhor foi a performance, pois um buffer maior consegue armazenar mais caracteres, assim fazendo menos chamadas.
```

### Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** __teste do programa___

**Por que você acha que foi mais rápido?**

```
Crei que o teste do programa foi mais rapido devido a menos chamadas de sistema.
```

---

## Entrega

Certifique-se de ter:
- [ ] Todos os códigos com TODOs completados
- [ ] Traces salvos em `traces/`
- [ ] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```