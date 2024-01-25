<script>
    import { priceData } from '../stores/priceData';
    import { browser } from '$app/environment';
  
    if (browser) {
      const socket = new WebSocket('wss://ws.okx.com:8443/ws/v5/public');
  
      socket.onopen = () => {
        socket.send(JSON.stringify({
          op: 'subscribe',
          args: [{ channel: 'tickers', instId: 'BTC-USDT' }]
        }));
      };
  
      socket.onmessage = (event) => {
        const message = JSON.parse(event.data);
        if (message.data && message.data[0]) {
          const newTime = new Date(parseInt(message.data[0].ts));
          const newPrice = parseFloat(message.data[0].last);
          priceData.update(data => [...data, { time: newTime, price: newPrice }]);
        }
      };
  
      socket.onerror = (error) => {
        console.error('WebSocket error:', error);
      };
    }
  </script>
  