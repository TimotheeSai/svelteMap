<script lang='ts'>
    import { onMount } from "svelte";

    import type { LatLng } from 'leaflet';

    import Map from './lib/Map.svelte'
    import PoiList from './lib/PoiList.svelte'
    // import { poi } from './store.js'


    let coord: LatLng;
    let poi = [];

    $: console.log('app', {poi})

    onMount(async () => {
        await fetchPoi()
    });

    const fetchPoi = async() => {
        const googleDocURL = 'https://docs.google.com/spreadsheets/d/1gAXNAZh6a_dCV4YqwZbVWdbKEKHY4LHBq3Ke2p2f3OY/edit#gid=0';

        const googleApiKey = 'AIzaSyBh9nKnVZm2RPeZa0ywCOxPAgJJfK87WhY';

        const apiUrl = 'https://sheets.googleapis.com/v4/spreadsheets/'
        const spreadsheetId = googleDocURL.indexOf('/d/') > 0
            ? googleDocURL.split('/d/')[1].split('/')[0]
            : googleDocURL

        // const spreadSheet = await fetch(apiUrl + spreadsheetId + '?key=' + googleApiKey).then(async r => (await r.json()))
        const sheet = await fetch(apiUrl + spreadsheetId + '/values/Points?key=' + googleApiKey).then(async r => (await r.json()))

        const keys = sheet?.values.shift()
        poi = sheet?.values.map((p: string[]) => (
            Object.fromEntries(
                p.map(
                    (elt,idx) => ([ keys[idx], elt ])
                )
            )
        )) ?? []
    }

</script>

<main>
    <Map 
        bind:coord 
        bind:poi
    />
    <div id='bot'>
        <div id='coord'>
            {coord ?? 'click map'}
        </div>
        <PoiList bind:poi/>
    </div>
</main>

<style>
    main {
        height: 100%;
        display: flex;
        flex-direction: column
    }
    #bot {
        max-height: 40%;
    }

</style>
