<script lang='ts'>
    import Icon from 'svelte-awesome';
    import mapMarker from 'svelte-awesome/icons/mapMarker';
    import map from 'svelte-awesome/icons/map';

    import type { PointOfInterest } from '../types'

    export let poi: PointOfInterest[];
    let selectedPoint: PointOfInterest;
    let columnsKeys = [
        'Group',
        // 'Marker Icon',
        // 'Marker Color',
        // 'Icon Color',
        // 'Custom Size',
        'Name',
        // 'Image',
        // 'Description',
        // 'Location',
        'Latitude',
        'Longitude'
    ];

    $: poi = poi.map(pt => (!pt?.selected ? {...pt, selected:false} : pt))

    const poiClick = (e: MouseEvent) => {
        const pointIndex = (e?.target as HTMLElement)?.dataset.rowId

        selectedPoint = poi[pointIndex]
        selectedPoint.selected = !selectedPoint?.selected
        poi = poi.map((pt, index) => (`${index}` === pointIndex ? selectedPoint : pt))
    };

</script>

<div class='h-full overflow-scroll'>
    <table>
        <thead>
            <tr class='border font-bold'>
                <th style='text-align:center;'>
                    <Icon data={map} />
                </th>
                {#each columnsKeys as col}
                    <th >
                        {col}
                    </th>
                {/each}
            </tr>
        </thead>
        <tbody>
            {#each poi as pt, i}
                <tr class='bot_poi-list_item border' data-id={i} on:click={poiClick}>
                    <td data-row-id={i} class='text-center'>
                        {#if pt?.selected }
                            <Icon data-row-id={i} data={mapMarker} />
                        {/if}
                    </td>
                    {#each Object.values(Object.fromEntries(Object.entries(pt).filter(([key]) => columnsKeys.includes(key)))) as pointInfo}
                        <td data-row-id={i}>
                            {pointInfo}
                        </td>
                    {/each}
                </tr>
            {/each}
        </tbody>
    </table>
</div>

<style>
    table {
        height: 100%;
        width: 53.75rem;
    }

    tr:nth-child(even)  {
        background-color: #f5f5f5;
    }
    
    td, th {
        text-align: left;
    }

</style>
