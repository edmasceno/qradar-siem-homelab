## Recuperação e Estabilização de Ambiente SIEM (IBM QRadar)

Este relatório detalha a intervenção técnica realizada em um ambiente QRadar SIEM legado que apresentava falhas críticas em múltiplas camadas, impossibilitando qualquer atividade de monitoramento.

### 1. Diagnóstico de Falhas
O ambiente apresentava cinco pontos de falha principais:
1. **Conectividade:** Bloqueio de SSH por algoritmos de criptografia legados.
2. **Infraestrutura:** Corrupção na configuração de armazenamento do VirtualBox (VDI).
3. **Licenciamento:** Licença expirada impedindo a abertura de portas de escuta (Linstat).
4. **Aplicação:** Serviço de consulta `ariel_query_server` corrompido, bloqueando a visualização de logs.
5. **Normalização:** Falta de mapeamento (DSM) para eventos customizados.

### 2. Ações de Resposta e Correção
O processo de recuperação seguiu uma abordagem de infraestrutura até a aplicação:

* **Estabilização da VM:** Reconfiguração das controladoras SATA e ajuste manual de protocolos de comunicação SSH.
* **Bypass de Licença:** Manipulação do relógio do sistema para contornar a expiração da licença Community Edition, permitindo a subida do serviço de coleta.
* **Auditoria de DB:** Intervenção via `psql` e análise de logs do `journalctl` para identificar falhas no servidor Ariel. O serviço foi restaurado manualmente após a limpeza de estados inconsistentes.
* **Normalização de Dados:** Desenvolvimento de script PowerShell para geração de tráfego e criação de QIDs customizados no DSM Editor para garantir que os dados brutos se transformassem em inteligência acionável.

### 3. Conclusão e Lições Aprendidas
O laboratório demonstrou que a operação de um SIEM depende totalmente da integridade do sistema operacional e da rede que o sustenta. O sucesso da estabilização exigiu conhecimentos avançados de administração Linux, bancos de dados relacionais e análise de protocolos.

A visibilidade foi recuperada em 100%, com a normalização de logs customizados funcionando conforme o esperado.

---
*Projeto de Cibersegurança*
