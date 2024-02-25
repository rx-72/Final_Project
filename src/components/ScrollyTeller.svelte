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
    <section>This is the first section.
    </section>
    <section>This is the second section.
        <App />
    </section>
    <section>This is the third section.</section>
  </div>
</Scroller>


<style>
    section {
    height: 80vh;
    background-color: rgba(0, 0, 0, 0.2); /* 20% opaque */
    /* color: white; */
    outline: black;
    text-align: center;
    max-width: 750px; /* adjust at will */
    color: black;
    padding: 1em;
    margin: 0 0 2em 0;
    width: 80%;
    margin-right:auto;
    margin-left:auto;
  }
</style>