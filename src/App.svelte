<script>
    import svelteLogo from './assets/svelte.svg'
    import Counter from './lib/Counter.svelte'
    import Map from './lib/Map.svelte'
    import PoiList from './lib/PoiList.svelte'
    import { onMount } from "svelte";
    // import { poi } from './store.js'

    import pathData from './assets/path.json'

    let coord;
    let poi = [];

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

        const sheet = await fetch(apiUrl + spreadsheetId + '/values/Points?key=' + googleApiKey).then(async r => (await r.json()))
        
        const spreadSheet = await fetch(apiUrl + spreadsheetId + '?key=' + googleApiKey).then(async r => (await r.json()))

        const keys = sheet?.values.shift()
        poi = sheet?.values.reduce((a, p, i) => ([
            ...a,
            Object.fromEntries(
                p.map(
                    (e,i) => ([ keys[i], e ])
                )
            )
        ]), []) ?? []
    }

</script>

<main>
    <Map 
        bind:coord 
        bind:poi
    />
    <div id='bot'>
        <PoiList {poi}/>
        <div id='coord'>
            {coord}
        </div>
    </div>
</main>

<style>
    main {
        height: 100%;
        display: flex;
        flex-direction: column
    }
    #bot {
        max-height: 20%;
        overflow: scroll;
    }

</style>
