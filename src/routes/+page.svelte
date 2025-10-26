<script>
  import { TextArea, TextField, Button } from "svelte-elegant";
  import { themeStore } from "svelte-elegant/stores";
  import Plus from "../components/Plus.svelte";
  import Delete from "../components/Delete.svelte";

  let theme;
  themeStore.subscribe((value) => {
    theme = value;
  });

  let inputs = [
    { id: 1, value: "" },
    { id: 2, value: "" },
  ];

  let question = "";
  let questRendered = "";
  let generatedAnswer = "";
  let rotationDegrees = 0;

  function addNewOption() {
    const maxId = Math.max(...inputs.map((input) => input.id));
    inputs = [...inputs, { id: maxId + 1, value: "" }];
  }

  function removeOption(id) {
    inputs = inputs.filter((input) => input.id !== id);
  }

  function generateAnswer() {
    questRendered = question;
    inputs = inputs.filter((input) => input.value !== "");
    rotationDegrees += 360;

    // Проверяем, есть ли варианты ответов
    if (inputs.length === 0) {
      inputs = [
        { id: 1, value: "" },
        { id: 2, value: "" },
      ];
      generatedAnswer = "No answers available";
      return;
    }

    // Проверяем, заполнены ли все варианты ответов
    const emptyAnswers = inputs.filter((input) => input.value.trim() === "");
    if (emptyAnswers.length > 0) {
      generatedAnswer = "Please fill all answer options";
      return;
    }

    // Выбираем случайный ответ
    const randomIndex = Math.floor(Math.random() * inputs.length);
    generatedAnswer = inputs[randomIndex].value;
  }
</script>

<div class="app">
  <div
    class="left-half"
    style:display="flex"
    style:flex-direction="column"
    style:height="100%"
    style:align-items="center"
    style:gap="0.5rem"
    style:box-sizing="border-box"
  >
    <p style:font-size="1.28rem">Ask your question:</p>
    <TextArea
      width="100%"
      variant="Filled"
      height="6rem"
      label="Question"
      bind:value={question}
    />
    <p style:font-size="1.28rem">Give me response options:</p>
    {#each inputs as input, index (input.id)}
      <div style:display="flex" style:width="100%">
        <TextField
          width="100%"
          label="Answer {index + 1}"
          bind:value={input.value}
        />
        <button
          onclick={() => removeOption(input.id)}
          style:margin-left="0.33rem"
          style:margin-right="0.33rem"
          style:background="none"
          style:border="none"
          style:padding="0"
          style:cursor="pointer"
        >
          <Delete />
        </button>
      </div>
    {/each}
    <button
      onclick={addNewOption}
      style:align-self="flex-start"
      style:display="flex"
      style:align-items="center"
      style:cursor="pointer"
      style:background="none"
      style:border="none"
      style:padding="0"
    >
      <Plus />
      <span
        style:margin-left="0.5rem"
        style:color={theme.palette.primary}
        style:font-size="0.94rem"
      >
        Add a new response option
      </span>
    </button>
  </div>
  <div
    class="right-half"
    style:display="flex"
    style:flex-direction="column"
    style:align-items="center"
    style:box-sizing="border-box"
  >
    <img
      src="/dice-2.png"
      height="292px"
      width="292px"
      alt="logo"
      style:margin-bottom="0.7rem"
      style:transform="rotate({rotationDegrees}deg)"
      style:transition="transform 1s ease"
    />
    {#if generatedAnswer}
      <div style:margin-bottom="1rem" style:width="100%">
        <p style:font-weight="bold">Question:</p>
        <p style:margin-bottom="1rem">{questRendered || "No question asked"}</p>
        <p style:font-weight="bold">Answer:</p>
        <p>{generatedAnswer}</p>
      </div>
    {/if}
    <div style:width="100%" style:padding-bottom="6px">
      <Button width="100%" onclick={generateAnswer}>Generate Answer</Button>
    </div>
  </div>
</div>

<style>
  .app {
    display: flex;
    padding-top: 4.35rem;
  }

  .left-half {
    width: 67.5%;
  }

  .right-half {
    width: 32.5%;
  }

  .left-half,
  .right-half {
    padding-left: 0.5rem;
    padding-right: 0.66rem;
  }

  @media (max-width: 768px) {
    .app {
      flex-direction: column;
      padding-top: 4.35rem;
    }

    .left-half,
    .right-half {
      width: 100%;
      padding-left: 0.5rem;
    }
  }
</style>
