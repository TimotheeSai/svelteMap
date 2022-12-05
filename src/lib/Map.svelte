<script lang="ts">
    import { onMount } from "svelte";
    import 'leaflet/dist/leaflet.css';
    import L from 'leaflet';

    import type { Map, LeafletMouseEvent, LatLng } from 'leaflet';
    import type * as geojson from 'geojson';
    import type { PointOfInterest } from '../types'

    import pathData from '../assets/path.json'

    let map: Map;
    let mapMarkers = [];
    export let poi: PointOfInterest[];
    export let coord: LatLng;

    onMount(async () => {
        map = L.map("map",
            {
                zoomControl: false, zoomSnap: 0.5
            }
        ).setView([48.367198, -1.842957], 9);
        L.tileLayer("https://a.tile.openstreetmap.org/{z}/{x}/{y}.png ", {
            maxZoom: 18
        }).addTo(map);
        L.geoJSON(pathData as geojson.GeoJsonObject).addTo(map);
    
        map.on('click', (e: LeafletMouseEvent) => {
            coord = e.latlng
        });

        return () => {
            map.remove();
        };
    })

    $: if (poi) {
        if (mapMarkers.length) mapMarkers.forEach((m) => m.remove());
        poi.forEach((( pt: { selected: boolean, Latitude: string, Longitude: string } ) => {
            if (pt.selected) {
                let marker = L.marker([parseFloat(pt.Latitude), parseFloat(pt.Longitude)], { 
                    icon: L.icon({
                        iconUrl: 'https://unpkg.com/leaflet@1.9.3/dist/images/marker-icon-2x.png',
                        iconSize: [25, 40],
                    })
                })
                marker.addTo(map)
                mapMarkers = [...mapMarkers, marker]
            }
        }))
    }

</script>


<div id="map" />

<style>
    #map {
        height: 100%;
        width:100%;
    }
</style>
