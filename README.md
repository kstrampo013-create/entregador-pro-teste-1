# Entregador Pro

Controle financeiro para entregadores de aplicativo: ganhos, despesas, vendas parceladas, empréstimos, carteira de recebíveis, contas mensais, calendário, locais de trabalho e relatórios (PDF/CSV). Funciona no navegador do celular e do computador e pode ser instalado na tela inicial (PWA).

## Funcionalidades

| Área | O que faz |
| --- | --- |
| Cadastro e acesso | Nome, telefone, e-mail e PIN de 6 números. “Manter conectado”, recuperação de PIN por e-mail, troca de PIN, edição do perfil. |
| Confirmação por SMS | Código de 6 dígitos enviado pelo servidor (Twilio). **Fica inativo até o SMS ser configurado** — o app mostra isso claramente. |
| Painel | Lucro do dia/semana/mês, km, R$/km, recebido na semana/mês, a receber, contas pendentes, gráfico, alertas de atraso e vencimentos em 3 dias, lista do mês com “marcar como paga”. |
| Ganhos / Despesas | Lançar, editar, excluir (com confirmação), filtros por mês e tópico, atalhos nomeados para lançamentos repetidos. |
| Vendas / Empréstimos | Parcelas geradas automaticamente; pagamento total com um toque ou parcial; histórico; desfazer pagamento. Juros só quando informados. |
| Carteira | Tudo a receber, sem duplicidade (calculado só das parcelas), com filtros de tipo, cliente, vencimento e situação. |
| Contas mensais | Recorrência mensal/bimestral/trimestral/semestral/anual, próximos vencimentos, marcar pago com valor efetivo. |
| Calendário | Vencimentos, contas, despesas previstas e pagamentos com cores de situação. |
| Calculadora | Períodos prontos ou personalizados, comparação com o período anterior, filtro por tópico, exportação PDF e CSV. |
| Locais | Foto da câmera ou galeria (armazenamento privado), endereço, app usado, observações. |
| Backup | Download completo em JSON. Dados de demonstração marcados como “demo” e removíveis. |

## Tecnologia

- TanStack Start (React 19 + Vite) com Tailwind CSS v4.
- Lovable Cloud: banco PostgreSQL com regras de acesso por linha (cada usuário só vê os próprios dados), autenticação, armazenamento privado de fotos e funções de servidor.
- Regras financeiras puras em `src/lib/finance.ts`, testadas em `src/lib/finance.test.ts`.

## Rodar localmente

```bash
bun install
bun run dev        # http://localhost:8080
bunx vitest run    # testes dos cálculos
```

O arquivo `.env` com a URL e a chave pública do backend é gerado automaticamente pelo Lovable Cloud.

## Variáveis de ambiente e credenciais

| Variável | Onde | Para quê |
| --- | --- | --- |
| `VITE_SUPABASE_URL`, `VITE_SUPABASE_PUBLISHABLE_KEY` | navegador | Conexão com o backend (automático, chave pública). |
| `SUPABASE_URL`, `SUPABASE_PUBLISHABLE_KEY`, `SUPABASE_SERVICE_ROLE_KEY` | servidor | Usadas pelas funções de servidor (automático, nunca expostas). |
| `LOVABLE_API_KEY` | servidor | Acesso ao gateway de conectores (automático). |
| `TWILIO_API_KEY` | servidor | Criada ao conectar a Twilio pelo conector do Lovable. **Pendente.** |
| `TWILIO_FROM_NUMBER` | servidor | Número Twilio que envia o SMS, formato `+55...`. **Pendente.** |

### Ativar o SMS

1. Crie uma conta na Twilio e compre um número que envie SMS para o Brasil.
2. No Lovable, peça: “conectar Twilio” e informe o número remetente quando solicitado.
3. Na Twilio, ative **SMS Pumping Protection** e limite **SMS Geo Permissions** ao Brasil para evitar fraudes de envio.

Sem essas credenciais, o telefone fica salvo como “não confirmado” e o restante do app funciona normalmente.

## Publicar

No Lovable, clique em **Publish**. O app fica em um endereço `*.lovable.app`; um domínio próprio pode ser ligado em *Settings → Domains*. Depois de publicado, abra no Chrome do Android e use **Adicionar à tela inicial** para instalar.

## Segurança

- Regras de acesso no banco em todas as tabelas: leitura e escrita só do próprio usuário.
- Pagamentos de parcelas não podem ultrapassar o saldo; o valor pago é recalculado pelo banco a partir do histórico.
- Valores de vendas e empréstimos não são editáveis depois de criados (só dados de contato e observações) para manter as parcelas consistentes.
- O status “telefone confirmado” só pode ser alterado pelo servidor após validar o código.
- Fotos ficam em armazenamento privado, acessadas por links temporários.
