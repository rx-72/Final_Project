<script>
    import Scroller from "@sveltejs/svelte-scroller";
    import App from '../components/App.svelte';
    import App2 from '../components/App2.svelte';
  
    let count, index, offset, progress;

    let sections = [
        { name: 'First Section', component: null },
        { name: 'Graph Section', component: App },
        { name: 'WorldGraph Section', component: App2 },
        { name: 'Fourth Section', component: null }
    ];
</script>

<Scroller
  top={0.0}
  bottom={1}
  threshold={0.5}
  bind:count
  bind:index
  bind:offset
  bind:progress
>
  <div class="index" slot="index">
    {#each sections as section, i}
      <div class="index-item" class:selected={i === index}>
        {section.name}
      </div>
    {/each}
  </div>

  <div class="foreground" slot="foreground">
    {#each sections as section, i}
      {#if i === 1 && section.component}
        <section class="Graph">
          <p>Volcano records in the US over the past 200+ years. Choose one or multiple filters and explore!</p>
          <p>Refresh the page if the graph isn't loading.</p>
          <svelte:component this={section.component} />
        </section>
      {:else if i === 2 && section.component}
        <section class="WorldGraph">
          <p>Whole World Volcanos.</p>
          <svelte:component this={section.component} />
        </section>
      {:else}
        <section class="WriteUp">
          {section.name}
        </section>
      {/if}
    {/each}
  </div>
</Scroller>


<style>
  .index {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    background-color: white;
    padding: 1em;
    z-index: 1;
    display: flex;
    justify-content: center;
  }

  .index-item {
    margin: 0 1em;
    cursor: pointer;
    font-weight: bold;
    color: gray;
  }

  .index-item.selected {
    color: black;
  }

  .Graph {
    height: 170vh;
    background-color: lightblue; /* 20% opaque */
    /* color: white; */
    outline: black;
    text-align: center;
    max-width: 5000px; /* adjust at will */
    color: black;
    padding: 1em;
    margin: 0 0 2em 0;
    width: 100%;
    margin-right:auto;
    margin-left:auto;
  }

  .WriteUp {
    height: 80vh;
    background-color: rgba(0, 0, 0, 0.2); /* 20% opaque */
    /* color: white; */
    outline: black;
    text-align: center;
    max-width: 5000px; /* adjust at will */
    color: black;
    padding: 1em;
    margin: 0 0 2em 0;
  }
</style>
