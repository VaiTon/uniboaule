<script lang="ts">
	import type { PageData } from './$types';

	import moment from 'moment';

	import markerIcon from 'leaflet/dist/images/marker-icon.png';
	import markerIcon2x from 'leaflet/dist/images/marker-icon-2x.png';
	import shadowUrl from 'leaflet/dist/images/marker-shadow.png';

	import Calendar from '@event-calendar/core';
	import TimeGrid from '@event-calendar/time-grid';
	import '@event-calendar/core/index.css';

	export let data: PageData;
	$: aula = data.aula;

	$: console.debug(aula);

	import { onMount } from 'svelte';
	import L from 'leaflet';
	import 'leaflet/dist/leaflet.css';
	onMount(() => {
		if (aula.relazioneEdificio.geo != null) {
			const map = L.map('map', { attributionControl: false }).setView(
				[aula.relazioneEdificio.geo.lat, aula.relazioneEdificio.geo.lng],
				13
			);
			L.control.attribution({ prefix: false }).addTo(map);

			const osmCopyright =
				'&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors';

			L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
				attribution: osmCopyright
			}).addTo(map);
			const iconDefault = L.icon({
				iconRetinaUrl: markerIcon2x,
				iconUrl: markerIcon,
				shadowUrl,
				iconSize: [25, 41],
				iconAnchor: [12, 41],
				popupAnchor: [1, -34],
				tooltipAnchor: [16, -28],
				shadowSize: [41, 41]
			});

			L.marker([aula.relazioneEdificio.geo.lat, aula.relazioneEdificio.geo.lng], {
				icon: iconDefault
			})
				.addTo(map)
				.bindPopup(aula.relazioneEdificio.descrizione)
				.openPopup();
		}
	});
</script>

<h1 class="text-4xl font-bold my-4">{aula?.descrizione}</h1>

<p class="my-4 grid gap-x-8 grid-cols-[max-content,1fr]">
	<span>Record creato il:</span> <span>{moment(aula.dataCreazione).format('lll')}</span>
	<span>Ultima modifica il:</span> <span>{moment(aula.dataModifica).format('lll')}</span>
</p>

<p class="grid gap-x-8 grid-cols-[max-content,1fr,max-content,1fr]">
	<span>Capienza:</span>
	<span>{aula?.capienza}</span>

	<span>Divisore capienza</span>
	<span>{aula?.divisoreCapienza}</span>

	<span>Capienza effettiva:</span>
	<span>{aula?.capienzaEffettiva}</span>

	<span>Numero postazioni:</span>
	<span>{aula?.numeroPostazioni}</span>

	<span>Metri quadri:</span>
	<span>{aula?.metriQuadri} mq</span>
</p>

<div>
	{#if aula.piano != null}
		<h2 class="text-2xl font-bold mt-6 mb-2">Piano</h2>
		<span>{aula?.piano.descrizione} (<code>{aula?.piano.codice}</code>)</span>
	{/if}
	<details>
		<summary>Debug</summary>
		<pre>{JSON.stringify(aula.piano, null, 2)}</pre>
	</details>
</div>

<div>
	<h2 class="text-2xl font-bold mt-6 mb-2">Edificio</h2>

	<div class="grid gap-x-8 grid-cols-[max-content,1fr]">
		<span>Descrizione:</span>
		<span>{aula?.relazioneEdificio.descrizione}</span>

		<span>Indirizzo:</span>
		<span>{aula?.relazioneEdificio.via}, {aula?.relazioneEdificio.comune}</span>

		<span>Plesso:</span>
		<span>{aula?.relazioneEdificio.plesso}</span>

		<span>Codice:</span>
		<span>{aula?.relazioneEdificio.codice}</span>

		<span>Orario apertura:</span>
		<span>{aula?.relazioneEdificio.orarioApertura}</span>

		<span>Orario chiusura:</span>
		<span>{aula?.relazioneEdificio.orarioChiusura}</span>

		<details>
			<summary>Debug</summary>
			<pre>{JSON.stringify(aula.relazioneEdificio, null, 2)}</pre>
		</details>
	</div>
</div>
<div>
	<div class="h-80 w-full mt-4" id="map" />
</div>
<div>
	<h2 class="text-2xl font-bold mt-6 mb-2">Prossimi impegni</h2>

	{#if data.impegni.length === 0}
		<p class="alert mb-4">Nessun impegno</p>
	{:else}
		<Calendar
			plugins={[TimeGrid]}
			options={{
				firstDay: 1,
				nowIndicator: true,
				flexibleSlotTimeLimits: true,
				slotMinTime: '08:00',
				slotMaxTime: '20:00',
				view: 'timeGridWeek',
				events: data.impegni.map((impegno) => {
					let title = impegno.nome;
					if (impegno.evento.dettagliDidattici[0].corso != null) {
						title += ` (${impegno.evento.dettagliDidattici[0].corso.descrizione})`;
					}

					return {
						id: impegno.id,
						title: title,
						start: moment(impegno.dataInizio).toDate(),
						end: moment(impegno.dataFine).toDate()
					};
				})
			}}
		/>
		<table class="table">
			<thead>
				<tr>
					<th>Tipo Attività</th>
					<th>Nome</th>
					<th>Corso</th>
					<th>Periodo</th>
				</tr>
			</thead>
			<tbody>
				{#each data.impegni as impegno}
					<tr>
						<td>{impegno.evento.tipoAttivita.descrizione}</td>
						<td>{impegno.nome}</td>
						<td>{impegno.evento.dettagliDidattici[0].corso?.descrizione ?? ''}</td>
						<td>{moment(impegno.dataInizio).format('lll')}</td>
						<td>{moment(impegno.dataFine).format('lll')}</td>
					</tr>
				{/each}
			</tbody>
		</table>
	{/if}
</div>

<details>
	<summary>Debug aula</summary>
	<pre>{JSON.stringify(aula, null, 2)}</pre>
</details>

<details>
	<summary>Debug eventi</summary>
	<pre>{JSON.stringify(data.impegni, null, 2)}</pre>
</details>
