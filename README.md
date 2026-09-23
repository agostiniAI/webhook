# 🚀 Guia Prático: Webhooks vs. Polling

Bem-vindo ao repositório de estudos e implementação de **Webhooks**! Este documento foi estruturado de forma simples e didática para explicar a mudança de paradigma entre consultas periódicas e a arquitetura orientada a eventos.

---

## 🛑 O Problema do Polling (Consultas Periódicas)

Tradicionalmente, quando uma aplicação precisa de saber se algo aconteceu num serviço externo (como um pagamento aprovado no Stripe), muitos desenvolvedores iniciantes recorrem ao **Polling**.

No **Polling**, a sua aplicação executa um script repetitivo (por exemplo, a cada 1 minuto) perguntando à API externa:
> *"Tem novidades? E agora? E agora?"*

### ⚠️ Principais Gargalos do Polling:
1. **Desperdício de Recursos:** Se consultar uma API 1.440 vezes por dia (uma por minuto) e só existirem 3 eventos reais, mais de 99% das requisições foram inúteis.
2. **Latência Inerente:** Se o evento acontecer logo a seguir a uma verificação, terá de esperar até à próxima consulta para tomar conhecimento.
3. **Limites de Taxa (Rate Limits):** Fazer requisições constantes consome a cota de uso do seu servidor e das APIs integradas, correndo o risco de bloqueios.

---

## ⚡ A Solução: Webhooks (Arquitetura Event-Driven)

Um **Webhook** inverte essa lógica: em vez de a sua aplicação ficar a perguntar o tempo todo, o próprio serviço externo envia uma notificação no instante exato em que o facto acontece.

> **A Analogia da Pizzaria:**  
> * **Polling:** Ficar a ligar para a pizzaria a cada minuto a perguntar se a pizza já saiu do forno.  
> * **Webhook:** Deixar o seu contacto para que a pizzaria lhe ligue no segundo em que o estafeta sair para a entrega.

---

## 🔄 Como Funciona o Ciclo de Vida de um Webhook?

1. **Criação da Rota (Endpoint):** Você cria uma rota HTTP POST na sua aplicação (ex.: `/webhook`).
2. **Subscrição no Serviço Externo:** No painel do serviço (Stripe, GitHub, gateway de pagamento), você cadastra essa URL.
3. **Ocorrência do Evento:** Um evento acontece na origem (ex.: `payment_approved`).
4. **Disparo HTTP POST:** O serviço externo envia um pacote de dados (payload em JSON) para a sua URL.
5. **Confirmação Imediata (Ack 200 OK):** A sua aplicação valida a chamada, responde `200 OK` em milissegundos para liberar a conexão e processa os dados de forma assíncrona.

---

## 📊 Comparativo Técnico

| Critério | Polling (Legado) | Webhooks (Event-Driven) |
| :--- | :--- | :--- |
| **Iniciador** | A sua aplicação consulta | O serviço externo avisa |
| **Tempo de Resposta** | Lento (depende do intervalo do script) | Tempo real (milissegundos) |
| **Consumo de Banda** | Alto e contínuo | Mínimo (apenas quando há eventos) |
| **Garantia de Estado** | Propenso a falhas de concorrência | Resiliente e desacoplado |

---

## 🛡️ Boas Práticas em Webhooks

* **Responda rápido (Status 200 OK):** Nunca faça processamentos pesados antes de confirmar a receção da requisição; utilize processamento em segundo plano ou filas.
* **Garantia de Idempotência:** Guarde o identificador único do evento para garantir que a mesma notificação não seja processada duas vezes em caso de retentativas de rede.
* **Segurança no Cabeçalho:** Utilize sempre ligações seguras (HTTPS) e valide o token/assinatura criptográfica no cabeçalho (*header*) para autenticar a origem do evento.

---

*Projeto desenvolvido para fins didáticos e consolidação de arquiteturas escaláveis em automação inteligente.*
