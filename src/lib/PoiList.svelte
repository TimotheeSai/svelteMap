<script>
    export let poi
</script>

<ul class='bot_poi-list'>
    <li class='bot_poi-list_header'>
            {#each Object.keys(poi[0] ?? {}) as pointInfo}
            <div>
                {pointInfo}
            </div>
            {/each}
    </li>
    {#each poi as pt, i}
        <li class='bot_poi-list_item'>
                {#each Object.values(pt) as pointInfo}
                <div>
                    {pointInfo}
                </div>
                {/each}
        </li>
    {/each}
</ul>

<style>
    ul.bot_poi-list {
        list-style: none;
        padding: 1rem;
        margin: 0;
        height: 100%;
        display: flex;
        flex-direction: column;
    }
    .bot_poi-list > li {
        display:grid;
        grid-template-columns: repeat(11, 1fr);
    }
</style>
