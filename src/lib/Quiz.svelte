<script>
    import {fly} from "svelte/transition";
    import {afterUpdate, beforeUpdate, onMount} from "svelte";
    import Question from "./Question.svelte";
    import Modal from "./Modal.svelte";

    let activeQuestion = 0;
    let score = 0;

    let quiz = getQuiz()

    let isModalOpen = false;


    onMount(() => {
        console.log('i mounted');
    })

    beforeUpdate(() => {
        console.log("Before Update")
    })

    afterUpdate(() => {
        console.log("After update")
    })

    async function getQuiz() {
        const res = await fetch('https://opentdb.com/api.php?amount=10&category=28&type=multiple');
        const quiz = await res.json()
        return quiz
    }

    function nextQuestion() {
        activeQuestion += 1;
    }

    function resetQuiz() {
        score = 0;
        quiz = getQuiz();
        questonNumber = 1;
        isModalOpen = false;
    }

    function addToScore() {
        score += 1;
    }

    //Reactive Statement
    $: if (score > 2) {
        // alert('You Won');
        isModalOpen = true;
    }

    // Reactive declaration
    $: questonNumber = activeQuestion + 1;

</script>
<div>
    <button on:click={resetQuiz}> Start New Quiz</button>

    <h3>My Score :{score}</h3>
    <h4>Question #{questonNumber}</h4>

    {#await quiz}
        Loading....
    {:then data}
        {#each data.results as question, index}
            {#if index === activeQuestion}
                <div in:fly={{x:100}} out:fly={{x:-200}} class="fade-wrapper">
                    <Question {addToScore} {nextQuestion} {question}/>
                </div>
            {/if}
        {/each}
    {/await}


</div>

{#if isModalOpen}
    <Modal>
        <h2> You Won!</h2>
        <p>Congratulation</p>
        <button on:click={resetQuiz}>Start Over</button>
    </Modal>
{/if}
<style>
    .fade-wrapper {
        position: absolute;
    }
</style>