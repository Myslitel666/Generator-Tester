<script>
  import { ColorThemeSwitch } from "svelte-elegant";
  import { goto } from "$app/navigation";
  import Header from "svelte-elegant/Header";

  import { themeStore, themeMode } from "svelte-elegant/stores";

  let theme;

  let logotypeFilter = "";
  let svelteColor = "";
  let elegantColor = "";

  // Подписываемся на изменения темы
  themeStore.subscribe((value) => {
    theme = value; //Инициализация объекта темы

    svelteColor = theme.palette.primary;
    elegantColor = $themeMode === "light" ? "#383838" : "#fdfdfd";
    logotypeFilter = $themeMode === "light" ? "brightness(80%)" : "";
  });
</script>

<Header>
  <button style:gap="0.5rem">
    <img src="/dice-2.png" height="45px" width="45px" alt="logo" />
    <p style:font-size="26px">
      <span
        style:color={svelteColor}
        style:filter={logotypeFilter}
        style:transition="all 0.3s"
      >
        Answer
      </span>
      <span style:color={svelteColor} style:transition="all 0.3s">
        Generator
      </span>
    </p>
  </button>
  <div style:margin-left="auto" style:margin-right="1.25rem">
    <ColorThemeSwitch />
  </div>
</Header>
