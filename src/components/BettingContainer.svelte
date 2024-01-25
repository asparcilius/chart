<script>
    import { writable } from 'svelte/store';
  
    // Creăm un store pentru soldul utilizatorului
    const balance = writable(1000); // Soldul inițial, de exemplu, 1000 de monede virtuale
  
    const betState = writable({
      direction: 'up', // direcția inițială
      wager: 10.00, // suma inițială pariată
      multiplier: 1.0, // multiplicatorul inițial
      // ...alte stări necesare
    });
  
    // Funcția pentru a verifica dacă utilizatorul are suficiente fonduri pentru pariu
    function canPlaceBet(wager) {
      let canBet = false;
      balance.subscribe($balance => {
        canBet = $balance >= wager;
      })();
      return canBet;
    }
  
    // Funcții pentru a actualiza starea pariurilor
    function setDirection(direction) {
      betState.update(state => {
        return { ...state, direction };
      });
    }
  
    function setWager(event) {
      const wager = parseFloat(event.target.value);
      betState.update(state => {
        return { ...state, wager };
      });
    }
  
    function setMultiplier(event) {
      const multiplier = parseFloat(event.target.value);
      betState.update(state => {
        return { ...state, multiplier };
      });
    }
  
    // Funcția de plasare a pariului
    function placeBet() {
      betState.update($betState => {
        if (canPlaceBet($betState.wager)) {
          balance.update($balance => $balance - $betState.wager);
          console.log('Bet placed', $betState);
          return $betState;
        } else {
          console.log('Insufficient funds');
          return $betState;
        }
      });
    }
  </script>
  
  <div class="betting-container">
    <h3>WILL THE PRICE GO UP OR DOWN?</h3>
    <div class="direction-buttons">
      <button on:click={() => setDirection('up')}>Up</button>
      <button on:click={() => setDirection('down')}>Down</button>
    </div>
    <div class="bet-options">
      <label for="wager">WAGER</label>
      <input id="wager" type="number" min="0" on:input={setWager}>
      <label for="multiplier">PAYOUT MULTIPLIER</label>
      <input id="multiplier" type="number" min="1" on:input={setMultiplier}>
    </div>
    <button on:click={placeBet}>PLACE BET</button>
  
    <!-- Afisam soldul utilizatorului -->
    <div class="balance">
      <h4>Your balance:</h4>
      <p>{$balance} virtual coins</p>
    </div>
  </div>
  
  <style>
    /* stilurile existente */
    .balance {
      margin-top: 1rem;
      padding: 1rem;
      background-color: #eee;
      border-radius: 8px;
      text-align: center;
    }
  </style>
  