<script>
    import { page } from '$app/stores';
    $: id = $page.params.id;

</script>

<h1>id: {id}</h1>