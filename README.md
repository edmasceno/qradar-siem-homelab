# 🛡️ QRadar SIEM Lab: Deployment & Advanced Troubleshooting

## 🎯 Visão Geral
Este repositório registra a criação de um sistema de monitoramento centralizado utilizando o **IBM QRadar Community Edition**. O projeto reproduz o ciclo de vida integral de um log em um SOC (Centro de Operações de Segurança), desde a ingestão bruta até a normalização e inteligência de dados.

## 🧐 O que é o IBM QRadar?
O QRadar é uma plataforma de ponta em **SIEM (Security Information and Event Management)**, funcionando como o sistema nervoso central de um SOC. Ele executa a coleta, correlação e análise de eventos em tempo real para identificar ameaças e garantir conformidade operacional.

---

## 💻 Arquitetura do Laboratório
* **SIEM:** IBM QRadar Community Edition v7.3.3 (CentOS 7).
* **Coletor:** Mecanismo Ariel (Event & Flow Processor).
* **Endpoints:** Host Windows (IP: `192.168.1.42`) enviando telemetria via PowerShell.
* **Infraestrutura:** Ambiente virtualizado com gestão via CLI (SSH).

---

## ✅ Pilar 1: Visibilidade de Rede (Data Ingestion)
O primeiro desafio de um Analista de SOC é assegurar que os ativos essenciais não tenham "pontos cegos".

* **Ingestão via Syslog:** Implementei a coleta de logs *agentless* (sem agentes) utilizando o protocolo UDP/514.
* **Validação de Conectividade:** Empreguei o `tcpdump` para confirmar a recepção de pacotes na interface de rede do SIEM, assegurando a integridade do fluxo entre o endpoint e o coletor.

![Captura de Pacotes](assets/06_tcpdump_verification.png)
*Evidência: Validação do tráfego UDP/514 por meio da CLI e recebimento de registros em tempo real.*

---

## ✅ Pilar 2: Correlação de Eventos (Standardization)
Dados brutos sem contexto são apenas ruído. A **Engenharia de Logs** foi o foco desta etapa.

* **Normalização (Parsing):** Utilize o **DSM Editor** para tratar eventos não identificados ("Unknown"). Mapeei mensagens personalizadas para a taxonomia estruturada do QRadar.
* **Mapeamento de QID:** Para o evento `host_Script`, criei o identificador exclusivo `11750001`, possibilitando ao sistema agrupar e contabilizar ações específicas de scripts administrativos em Dashboards de severidade.

![DSM Editor Mapping](assets/10_mapeamento_qid_custom.png)
*Evidência: Processo de mapeamento de QID para converter logs "Unknown" em alertas acionáveis.*

---

## ✅ Pilar 3: Resposta a Incidentes (Troubleshooting & RCA)
A resiliência operacional é essencial. Registrei falhas críticas e conduzi a análise de causa raiz (RCA).

* **Gestão de Licenciamento:** Corrigi o problema de expiração de licença (`Waiting for valid license`) através de ajustes no relógio do sistema e desativação do serviço NTP, permitindo a restauração do serviço de ingestão `ecs-ec-ingress`.
* **Integridade do Banco de Dados:** Resolvi problemas de conexão no servidor de consultas Ariel, empregando comandos `psql` e análise de processos com `ps aux` para restabelecer o serviço de buscas do SIEM.
* **Gestão de Recursos:** Acompanhei o consumo de memória e CPU via `top`, otimizando a estabilidade dos processos Java da plataforma.

![Troubleshooting Ariel](assets/17_investigacao_db_psql.png)
*Evidência: Análise técnica na camada de banco de dados (Ariel Query Server).*

---

## 🛠️ Tecnologias e Comandos Utilizados
* **Análise de Rede:** `tcpdump -i any port 514`.
* **Gestão de Serviços:** `systemctl status ecs-ec-ingress`.
* **Automação de Logs:** Script PowerShell para simulação de eventos Syslog RFC-3164.
* **Debug de Sistema:** `journalctl -u ariel_query_server`.

---

## 📚 Conclusão e Aprendizados
Este laboratório provou que a atuação em Cybersecurity vai além da ferramenta. Para garantir a integridade do monitoramento, foi necessário dominar administração Linux, protocolos de rede e engenharia de dados. A habilidade de converter falhas de infraestrutura em oportunidades para diagnóstico técnico é o que caracteriza a maturidade de um Analista de SOC.

---
*Projeto de estudo em Blue Team Operations.*
