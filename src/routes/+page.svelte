<script lang="ts">
    import { open } from "@tauri-apps/plugin-dialog";
    import { readTextFile, BaseDirectory } from "@tauri-apps/plugin-fs";
    import "../output.css";

    const words: string[][] = [];
    const usedWords: string[][] = [];

    let file: string | null;
    let wordIndex: number | null = null;
    let choise: string[] = getRandomWords();
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
            alert("Слова закончились");
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

            console.log(text);
        }
    }
</script>

<section
    class="flex flex-col gap-y-5 justify-center items-center w-full dark:bg-gray-800 h-screen"
>
    <section class="-mt-52">
        <section
            class="flex flex-col min-w-0 text-white sm:min-w-[550px] gap-y-3 mb-4"
        >
            <div>Всего: {wordsLength}</div>
            <div>Пройдено: {usedWordsLength}</div>
            <div>
                <button
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
        <section
            class="flex border-2 shadow-2xl rounded-2xl justify-center items-center p-5 min-h-[200px] min-w-0 sm:min-w-[550px] mx-2 bg-white dark:bg-gray-800"
        >
            {#if words.length !== 0}
                <div class="block m-auto w-full">
                    <div
                        class="block w-full text-center text-2xl text-black dark:text-white"
                    >
                        <p>{choise[2]}</p>
                    </div>

                    <div
                        class="block w-full text-center text-2xl text-black dark:text-white"
                    >
                        <p>{choise[1]}</p>
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

                    {#if showBlockWithResult}
                        <div class="mt-4">
                            <div>
                                <p class="text-green-500">{choise[0]}</p>
                            </div>
                        </div>

                        {#if result !== choise[0]}
                            <div class="mt-4">
                                <div>
                                    <p class="text-red-500">
                                        {#if result === ""}
                                            {choise[0]}
                                        {:else}
                                            {result}
                                        {/if}
                                    </p>
                                </div>
                            </div>
                        {/if}
                    {/if}

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
            {/if}
        </section>
    </section>
</section>
