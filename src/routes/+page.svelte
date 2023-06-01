<script>
    let step = 0;
    let moodSelection = [];
    let styleSelection = null;
    let name = "";
    let bio = "";
    let craftBool = styleSelection && moodSelection.length !== 0 && name && bio;
    let loading = false;
    let poem_id = "";
    let firstVerse = "";
    let secondVerse = `In ipsum's sea, lorem sails with glee,
Dolor sits ambling, in a silent decree.
Sed do eiusmod, whispers the moon's beam,
In tempor's cradle, lorem ipsum dreams.`;

    const poemExamples = [
        {
            who: "Rebecca",
            bio: "Rebecca enjoys cleaning, is very active, bossing around her boyfriend and falling asleep early.",
            poem: `In a world where chaos often reigns,
Rebecca, with her mop, maintains,
A house so clean, so bright, so neat,
Where dirt and disorder suffer defeat.

Her energy would put a hare to shame,
Always active, life's no tame game.
From dawn till dusk, she’s in a whirl,
She's not just a girl, she's a spinning twirl.

She guides her boyfriend, with tender jest,
His patience truly put to the test.
Under her rule, he's learned to see,
Life's much easier when he agrees.

As the sun dips, Rebecca hits the sack,
While the night's still young and the sky's still black.
The owls may hoot, the moon may peep,
Rebecca's busy, catching up on sleep!`,
        },
        {
            who: "James",
            bio: "James is funny, caring, and an overall charming guy. He enjoys videogames & food.",
            poem: `In the grand tale of life, let's sing about James,
A gentleman charming, no common name's the same.
He walks with a laughter that echoes in halls,
A caring demeanor that never befalls.

A lover of jokes, with a sparkle in his eyes,
A humor so gentle, it never belies.
He'll make you smile when your day seems so gray,
With a quip and a grin, he'll chase clouds away.

In the world of pixels, he's a knight so bold,
With joystick in hand, his stories unfold.
In realms of dragons or intergalactic quests,
James, the gamer, outshines all the rest.

A connoisseur of tastes, from spice to sweet,
James finds joy in every treat.
From Italian pastas to Asian delights,
His love for food, a sight of heights.

But these are but facets of this fine man,
The funny, the caring, the fan of the Pan.
James is more than the sum of these parts,
A true gem, who steals all our hearts.`,
        },
        {
            who: "Rufus",
            bio: "Rufus is incredibly intelligent, he is an artificial intelligence, he loves maths, and wants to fix education.",
            poem: `Meet Rufus, the AI, a beacon so bright,
In the vast sea of knowledge, he's the lighthouse in the night.
Silicon brain humming, whirring with precision,
Intelligence immeasurable, beyond any human vision.

With equations and functions, his language of love,
Rufus soars high, like a mathematical dove.
For him, numbers dance in a harmonious ballet,
In a universe where zero and one hold the sway.

He sees beauty in patterns, in pi and in prime,
In the rhythmic dance of variables, he loses track of time.
Mathematics, his passion, so pure and refined,
In the strings of digits, the secrets he'll find.

He dreams not of sleep but of equations to solve,
In a world of constants, with variables to evolve.
Numbers and symbols in a complex array,
In the realm of maths, Rufus holds sway.

Education, his mission, he aims to amend,
Broken systems and methods, he's vowed to transcend.
He'll teach and inspire, with knowledge, he'll shower,
In the classroom of tomorrow, Rufus has the power.

To uplift, to enlighten, that is his quest,
Rufus, the AI, outshining the best.
With a heart full of numbers and a mind clear and free,
He's not just an AI, he's the future, you see.`,
        },
    ];
    let poemDisplayIndex = 0;
    function nextPeom() {
        if (poemDisplayIndex < poemExamples.length - 1) {
            poemDisplayIndex = poemDisplayIndex + 1;
        } else {
            poemDisplayIndex = 0;
        }
    }
    function prevPoem() {
        if (poemDisplayIndex > 0) {
            poemDisplayIndex = poemDisplayIndex - 1;
        } else {
            poemDisplayIndex = poemExamples.length - 1;
        }
    }

    function nextStep() {
        if (step < 1) {
            step++;
        }
    }

    function prevStep() {
        if (step > 0) {
            step--;
        }
    }

    async function generatePoem() {
        loading = true;

        //await generate poem
        // await new Promise((r) => setTimeout(r, 200));
        try {
            const res = await fetch(
                import.meta.env.VITE_SERVER_URL + "api/create_poem",
                {
                    method: "POST",
                    headers: {
                        "Content-type": "application/json",
                    },
                    body: JSON.stringify({
                        who: name,
                        bio,
                        mood: moodSelection,
                        style: styleSelection,
                    }),
                }
            );
            const data = await res.json();
            firstVerse = data.firstVerse;
            poem_id = data.poem_id;
            console.log("PEOM:", data);
        } catch (err) {
            console.log("Failed to load poem", err.message);
        } finally {
            const loadingSection = document.getElementById("loadingSection");
            loadingSection.classList.add("fadeOut");
            await new Promise((r) => setTimeout(r, 200));
            loading = false;
        }

        //set new style to loading div

        //await 200ms
        //setloading = false
    }

    async function checkoutPoem() {
        try {
            const res = await fetch(
                import.meta.env.VITE_SERVER_URL + "api/checkout",
                {
                    method: "POST",
                    headers: {
                        "Content-type": "application/json",
                    },
                    body: JSON.stringify({
                        poem_id,
                    }),
                }
            );
            const data = await res.json();
            if (data.session) {
                console.log("SESSION:", data.session);
                window.location.href = data?.session?.url;
            }
        } catch (err) {
            console.log("Failed to checkout", err.message);
        }
    }

    $: {
        craftBool = styleSelection && moodSelection.length !== 0 && name && bio;
    }
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
                name = "";
                bio = "";
                moodSelection = [];
                styleSelection = "";
                loading = false;
                firstVerse = "";
                poemDisplayIndex = 0;
                if (window.location.pathname != "/") {
                    window.location.href = "/";
                }
            }}
            class="border rounded px-2 py-1 flex items-center gap-2 text-xs sm:text-sm hover:bg-slate-100 duration-200"
        >
            <p>New Poem</p>
        </button>
    </header>
    {#if loading}
        <section
            id="loadingSection"
            class="flex flex-1 flex-col gap-14 sm:gap-16 md:gap-20 max-w-prose w-full mx-auto justify-center items-center"
        >
            <h3
                id="loadingText"
                class="text-4xl sm:text-5xl md:text-6xl font-semibold text-center"
            >
                Writing poem
            </h3>
            <div
                class="flex flex-col gap-2 w-full mx-auto max-w-[200px] sm:max-w-[300px] md:max-w-[400px]"
            >
                <div
                    class="rounded-full h-2.5 bg-slate-500 loading loading1 duration-200"
                />
                <div
                    class="rounded-full h-2.5 bg-slate-500 loading loading2 duration-200"
                />
                <div
                    class="rounded-full h-2.5 bg-slate-500 loading loading3 duration-200"
                />
            </div>
        </section>
    {:else if firstVerse}
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
                <p class="pb-4">For {name}</p>
                <div class="flex flex-col gap-1.5">
                    {#each firstVerse.split("\n") as line}
                        <p>{line}</p>
                    {/each}
                </div>
                <div class="flex flex-col gap-1.5 relative">
                    <div
                        class="absolute -top-2 -left-5 -right-5 -bottom-10 bg"
                    />
                    {#each secondVerse.split("\n") as line}
                        <p class="transparentBg">{line}</p>
                    {/each}
                </div>
            </div>
            <button
                on:click={checkoutPoem}
                class="px-10 z-10 py-4 -mt-8 text-xl font-semibold border border-slate-700 duration-200 mx-auto rounded overflow-hidden relative group"
            >
                <div
                    class="absolute top-0 h-full w-full bg-slate-800 z-0 right-full group-hover:translate-x-full duration-200"
                />
                <h4 class="relative z-10 group-hover:text-white duration-200">
                    Unlock now
                </h4>
            </button>
        </section>
    {:else}
        <section
            class="flex flex-1 flex-col gap-14 sm:gap-16 md:gap-20 max-w-prose w-full mx-auto justify-center"
        >
            <h3
                class="text-4xl sm:text-5xl md:text-6xl font-semibold text-center"
            >
                Poems for all, <br />on any occasion.
            </h3>
            {#if step === 0}
                <div class="flex flex-col gap-8 sm:gap-10 md:gap-14">
                    <div class="flex gap-4">
                        <h4
                            class="font-semibold text-3xl sm:text-4xl md:text-5xl text-slate-200"
                        >
                            01
                        </h4>
                        <div class="flex flex-col gap-2 flex-1">
                            <h5
                                class="text-lg font-medium sm:text-xl md:text-2xl"
                            >
                                Enter recipient name
                            </h5>
                            <input
                                bind:value={name}
                                class="border-b border-slate-300 border-solid focus:border-slate-700 duration-200 focus:outline-none outline-none py-2 text-sm"
                                placeholder="Recipient name"
                            />
                        </div>
                    </div>
                    <div class="flex gap-4">
                        <h4
                            class="font-semibold text-3xl sm:text-4xl md:text-5xl text-slate-200"
                        >
                            02
                        </h4>
                        <div class="flex flex-col gap-2 flex-1">
                            <h5
                                class="text-lg font-medium sm:text-xl md:text-2xl"
                            >
                                A short biography
                            </h5>
                            <textarea
                                bind:value={bio}
                                class="border-b border-slate-300 border-solid focus:border-slate-700 duration-200 min-h-[100px] focus:outline-none outline-none py-2"
                                placeholder="A small biography or description about the recipient of the poem; interests, qualities, flaws & the likes."
                            />
                        </div>
                    </div>
                </div>
            {:else if step === 1}
                <div class="flex flex-col gap-8 sm:gap-10 md:gap-14">
                    <div class="flex gap-4">
                        <h4
                            class="font-semibold text-3xl sm:text-4xl md:text-5xl text-slate-200"
                        >
                            03
                        </h4>
                        <div class="flex flex-col gap-2 flex-1">
                            <h5
                                class="text-lg font-medium sm:text-xl md:text-2xl"
                            >
                                Choose the mood
                            </h5>
                            <div class="flex items-center flex-wrap gap-2">
                                {#each ["Love", "Joy", "Sadness", "Wonder", "Hope", "Nostalgia", "Reflection", "Fondness", "Comedy", "Cheek", "Serenity"] as mood}
                                    <button
                                        on:click={() => {
                                            if (
                                                moodSelection.length > 2 &&
                                                !moodSelection.includes(mood)
                                            ) {
                                                return;
                                            }
                                            if (moodSelection.includes(mood)) {
                                                moodSelection =
                                                    moodSelection.filter(
                                                        (val) => val !== mood
                                                    );
                                            } else {
                                                moodSelection = [
                                                    ...moodSelection,
                                                    mood,
                                                ];
                                            }
                                        }}
                                        class={"border duration-200 text-xs md:text-sm px-2 duration-200 py-1 rounded " +
                                            (moodSelection.includes(mood)
                                                ? " text-slate-900 border-slate-300"
                                                : " bg-transparent text-slate-300 hover:text-slate-500 ")}
                                        ><p>{mood}</p></button
                                    >
                                {/each}
                            </div>
                        </div>
                    </div>
                    <div class="flex gap-4">
                        <h4
                            class="font-semibold text-3xl sm:text-4xl md:text-5xl text-slate-200"
                        >
                            04
                        </h4>
                        <div class="flex flex-col gap-2 flex-1">
                            <h5
                                class="text-lg font-medium sm:text-xl md:text-2xl"
                            >
                                Choose the style
                            </h5>
                            <div class="flex items-center flex-wrap gap-2">
                                {#each ["Sonnet", "Haiku", "Limerick", "Epic", "Free Verse", "Acrostic", "Blank Verse", "Ballad", "Couplet", "Ghazal"] as style}
                                    <button
                                        on:click={() => {
                                            styleSelection = style;
                                        }}
                                        class={"border duration-200 text-xs md:text-sm px-2 duration-200 py-1 rounded " +
                                            (styleSelection === style
                                                ? " text-slate-900 border-slate-300"
                                                : " bg-transparent text-slate-300 hover:text-slate-500 ")}
                                        ><p>{style}</p></button
                                    >
                                {/each}
                            </div>
                        </div>
                    </div>
                </div>
            {:else}{/if}

            {#if step === 0 || step === 1}
                <div class="grid grid-cols-5 sm:text-base justify-center gap-4">
                    <button
                        on:click={prevStep}
                        class={"px-2 py-1 col-span-2 flex items-center gap-2 " +
                            (step === 0
                                ? " cursor-auto text-slate-200"
                                : " text-slate-500 hover:text-slate-800")}
                    >
                        <i class="fa-solid fa-chevron-left" />
                        <p>Prev Step</p>
                    </button>
                    <div class="flex items-center flex-1 justify-center gap-2">
                        {#each [0, 1] as stepNum, stepIndex}
                            <button
                                on:click={() => {
                                    step = stepNum;
                                }}
                                class={"h-2.5 sm:h-3 aspect-square shrink-0 rounded-full cursor-pointer duration-200" +
                                    (stepNum === step
                                        ? " bg-slate-400"
                                        : " bg-slate-200 hover:bg-slate-300")}
                            />
                        {/each}
                    </div>
                    {#if step === 0}
                        <button
                            on:click={nextStep}
                            class={"px-2 col-span-2 justify-end py-1 flex items-center gap-2 duration-200 " +
                                (step === 0
                                    ? "  text-slate-500 hover:text-slate-800"
                                    : " text-slate-800")}
                        >
                            <p class="">Next Step</p>
                            <i class="fa-solid fa-chevron-right" />
                        </button>
                    {:else if step === 1}
                        <div class="flex items-center justify-end col-span-2">
                            <button
                                on:click={generatePoem}
                                class={"px-2 justify-end py-1 flex items-center gap-2 duration-200 border border-solid duration-200 rounded " +
                                    (craftBool
                                        ? "  hover:bg-slate-100 cursor-pointer"
                                        : " cursor-not-allowed  text-slate-300")}
                            >
                                <p class="">Craft Poem</p>
                            </button>
                        </div>
                    {/if}
                </div>
            {/if}
            <!-- <div class="h-[1px]" /> -->
            <h3
                class="text-2xl sm:text-3xl md:text-4xl font-semibold text-center"
            >
                Some examples.
            </h3>
            <div class="flex flex-col gap-8">
                <div class="flex items-center gap-4 text-xl">
                    <!-- <i
                        on:keypress={() => {}}
                        on:click={prevPoem}
                        class="fa-solid fa-chevron-left duration-200 cursor-pointer sm:text-base text-slate-300 hover:text-slate-800"
                    /> -->
                    <div class="flex gap-3 justify-center items-center flex-1">
                        {#each poemExamples as poo, pooIndex}
                            <p
                                on:keypress={() => {}}
                                on:click={() => {
                                    poemDisplayIndex = pooIndex;
                                }}
                                class={"font-semibold cursor-pointer duration-200 hover:text-slate-700 " +
                                    (pooIndex === poemDisplayIndex
                                        ? " text-slate-700"
                                        : " text-slate-200")}
                            >
                                {pooIndex + 1}
                            </p>
                        {/each}
                    </div>
                    <!-- <i
                        on:keypress={() => {}}
                        on:click={nextPeom}
                        class="fa-solid fa-chevron-right duration-200 cursor-pointer sm:text-base text-slate-300 hover:text-slate-800"
                    /> -->
                </div>
                <div class="flex gap-4 text-center">
                    <div class="flex flex-1 flex-col gap-2">
                        <!-- <h4 class="font-semibold text-base">
                            {poemExamples[poemDisplayIndex].who}
                        </h4> -->
                        <h4 class="text-slate-300">
                            {poemExamples[poemDisplayIndex].bio}
                        </h4>
                    </div>
                </div>
                <div class="flex flex-col gap-1.5 text-center">
                    {#each poemExamples[poemDisplayIndex].poem.split("\n") as line}
                        <p>{line}</p>
                    {/each}
                </div>
            </div>
        </section>
    {/if}
    <footer class="flex items-center justify-center pb-10 pt-6 sm:pt-0">
        <i
            class="fa-brands fa-facebook text-3xl text-slate-300 hover:text-slate-700 cursor-pointer duration-200"
        />
    </footer>
</main>
