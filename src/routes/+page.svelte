<script lang="ts">
    import { onMount } from "svelte";

    let data = new Date();
    let mostVisited: { title: string; url: string }[] = [];
    let search: string;
    $: hour = data.getHours();
    $: minutes = data.getMinutes();
    $: progress = 0;
    $: am_pm = "";

    onMount(async () => {
        setInterval(() => {
            data = new Date();
            am_pm = hour >= 12 ? "PM" : "AM";
            progress = (minutes / 60) * 62;
        }, 500);
        mostVisited = (await chrome.topSites.get()) as {
            title: string;
            url: string;
        }[];
    });

    function faviconURL(u: string) {
        const url = new URL(chrome.runtime.getURL("/_favicon/"));
        url.searchParams.set("pageUrl", u);
        url.searchParams.set("size", "32");
        return url.toString();
    }
</script>

<div class="flex flex-col w-max">
    <div class="flex flex-row gap-2">
        <h2 class="text-7xl font-bold">
            {hour % 12 == 0 ? 12 : hour >= 12 ? hour - 12 : hour}:{minutes
                .toString()
                .padStart(2, "0")}
        </h2>
        <h3 class="m-auto text-2xl text-zinc-400">{am_pm}</h3>
    </div>
    <div
        class="mx-2 h-1 rounded-xl bg-blue-500 transition-all duration-300"
        style={`width: ${progress}%;`}
    />
</div>
<div
    class="flex flex-col gap-4 absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 max-w-2xl w-full center"
>
    <form
        on:submit|preventDefault={() => {
            window.location.href =
                "https://google.com/search?q=" +
                encodeURIComponent(search ? search : "cute dog photos");
        }}
        class="rounded-xl bg-zinc-700 p-2 px-4 flex flex-row items-center gap-1"
    >
        <i class="fa-solid fa-magnifying-glass" />
        <input
            type="text"
            class="p-2 rounded-xl bg-zinc-700 w-full outline-none"
            placeholder="Search..."
            bind:value={search}
        />
        <button class="p-2 hover:translate-x-1 transition-all duration-150"
            ><i class="fa-solid fa-chevron-right" /></button
        >
    </form>
    <div class="flex flex-row gap-4">
        <div
            class="rounded-xl bg-zinc-700 p-4 flex flex-row gap-2 items-center flex-1"
        >
            <i class="fa-solid fa-cloud fa-2xl" />
            <h3 class="text-2xl font-bold">16°</h3>
            <span class="text-xl text-zinc-400 font-medium mt-auto"
                >Newcastle</span
            >
        </div>
        {#each mostVisited.slice(0, 5) as site}
            <a href={site.url} target="_self"
                ><img
                    class="rounded-xl bg-zinc-700 p-4"
                    src={faviconURL(site.url)}
                    alt={site.title}
                /></a
            >
        {/each}
    </div>
    <div class="flex flex-row gap-4">
        <div class="rounded-xl bg-zinc-700 p-4 flex flex-col gap-4">
            <div class="flex flex-row justify-between items-center flex-1">
                <h2 class="font-bold text-xl">Agenda</h2>
                <i class="fa-solid fa-plus" />
            </div>
            <a
                href="/"
                class="rounded-xl bg-zinc-600 p-4 flex flex-row items-center gap-3 hover:-translate-y-1 transition-all duration-100"
                ><h3 class="font-bold text-lg">Monthly zoom meeting</h3>
                <span class="text-zinc-400">15 minutes</span>
                <i class="fa-solid fa-arrow-up-right-from-square ml-auto" /></a
            >
            <div
                class="bg-zinc-600/80 h-1 rounded-xl w-5/6 mx-auto flex items-center justify-center my-2"
            >
                <span class="px-2 bg-zinc-700 tetx-lg font-medium">12:30</span>
            </div>
            <a
                href="/"
                class="rounded-xl bg-zinc-600 p-4 flex flex-row items-center gap-3"
                ><h3 class="font-bold text-lg">Monthly zoom meeting</h3>
                <span class="text-zinc-400">15 minutes</span>
                <i class="fa-solid fa-arrow-up-right-from-square ml-auto" /></a
            >
            <a
                href="/"
                class="rounded-xl bg-zinc-600 p-4 flex flex-row items-center gap-3"
                ><h3 class="font-bold text-lg">Monthly zoom meeting</h3>
                <span class="text-zinc-400">15 minutes</span>
                <i class="fa-solid fa-arrow-up-right-from-square ml-auto" /></a
            >
        </div>
        <div class="rounded-xl bg-zinc-700 p-4 flex flex-col gap-4 flex-1" />
    </div>
</div>

<a
    href="/configure/settings"
    class="absolute bottom-10 right-10 -translate-x-1/2 -translte-y-1/2 hover:-translate-y-1 transition-all duration-150"
>
    <i class="fa-solid fa-gear fa-xl" />
</a>
