<script lang="ts">
    import { open } from "@tauri-apps/plugin-dialog";
    import { readTextFile, BaseDirectory } from "@tauri-apps/plugin-fs";
    import "../output.css";

    let words: { word: string; pronunciation: string; translation: string }[] =
        [];
    const usedWords: {
        word: string;
        pronunciation: string;
        translation: string;
    }[] = [];

    let file: string | null;
    let wordIndex: number | null = null;
    let choise: { word: string; pronunciation: string; translation: string } =
        getRandomWords();
    let isStarting: boolean = false;
    let result: string = "";
    let showBlockWithResult: boolean = false;
    let wordsLength = words.length;
    let usedWordsLength = usedWords.length;

    function start() {
        if (!isStarting) {
            isStarting = true;
        }
        clearAll();
        choise = getRandomWords();
    }

    function getRandomWords() {
        wordIndex = Math.floor(Math.random() * (words.length + 1));
        return words[wordIndex];
    }

    function check() {
        showBlockWithResult = true;
    }

    function continueTrain() {
        showBlockWithResult = false;
        updateArrayWithWords();
        start();
    }

    function updateArrayWithWords() {
        if (wordIndex) {
            usedWords.push(choise);
            words.splice(wordIndex, 1);

            wordsLength = words.length;
            usedWordsLength = usedWords.length;
        } else if (wordIndex === 0) {
            alert("Words finished.");
        } else {
            alert("You have problems in the code.");
        }
    }

    function clearAll() {
        result = "";
    }

    async function readFile() {
        if (file) {
            const text = await readTextFile(file, {
                baseDir: BaseDirectory.AppConfig,
            });

            words = JSON.parse(text);

            start();
        }
    }
</script>

<section
    class="flex flex-col gap-y-5 justify-center items-center w-full dark:bg-gray-800 h-screen"
>
    <section class="">
        <section
            class="flex flex-col min-w-0 text-white sm:min-w-[550px] gap-y-3 mb-4"
        >
            <div>All: {wordsLength}</div>
            <div>Passed: {usedWordsLength}</div>
            <div>
                <button
                    class="border-2 rounded-2xl px-4 py-2"
                    on:click={async () => {
                        file = await open({
                            multiple: false,
                            directory: false,
                        });

                        await readFile();
                    }}>Upload</button
                >
            </div>
        </section>
        {#if words.length !== 0}
            <section
                class="flex border-2 shadow-2xl rounded-2xl justify-center items-center p-5 min-h-[200px] min-w-0 sm:min-w-[550px] mx-2 bg-white dark:bg-gray-800"
            >
                <div class="block m-auto w-full">
                    <div
                        class="block w-full text-center text-2xl text-black dark:text-white"
                    >
                        <p>{choise.translation}</p>
                    </div>

                    <div
                        class="block w-full text-center text-2xl text-black dark:text-white"
                    >
                        <p>{choise.pronunciation}</p>
                    </div>

                    <div class="mt-4">
                        <div>
                            <input
                                type="text"
                                class="input border-2 border-gray-200 dark:border-gray-600 w-full p-3 rounded-lg bg-white dark:bg-gray-700 text-black dark:text-white"
                                placeholder="Word"
                                bind:value={result}
                            />
                        </div>
                    </div>

                    <div class="min-h-[70px]">
                        <div class="mt-4">
                            <div>
                                <p>
                                    {#if showBlockWithResult}
                                        <span class="text-green-500"
                                            >{choise.word}</span
                                        >
                                    {:else}
                                        <span class="opacity-0">Word</span>
                                    {/if}
                                </p>
                            </div>
                        </div>

                        <div class="mt-4">
                            <div>
                                <p>
                                    {#if showBlockWithResult && result !== choise.word}
                                        <span class="text-red-500">
                                            {#if result === ""}
                                                {choise.word}
                                            {:else}
                                                {result}
                                            {/if}</span
                                        >
                                    {:else}
                                        <span class="opacity-0">Result</span>
                                    {/if}
                                </p>
                            </div>
                        </div>
                    </div>

                    <div class="block w-full mt-4">
                        <button
                            on:click={() =>
                                showBlockWithResult ? continueTrain() : check()}
                            class="btn flex m-auto rounded-2xl border-2 py-3 px-8 border-gray-200 dark:border-gray-600 bg-white dark:bg-gray-700 text-black dark:text-white"
                        >
                            {#if showBlockWithResult}
                                Continue
                            {:else}
                                Check
                            {/if}
                        </button>
                    </div>
                </div>
            </section>
        {/if}
    </section>
</section>
