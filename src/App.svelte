<script>
  import { debug } from "svelte/internal";
  import AnswerInput from "./AnswerInput.svelte";

  import * as vocab from "./N5-Vocab.json";

  let showScore = false;
  let score = 0;

  let numberOfCards = 5;
  let hiraWords = [];
  let hiraAnswers = [];
  let vocabList = Object.entries(vocab).filter((a) => a[0] != "default");
  let currentCard = 0;
  let rightAnswer;
  let showAnswer = false;

  const handleClick = () => {
    hiraWords = [];
    hiraAnswers = [];
    showAnswer = false;
    while (hiraWords.length < numberOfCards) {
      let randomItem =
        Object.entries(vocabList)[
          Math.floor(Math.random() * (Object.entries(vocabList).length - 0)) + 0
        ][1];
      if (!(hiraWords.findIndex((a) => a == randomItem[1]) != -1)) {
        hiraWords.push(randomItem[1]);
        hiraAnswers.push(randomItem[0]);
      }
    }
  };

  const checkAnswer = (answer) => {
    if (hiraWords[currentCard] == vocab[answer.detail.toLowerCase()]) {
      rightAnswer = true;
      score++;
      nextCard();
    } else {
      rightAnswer = false;
    }
  };

  const nextCard = () => {
    if (currentCard + 1 == numberOfCards) {
      showScore = true;
    } else {
      currentCard++;
    }
    console.log(currentCard + " - " + numberOfCards);
  };
</script>

<main>
  <div class="gameContainer">
    <div class="card">
      {#if hiraWords.length == 0}
        <button on:click={handleClick}>
          <h1>Comenzar!</h1>
        </button>
      {:else if showScore}
        <h1>{score + "/" + numberOfCards}</h1>
      {:else}
        <h1>{hiraWords[currentCard]}</h1>
      {/if}
    </div>

    <div class="answerSection">
      {#if rightAnswer == true}
        <h2 class="correctAnswer">Correcto!</h2>
      {:else if rightAnswer === false}
        <h2 class="wrongAnswer">Falso!</h2>
      {/if}

      {#if hiraWords.length > 0 && !showScore}
        <AnswerInput on:checkAnswer={checkAnswer} />
        <button class="showAnswerButton" on:click={() => (showAnswer = true)}
          >Show Answer</button
        >
      {/if}
      {#if showScore}
        <button
          class="playAgainButton"
          on:click={() => console.log("play again")}>Play Again</button
        >
      {/if}
    </div>

    {#if showAnswer}
      <p>{hiraAnswers[currentCard]}</p>
      <button
        on:click={() => {
          showAnswer = false;
          nextCard();
        }}>Next</button
      >
    {/if}
  </div>
</main>

<style>
  main {
    margin: 0;
    padding: 0;
    height: 100vh;
  }
  .gameContainer {
    width: 500px;
    margin: auto;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  .card {
    height: 350px;
    width: 350px;
    border-radius: 20px;
    border: 1px solid #cfcfcf;
    display: flex;
    justify-content: center;
    box-shadow: 0px 0px 20px 0px #cfcfcf;
    align-items: center;
  }
  .card button {
    height: 100%;
    width: 100%;
    border-radius: 20px;
    background-color: white;
    border: none;
    margin: 0;
  }
  .card button:hover {
    font-size: 21px;
    cursor: pointer;
  }
  .answerSection {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .answerSection h2 {
    margin: 0;
    margin-top: 40px;
  }
  .correctAnswer {
    color: limegreen;
  }
  .wrongAnswer {
    color: crimson;
  }
  .showAnswerButton {
    color: white;
    background-color: crimson;
    border-radius: 5px;
    border: none;
    font-weight: bold;
    padding: 10px;
  }
  .showAnswerButton:hover {
    cursor: pointer;
  }
  .playAgainButton {
    margin-top: 40px;
    color: white;
    background-color: limegreen;
    border-radius: 5px;
    border: none;
    font-weight: bold;
    padding: 10px;
  }
  .showAnswerButton:hover {
    cursor: pointer;
  }
</style>
