<script lang='ts'>
    import { onMount } from "svelte";

    import type { LatLng } from 'leaflet';
    import type { PointOfInterest } from './types'

    import Map from './lib/Map.svelte'
    import MarkerIcon from './lib/MarkerIcon.svelte'
    import PoiList from './lib/PoiList.svelte'
    // import { poi } from './store.js'


    let coord: {user: LatLng, click: LatLng};
    let poi: PointOfInterest[] = [];

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

    const filterCategory = (category: string) => {
        poi = poi.map(e => ({...e, selected: e.Group === category }))
    }

</script>

<main>
    <div>
        <div class='flex flex-row justify-center'>
            {#each [... new Set(poi.map(e =>e.Group))] as  cat}
                <MarkerIcon category={cat} on:message={(e) => {filterCategory(e?.detail?.category)}} />
            {/each}
        </div>
    </div>
    <Map 
        bind:coord 
        bind:poi
    />
    <div id='bot'>
        <div class='flex flex-col items-center'>
            {#if coord?.user}
                <div class='bg-blue-100  max-w-lg p-4 m-2 border-blue-100 rounded text-blue-500 font-bold'>
                    {`You're position is ${coord?.user?.lat}, ${coord?.user?.lng}`}
                </div>
            {/if}
            {#if coord?.click}
                <div class='bg-green-100 max-w-lg p-4 m-2 border-green-100 rounded text-green-500 font-bold'>
                    {`You clicked on ${coord?.click?.lat}, ${coord?.click?.lng}`}
                </div>
            {/if}
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
