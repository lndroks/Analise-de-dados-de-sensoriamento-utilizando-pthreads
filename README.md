# Analise-de-dados-de-sensoriamento-utilizando-pthreads

````markdown
# Fluxo de Execução do Sistema

## Inicialização

1. A função `main()` é iniciada.
2. O tempo de execução começa a ser medido com `gettimeofday()`.

---

## Criação das Threads

A `main()` cria 3 threads concorrentes:

| Thread | Função | Responsabilidade |
|---|---|---|
| `thread_leitura` | Produtora | Lê e processa arquivos JSON |
| `thread_estatisticas` | Consumidora | Calcula estatísticas |
| `thread_logs` | Auditoria | Gera logs do sistema |

---

# Fluxo de Processamento

## 1. Thread Produtora (`thread_leitura`)

A thread produtora:

1. Lê os arquivos JSON.
2. Converte os dados usando cJSON.
3. Extrai medições dos sensores.
4. Remove medições duplicadas.
5. Insere os dados no buffer compartilhado.

---

## 2. Buffer Compartilhado

O buffer funciona como uma fila entre produtor e consumidor.

### Controle de concorrência

- `mutex_buffer`
  - Protege acesso simultâneo ao buffer.

- `cond_pode_produzir`
  - Faz o produtor esperar caso o buffer esteja cheio.

- `cond_pode_consumir`
  - Faz o consumidor esperar caso o buffer esteja vazio.

---

## 3. Thread Consumidora (`thread_estatisticas`)

A thread consumidora:

1. Remove medições do buffer.
2. Identifica a cidade da medição.
3. Atualiza estatísticas:
   - temperatura
   - umidade
   - pressão
   - bateria
   - spreading factor

---

## 4. Thread de Logs (`thread_logs`)

Executa em paralelo registrando:

- quantidade de itens no buffer
- medições processadas
- estado do sistema

Os logs são gravados em:

```txt
log_auditoria.txt
```

---

# Finalização

1. A thread produtora sinaliza que terminou a leitura.
2. A thread consumidora termina após esvaziar o buffer.
3. A thread de logs encerra a auditoria.
4. A `main()` aguarda todas as threads usando `pthread_join()`.
5. O relatório final é exibido.

---

# Modelo Produtor-Consumidor

O projeto utiliza o padrão clássico:

```text
[Thread Leitura]
       ↓
[Buffer Compartilhado]
       ↓
[Thread Estatísticas]
```

Com uma terceira thread independente:

```text
[Thread Logs]
```

---

# Fluxo Geral do Programa

```text
main()
 ├── inicia thread_leitura()
 │      └── processar_arquivo_json()
 │              └── insere medições no buffer
 │
 ├── inicia thread_estatisticas()
 │      └── remove medições do buffer
 │              └── atualiza estatísticas
 │
 ├── inicia thread_logs()
 │      └── monitora execução
 │
 └── pthread_join()
        └── imprime resultados finais
```

---

# Tecnologias Utilizadas

- Linguagem C
- pthreads
- Mutex
- Variáveis de condição
- cJSON
- Programação concorrente
- Modelo produtor-consumidor
````

## Compilar o programa: 

`
gcc main.c cJSON.c -o sensores -lpthread
`

ou (se usar clock do sistema):

`
gcc main.c cJSON.c -o sensores -lpthread -lrt
`

--> gera o executavel = sensores

## EXECUTAR PROGRAMA

rodar ./sensores

Se tudo estiver correto você verá algo como:

[Produtor] Lendo senzemo_cx_bg.json...
[Produtor] Lendo mqtt_senzemo_cx_bg.json...
[Produtor] Finalizou a leitura de todos os arquivos.
[Consumidor] Estatisticas calculadas com sucesso!

Depois aparecerá o relatório:

============================================================
ANALISE DE DADOS DOS SENSORES - CityLivingLab
Processamento utilizando pthreads
============================================================

Total de registros processados Caxias: 52184
Total de registros processados Bento: 52184

------------------------------------------------------------
TEMPERATURA (C)
------------------------------------------------------------
Cidade            | Minima | Data/Hora             | Maxima | Data/Hora             | Media
-----------------------------------------------------------------------------------------------
Caxias do Sul     | ...
Bento Goncalves   | ...

Verificar o arquivo de log

Seu programa também cria um log:

log_auditoria.txt

Para ver:

cat log_auditoria.txt

ou

nano log_auditoria.txt

###  Se der erro de pthread

Compile assim:

gcc -pthread main.c cJSON.c -o sensores

### Se quiser testar performance

Rode com:

time ./sensores

Ele vai mostrar o tempo também

## APós rodar

nano processamento.log