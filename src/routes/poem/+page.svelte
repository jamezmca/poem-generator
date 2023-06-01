<script>
    import { page } from "$app/stores";

    const url = $page.url;

    const poem_id = url.searchParams.get("poem_id");
    import { onMount } from "svelte";
    export let ref;
    let poem = "";

    onMount(() => {
        if (!poem_id) {
            return;
        }
        async function getPoem() {
            try {
                const res = await fetch(
                    import.meta.env.VITE_SERVER_URL +
                        "api/get_poem?poem_id=" +
                        poem_id
                );
                const data = await res.json();
                console.log("FETCHED POEM: ", data.poem);
                poem = data.poem;
            } catch (err) {
                console.log("Failed to get poem", err.message);
            }
        }
        getPoem();
    });
</script>

<main class="min-h-screen flex flex-col p-4 text-sm gap-10 sm:gap-14 md:gap-24">
    <header
        class="flex items-center justify-between border-b pb-3 mx-auto max-w-[1200px] w-full"
    >
        <div class="flex items-center gap-3 sm:text-base">
            <i class="fa-solid fa-feather" />
            <h1 class="">The Occasional Poet</h1>
            <!-- <div class="absolute top-0 left-0 w-2/3 h-[1px] bg-slate-800" />
            <div class="absolute bottom-0 right-0 w-2/3 h-[1px] bg-slate-800" /> -->
        </div>
        <button
            on:click={() => {
                window.location.href = "/";
            }}
            class="border rounded px-2 py-1 flex items-center gap-2 text-xs sm:text-sm hover:bg-slate-100 duration-200"
        >
            <p>New Poem</p>
        </button>
    </header>
    {#if poem}
        <section
            id="finPoem"
            class="flex flex-1 flex-col gap-14 fadeIn sm:gap-16 md:gap-20 max-w-prose w-full mx-auto justify-center"
        >
            <h3
                id="loadingText"
                class="text-4xl sm:text-5xl md:text-6xl font-semibold text-center"
            >
                Your poem.
            </h3>
            <div class="flex flex-col gap-3 text-center text-xs sm:text-sm">
                <div class="flex flex-col gap-1.5">
                    {#each poem.split("\n") as line}
                        <p>{line}</p>
                    {/each}
                </div>
            </div>
            <footer class="flex items-center justify-center pb-10 pt-6 sm:pt-0">
                <i
                    class="fa-brands fa-facebook text-3xl text-slate-300 hover:text-slate-700 cursor-pointer duration-200"
                />
            </footer>
        </section>
    {:else}
        <section
            id="finPoem"
            class="flex flex-1 flex-col gap-14 items-center sm:gap-16 md:gap-20 max-w-prose w-full mx-auto justify-center"
        >
            <h3
                id="loadingText"
                class="text-4xl sm:text-5xl md:text-6xl font-semibold text-center"
            >
                Loading poem...
            </h3>
        </section>
    {/if}
</main>
