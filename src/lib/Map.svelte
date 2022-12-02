<script>
    import { onMount } from "svelte";
    import pathData from '../assets/path.json'
    import L from 'leaflet';
    import 'leaflet/dist/leaflet.css';

    let map;
    export let poi;
    export let coord;

    onMount(async () => {
        map = L.map("map", { zoomControl: false }).setView([48.367198, -1.842957], 9);
        L.tileLayer("https://a.tile.openstreetmap.org/{z}/{x}/{y}.png ", {
            maxZoom: 18
        }).addTo(map);
        L.geoJSON(pathData).addTo(map);
    
        map.on('click', (e) => {
            coord = e.latlng
        });

        return () => {
            map.remove();
        };
    })

    $: poi = poi.map((pt => {
            L.marker([pt.Latitude, pt.Longitude], { 
                icon: L.icon({
                    iconUrl: 'https://unpkg.com/leaflet@1.9.3/dist/images/marker-icon-2x.png',
                    iconSize: [25, 40],
                })
            }).addTo(map)
        }))

</script>


<div id="map" />

<style>
    #map {
        height: 100%;
        width:100%;
    }
</style>
