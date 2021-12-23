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

<style lang="scss">
  form {
    margin: 40px 0;
    display: flex;
    max-width: 100%;
  }
  input {
    border-radius: 5px;
    padding: 9px;
    background-color: #fcfbf6;
    max-width: calc(100% - 70px);
  }
  .submitButton {
    background-color: #5ac3d9;
    color: #fcfbf6;
    padding: 10px;
    font-weight: bold;
    border-radius: 5px;
    border: none;
    width: 70px;

    &:hover {
      cursor: pointer;
    }
  }
</style>
