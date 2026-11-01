<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import type { Report } from '#lib/kb/causes';
  import { groupByProblem, type Problem } from '#lib/kb/problems';
  import { tenantOf } from '#lib/kb/locales';

  // Inline count: <ProblemCount ticket={4471} problem="hint" />. Leave out `problem` for the number of problems.
  let { ticket, problem }: { ticket: number; problem?: Problem } = $props();

  const groups = $derived(groupByProblem(reports as Report[], ticket, tenantOf, notes.oneDayMove));
  const n = $derived(problem ? (groups.find((g) => g.problem === problem)?.reports.length ?? 0) : groups.length);
</script>

<strong>{n}</strong>
