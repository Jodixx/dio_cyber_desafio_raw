# Relatório de Projeto — Malware Simulado em Python (Ransomware & Keylogger)

**Autor:** Jodi Vockt
**Curso / Evento:** DIO — Curso de Cybersecurity
**Data:** 24/10/2025
**Ambiente:** Laboratório controlado (VM isolada, snapshots, sem conectividade externa)
**Observações importantes:** todos os testes foram realizados em ambiente 100% controlado e isolado, com snapshots antes e depois. Este documento **não** contém código malicioso executável; contém descrições, pseudocódigo educativo e orientações de defesa. Qualquer implementação prática deve seguir políticas éticas e autorização formal.

---

## Sumário

1. [Entendendo o desafio](#entendendo-o-desafio)
2. 
3. [Ambiente de teste (topologia)](#ambiente-de-teste-topologia)
4. [Objetivos do experimento](#objetivos-do-experimento)
5. [Metodologia — visão geral](#metodologia---visão-geral)
6. [Ransomware simulado — descrição e procedimentos seguros](#ransomware-simulado---descrição-e-procedimentos-seguros)
7. [Keylogger simulado — descrição e procedimentos seguros](#keylogger-simulado---descrição-e-procedimentos-seguros)
8. [Coleta de evidências e monitoramento](#coleta-de-evidências-e-monitoramento)
9. [Resultados esperados e checklist de validação](#resultados-esperados-e-checklist-de-validação)
10. [Análise de risco e impacto](#análise-de-risco-e-impacto)
11. [Detecção, mitigação e recomendações defensivas](#detecção-mitigação-e-recomendações-defensivas)
12. [Lições aprendidas e próximos passos](#lições-aprendidas-e-próximos-passos)
13. [Apêndice A — Pseudocódigo educativo (não-executável)](#apêndice-a---pseudocódigo-educativo-não-executável)
14. [Apêndice B — Recursos, leituras e ferramentas seguras](#apêndice-b---recursos-leituras-e-ferramentas-seguras)

---


## Escopo, ética e segurança

* **Escopo:** experimentos restritos a uma VM isolada (snapshot antes/depois), sem acesso à rede corporativa ou internet pública.
* **Autorização:** testes realizados com permissão para fins acadêmicos. Registrar a autorização caso faça parte de entrega institucional.
* **Regra de ouro:** não publicar código malicioso, não transferir artefatos para outros ambientes, e sempre restaurar snapshot após o teste.

> **Aviso:** este relatório não fornece código que possa ser usado para atacar computadores alheios. Fornece pseudocódigo, descrições e orientações defensivas para aprendizado.

---

## Ambiente de teste (topologia)

* Host: Máquina de desenvolvimento (com VirtualBox/VMware)
* VM alvo: Linux/Windows, completamente isolada.

---

## Objetivos do experimento

1. Demonstrar, em alto nível, o fluxo de um ransomware simulado: criação de arquivos de teste → criptografia controlada → geração de mensagem de resgate → descriptografia com chave guardada localmente para fins de validação.
2. Demonstrar, em alto nível, o fluxo de um keylogger simulado: captura de eventos de teclado gravados localmente em arquivo txt, sem exfiltração (ou com exfiltração simulada para um servidor de teste isolado).
3. Coletar evidências de execução (logs, captures de rede, alterações de arquivos) e avaliar detecção e mitigação.
4. Formular recomendações defensivas aplicáveis.

---

## Metodologia — visão geral

Para cada exercício (ransomware, keylogger) seguir:

1. Preparação: criar snapshot; gerar arquivos de teste (documentos .txt/.docx) em pasta dedicada `C:\LabFiles` ou `/home/lab/files`.
2. Instrumentação: habilitar logging (sysmon/auditd), iniciar captura de pacotes local (tcpdump), habilitar monitor de integridade de arquivos (tripwire ou inotifywait) para observar alterações.
3. Execução controlada: rodar o experimento com supervisão, colher logs e prints de tela. Não realizar exfiltração para redes externas.
4. Coleta: salvar outputs em pasta de evidências da VM (exportar antes de destruir a VM).
5. Restauração: reverter a VM ao snapshot inicial.

---

## Ransomware simulado — descrição e procedimentos seguros

**Objetivo educativo:** entender o ciclo de criptografia de arquivos e as estratégias de mitigação.

**Comportamento simulado (alto nível):**

* Identificar arquivos de teste dentro de pasta controlada (`LabFiles`).
* Aplicar transformação criptográfica **não destrutiva**: por exemplo, armazenar uma cópia original em subpasta `backup_disabled_by_policy` (no ambiente real não se faria isso — aqui é para garantir recuperação), ou usar uma chave local temporária para criptografia e descriptografia imediata para validação.
* Gerar um arquivo de texto com a mensagem de resgate explicativa (apenas texto demonstrativo).

**Nota importante de segurança:** Não executar algoritmos de criptografia que tornem dados irrecuperáveis em ambiente que não seja descartável. Sempre mantenha backups e snapshots.

**Evidências a coletar:**

* Lista de arquivos antes/depois (`ls -la` / `dir`) com timestamp.
* Logs de sistema mostrando processo que fez alterações (pid, usuário).
* Captura de disco ou hash de arquivos (sha256) antes e depois para validar mudança.
* Conteúdo do arquivo de "resgate" (print).

**Pistas para o relatório (o que documentar):**

* Comando/ação executada (ex.: script iniciado: `python simulate_ransom.py --target /home/lab/files`) — **documente** o nome do script, hora e usuário.
* Checksums antes/depois (por exemplo: `sha256sum arquivo.txt`).
* Como foi feita a chave de criptografia e onde ela foi armazenada (apenas descrição; não publique chave privada em repositórios).

---

## Keylogger simulado — descrição e procedimentos seguros

**Objetivo educativo:** demonstrar como a captura de teclas pode ocorrer e como detectá-la.

**Comportamento simulado (alto nível):**

* Capturar eventos de teclado enquanto um usuário realiza tarefas de teste na VM e gravar em arquivo local (`keystrokes.txt`) dentro de pasta restrita.
* **Não** implementa envio automático para recursos externos; se for necessário demonstrar exfiltração, enviar para um servidor de testes isolado e documentado.

**Evidências a coletar:**

* Arquivo gerado com registro de teclas (mostrar apenas exemplos inofensivos; remova senhas e dados sensíveis do relatório).
* Logs do sistema que indiquem criação/modificação de arquivo e processo responsável.
* Captura de rede (se houver tentativa de envio) e análise de destino.

**Pontos éticos:** remover informações sensíveis do arquivo capturado antes de anexar ao relatório. Nunca salvar credenciais reais.

---

## Coleta de evidências e monitoramento

Registre:

* Snapshots (nome e hash) da VM antes/depois.
* Saída dos logs: sysmon/auditd, auth.log, syslog.
* Capturas de rede relevantes (pcap) gravadas localmente.
* Checksums (sha256) dos arquivos alvo pré e pós-experimento.
* Prints de tela com timestamps (execução, criação de mensagem de resgate, arquivo de keystrokes).

**Formato recomendado para evidências:**

* Pasta `evidencias/` com subpastas: `ransom/`, `keylogger/`, `logs/`, `pcap/`.
* Arquivos nomeados com `YYYYMMDD_HHMMSS_descr.png` ou `.txt`.

---

## Resultados esperados e checklist de validação

* [ ] Snapshot criado antes do teste.
* [ ] Arquivos de teste gerados (lista no relatório).
* [ ] Logs coletados durante a execução.
* [ ] Arquivo de resgate gerado (texto demonstrativo) e capturado em print.
* [ ] Arquivo de keystrokes gerado (conteúdo sanitizado para o relatório).
* [ ] Captura de rede (se aplicável) arquivada.
* [ ] VM restaurada ao snapshot original.

---

## Análise de risco e impacto

* **Risco técnico:** médio/alto se executado em ambiente produtivo (ransomware destrutivo, keyloggers expondo credenciais).
* **Impacto organizacional:** perda de dados, exfiltração de credenciais, comprometimento de contas.
* **Probabilidade:** aumenta quando não há políticas de atualização, backup, EDR ou política de privilégio mínimo.

---

## Detecção, mitigação e recomendações defensivas

### Detecção

* Instrumentar endpoints com EDR (detecção de processos que acessam muitos arquivos e criam padrões de criptografia).
* Monitorar criação de arquivos fora de padrões (e.g., arquivos com extensão alterada, criação de `*_README_FOR_DECRYPT.txt`).
* Monitorar chamadas de API criptográficas em processos atípicos (ex.: uso de OpenSSL, libsodium por processos não autorizados).
* Detectar keyloggers via monitoramento de hooks de teclado ou uso de APIs de captura (syscalls incomuns).

### Mitigação

* Backup e recuperação testados (3-2-1).
* Implementar princípio do menor privilégio (contas sem permissão para criptografar pastas críticas).
* Aplicar políticas de restrição de execução (AppLocker, políticas de execução do Linux).
* Usar proteção anti-tamper para backups e armazenamento offline.

### Resposta e contenção

* Isolar o host afetado (remoção de conectividade), preservar evidências (snapshot), coletar logs e iniciar procedimento de forense.
* Restaurar a partir de backups validados e rotacionados.

---

## Lições aprendidas e próximos passos

* Validar se a instrumentação existente detectaria o comportamento observado.
* Implementar simulações regulares (tabletop + laboratório controlado) para treinar SOC.
* Expandir o experimento: incluir simulação de exfiltração controlada e resposta automatizada.

---

## Apêndice A — Pseudocódigo educativo (não-executável)

> **Atenção:** abaixo há apenas pseudocódigo para fins educacionais. Não execute nem copie como script funcional.

### Ransomware (pseudocódigo)

```
# pseudocódigo educativo
listar_arquivos_em_pasta(target_folder)
para cada arquivo em lista:
    if arquivo for do tipo permitido (ex.: .txt, .docx):
        ler_conteudo(arquivo)
        conteudo_transformado = função_criptográfica_simulada(conteudo)
        salvar_conteudo(arquivo + ".ENC")
        registrar_em_log(processo, arquivo, timestamp)
gerar_arquivo_resgate(mensagem_texto)
```

### Keylogger (pseudocódigo)

```
# pseudocódigo educativo
iniciar_monitor_de_teclado()
abrir_arquivo('keystrokes.txt')
para cada evento_tecla:
    gravar_timestamp_e_tecla_no_arquivo()
fechar_arquivo()
registrar_processo_e_timestamps()
```

---

## Apêndice B — Recursos, leituras e ferramentas seguras

* MITRE ATT&CK (técnicas relevantes: **T1486** — Data Encrypted for Impact; **T1056** — Input Capture)
* Artigos e whitepapers de EDR/forense (recomendado incluir links institucionais).
* Ferramentas seguras para exercício: Sysmon, auditd, inotify-tools, Velociraptor (para investigação), Volatility (forense de memória).
* Wordlists e datasets para simulação de senhas: SecLists (uso responsável).

---

## Observações finais

Se quiser, eu posso:

* Gerar este arquivo em formato Markdown dentro do canvas (já criado).
* Preencher trechos com exemplos de evidências que você me enviar (prints, outputs, hashes) e sanitizar informações sensíveis.
* Gerar um checklist em formato de tabela pronto para impressão.

*Fim do documento.*
