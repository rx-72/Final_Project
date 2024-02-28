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

    const points = [
		{ lat: 60.480, long: -152.750},
	].map(p => projection([p.long, p.lat]))

    onMount(async () => {
		const us = await fetch('https://cdn.jsdelivr.net/npm/us-atlas@3/counties-albers-10m.json')
			.then(d => d.json())
		console.log({ us })
		
		states = topojson.feature(us, us.objects.states).features;
		// console.log({ features })
		
		counties = topojson.feature(us, us.objects.counties).features;

		mesh = topojson.mesh(us, us.objects.states, (a, b) => a !== b);
		
		$: console.log({ states, counties, mesh })
	})

        
    function coord_proj_cx(d) {
        let coords = [
		{ lat: d['latitude'], long: d['longitude'] },
	    ].map(p => projection([p.long, p.lat]))
        //$: console.log(d['longitude'])
        //$: console.log(coords[0][0])

        
        return coords[0][0]
    }

    function coord_proj_cy(d) {
        let coords = [
		{ lat: d['latitude'], long: d['longitude'] },
	    ].map(p => projection([p.long, p.lat]))
        //$: console.log(d['longitude'])
        $: console.log(coords[0][1])

        
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

<div class="volcanos">
    <svg viewBox="-35 10 975 610">

    <g fill="white" stroke="black">
        {#each states as feature, i}
            <path d={path(feature)} on:click={() => selected = feature} class="state" in:draw={{ delay: i * 50, duration: 1000 }} />
        {/each}
                    
    
    </g>

    {#each US_volcanos as d, i}
		<circle 
            cx={coord_proj_cx(d)}
            cy={coord_proj_cy(d)}
            r={explosive(d)} 
            fill="orange"
            opacity = 0.6
            stroke="gray" 
        />
	{/each}
    

    </svg>
</div>