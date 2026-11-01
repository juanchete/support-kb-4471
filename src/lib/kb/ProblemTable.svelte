<script lang="ts">
  // The problems inside a merged ticket, counted the desk's way (causes.ts), with what became of each.
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import tickets from '#lib/data/tickets-4471.json';
  import { groupByCause, readAs, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  let { ticket }: { ticket: number } = $props();

  type Ticket = { url: string; outcome: string; by: string };
  const t = tickets as unknown as Record<string, Ticket>;
  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  const groups = $derived(groupByCause(merged, tenantOf));
  const num = (url: string): string => `#${url.split('/').pop()}`;
</script>

<p class="sum">
  <strong>{merged.length}</strong> reports in #{ticket} ·
  <strong>{groups.length}</strong> {groups.length === 1 ? 'problem' : 'problems'} ·
  {(reports as Report[]).length - merged.length} duplicate rows in the export dropped
</p>

<table>
  <thead>
    <tr><th>problem</th><th>reports</th><th>tenants</th><th>seen</th><th>ticket</th><th>outcome</th></tr>
  </thead>
  <tbody>
    {#each groups as g (g.cause)}
      <tr>
        <td><code>{g.cause}</code></td>
        <td>{g.reports.length}</td>
        <td>{g.tenants}</td>
        <td>{g.firstSeen} → {g.lastSeen}</td>
        <td>{#if t[g.cause]}<a href={t[g.cause].url}>{num(t[g.cause].url)}</a>{:else}none{/if}</td>
        <td>{#if t[g.cause]}<b>{t[g.cause].outcome}</b> · {t[g.cause].by}{/if}</td>
      </tr>
    {/each}
  </tbody>
</table>

{#each groups as g (g.cause)}
  {@const moved = readAs(g, notes.oneDayMove)}
  {#if moved}
    <p class="note">
      {moved} of the <code>{g.cause}</code> reports read as a one-day move, not a day/month swap. Counted under
      <code>{g.cause}</code> by tenant; tracked in <a href={t.oneDayMove.url}>{num(t.oneDayMove.url)}</a>
      (<b>{t.oneDayMove.outcome}</b> · {t.oneDayMove.by}).
    </p>
  {/if}
{/each}

<style>
  .sum { font-size: 1.1rem; }
  .note { color: var(--muted); font-size: 0.85em; }
</style>
