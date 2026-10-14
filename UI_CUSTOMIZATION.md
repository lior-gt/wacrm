# UI customization workspace

Use the `ui-customizations` branch to change WACRM's presentation while keeping
the existing authentication, Supabase queries, realtime subscriptions, and API
operations. The full application is included; no separate frontend app or
later reconnection step is necessary.

## UI entry points

| What you want to edit | Source file |
| --- | --- |
| Inbox layout and how its panels connect | [inbox/page.tsx](src/app/(dashboard)/inbox/page.tsx) |
| Conversation list, previews, and unread indicators | [conversation-list.tsx](src/components/inbox/conversation-list.tsx) |
| Message thread, assignment, and status controls | [message-thread.tsx](src/components/inbox/message-thread.tsx) |
| Reply composer | [message-composer.tsx](src/components/inbox/message-composer.tsx) |
| Individual messages | [message-bubble.tsx](src/components/inbox/message-bubble.tsx) |
| Customer side panel: details, tags, deals, notes | [contact-sidebar.tsx](src/components/inbox/contact-sidebar.tsx) |
| Navigation | [sidebar.tsx](src/components/layout/sidebar.tsx) |
| App header | [header.tsx](src/components/layout/header.tsx) |
| Sales pipeline board | [pipelines/page.tsx](src/app/(dashboard)/pipelines/page.tsx) |
| Automation list | [automations/page.tsx](src/app/(dashboard)/automations/page.tsx) |
| Contacts screen | [contacts/page.tsx](src/app/(dashboard)/contacts/page.tsx) |
| English UI text | [en.json](messages/en.json) |

Start by simplifying the customer side panel and the thread controls. Keep the
existing components and handlers; hide or rearrange presentation sections.
Reuse the controls under `src/components/ui/` and existing Tailwind styles.

## Preserve these connections

- [use-auth.tsx](src/hooks/use-auth.tsx): session, account, and role context.
- [Supabase client](src/lib/supabase/client.ts) and
  [server client](src/lib/supabase/server.ts): existing data access.
- [Types](src/types/index.ts): customer, conversation, message, and deal shapes.
- The inbox page's realtime subscriptions and selected-conversation callbacks.
- The message thread's status and assignment handlers and reply requests to
  `/api/whatsapp/send`, including loading, delivery, and failure feedback.
- Backend routes under `src/app/api/`, business logic under `src/lib/`, and
  database migrations under `supabase/migrations/` for presentation-only edits.

Some frontend components make Supabase calls directly. A file being in the
frontend does not mean all its logic is visual: preserve those calls and the
account scoping when changing JSX. Hiding a button does not replace backend
permissions or database row-level security.

## Local preview

The existing WACRM demo runs at localhost:3001 from a separate checkout. Editing
this branch does not change that demo automatically.

1. Install Node 20 or later and the locked dependencies with `npm ci`.
2. Read `AGENTS.md`. Before editing Next.js behavior, consult the installed
   framework guides under `node_modules/next/dist/docs/`.
3. Create an ignored `.env.local` using `.env.local.example`. Supply local
   Supabase URL, public key, service-role key, and an encryption key. Preserve
   the encryption key if reusing existing encrypted settings.
4. Use a sample-data Supabase database, not production. For this computer's
   existing demo, its backend is at `http://127.0.0.1:54321`; credentials remain
   in the separate local demo checkout and are deliberately absent from GitHub.
5. Start this checkout on another local port:

```sh
npm run dev -- --hostname 127.0.0.1 --port 3002
```

Open http://localhost:3002/login and use a demo account. Changes hot-reload.
If testing email sign-in, invitation, or password-reset links on this port, also
update the local Supabase redirect allowlist; see [auth-emails.md](docs/auth-emails.md).

Reusing the demo database means assignment and status changes from either
checkout affect the same sample records. Do not reset, reseed, or apply new
migrations just to change the UI. WhatsApp sending still needs a real integration;
the existing sample conversations cannot send real messages.

For designs without a running backend, provide mock props and callbacks to
the existing components using the same data types. That is a separate preview
task; this branch does not add a mock server or disable authentication.

## Checks before merging a UI change

- Run `npm run typecheck`, lint the changed files, and run `npm run build`.
- Check conversation selection, assignment persistence, unread indicators,
  status changes, and the composer at desktop and narrow widths.
- Verify keyboard use and loading/error states still work.
- Keep `.env.local`, access tokens, local Supabase status files, logs, and demo
  exports out of commits.
- For packaging the UI and backend together, reuse the existing Docker setup
  in [docker.md](docs/docker.md); no second application is needed.

This branch currently adds only this guide. No redesign, backend change,
dependency installation, or replacement of the running demo has been applied.
