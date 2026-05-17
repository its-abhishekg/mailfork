# mailfork

> Real email infrastructure for QA and testing teams.


**For new projects, install `@mailfork/sdk` directly.**  
`mailfork` exists as a convenience alias and for name reservation.

---

## Install

```bash
npm install @mailfork/sdk
# or
npm install mailfork
```

## Quick start

```ts
import { MailForkClient } from '@mailfork/sdk';

const mf = new MailForkClient({
  apiKey: process.env.MAILFORK_API_KEY!,
  orgSlug: 'acme',
});

// List emails in an inbox
const emails = await mf.emails.list('inbox-id');

// Extract an OTP from the next email (polls until found or timeout)
const otp = await mf.emails.extractOtp('inbox-id', {
  from: 'no-reply@yourapp.com',
  subject: 'Your verification code',
});

// Extract a magic link URL
const url = await mf.emails.extractUrl('inbox-id', {
  button_text: 'Verify email',
});
```

## Playwright integration

```ts
import { withMailFork } from '@mailfork/sdk/playwright';

const mf = withMailFork({
  apiKey: process.env.MAILFORK_API_KEY!,
  orgSlug: 'acme',
});

test('email verification flow', async ({ page }) => {
  const otp = await mf.waitForOtp(page, 'inbox-id', async () => {
    await page.click('#send-code');
  });
  await page.fill('#otp-input', otp);
});
```

## Ephemeral aliases (CI/CD)

Create a short-lived tagged address per test run — no inbox clutter, no conflicts between parallel runs.

```ts
const alias = await mf.inboxes.createAlias('inbox-id', {
  tag: `run-${Date.now()}`,
  is_ephemeral: true,
  ttl_hours: 1,
  on_expire: 'delete',
});

// use alias.tag in your test: inbox+run-1234567890@team.acme.mailfork.dev
```

## Resources

- **Docs:** [mailfork.dev/docs](https://mailfork.dev/docs)
- **SDK package:** [`@mailfork/sdk`](https://www.npmjs.com/package/@mailfork/sdk)
- **Issues:** [github.com/its-abhishekg/mailfork](https://github.com/its-abhishekg/mailfork/mailfork/issues)

## License

MIT
