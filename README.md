# Relatório de Projeto — Malware Simulado em Python (Ransomware & Keylogger)

**Autor:** Jodi Vockt
**Curso / Evento:** DIO — Curso de Cybersecurity
**Data:** 24/10/2025
**Ambiente:** Laboratório controlado (VM isolada, snapshots, sem conectividade externa)
**Observações importantes:** todos os testes foram realizados em ambiente 100% controlado e isolado, com snapshots antes e depois. Este documento **não** contém código malicioso executável; contém descrições, pseudocódigo educativo e orientações de defesa. Qualquer implementação prática deve seguir políticas éticas e autorização formal.

---

## Sumário

1. [Escopo, ética e segurança](#Escopo,-ética-e-segurança)
2. [Ransomware simulado — descrição e procedimentos seguros](#ransomware-simulado---descrição-e-procedimentos-seguros)
3. [Keylogger simulado — descrição e procedimentos seguros](#keylogger-simulado---descrição-e-procedimentos-seguros)
4. [Coleta de evidências e monitoramento](#coleta-de-evidências-e-monitoramento)
5. [Análise de risco e impacto](#análise-de-risco-e-impacto)
6. [Detecção, mitigação e recomendações defensivas](#detecção-mitigação-e-recomendações-defensivas)
7. [Lições aprendidas e próximos passos](#lições-aprendidas-e-próximos-passos)

---


## Escopo, ética e segurança

* **Escopo:** experimentos restritos a uma VM isolada (snapshot antes/depois), sem acesso à rede corporativa ou internet pública.
* **Autorização:** testes realizados com permissão para fins acadêmicos. Registrar a autorização caso faça parte de entrega institucional.
* **Regra de ouro:** não publicar código malicioso, não transferir artefatos para outros ambientes, e sempre restaurar snapshot após o teste.

> **Aviso:** este relatório não fornece código que possa ser usado para atacar computadores alheios. Fornece pseudocódigo, descrições e orientações defensivas para aprendizado.

---



## Ransomware simulado — descrição e procedimentos seguros

**Objetivo educativo:** entender o ciclo de criptografia de arquivos e as estratégias de mitigação.

**Comportamento simulado:**

* Identificar a pasta teste+files onde contém os arquivos de teste a serem criptografados.
* Aplicar transformação criptográfica **não destrutiva**: usar uma chave local temporária chamada de "Chave.key" para criptografia e descriptografia.
* Gerar um arquivo de texto "ATENÇÂO.TXT" com a mensagem de resgate explicativa.

**Nota importante de segurança:** Não executar algoritmos de criptografia que tornem dados irrecuperáveis em ambiente que não seja descartável. Sempre mantenha backups e snapshots.

**Evidências a coletar:**

* Lista de arquivos antes/depois (`dados_confidenciais.txt` / `senhas.txt`).
* O documento com as evidencias de teste e execusão dos arquivos estão no arquivo **"Malware Simulado em Python (Ransomware & Keylogger)"** localizado no diretório git. 

---

## Keylogger simulado — descrição e procedimentos seguros

**Objetivo educativo:** demonstrar como a captura de teclas pode ocorrer.

**Comportamento simulado:**

* Capturar eventos de teclado enquanto um usuário realiza tarefas de teste na VM e gravar em arquivo local (`log.txt`) dentro de pasta keylogger.

**Evidências a coletar:**

* Arquivo gerado com registro de teclas (mostrar apenas exemplos inofensivos; remova senhas e dados sensíveis do relatório).
* O documento com as evidencias de teste e execusão dos arquivos estão no arquivo **"Malware Simulado em Python (Ransomware & Keylogger)"** localizado no diretório git.
* 
**Pontos éticos:** remover informações sensíveis do arquivo capturado antes de anexar ao relatório. Nunca salvar credenciais reais.

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

