# Processador de Dados IoT - CityLivingLab

Este projeto foi desenvolvido para a disciplina de Fundamentos de Sistemas Operacionais na Universidade de Caxias do Sul (UCS). O software realiza o processamento paralelo de arquivos JSON contendo dados de sensores IoT (temperatura, umidade, pressão, bateria) das cidades de Caxias do Sul e Bento Gonçalves.

## 🚀 O que o projeto faz
- Lê milhares de registros de sensores IoT.
- Descarta pacotes de dados duplicados ou corrompidos.
- Calcula médias, valores máximos e mínimos para cada cidade.
- Registra o andamento do processamento em um arquivo de log.

## 🛠️ Como foi construído
O sistema foi escrito em **C** e utiliza **POSIX Threads (pthreads)** para processar os dados concorrentemente, dividindo o trabalho em 3 tarefas principais de forma sincronizada (Modelo Produtor-Consumidor com Mutex):
1. **Thread de Leitura (Produtor)**
2. **Thread de Estatísticas (Consumidor)**
3. **Thread de Logs (Auditoria)**

*A biblioteca `cJSON` foi utilizada para a extração dos dados dos arquivos de texto.*

## ⚙️ Como executar

Para rodar este projeto, você precisa de um ambiente Linux (ou WSL/VM) com o compilador `gcc` instalado.

1. Faça o clone do repositório e abra a pasta do projeto no terminal.
2. Compile o código com o seguinte comando:
   ```bash
   gcc main.c cJSON.c -o trabalho -pthread
3. Execute o programa:
   ```bash
    ./trabalho
