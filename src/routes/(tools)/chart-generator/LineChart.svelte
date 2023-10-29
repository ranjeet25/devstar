<script>
	import { onMount } from 'svelte';
	import * as am5 from '@amcharts/amcharts5?client';
	import * as am5xy from '@amcharts/amcharts5/xy?client';
	import am5themes_Animated from '@amcharts/amcharts5/themes/Animated?client';
	// import * as am5plugins_exporting from '@amcharts/amcharts5/plugins/exporting?client';
	import * as am5plugins_exporting from '../../../../node_modules/@amcharts/amcharts5/plugins/exporting';

	let chartdiv;
	let chart;
	let data = [];
	let chartWidth = '100%';
	let xAxis;
	let series;

	function handleResize() {
		if (window.innerWidth > 768) {
			chartWidth = '600px';
		} else {
			chartWidth = '100%';
		}
	}

	onMount(() => {
		var root = am5.Root.new(chartdiv);
		handleResize();
		root.setThemes([am5themes_Animated.new(root)]);

		chart = root.container.children.push(
			am5xy.XYChart.new(root, {
				panX: true,
				panY: true,
				wheelX: 'panX',
				wheelY: 'zoomX',
				pinchZoomX: true
			})
		);

		xAxis = chart.xAxes.push(
			am5xy.CategoryAxis.new(root, {
				maxDeviation: 0.3,
				categoryField: 'country',
				renderer: am5xy.AxisRendererX.new(root, { minGridDistance: 30 }),
				tooltip: am5.Tooltip.new(root, {})
			})
		);

		var yAxis = chart.yAxes.push(
			am5xy.ValueAxis.new(root, {
				maxDeviation: 0.3,
				renderer: am5xy.AxisRendererY.new(root, {
					strokeOpacity: 0.1
				})
			})
		);

		series = chart.series.push(
			am5xy.LineSeries.new(root, {
				name: 'Series 1',
				xAxis: xAxis,
				yAxis: yAxis,
				valueYField: 'value',
				categoryXField: 'country',
				tooltip: am5.Tooltip.new(root, {
					labelText: '{valueY}'
				})
			})
		);

		series.bulletField.set('type', 'CircleBullet');
		series.bulletField.set('circle', function (circle) {
			circle.fillOpacity.set(1);
			circle.radius.set(5);
		});

		series.bullets.push(
			am5.Bullet.new(root, {
				type: 'CircleBullet',
				fillOpacity: 1
			})
		);

		series.appear(1000);
		chart.appear(1000, 100);
		updateChart();
		window.addEventListener('resize', handleResize);
	});

	function downloadChart() {
  if (chart && chart.container) {
    const chartImage = chart.container.toBlob((blob) => {
      const url = URL.createObjectURL(blob);

      const a = document.createElement('a');
      a.style.display = 'none';
      a.href = url;
      a.download = 'chart.png';
      document.body.appendChild(a);

      a.click();

      window.URL.revokeObjectURL(url);
    });
  }
}

	function updateChart() {
		if (chart) {
			xAxis.data.setAll(data);
			series.data.setAll(data);
		}
	}

	function addData() {
		const XaxisInput = document.querySelector('#XaxisInput').value;
		const YaxisInput = parseFloat(document.querySelector('#YaxisInput').value);

		if (XaxisInput && !isNaN(YaxisInput)) {
			data.push({ country: XaxisInput, value: YaxisInput });
			document.querySelector('#last_data_added').style.display = 'flex';
			document.querySelector('#Xaxis_value').innerHTML = XaxisInput;
			document.querySelector('#Yaxis_value').innerHTML = YaxisInput;
			document.querySelector('#XaxisInput').value = '';
			document.querySelector('#YaxisInput').value = '';
		}
	}

	function viewAllData() {
		if (document.querySelector('#alldata').style.display === 'none') {
			document.querySelector('#alldata').style.display = 'block';
			document.querySelector('#view_all_btn').innerText = 'Close';
		} else {
			document.querySelector('#alldata').style.display = 'none';
			document.querySelector('#view_all_btn').innerText = 'View all';
		}
	}
</script>

<div
	class="main-container mb-4 flex justify-center items-center flex-col lg:flex-row py-4 px-12 border-2 rounded-lg shadow-sm shadow-blue-300"
>
	<div class="min-w-full lg:min-w-0 bg-transparent lg:w-1/3">
		<div id="input-div">
			<input
				type="text"
				id="XaxisInput"
				class="mb-2 rounded-lg border-gray-300 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-blue-500"
				placeholder="Value on x-axis"
			/>
			<input
				type="number"
				id="YaxisInput"
				class="rounded-lg border-gray-300 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:border-blue-500"
				placeholder="Value on y-axis"
				min="0"
				step="1"
			/>
		</div>
		<div class="button-container">
			<button
				class="mt-4 bg-white hover-bg-gray-100 text-gray-800 font-semibold py-2 px-4 border border-gray-400 rounded shadow"
				on:click={addData}
			>
				Add Data
			</button>
			<button
				class="mt-4 bg-white hover-bg-gray-100 text-gray-800 font-semibold py-2 px-4 border border-gray-400 rounded shadow"
				on:click={updateChart}
			>
				Update Chart
			</button>
			<button
				class="mt-4 text-xs bg-white hover-bg-gray-100 text-gray-800 font-semibold py-2 px-4 border border-gray-400 rounded shadow"
				on:click={downloadChart}
			>
				Download ⬇️
			</button>
		</div>
		<div style="display: none;" id="last_data_added" class="py-2">
			<p class="text-sm font-bold py-2 dark:text-white">
				Added Data
				<span class="px-2 py-1 text-green-700 bg-green-200 rounded-md" id="Xaxis_value" />
				<span class="py-1 px-2 text-green-700 bg-green-200 rounded-md" id="Yaxis_value" />
				<button
					on:click={viewAllData}
					id="view_all_btn"
					class="btn py-1 px-2 text-blue-700 bg-blue-200 rounded-md"
				>
					View all
				</button>
			</p>
		</div>
		<div
			style="display: none;"
			id="alldata"
			class="w-3/4 h-32 bg-gray-100 mt-2 rounded-sm py-4 overflow-y-auto"
		>
			<table id="alldata_table" class="w-full overflow-y-auto" />
		</div>
	</div>
	<div class="chart_container lg:w-1/3">
		<div
			id="chartdiv"
			bind:this={chartdiv}
			style="width: {chartWidth}; height: 400px; background-color: rgb(243, 243, 243); border-radius: 10px;"
		/>
	</div>
</div>

<style>
	body{
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif,
			'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';
	}

	.chart_container {
		display: flex;
		padding: 20px;
		width: 500px;
	}

	@media (max-width: 768px) {
		.main-container {
			min-width: 350px;
		}
		.chart_container {
			flex-direction: column;
			justify-content: center;
			align-items: center;
			width: 350px;
			height: 300px;
		}
		.button-container {
			justify-content: center;
		}
		#chartdiv {
			width: 100%;
		}
	}
</style>
