<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
    import Graph from './Graph.svelte';

    let volcanos = [];

    onMount(async () => {
        const res = await fetch(
            '/Volcano_data.csv',
        );
        const csv = await res.text();
        await d3.csvParse(csv, (d) => {
            volcanos.push({
               year: new Date(Date.UTC(d["year"])),
               month: d["month"],
               day: d["day"],
               name: d["name"],
               location: d["location"],
               country: d["country"],
               elevation: d["elevation"],
               type: d["type"],
               status: d["status"],
               total_deaths: d["total_deaths"],
               total_deaths_description: d["total_deaths_description"],
               total_injuries: d["total_injuries_description"],
               total_damage: d["total_damage_description"],
               houses_destroyed: d["total_houses_destroyed_description"],
               Tsunami_caused: d["Tsunami_caused?"], 
               Earthquake_caused: d["Earthquake_caused?"],
               Volcano_explosive_index: d["Volcano Explosive Index"],
               longitude: d["longitude"],
               latitude: d["latitude"],
            });
        });
        volcanos = volcanos;
    });

    $: console.log(volcanos)
</script>

<main>
    <Graph {volcanos} />
</main>