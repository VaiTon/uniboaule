<script lang="ts">
	import dayjs from 'dayjs';
	import type { Aula, Impegno } from './api';

	export let impegnoAule: { aula: Aula; impegno: Impegno | undefined }[];
</script>

<table class="table">
	<thead>
		<tr>
			<th>Aula</th>
			<th>Capienza</th>
			<th>Impegno</th>
			<th>Orari</th>
		</tr>
	</thead>
	{#each impegnoAule as { aula, impegno }}
		<tr>
			<td>{aula.descrizione}</td>
			<td>{aula.capienza}</td>
			<td>{impegno ? impegno.nome : 'libera'}</td>
			<td>
				{#if impegno}
					{dayjs(impegno.dataInizio).format('HH:mm')} - {dayjs(impegno.dataFine).format('HH:mm')}
				{/if}
			</td>
		</tr>
	{/each}
</table>
