<svelte:head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
</svelte:head>

<script lang="ts">
    import { onMount } from "svelte";
    import "leaflet/dist/leaflet.css";
    import L from "leaflet";

    import type { Map, LeafletMouseEvent, LatLng, LocationEvent } from "leaflet";
    import type * as geojson from "geojson";
    import type { PointOfInterest } from "../types";

    import pathData from "../assets/path.json";

    let map: Map;
    let mapMarkers = [];
    export let poi: PointOfInterest[];
    export let coord: {user: LatLng, click: LatLng};

    onMount(async () => {
        map = L.map("map", {
            zoomControl: false,
            zoomSnap: 0.5,
        }).setView([48.367198, -1.842957], 9);
        L.tileLayer("https://a.tile.openstreetmap.org/{z}/{x}/{y}.png ", {
            maxZoom: 18,
        }).addTo(map);
        L.geoJSON(pathData as geojson.GeoJsonObject).addTo(map);

        map.on("click", (e: LeafletMouseEvent) => {
            coord = {...coord, click: e.latlng}
        });

        map.locate({setView: false, maxZoom: 9});
        map.on('locationfound', onLocationFound);
        return () => {
            map.remove();
        };
    });

    $: if (poi) {
        if (mapMarkers.length) mapMarkers.forEach((m) => m.remove());
        poi.forEach((pt: PointOfInterest) => {
            if (!pt.selected) return;
            let marker = L.marker(
                [parseFloat(pt.Latitude), parseFloat(pt.Longitude)],
                {
                    icon: L.icon({
                        iconUrl:
                            "https://unpkg.com/leaflet@1.9.3/dist/images/marker-icon-2x.png",
                        iconSize: [25, 40],
                    }),
                }
            );

            if (pt.Description) {
                marker.bindPopup(pt.Description);
                marker.on("mouseover", (e) => {
                    e.target.openPopup();
                });
                marker.on("mouseout", (e) => {
                    e.target.closePopup();
                });
            }
            marker.addTo(map);
            mapMarkers = [...mapMarkers, marker];
        });
    }

    const onLocationFound = (e: LocationEvent) => {
        coord = {...coord, user: e.latlng}
    }

</script>

<div id="map" class='h-full w-full'/>
