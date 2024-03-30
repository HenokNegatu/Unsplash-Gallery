<script lang="ts">
    import "../style.scss";
    import axios from "axios";
    import { onMount } from "svelte";
    import { fade, fly } from "svelte/transition";
    import MagnifyingGlass from "phosphor-svelte/lib/MagnifyingGlass";
    import Images from "phosphor-svelte/lib/Images";

    let term: string = "";
    let page: number = 1;
    let photos: {
        id: string;
        alt_description: string;
        urls: {
            regular: string;
        };
    }[] = [];

    let theme: boolean = false;

    const fetchData = async () => {
        try {
            const response = await axios.get(
                `https://api.unsplash.com/search/photos/?page=${page}&query=${term} | office&client_id=${import.meta.env.VITE_ACCESS_KEY}`,
            );
            photos = response.data.results;
        } catch (e) {
            console.log(e);
        }
    };

    const handleSearch = () => {
        if (!term) return;
        else page = 1;
        fetchData();
        term = "";
    };
    const handlePageRequest = (todo: string) => {
        if (todo === "+") page++;
        else if (todo === "-" && page > 1) page--;
        else return;
        fetchData();
    };

    onMount(() => fetchData());
</script>

<div class={theme ? "dark-theme" : "light-theme"}>
    <div class="nav">
        <Images class="logo" size={42} color="teal" weight="duotone" />
        <form class="form">
            <input
                class="input"
                type="text"
                placeholder="Type.."
                bind:value={term}
            />
            <button class="search" type="submit" on:click={handleSearch}
                ><MagnifyingGlass
                    size={25}
                    color="teal"
                    weight="duotone"
                /></button
            >
        </form>
        <button class="theme-btn" on:click={() => (theme = !theme)}
            >theme</button
        >
    </div>
    <div class="wrapper">
        {#each photos as photo, i (photo.id)}
            <div class="card">
                <img
                    src={photo.urls.regular}
                    alt={photo.alt_description}
                    in:fly={{ y: 200, duration: 2000, delay: i * 200 }}
                    out:fade
                />
            </div>
        {/each}
    </div>
    <div class="page-nav">
        <button on:click={() => handlePageRequest("-")}>Prev</button>
        <p class={theme ? "page-display-dark" : "page-display"}>{page}</p>
        <button on:click={() => handlePageRequest("+")}>Next</button>
    </div>
</div>
