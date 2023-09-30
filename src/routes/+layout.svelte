<script lang="ts">
    import { onMount } from "svelte";
    import "../app.css";
    import { writable, type Writable } from "svelte/store";

    let update: Writable<Boolean> = writable(false);
    let currentVersion;
    let downloadLink: string;

    onMount(async () => {
        fetch(
            "https://api.github.com/repos/pyscripter99/EasyHome/releases/latest"
        )
            .then(async (data) => {
                return data.json();
            })
            .then(async (data) => {
                currentVersion = await fetch("/version").then((d) => {
                    return d.text();
                });
                downloadLink = data.assets[0].browser_download_url;
                if (currentVersion != data.tag_name) {
                    update.set(true);
                }
            });
    });
</script>

<div class="m-10"><slot /></div>
{#if $update}
    <div class="absolute top-5 right-5 rounded-full">
        <div class="relative">
            <button
                class="peer"
                on:click={() => {
                    window.open(downloadLink, "_blank");
                }}
            >
                <i class="fa-solid fa-circle-info text-yellow-500 fa-2xl" />
            </button>
            <span
                class="absolute top-10 w-max right-0 opacity-25 translate-x-52 peer-hover:opacity-100 peer-hover:translate-x-0 p-2 bg-zinc-600 rounded-xl transition-all duration-300"
                >An update is available!</span
            >
        </div>
    </div>
{/if}
