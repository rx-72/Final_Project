<script>
        import * as d3 from 'd3';
        import { onMount } from 'svelte';
	import * as topojson from 'topojson-client';
	import { geoPath, geoAlbersUsa } from 'd3-geo';
	import { draw } from 'svelte/transition';
        let svg;
        let gx;
        let gy;
        // grabbing the data
        export let volcanos;
        export let US_volcanos;

        const projection = geoAlbersUsa().scale(1300).translate([487.5, 305])
        const path = geoPath().projection(null);

        let states = [];
        let counties = [];
        let mesh;
        let selected;
        let filterState = {
                year: null, // Initialize with no year filter
                location: null // Initialize with no location filter
         };
        let filteredVolcanos; 

        function filterByYear(year) {
                if (year === '1800s') {
                filterState.year = 1800;
                } else if (year === '1900s') {
                filterState.year = 1900;
                } else if (year === '2000s') {
                filterState.year = 2000;
                } else if (year === 'pre-1800s') {
                filterState.year = 'pre-1800s'; 
                } else {
                filterState.year = null; // Reset filter in other cases
                }
                updateFilteredData(); 
        }

        function filterByLocation(location) {
                filterState.location = location;
                updateFilteredData();
        }

        function updateFilteredData() {
                filteredVolcanos = US_volcanos.filter(d => {
                        const yearMatches = filterState.year === 'pre-1800s' ? d.year < 1800 : filterState.year !== null ? d.year >= filterState.year && d.year <= filterState.year + 99 : true; 
                        const locationMatches = filterState.location ? d.location === filterState.location : true;
                        console.log("Filtering by year:", filterState.year);
                        console.log("Filtering by location:", filterState.location);
                        // console.log("Number of matches:", filteredVolcanos.length);
                        
                        return yearMatches && locationMatches;
                });
        }

        const points = [
		{ lat: 60.480, long: -152.750},
	].map(p => projection([p.long, p.lat]))

        onMount(async () => {
		const us = await fetch('https://cdn.jsdelivr.net/npm/us-atlas@3/counties-albers-10m.json')
			.then(d => d.json())
                updateFilteredData();
		console.log({ us })
		
		states = topojson.feature(us, us.objects.states).features;
		// console.log({ features })
		
		counties = topojson.feature(us, us.objects.counties).features;

		mesh = topojson.mesh(us, us.objects.states, (a, b) => a !== b);
		
	})

                
        function coord_proj_cx(d) {
                let coords = [  
                        { lat: d['latitude'], long: d['longitude'] },
                        ].map(p => projection([p.long, p.lat]))
                //$: console.log(coords[0][0])
                return coords[0][0]
        }

        function coord_proj_cy(d) {
                let coords = [
		        { lat: d['latitude'], long: d['longitude'] },
	                ].map(p => projection([p.long, p.lat]))
                //$: console.log(d['longitude'])
                //$: console.log(coords[0][1])

                return coords[0][1]
        }
        

        const marginTop = 50;
        const marginRight = 50;
        const marginBottom = 50;
        const marginLeft = 50;
        let height = 100 - marginTop - marginBottom
        let width = 100 - marginLeft - marginRight;

        $: years = d3.group(volcanos, d => d.year);

        $: Tsunami = d3.group(volcanos, d => d.Tsunami_caused);
        $: earthquake = d3.group(volcanos, d => d.Earthquake_caused);

        $: deadly = [volcanos[277], volcanos[517], volcanos[584], volcanos[540], volcanos[291], volcanos[193], volcanos[822], volcanos[574], volcanos[302], volcanos[304]]

        $: US_years = d3.group(US_volcanos, d => d.year);

        $: US_Tsunami = d3.group(US_volcanos, d => d.Tsunami_caused);
        $: US_earthquake = d3.group(US_volcanos, d => d.Earthquake_caused);

        // $: console.log(deadly)

        function explosive(groups) {
                
                return (2.5 * groups.Volcano_explosive_index)
        }

        console.log(points)
        // debugger;

</script>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
<div class="filters">
    <p>Current Filters:</p>
    <p>Year: {filterState.year === null ? 'All' : filterState.year}</p>
    <p>Location: {filterState.location ? filterState.location : 'All'}</p>
</div>

<div class="buttons">
    <button class="btn btn-outline-primary" on:click={() => filterByYear('1800s')}>1800s</button>
    <button class="btn btn-outline-primary" on:click={() => filterByYear('1900s')}>1900s</button>
    <button class="btn btn-outline-primary"on:click={() => filterByYear('2000s')}>2000s</button>
    <button class="btn btn-outline-primary"on:click={() => filterByYear('pre-1800s')}>pre-1800s</button>
    <button class="btn btn-outline-primary" on:click={() => filterByYear(null)}>Reset Year</button>
</div>
<div class="buttons">
        {#each Array.from(new Set(US_volcanos.map(d => d.location))) as location}
                <button class="btn btn-outline-primary" on:click={() => filterByLocation(location)}>{location}</button>
        {/each}
        <button class="btn btn-primary" on:click={() => filterByLocation(null)}>Reset Location</button>
</div>
<div class="volcanos">
        <svg viewBox="-35 10 975 610">
                {#if states && counties && mesh}
                        <g fill="white" stroke="black">
                                {#each states as feature, i}
                                        <path d={path(feature)} on:click={() => selected = feature} class="state" in:draw={{ delay: i * 50, duration: 1000 }} />
                                {/each}
                        </g>

                        {#each US_volcanos as d, i}
                                {#if filteredVolcanos.includes(d)}
                                        <circle 
                                                cx={coord_proj_cx(d)}
                                                cy={coord_proj_cy(d)}
                                                r={10 ** (d.Volcano_explosive_index - 4.7)} 
                                                fill={d.Volcano_explosive_index > 5 ? "red" : "orange"}
                                                opacity = 0.6
                                                stroke="gray" 
                                        />
                                {/if}
                        {/each}
                {/if}
        </svg>
</div>