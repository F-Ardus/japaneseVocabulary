<script>
  import AnswerInput from "./AnswerInput.svelte";

  import * as vocab from "./N5-Vocab.json";

  //FUNCTIONS
  const generateFullVocabList = () => {
    let dict = {};
    let allLists = Object.entries(vocab.default);
    for (let index = 0; index < Object.entries(vocab.default).length; index++) {
      dict["s" + (index + 1)] = allLists.sort()[index][1];
    }
    return dict;
  };

  const generateAllVocab = () => {
    let res = [];
    for (
      let index = 1;
      index <= Object.entries(fullVocabList).length;
      index++
    ) {
      res = res.concat(fullVocabList["s" + index]);
    }
    return res;
  };

  const getVocabList = () => {
    if (selectedList == "all") {
      return allVocab;
    } else {
      return fullVocabList[selectedList];
    }
  };

  const initWords = () => {
    let maxCards;
    if (numberOfCards == "nonstop") {
      maxCards = vocabList.length;
    } else if (numberOfCards > vocabList.length) {
      let numberList = getNumbersList(vocabList);
      numberOfCards = numberList[numberList.length - 1];
      maxCards = numberOfCards;
    } else {
      maxCards = numberOfCards;
    }
    while (hiraWords.length < maxCards) {
      let randomItem =
        vocabList[Math.floor(Math.random() * (vocabList.length - 0)) + 0];
      if (!(hiraWords.findIndex((a) => a == randomItem[1]) != -1)) {
        hiraWords.push(randomItem[1]);
        hiraAnswers.push(randomItem[0]);
      }
    }
  };

  const restartGame = () => {
    vocabList = getVocabList();
    showScore = false;
    score = 0;
    hiraWords = [];
    hiraAnswers = [];
    currentCard = 0;
    rightAnswer = undefined;
    showAnswer = false;
    initWords();
  };

  const checkAnswer = (answer) => {
    let correspondToAnswer = vocabList.find(
      (i) => i[0] === answer.detail.toLowerCase()
    )?.[1];
    if (hiraWords[currentCard] == correspondToAnswer) {
      rightAnswer = true;
      showAnswer = false;
      score++;
      nextCard();
    } else {
      rightAnswer = false;
    }
  };

  const nextCard = () => {
    let maxCards = vocabList.length;
    if (currentCard + 1 == maxCards) {
      if (numberOfCards == "nonstop") {
        restartGame();
      } else {
        showScore = true;
      }
    } else {
      currentCard++;
    }
  };

  const getNumbersList = (list) => {
    let posibleNumbers = [];
    for (let index = 4; index < list.length; index += 5) {
      posibleNumbers.push(index + 1);
    }
    return posibleNumbers;
  };

  //CONSTANTS
  const fullVocabList = generateFullVocabList();
  const allVocab = generateAllVocab();

  //MODIFIABLES
  let numberOfCards = 5;
  let selectedList = "s1";

  //INIT STATE
  let vocabList = getVocabList();
  let showScore = false;
  let score = 0;
  let hiraWords = [];
  let hiraAnswers = [];
  let currentCard = 0;
  let rightAnswer = undefined;
  let showAnswer = false;
</script>

<main>
  <div class="gameContainer">
    <div>
      <select
        bind:value={selectedList}
        on:change={restartGame}
        class="vocabSelector"
      >
        {#each Object.entries(fullVocabList) as list}
          <option value={list[0]}>{"N5 Set - " + list[0]}</option>
        {/each}
        <option value={"all"}>Todos</option>
      </select>

      <select
        bind:value={numberOfCards}
        on:change={restartGame}
        class="vocabSelector"
      >
        {#each getNumbersList(vocabList) as amount}
          <option value={amount}>{amount}</option>
        {/each}
        {#if vocabList.length % 5 != 0}
          <option value={vocabList.length}
            >{"All (" + vocabList.length + ")"}</option
          >
        {/if}
        <option value={"nonstop"}>Non Stop</option>
      </select>
    </div>

    <div class="card">
      {#if hiraWords.length == 0}
        <button on:click={restartGame}>
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
        <button
          class="showAnswerButton"
          on:click={() => {
            showAnswer = true;
            score--;
          }}>Show Answer</button
        >
      {/if}
      {#if showScore}
        <button class="playAgainButton" on:click={restartGame}
          >Play Again</button
        >
      {/if}
    </div>

    {#if showAnswer}
      <p>{hiraAnswers[currentCard]}</p>
      <button
        on:click={() => {
          showAnswer = false;
          score++;
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
    max-height: 100%;
    max-width: 100%;
  }
  .vocabSelector {
    background-color: white;
    width: 150px;
    margin-bottom: 40px;
    border-radius: 5px;
  }
  .gameContainer {
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
  .playAgainButton:hover {
    cursor: pointer;
  }
</style>
