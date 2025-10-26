<script>
  import { TextField, Button } from "svelte-elegant";
  import { themeStore } from "svelte-elegant/stores";

  let theme;
  themeStore.subscribe((value) => {
    theme = value;
  });

  const diceResults = [
    { face: "⚀", value: 0 },
    { face: "⚁", value: 0 },
    { face: "⚂", value: 0 },
    { face: "⚃", value: 0 },
    { face: "⚄", value: 0 },
    { face: "⚅", value: 0 },
  ];

  let experiments = "100";
  let series = "4";
  let isGenerated = false;

  function generateDice() {
    return Math.floor(Math.random() * 6) + 1;
  }

  function generate() {
    isGenerated = true;
    // Выбираем случайный ответ
    for (let i = 0; i < Number(experiments); i++) {
      const roll = generateDice();
      diceResults[roll - 1].value = diceResults[roll - 1].value + 1;
    }
  }
</script>

<div class="app">
  <div class="menu">
    <TextField
      label="Experiments"
      bind:value={experiments}
      width="100%"
      type="number"
    />
    <TextField label="Series" bind:value={series} width="100%" type="number" />
    <Button width="100%" onclick={generate}>Generate</Button>
  </div>
  {#if isGenerated}
    <div class="result">
      {#each diceResults as result}
        <div>
          <div class="dice">{result.face}</div>
          <div class="value">{result.value}</div>
        </div>
      {/each}
    </div>
  {/if}
</div>

<style>
  .app {
    display: flex;
    flex-direction: column;
    padding-top: 4.35rem;
    justify-content: center;
    align-items: center;
  }

  .result {
    display: flex;
    margin-top: -12px;
    user-select: none;
    pointer-events: none;
    gap: 7px;
  }

  .dice {
    font-size: 36px;
  }

  .value {
    text-align: center;
  }

  .menu {
    display: flex;
    flex-direction: column;
    gap: 7px;
    width: 290px;
    padding-bottom: 6px;
  }

  @media (max-width: 768px) {
    .app {
    }
  }
</style>
