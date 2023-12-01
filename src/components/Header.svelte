<script>
  import { onMount } from "svelte";
  import { darkMode } from "../stores/darkModeStore";
  import iconSunLight from "/images/icon-sun-light.svg";
  import iconSunDark from "/images/icon-sun-dark.svg";
  import iconMoonLight from "/images/icon-moon-light.svg";
  import iconMoonDark from "/images/icon-moon-dark.svg";

  let isDarkMode;

  const toggleDarkMode = () => {
    darkMode.update((value) => {
      isDarkMode = !value;
      document.body.classList.toggle("dark", isDarkMode);
      // @ts-ignore
      localStorage.setItem("darkMode", isDarkMode);
      return isDarkMode;
    });
  };

  onMount(() => {
    // Check the local storage for the user's dark mode preference
    const storedDarkMode = localStorage.getItem("darkMode");
    if (storedDarkMode !== null) {
      isDarkMode = JSON.parse(storedDarkMode);
      document.body.classList.toggle("dark", isDarkMode);
      darkMode.set(isDarkMode);
    }
  });
</script>

<header class="header">
  <div>test</div>
  <div class="header__switch">
    <div class="header__switch-icon">
      <img src={isDarkMode ? iconSunLight : iconSunDark} alt="sun icon" />
    </div>
    <label>
      <input
        type="checkbox"
        bind:checked={$darkMode}
        on:click={toggleDarkMode}
      />
      <span />
    </label>
    <div class="header__switch-icon">
      <img src={isDarkMode ? iconMoonLight : iconMoonDark} alt="moon icon" />
    </div>
  </div>
</header>

<style lang="scss">
  :global(body.dark) {
    background-color: #1a1a1a;
    color: #ffffff;
  }

  .header {
    width: 100%;
    padding: 16px 24px;
    display: flex;
    align-items: center;
    justify-content: flex-end;

    &__switch {
      display: flex;
      align-items: center;
      gap: 8px;
      margin-left: auto;

      &-icon {
        width: 16px;
        height: 16px;

        img {
          width: 100%;
          height: 100%;
          object-fit: cover;
        }
      }

      label {
        position: relative;
        display: inline-block;
        width: 32px;
        height: 20px;

        input {
          opacity: 0;
          width: 0;
          height: 0;
        }

        span {
          position: absolute;
          cursor: pointer;
          top: 0;
          left: 0;
          right: 0;
          bottom: 0;
          background-color: var(--purple);
          -webkit-transition: 0.4s;
          transition: 0.4s;
          border-radius: 34px;

          &::before {
            position: absolute;
            content: "";
            height: 12px;
            width: 12px;
            left: 4px;
            bottom: 4px;
            background-color: white;
            -webkit-transition: 0.4s;
            transition: 0.4s;
            border-radius: 50%;
          }
        }

        input:checked + span:before {
          -webkit-transform: translateX(12px);
          -ms-transform: translateX(12px);
          transform: translateX(12px);
        }
      }
    }
  }
</style>
