<script lang="ts">
    import { Label, Range, Checkbox } from 'flowbite-svelte';

    import Intro from '$lib/Intro.svelte';
    import Copy from '$lib/Copy.svelte';

    export let data;

    const charsets = {
        uppercase: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ',
        lowercase: 'abcdefghijklmnopqrstuvwxyz',
        numbers: '0123456789',
        symbols: '!@#$%^&*?',
    };

    let length = 12;
    let characters = ['uppercase', 'lowercase', 'numbers', 'symbols'];
    let password: string;

    let chartType = 'bar';
    let totalEntries = 0;
    let xAxis = '';
    let yAxis = '';


    const generateChart = () => {
    
        if (chartType === "bar") {
          
            const ctx = document.getElementById("chartCanvas").getContext("2d");
            const chartData = {
                labels: xAxis.split(","),
                datasets: [
                    {
                        label: "Y-Axis",
                        data: yAxis.split(",").map(Number),
                        backgroundColor: "rgba(75, 192, 192, 0.2)",
                        borderColor: "rgba(75, 192, 192, 1)",
                        borderWidth: 1,
                    },
                ],
            };
            const chartOptions = {
                scales: {
                    y: {
                        beginAtZero: true,
                    },
                },
            };
            new Chart(ctx, {
                type: "bar",
                data: chartData,
                options: chartOptions,
            });
        } else if (chartType === "line") {
  
        }
    };
</script>

<Intro heading={data.meta.title} description={data.meta.description} />

<section class="bg-white dark:bg-gray-900">
    <div class="py-8 px-4 mx-auto max-w-screen-xl lg:px-12">
        <div class="card gap-16 items-center mx-auto max-w-screen-xl lg:grid lg:grid-cols-2 overflow-hidden rounded-lg">
           
            <div class="p-8">
              
            </div>

           
            <div class="p-8 h-full flex rounded-lg relative justify-center items-center">
               
                <div class="mb-4">
                    <label class="block text-gray-900 dark:text-white font-bold mb-2">Select Chart Type</label>
                    <select class="w-full border border-gray-300 rounded-md py-2 px-3 bg-white dark:bg-gray-800 dark:text-white focus:outline-none focus:border-blue-500" bind:value={chartType}>
                        <option value="bar">Bar Chart</option>
                        <option value="line">Line Chart</option>
                       
                    </select>
                </div>

              
                <div class="mb-4">
                    <label class="block text-gray-900 dark:text-white font-bold mb-2">Total Number of Entries</label>
                    <input class="w-full border border-gray-300 rounded-md py-2 px-3 bg-white dark:bg-gray-800 dark:text-white focus:outline-none focus:border-blue-500" type="number" bind:value={totalEntries} min="1" />
                </div>

            
                <div class="mb-4">
                    <label class="block text-gray-900 dark:text-white font-bold mb-2">X-Axis (Labels)</label>
                    <input class="w-full border border-gray-300 rounded-md py-2 px-3 bg-white dark:bg-gray-800 dark:text-white focus:outline-none focus:border-blue-500" type="text" bind:value={xAxis} />
                </div>
                <div class="mb-4">
                    <label class="block text-gray-900 dark:text-white font-bold mb-2">Y-Axis (Values)</label>
                    <input class="w-full border border-gray-300 rounded-md py-2 px-3 bg-white dark:bg-gray-800 dark:text-white focus:outline-none focus:border-blue-500" type="text" bind:value={yAxis} />
                </div>

              
                <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-full focus:outline-none focus:shadow-outline-blue active:bg-blue-800" on:click={generateChart}>Generate Chart</button>
            </div>
        </div>
    </div>
</section>
