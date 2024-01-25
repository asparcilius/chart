<script>
	import ChartComponent from '../components/ChartComponent.svelte';
	import DataFetcher from '../components/DataFetcher.svelte';
	import BettingContainer from '../components/BettingContainer.svelte';
	import '../styles/app.css';
	import { onMount, onDestroy } from 'svelte';

	let showBettingPanel = false;

function toggleBettingPanel() {
  // Toggle the betting panel only on mobile
  showBettingPanel = !showBettingPanel;
}
onMount(() => {
    function handleResize() {
      if (window.innerWidth >= 769) {
        showBettingPanel = true;
      } else {
        showBettingPanel = false;
      }
    }

    window.addEventListener('resize', handleResize);

    // Asigurați-vă că panoul de pariere este setat corect la încărcarea inițială
    handleResize();

    return () => {
      window.removeEventListener('resize', handleResize);
    };
  });

  onDestroy(() => {
    // Nu este nevoie de codul de cleanup aici, onMount se ocupă de asta
  });
</script>

<main>
	<div class="dashboard">
		<div class="chart-container">
			<ChartComponent />
			<DataFetcher />
		</div>
		<div class="betting-container-panel">
			

			<button on:click={toggleBettingPanel} class="mobile-bet-button">New Bet</button>
			{#if showBettingPanel}
				<BettingContainer />
			{/if}
			<!-- Elementele interfeței pentru sistemul de pariuri -->
			<!-- Aici vei adăuga butoanele, input-urile și alte controale necesare -->
		</div>
	</div>

	<!-- Orice alte componente sau elemente adiționale -->
</main>
