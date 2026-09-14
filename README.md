# 🤖 PHGBR Chat & Robust Agent Layer

Este repositório contém um **Userscript avançado (Tampermonkey)** que atua como um cliente de IA multi-provedor (OpenRouter, DeepSeek, Gemini, HuggingFace, Mistral, etc.) injetado nativamente no navegador através de um ambiente isolado por **Shadow DOM**.

O projeto foi desenhado sob a perspectiva de engenharia de software para estender as capacidades de modelos de linguagem (LLMs), permitindo que eles atuem como agentes autônomos de manipulação de código e automação de interface direto no cliente.

## 🚀 Diferenciais Técnicos e Arquitetura

### 1. Sistema Cirúrgico de Edição (Engine de Code-Patching)
Diferente de interfaces comuns que exigem o reenvio de arquivos inteiros, este script implementa um motor de aplicação de mudanças local:
- **Parser Tolerante a Falhas:** Captura e normaliza blocos estruturados de `search-replace` e `unified diff` (`diff -U0`) gerados por IA, tratando Newlines literais (\r\n vs \n) e escapamento de entidades HTML.
- **Auto-Retry Multiestratégia:** Em caso de falha de casamento de string estrita, o script ativa em cascata: normalização de whitespace → supressão de indentação → Bitap Fuzzy Match (com suporte a Web Worker assíncrono) → âncoras de linhas exclusivas.

### 2. Sandbox Isolado por Web Workers (Engine de Plugins)
- Permite a injeção dinâmica de scripts de extensão `.plugin.js`.
- O código do plugin roda inteiramente dentro de um **Web Worker assíncrono isolado**, sem acesso direto ao escopo global do site hospedeiro, comunicando-se com a thread principal apenas por mensagens estruturadas (`postMessage`).
- Implementa um fluxo formal de **Handshake e Revisão Manual**, onde o usuário inspeciona e autoriza individualmente quais funções do plugin a IA poderá invocar via *function calling*.

### 3. Criptografia Local de Segredos (AES-GCM 256-bit)
- Mitiga o risco de exfiltração de chaves de API por outros scripts maliciosos na mesma página.
- **Modo Híbrido:** Suporta proteção *device-bound* (derivação de chave estável via PBKDF2 com 100k iterações baseado em metadados do host) ou *Passphrase* (senha definida pelo usuário, retida estritamente em memória de sessão).

### 4. Saneamento e Privacidade de Rede (Bypass de Vazamento)
- TODAS as requisições para as APIs de IA são blindadas forçando o parâmetro `anonymous: true` no `GM_xmlhttpRequest`, bloqueando o envio acidental de cookies de sessão da página que o usuário está visitando (ex: redes sociais ou home banking).
- Ocultação opcional do HTTP-Referer real por um cabeçalho genérico (`https://userscript.local`) para proteger o histórico de navegação do usuário.

## 🛠️ Como Instalar (Uso Pessoal)
1. Instale a extensão **Tampermonkey** no seu navegador.
2. Crie um novo script e cole o conteúdo do arquivo `phgbr_chat_agent.user.js` contido neste repositório.
3. Acesse qualquer página da web, configure suas chaves de API na aba **Config** do painel e salve.
