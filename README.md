# 🛡️ QRadar SIEM Lab: Deployment & Advanced Troubleshooting

## 🎯 Visão Geral
Este repositório registra a configuração de um ambiente de monitoramento utilizando o **IBM QRadar Community Edition**. O foco principal foi a resolução de questões críticas de infraestrutura, como a expiração de licenças em snapshots antigos, recuperação de serviços de banco de dados e ingestão de logs usando o PowerShell.

## 🧐 O que é o IBM QRadar?
Uma das principais plataformas de SIEM (Gerenciamento e Análise de Eventos de Segurança) no mercado mundial é o **IBM QRadar**. Sua função é atuar como o "cérebro" de um SOC (Centro de Operações de Segurança), reunindo, correlacionando e analisando milhões de eventos produzidos por dispositivos de rede, servidores e aplicativos em tempo real.

## 💻 Ambiente do laboratório
* **SIEM:** Edição Comunitária do IBM QRadar v7.3.3 (CentOS 7).
* **Rede:** VM em modo Bridge (IP: `192.168.1.47`).
* **Endpoints:** Enviar eventos por meio de Syslog/UDP em Windows 11.

---

## 🛠️ Etapa 1: Implementação e Acesso
A instalação inicial foi realizada em um snapshot limpo do CentOS. O primeiro desafio surgiu ao tentar acessar o sistema remoto via SSH, quando o Windows 11 recusou a conexão por não ser compatível com os algoritmos legados (MACs).

**Ação:** **Forçando** o uso de `hmac-sha1` para configurar a gestão por meio do terminal.

![Processo de atualização](assets/01_centos_setup.png)
*Figura 1: Processo de atualização de pacotes e setup do sistema base.*

![Login SSH e Terminal](assets/11_monitoramento_syslog_ssh.png)
*Figura 2: Resolução de problemas de MACs SSH e monitoramento inicial via CLI.*

![Interface QRadar](assets/02_qradar_interface.png)
*Figura 3: Primeiro acesso à interface administrativa após a configuração de credenciais.*

---

## 🔍 Etapa 2: Resolução de problemas de infraestrutura (Virtualização)
Antes de estabilizar o SIEM, enfrentei problemas na camada de virtualização que impediam a operação da máquina.

* **O Erro MachineWrap:** Um erro grave de interface (E_FAIL) no VirtualBox impediu a anexação adequada do disco VDI.
* **A Solução:** Para restaurar a integridade do boot, foi preciso ajustar as controladoras SATA e IDE nas configurações de armazenamento.

![Erro de Virtualização](assets/12_erro_virtualbox_vdi.png)
*Figura 4: Diagnóstico de falha crítica na anexação do disco rígido virtual.*

![Configuração de Armazenamento](assets/13_configuracao_armazenamento.png)
*Figura 5: Reconfiguração das controladoras para recuperação da VM.*

---

## 🔍 Etapa 3: Diagnóstico de Serviços e Licença (Causa Raiz)

### 1. A restrição da licença ("Time Travel")
Embora o serviço de coleta `ecs-ec-ingress` estivesse em funcionamento, a porta 514 permanecia fechada. A análise dos registros apontou que a licença havia expirado.

**Solução:** Realizei um "Time Travel" no servidor, configurando o relógio para **maio de 2020** e desativando o NTP para verificar a licença.

![Status do Coletor](assets/04_service_status.png)
*Figura 6: Verificação do status do coletor de eventos e porta 514.*

### 2. Monitoramento de Performance
Utilizei o comando `top` para monitorar a carga dos processos Java e a estabilização do sistema.

![Monitoramento Top](assets/05_resource_monitoring.png)
*Figura 7: Análise de consumo de hardware em tempo real.*

---

## 🚨 Etapa 4: Recuperação Crítica (Ariel e Banco de Dados)
Identifiquei um problema que impedia a visualização de logs: erro de conexão com o servidor de consulta.

* **Diagnóstico de Falha:** Utilizando o `journalctl`, constatei que o serviço `ariel_query_server` não conseguiu iniciar.
* **Intervenção Manual:** Utilizei o `psql` para acessar o banco de dados e investigar as tabelas de sistema, além de analisar processos órfãos com o comando `ps aux | grep ariel`, a fim de forçar a restauração do serviço.

![Erro de Servidor de Consulta](assets/14_erro_servidor_consulta.png)
*Figura 8: Interface do QRadar exibindo falha no servidor de busca Ariel.*

![Log do Journalctl](assets/16_falha_servico_ariel_query.png)
*Figura 9: Análise de logs do sistema identificando a causa da falha do serviço.*

![Investigação PSQL](assets/17_investigacao_db_psql.png)
*Figura 10: Troubleshooting avançado via terminal investigando a base de dados Ariel.*

---

## 📡 Etapa 5: Coleta e Engenharia de Logs (Editor DSM)

### Aquisição por meio do PowerShell
Desenvolvi um script PowerShell para converter eventos locais em mensagens Syslog e enviá-las pelo protocolo UDP na porta 514. Utilizei o `tcpdump` e o monitoramento por SSH para validar o tráfego.

![Script PowerShell](assets/03_powershell_sender.png)
*Figura 11: Implementação do script para envio de telemetria customizada.*

![Captura TCPDump](assets/06_tcpdump_verification.png)
*Figura 12: Confirmação visual de tráfego UDP/514 chegando ao SIEM.*

### Normalização e Mapeamento de QID
Logs "Unknown" foram padronizados. Empreguei o **DSM Editor** para desenvolver o **QID 11750001**, o que possibilitou o reconhecimento e a classificação adequada do evento `host_Script`.

![Filtro de Origem](assets/9_filtro_log_source.png)
*Figura 13: Aplicação de filtro de busca para isolar a nova fonte de log.*

![Mapeamento DSM](assets/10_mapeamento_qid_custom.png)
*Figura 14: Engenharia de logs convertendo dados desconhecidos em eventos acionáveis.*

---

## ✅ Resultado Final e Painéis de Controle
Após a recuperação da infraestrutura e normalização dos logs, os eventos começaram a ser apresentados e reunidos em painéis de controle de monitoramento.

![Log Activity Real Time](assets/18.png)
*Figura 15: Visualização dos registros processados em tempo real na aba Log Activity com o filtro de origem aplicado.*

![Recepção com Sucesso](assets/08_recepcao_log_sucesso.png)
*Figura 16: Confirmação da normalização do evento customizado após o mapeamento de QID.*

![Saúde do Sistema](assets/07_final_log_activity.png)
*Figura 17: Aba Log Activity processando métricas de saúde e eventos com sucesso.*

---

## 📚 Conclusão
Este laboratório demonstrou que a atuação em SOC vai além de monitorar painéis; é imprescindível ter domínio sobre a infraestrutura, banco de dados e rede para assegurar uma visibilidade completa.

*Projeto de estudo em Blue Team Operations.*
