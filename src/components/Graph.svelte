<script>
    import * as d3 from 'd3';
    let svg;
    let gx;
    let gy;
    // grabbing the data
    export let volcanos;

    let exp_ind = [
        {text: 0, show: true},
        {text: 1, show: true},
        {text: 2, show: true},
        {text: 3, show: true},
        {text: 4, show: true},
        {text: 5, show: true},
        {text: 6, show: true},
        {text: 7, show: true}
    ]

    let exp_ind_f = [
        {text: 0, show: false},
        {text: 1, show: false},
        {text: 2, show: false},
        {text: 3, show: false},
        {text: 4, show: false},
        {text: 5, show: false},
        {text: 6, show: false},
        {text: 7, show: false}
    ]

    const color = {
        0 : 'black',
        1: 'gray',
        2: 'purple',
        3: 'blue',
        4: 'green',
        5: 'yellow',
        6: 'orange',
        7: 'red',
    }


    $: console.log(volcanos);

    let width = 928;
    let height = 1000;
    const marginTop = 10;
    const marginRight = 30;
    const marginBottom = 20;
    const marginLeft = 100;

    $: x = d3
    .scaleLinear()
    .domain(d3.extent(volcanos, (d) => d.latitude))
    .range([marginLeft, width - marginRight]);

    $: y = d3
    .scaleLinear()
    .domain(d3.extent(volcanos, (d) => d.longitude))
    .range([height - marginBottom, marginTop])

    $: d3.select(gx).call(d3.axisBottom(x).ticks(width / 100));
    $: d3.select(gy)
    .call(d3.axisLeft(y).ticks(width / 100))

    $: years = d3.group(volcanos, d => d.year);

    $: Tsunami = d3.group(volcanos, d => d.Tsunami_caused);
    $: earthquake = d3.group(volcanos, d => d.Earthquake_caused);

    $: deadly = [volcanos[277], volcanos[517], volcanos[584], volcanos[540], volcanos[291], volcanos[193], volcanos[822], volcanos[574], volcanos[302], volcanos[304]]

    $: console.log(deadly)
    // $: console.log(years)

    function explosive(groups) {
        // console.log(groups);
        
        if (groups.Volcano_explosive_index === 1) {
                // console.log(x);
            return "gray"
        }

        if (groups.Volcano_explosive_index === 2) {
                // console.log(x);
            return "purple"
        }

        if (groups.Volcano_explosive_index === 3) {
                // console.log(x);
            return "blue"
        }

        if (groups.Volcano_explosive_index === 4) {
                // console.log(x);
            return "green"
        }
        if (groups.Volcano_explosive_index === 5) {
                // console.log(x);
            return "yellow"
        }
        if (groups.Volcano_explosive_index === 6) {
                // console.log(x);
            return "orange"
        }
        if (groups.Volcano_explosive_index === 7) {
                // console.log(x);
            return "red"
        }
        
        return "black"
    }

</script>

<div class="volcanos">
    <svg
    bind:this={svg}
        {width}
        {height}
        viewBox="0 0 {width} {height}"
        style="max-width: 100%; height: auto; overflow: visible; font: 10px sans-serif;"
    >

    <g stroke="#000" stroke-opacity="0.2">
        {#each volcanos as d, i}
            <circle
            key={i}
            cx={x(d.latitude)}
            cy={y(d.longitude)}
            fill={explosive(d)}
            r=1
            />
        {/each}
    </g>

    <div class = "legend" style="position: absolute;">
        {#each exp_ind as source}
            <div class="source-item" style="background-color: {source.show ? color[source.text] : 'white'} ;">
            <input
                bind:checked={source.show}
                id={source.text}
                type="checkbox"
                class="checkbox-round"
            />

                <label style="color: black;">{source.text}</label>
            </div>
        {/each}
    </div>

    </svg>
</div>