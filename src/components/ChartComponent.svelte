<script>
	import { onMount, onDestroy } from 'svelte';
	import { createChart } from 'lightweight-charts';
	import { priceData } from '../stores/priceData';
	import { writable } from 'svelte/store';

	let chartContainer;
	let chart;
	let areaSeries;
	let chartData = []; // Store chart data here
	const chartDataStore = writable([]);
    const bets = writable([]); // Store pentru pariuri


	const resizeChart = (node) => {
		const handleResize = () => {
			if (chart) {
				chart.applyOptions({ width: node.clientWidth });
			}
		};

		window.addEventListener('resize', handleResize);
		return {
			destroy() {
				window.removeEventListener('resize', handleResize);
			}
		};
	};

	onMount(() => {
		if (typeof window !== 'undefined') {
			chart = createChart(chartContainer, {
				// ... chart options
				layout: {
					backgroundColor: 'transparent',
					textColor: 'black'
				},
				width: chartContainer.offsetWidth,
				height: 400,
				grid: {
					vertLines: {
						color: 'transparent'
					},
					horzLines: {
						color: 'transparent'
					}
				}
			});
			priceData.subscribe((newDataPoint) => {
                
				if (newDataPoint) {
					updateData(newDataPoint);
				}
			});
			chart.applyOptions({
				timeScale: {
					timeVisible: true,
					secondsVisible: true
				}
			});
			areaSeries = chart.addAreaSeries({
				topColor: 'rgba(255, 165, 0, 0.56)', // Adjust the RGBA for gradient fill if needed
				bottomColor: 'rgba(255, 165, 0, 0.04)', // Adjust the RGBA for gradient fill if needed
				lineColor: 'rgba(255, 165, 0, 1)', // Orange color for the line
				lineWidth: 2
			});
            
			priceData.subscribe((data) => {
				if (data.length > 0) {
					// Sort data by time in ascending order and ensure unique timestamps
					let lastTimestamp = 0;
					const sortedData = [...data]
						.sort((a, b) => a.time - b.time)
						.map((d) => {
							let timestamp = Math.floor(d.time.getTime() / 1000);
							if (timestamp <= lastTimestamp) {
								timestamp = lastTimestamp + 1; // Adjust timestamp to be unique
							}
							lastTimestamp = timestamp;
							return { time: timestamp, value: d.price };
						});
					const lastDataPoint = sortedData[sortedData.length - 1];
					areaSeries.setMarkers([
						{
							time: lastDataPoint.time,
							position: 'inSeries',
							color: 'orange',
							shape: 'circle',
							size: 2
						}
					]);
					areaSeries.setData(sortedData);
				}
			});

			// Function to update data on the chart
			function updateData(newDataPoint) {
				const newTime = Math.floor(newDataPoint.time / 1000);
				const newValue = newDataPoint.price;

				// Add the new data point to the chart data array
				chartData.push({ time: newTime, value: newValue });

				// Update the store with the new data array
				chartDataStore.set(chartData);

				// If areaSeries is defined, update the chart
				if (areaSeries) {
					areaSeries.update({ time: newTime, value: newValue });
				}
			}
		}
	});

	onDestroy(() => {
		if (chart) {
			chart.remove();
		}
	});
</script>

<div bind:this={chartContainer} use:resizeChart />
