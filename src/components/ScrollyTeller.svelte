<script>
    import Scroller from "@sveltejs/svelte-scroller";
    import App from '../components/App.svelte';
  
    let count, index, offset, progress;

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
  <!-- <div class="background" slot="background">

  </div> -->

  <div class="foreground" slot="foreground">
    <section class="WriteUp">This is the first section.
    </section>
    <section class="Graph">This is the second section.
      <p>Refresh the page if the graph isn't loading.</p>
        <App />
    </section>
    <section class="WriteUp">This is the third section.</section>
  </div>
</Scroller>


<style>
    .Graph {
    height: 130vh;
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
