<script>
    import Header from "./Header.svelte";
</script>

<Header />
<slot />

<style>
    :global(*) {
        padding: 0;
        margin: 0%;
    }
</style>