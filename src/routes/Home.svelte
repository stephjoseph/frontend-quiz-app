<script>
  // @ts-ignore
  import { link } from "svelte-routing";
  import Header from "../components/Header.svelte";

  const links = [
    {
      text: "HTML",
      icon: "/images/icon-html.svg",
      path: "/html",
      color: "#FFF1E9",
    },
    {
      text: "CSS",
      icon: "/images/icon-css.svg",
      path: "/css",
      color: "#E0FDEF",
    },
    {
      text: "Javascript",
      icon: "/images/icon-js.svg",
      path: "/javascript",
      color: "#EBF0FF",
    },
    {
      text: "Accessibility",
      icon: "/images/icon-accessibility.svg",
      path: "/accessibility",
      color: "#F6E7FF",
    },
  ];
</script>

<Header />
<main class="home">
  <div class="home__heading">
    <h1>
      Welcome to the
      <span>Frontend Quiz!</span>
    </h1>
    <span>Pick a subject to get started.</span>
  </div>

  <ul class="home__links">
    {#each links as item, index (item.text)}
      <li>
        <a href={item.path} use:link>
          <div style="background-color: {item.color}">
            <img src={item.icon} alt={item.text + " icon"} />
          </div>
          <span> {item.text}</span>
        </a>
      </li>
    {/each}
  </ul>
</main>

<style lang="scss">
  .home {
    padding: 32px 24px 250px;
    display: flex;
    flex-direction: column;
    width: 100%;
    gap: 40px;

    &__heading {
      display: flex;
      flex-direction: column;
      width: 100%;
      gap: 16px;

      h1 {
        color: var(--dark-navy);
        font-size: 40px;
        font-style: normal;
        font-weight: 300;
        line-height: 100%; /* 40px */

        span {
          color: var(--dark-navy);
          font-size: 40px;
          font-style: normal;
          font-weight: 500;
          line-height: 100%; /* 40px */
        }
      }

      span {
        color: var(--grey-navy);
        font-size: 14px;
        font-style: italic;
        font-weight: 400;
        line-height: 150%; /* 21px */
      }
    }

    &__links {
      list-style-type: none;
      display: flex;
      flex-direction: column;
      gap: 12px;
      width: 100%;

      li {
        a {
          display: flex;
          align-items: center;
          gap: 16px;
          width: 100%;
          background: var(--pure-white);
          padding: 12px;
          border-radius: 12px;
          color: var(--dark-navy);
          font-size: 18px;
          font-style: normal;
          font-weight: 500;
          line-height: 100%; /* 18px */

          div {
            width: 40px;
            height: 40px;
            padding: 6px;
            border-radius: 6px;

            img {
              width: 100%;
              height: 100%;
              object-fit: cover;
            }
          }
        }
      }
    }
  }

  body.dark {
    .home {
      &__heading {
        h1 {
          color: var(--pure-white);

          span {
            color: var(--pure-white);
          }
        }

        span {
          color: var(--light-bluish);
        }
      }

      &__links {
        li {
          a {
            background: var(--navy);
            color: var(--pure-white);
          }
        }
      }
    }
  }

  @media screen and (min-width: 768px) {
    .home {
      padding: 0px 64px 240px;
      gap: 64px;

      &__heading {
        h1 {
          font-size: 64px;
          display: flex;
          flex-direction: column;
          gap: 8px;

          span {
            font-size: 64px;
          }
        }
        span {
          font-size: 20px;
        }
      }

      &__links {
        gap: 24px;

        li {
          a {
            gap: 32px;
            width: 100%;
            border-radius: 24px;
            font-size: 28px;

            div {
              width: 56px;
              height: 56px;
              padding: 8px;
              border-radius: 12px;
            }
          }
        }
      }
    }
  }

  @media screen and (min-width: 1280px) {
    .home {
      padding: 0px 140px 280px;
      gap: 0px;
      flex-direction: row;
      justify-content: space-between;

      &__heading {
        width: 40.09%;
        gap: 48px;
      }

      &__links {
        width: 48.62%;

        li {
          a {
            padding: 20px;
          }
        }
      }
    }
  }
</style>
