# Projeto de Monitoramento de Sistema em Ubuntu Linux

## Introdução
Este projeto consiste na criação de um script em Bash para monitoramento de diferentes aspectos de um sistema operacional Ubuntu Linux. O desenvolvimento foi realizado em uma máquina virtual (VM) com Ubuntu, acessada via SSH, e contempla funções específicas para análise de logs, rede, disco e hardware. Além disso, o script foi configurado para execução automática utilizando o **systemd**, garantindo monitoramento periódico do servidor.

## Ambiente de Desenvolvimento
1. Criação de uma máquina virtual com **Ubuntu Linux**.  
2. Acesso à VM por meio de **SSH** para execução dos comandos e desenvolvimento do projeto.  
3. Instalação e utilização de ferramentas nativas do sistema, como `grep`, `awk`, `df`, `du`, `free`, `curl`, `ping` e `systemctl`.  
4. Instalação do pacote `sysstat` para utilização do comando `iostat`:
   ```bash
   sudo apt update
   sudo apt install sysstat -y
   ```

## Estrutura do Script
O script foi desenvolvido em Bash e contém diversas funções, cada uma responsável por uma parte do monitoramento:

### 1. Monitoramento de Logs
- Acessa os arquivos **`/var/log/syslog`** e **`/var/log/auth.log`**.  
- Utiliza expressões regulares e o comando `awk` para filtrar informações relevantes.  
- Salva os resultados em arquivos de relatórios dentro de um diretório específico.

### 2. Monitoramento de Rede
- Executa o comando `ping` para verificar a conectividade com a internet.  
- Utiliza `curl` para testar a disponibilidade de um site e validar se a resposta está correta.  
- Registra os resultados em arquivo de log.

### 3. Monitoramento de Disco
- Utiliza o comando `df` para verificar o uso de disco em diferentes partições.  
- Emprega o comando `du` para avaliar o espaço ocupado em diretórios específicos.  
- Gera relatórios com os percentuais de uso e informações detalhadas de armazenamento.

### 4. Monitoramento de Hardware
- Utiliza o comando `free` para verificar a quantidade de memória RAM total, usada e livre.  
- Analisa também informações relacionadas ao uso da CPU e às operações de leitura e escrita com `iostat`.  
- Registra os resultados em arquivo de log.

### 5. Função Principal
- Uma função principal foi criada para chamar todas as demais funções.  
- Dessa forma, o script realiza o monitoramento completo do sistema em uma única execução.

## Execução Manual
Para executar o script manualmente, basta rodar:

```bash
chmod +x monitoramento-sistema.sh
./monitoramento-sistema.sh
```

Os relatórios serão gerados dentro do diretório `monitoramento_sistema`.

## Automação com systemd
O script foi configurado para execução automática utilizando o **systemd**.  

### 1. Criar o serviço
Crie o arquivo de serviço em `/etc/systemd/system/monitoramento.service`:

```ini
[Unit]
Description=Serviço de Monitoramento de Sistema
After=network.target

[Service]
ExecStart=/home/gustavo/monitoramento-sistema.sh
WorkingDirectory=/home/gustavo
StandardOutput=append:/home/gustavo/monitoramento_sistema/monitoramento.log
StandardError=append:/home/gustavo/monitoramento_sistema/monitoramento_error.log
Restart=always

[Install]
WantedBy=multi-user.target
```

### 2. Criar o timer
Crie o arquivo `/etc/systemd/system/monitoramento.timer`:

```ini
[Unit]
Description=Timer para execução periódica do monitoramento

[Timer]
OnBootSec=1min
OnUnitActiveSec=5min
Unit=monitoramento.service

[Install]
WantedBy=timers.target
```

### 3. Ativar e iniciar
```bash
sudo systemctl daemon-reload
sudo systemctl enable monitoramento.timer
sudo systemctl start monitoramento.timer
```

### 4. Verificar status
```bash
systemctl status monitoramento.timer
systemctl list-timers
```

---

## Conclusão
Este projeto demonstra a aplicação prática de comandos e ferramentas do Linux para monitoramento de sistemas. Através da criação de funções em Bash, foi possível estruturar um script modular e eficiente, capaz de coletar informações relevantes sobre logs, rede, disco e hardware. A integração com o **systemd** assegura que o monitoramento seja realizado de forma automatizada e confiável, contribuindo para a manutenção e estabilidade do ambiente de servidor.

---

Quer que eu inclua também uma seção de **exemplo de saída dos relatórios** (trechos dos arquivos gerados), para que quem acessar o repositório veja claramente o que esperar ao rodar o script?
