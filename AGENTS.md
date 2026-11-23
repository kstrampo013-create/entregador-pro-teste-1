<!-- LOVABLE:BEGIN -->
> [!IMPORTANT]
> This project is connected to [Lovable](https://lovable.dev). Avoid rewriting
> published git history — force pushing, or rebasing/amending/squashing commits
> that are already pushed — as it rewrites history on Lovable's side and the
> user will likely lose their project history.
>
> Commits you push to the connected branch sync back to Lovable and show up in
> the editor, so keep the branch in a working state.
<!-- LOVABLE:END -->

## Architecture rules

- Domain math (installments, interest, periods, wallet, bills, goals) lives only in `src/lib/finance.ts` and is unit-tested in `src/lib/finance.test.ts`; UI never re-implements it.
- Browser reads/writes go straight to the database through the generated client with RLS (`src/hooks/use-data.ts`); server functions are used only where secrets are needed (SMS in `src/lib/sms.functions.ts`).
- Installment paid amounts are derived by DB triggers from `installment_payments`; never write `installments.paid_amount` from the app (column is not granted).
- Sales/loans plus their installments are created atomically through the `create_*_with_installments` RPCs, which validate that installments sum to the financed total.
- New records use a client-generated UUID so double submits hit a unique violation that is treated as success (no duplicates).
- Demo data is flagged `is_demo = true` on every table so it can be removed without touching real data.
- PWA is manifest-only (no service worker) to keep the preview safe.
- Nels Nere news/AI/Telegram server work lives in `src/lib/*.server.ts` and `/api/thassi/*` routes (auth checked per request); one managed 5-minute schedule calls `/api/public/jobs/nels-nere`, which locks via `job_state` so runs never overlap.
- Overdue bills are computed across every month since each bill started (`useOverdueBills`), so the dashboard alert and the bills page always list the same late items with a pay action.
