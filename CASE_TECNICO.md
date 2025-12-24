# 📄 CASE_TECNICO.md  
## Recuperação e Estabilização de Ambiente SIEM (IBM QRadar)

---

## 📌 Contexto do Caso

Ao criar um ambiente de estudos com o **IBM QRadar Community Edition**, ficou evidente que o sistema continha **várias falhas críticas** que impediam a ingestão, correlação e visualização de eventos de segurança.

O ambiente foi configurado a partir de um **snapshot legado**, o que resultou em dificuldades típicas de situações reais de SOC, tais como:
- Licenciamento expirado
- Serviços essenciais instáveis
- Falhas em banco de dados
- Inconsistências na camada de virtualização
- Logs não normalizados

Este caso documenta o **procedimento integral de diagnóstico, recuperação e validação operacional** do SIEM.

---

## 🎯 Objetivo

Reestabelecer a **visibilidade operacional do SIEM**, assegurando:
- Estabilidade da infraestrutura
- Funcionamento dos serviços críticos
- Ingestão confiável de logs
- Normalização e classificação correta dos eventos

---

## 🧱 Ambiente Técnico

- **SIEM:** IBM QRadar Community Edition v7.3.3  
- **Sistema Operacional:** CentOS 7  
- **Virtualização:** Oracle VirtualBox  
- **Endpoints:** Windows 11  
- **Protocolo de Logs:** Syslog via UDP (porta 514)

---

## 🚧 Problemas Identificados

### 1️⃣ Falha de Acesso Administrativo
- Incompatibilidade de algoritmos MAC durante acesso SSH a partir do Windows 11
- Bloqueio inicial de administração remota

### 2️⃣ Instabilidade na Virtualização
- Erro crítico *MachineWrap (E_FAIL)* no VirtualBox
- Disco VDI não anexado corretamente
- Sistema incapaz de inicializar de forma consistente

### 3️⃣ Licença Expirada
- Serviço `ecs-ec-ingress` ativo
- Porta UDP/514 fechada
- QRadar incapaz de receber eventos

### 4️⃣ Falha no Servidor de Consultas
- Logs não exibidos na interface
- Serviço `ariel_query_server` falhando na inicialização
- Impacto direto na visibilidade de eventos

### 5️⃣ Logs Não Normalizados
- Eventos classificados como *Unknown*
- Ausência de QID e mapeamento DSM

---

## 🔍 Processo de Diagnóstico

O troubleshooting adotou uma metodologia **organizada e gradual**, semelhante à empregada em contextos de SOC:

1. **Validação de acesso e sistema**
   - Ajuste manual de algoritmos SSH
   - Verificação de serviços e recursos via CLI

2. **Análise de infraestrutura**
   - Revisão das controladoras de armazenamento da VM
   - Correção de inconsistências SATA/IDE

3. **Investigação de licenciamento**
   - Análise de logs e comportamento da porta 514
   - Identificação de restrição por licença expirada

4. **Análise de serviços críticos**
   - Uso de `journalctl` para identificar falhas no Ariel
   - Verificação de processos órfãos
   - Acesso direto ao banco via `psql`

5. **Validação de ingestão**
   - Monitoramento de tráfego com `tcpdump`
   - Confirmação de recebimento de pacotes Syslog

---

## 🛠️ Ações Corretivas Implementadas

- Forçado o uso de `hmac-sha1` para acesso administrativo via SSH
- Reconfiguração das controladoras de armazenamento da máquina virtual
- Uso de *Time Travel* (ajuste temporário de data e desativação de NTP) para verificar a licença
- Restauração manual do serviço `ariel_query_server`
- Criação de um script PowerShell para o envio de eventos Syslog
- Geração de QID personalizado por meio do DSM Editor para a normalização dos registros de log

---

## ✅ Resultado Final

Após as correções:

- SIEM estabilizado e em funcionamento
- Serviços essenciais operando de forma adequada
- Eventos sendo recebidos na porta UDP/514 aberta
- Logs processados, normalizados e categorizados
- Eventos apresentados de forma adequada na aba **Log Activity**
- Painéis exibindo métricas reais de saúde e segurança

---

## 🤖 Uso de Inteligência Artificial como Ferramenta de Apoio

Ferramentas de **Inteligência Artificial** foram empregadas como suporte ao processo de pesquisa e aprendizado durante o desenvolvimento deste laboratório e a criação da documentação.

A IA foi utilizada principalmente para:
- Ajudar na estruturação do pensamento técnico
- Auxiliar na compreensão de erros recorrentes e mensagens de log
- Analisar a clareza, a coesão e a organização da documentação
- Agilizar o processo de pesquisa e confirmação de hipóteses

Todas as decisões técnicas, diagnósticos, testes e correções foram conduzidos de forma prática no ambiente, com validação direta dos resultados. A IA atuou como **ferramenta complementar**, não substituindo a análise humana nem a execução das atividades.

O uso desse tipo de ferramenta reflete práticas atuais do mercado, onde a capacidade de **utilizar IA de forma crítica e responsável** torna-se um diferencial profissional.

---

## 🧠 Lições Aprendidas

Este case reforça que a atuação em um SOC vai além da operação da ferramenta SIEM. É essencial possuir:

- Conhecimento sólido em **Linux**
- Entendimento de **banco de dados**
- Capacidade de **diagnóstico de serviços**
- Visão de **infraestrutura e rede**
- Habilidade de **engenharia de logs**

A confiabilidade do monitoramento depende diretamente da **estabilidade da base técnica**.

---

## 📎 Referência

O arquivo [`README.md`](README.md) deste repositório contém a documentação técnica detalhada, os comandos empregados e as evidências visuais.

---

*Projeto de estudo em Blue Team Operations.*
