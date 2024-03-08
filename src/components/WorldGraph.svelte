<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
    import * as topojson from 'topojson-client';
    import { geoPath, geoNaturalEarth1 } from 'd3-geo';

    const projection = geoNaturalEarth1(); 
    const path = geoPath().projection(projection);

    let worldData;

    onMount(async () => {
        // Fetch world GeoJSON directly from Natural Earth

        // NOT SURE WHY THIS ISN'T WORKING
        worldData = await fetch('https://unpkg.com/world-atlas@2/countries-110m.json') 
                                  .then(d => d.json())

        const countries = topojson.feature(worldData, worldData.objects.countries).features;

        svg.selectAll('path')
           .data(countries)
           .enter()
           .append('path')
           .attr('d', path)
           .attr('fill', 'lightblue')
           .attr('stroke', 'black'); 
    })

    let svg; 
</script>

<svg bind:this={svg} width="960" height="600"></svg>
<h1>The Big Gamers</h1>

<svg bind:this={svg}></svg>