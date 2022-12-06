<script lang="ts">
    import Icon from "svelte-awesome";
    import {
        map,
        camera,
        sunO,
        cutlery,
        hotel,
        male,
    } from "svelte-awesome/icons";
    import { createEventDispatcher } from "svelte";

    export let category: string;

    const dispatch = createEventDispatcher();

    const handleClick = () => {
        dispatch("message", {
            category: category,
        });
    };

    const iconsCategories = {
        Culture: { icon: camera, color: "bg-red-500" },
        Plage: { icon: sunO, color: "bg-yellow-500" },
        WC: { icon: male, color: "bg-black" },
        Resto: { icon: cutlery, color: "bg-green-500" },
        Hotel: { icon: hotel, color: "bg-blue-500" },
    };

    $: iconData = iconsCategories[category] || {
        icon: map,
        color: "bg-red-500",
    };
</script>

<div
    class="rounded-full border border-white w-10 h-10 m-1 overflow-hidden {iconData.color}"
    on:click={handleClick}
>
    <Icon
        data={iconData.icon}
        class="{iconData.color} w-10 h-10 p-2 text-white"
    />
</div>
