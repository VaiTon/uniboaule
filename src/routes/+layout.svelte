<script lang="ts">
	import '../app.scss';
	let menuOpen = false;
	function toggleMenu() {
		menuOpen = !menuOpen;
	}
</script>

<nav class="container navbar" aria-label="main navigation">
	<div class="navbar-brand">
		<a class="navbar-item" href="/">
			<h2>Aule@Unibo</h2>
		</a>
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		<!-- svelte-ignore a11y-missing-attribute -->
		<a
			role="button"
			class="navbar-burger"
			aria-label="menu"
			aria-expanded="false"
			on:click={toggleMenu}
		>
			<span aria-hidden="true" />
			<span aria-hidden="true" />
			<span aria-hidden="true" />
		</a>
	</div>
	<div class="navbar-menu" class:is-active={menuOpen}>
		<div class="navbar-start">
			<div class="navbar-item">
				<a href="/matematica">Matematica</a>
			</div>
			<div class="navbar-item">
				<a href="/ingegneria">Ingegneria</a>
			</div>
		</div>
	</div>
</nav>

<main class="container">
	<slot />
</main>

<style lang="scss">
</style>
