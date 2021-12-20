<script>
  import { createEventDispatcher } from "svelte";

  let dispatch = createEventDispatcher();
  let answer;

  const handleSubmit = () => {
    dispatch("checkAnswer", answer);
    answer = "";
  };
</script>

<form on:submit|preventDefault={handleSubmit}>
  <input bind:value={answer} type="text" placeholder="Answer" />
  <button class="submitButton">Submit</button>
</form>

<style>
  form {
    margin: 40px 0;
  }
  input {
    border-radius: 5px;
    padding: 9px;
  }
  .submitButton {
    background-color: dodgerblue;
    color: white;
    padding: 10px;
    font-weight: bold;
    border-radius: 5px;
    border: none;
  }
  .submitButton:hover {
    cursor: pointer;
  }
</style>
