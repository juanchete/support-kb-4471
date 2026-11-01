<script lang="ts">
  // Inline count, the desk's way: <ProblemCount ticket={4471} cause="hint" />.
  // No `cause`: the number of problems. `oneDayMove`: the reports the desk read as a one-day move.
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { groupByCause, readAs, uniqueById, type Cause, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  let { ticket, cause, oneDayMove = false }: { ticket: number; cause?: Cause; oneDayMove?: boolean } = $props();

  const groups = $derived(groupByCause(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket), tenantOf));
  const n = $derived(
    oneDayMove
      ? groups.reduce((s, g) => s + readAs(g, notes.oneDayMove), 0)
      : cause
        ? (groups.find((g) => g.cause === cause)?.reports.length ?? 0)
        : groups.length,
  );
</script>

<strong>{n}</strong>
