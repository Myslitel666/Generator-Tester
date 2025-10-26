<script lang="ts">
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

  let serialResults: { dice: String; value: String }[] = [];
  let delta: number[] = [];
  let resultDelta = 0;

  let experiments = "100";
  let series = "4";
  let isGenerated = false;

  function generateDice() {
    return Math.floor(Math.random() * 6) + 1;
  }

  function clearSeries() {
    serialResults = [];
    delta = [];
  }

  function clearDice() {
    for (let i = 0; i < diceResults.length; i++) {
      diceResults[i].value = 0;
    }
  }

  function formatNumber(value) {
    if (Number.isInteger(value)) return `${value}%`;
    return `${value.toFixed(1).replace(".", ",")}%`;
  }

  function generate() {
    isGenerated = true;
    clearSeries();
    // Генерируем броски кубика
    for (let j = 0; j < Number(series); j++) {
      clearDice();
      for (let i = 0; i < Number(experiments); i++) {
        const roll = generateDice();
        diceResults[roll - 1].value++;
      }
      // ГЛУБОКОЕ КОПИРОВАНИЕ объектов
      serialResults.push(JSON.parse(JSON.stringify(diceResults)));

      const serialValues = diceResults.map((d) => d.value);
      const idealOutcome = Number(experiments) / 6;
      for (let i = 0; i < serialValues.length; i++) {
        serialValues[i] = Math.abs(idealOutcome - serialValues[i]);
      }
      const avg = serialValues.reduce((a, b) => a + b, 0) / serialValues.length;
      delta.push((avg / idealOutcome) * 100);
    }

    resultDelta = delta.reduce((a, b) => a + b, 0) / delta.length;
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
    <div style:font-weight="600">Average Delta</div>
    <div style:font-size="32px">{formatNumber(resultDelta)}</div>
    <div class="result">
      {#each diceResults as result, i}
        <div>
          <div class="dice">{result.face}</div>
          {#each serialResults as seria}
            <div class="value">{seria[i].value}</div>
          {/each}
        </div>
      {/each}
      <div>
        <div class="dice" style:font-size="16px" style:font-weight="600">
          Avg. Δ
        </div>
        {#each delta as d}
          <div class="value">{formatNumber(d)}</div>
        {/each}
      </div>
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
    margin-top: -8px;
    user-select: none;
    pointer-events: none;
    gap: 7px;
  }

  .dice {
    font-size: 36px;
    height: 42px;
    display: flex; /* ← ДОБАВЬ ЭТО */
    align-items: center; /* ← ВЕРТИКАЛЬНОЕ ВЫРАВНИВАНИЕ */
    justify-content: center; /* ← ГОРИЗОНТАЛЬНОЕ ВЫРАВНИВАНИЕ */
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
