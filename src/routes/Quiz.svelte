<script>
  // @ts-nocheck
  import { link } from "svelte-routing";
  import { onMount } from "svelte";
  import { writable } from "svelte/store";
  import Header from "../components/Header.svelte";
  import iconCorrect from "/images/icon-correct.svg";
  import iconIncorrect from "/images/icon-incorrect.svg";

  export let quizName;
  let quiz = null;
  let currentQuestion = 0;
  let selectedOption = null;
  let isSubmitted = false;
  let correctAnswersCount = 0;
  let showScore = false;
  let noOptionSelected = false;

  onMount(async () => {
    try {
      const response = await fetch("/data.json");
      const data = await response.json();

      // Assuming the structure of your data.json is { quizzes: [...] }
      const availableQuizzes = data.quizzes;

      quiz = availableQuizzes.find(
        (q) => q.title.toLowerCase() === quizName.toLowerCase(),
      );
    } catch (error) {
      console.error("Error fetching quiz data:", error);
    }
  });

  const handleOptionSelect = (option) => {
    if (!isSubmitted) {
      selectedOption = option;
    }
  };

  const handleSubmit = () => {
    if (selectedOption !== null) {
      const currentQuestionData = quiz.questions[currentQuestion];
      if (selectedOption === currentQuestionData.answer) {
        correctAnswersCount++;
      }
      isSubmitted = true;
      noOptionSelected = false;
    } else {
      noOptionSelected = true;
      return;
    }
  };

  const handleNextQuestion = () => {
    currentQuestion++;
    selectedOption = null;
    isSubmitted = false;

    if (currentQuestion === quiz.questions.length) {
      showScore = true;
    }
  };

  const handleFinish = () => {
    showScore = true;
    window.scrollTo({ top: 0, behavior: "smooth" });
  };

  const getLetterLabel = (option) => {
    const letters = ["A", "B", "C", "D"];
    const index = quiz.questions[currentQuestion].options.indexOf(option);
    return letters[index];
  };

  $: isLastQuestion = quiz && currentQuestion === quiz.questions.length - 1;
</script>

<Header {quiz} />
{#if quiz && !showScore}
  <section class="quiz">
    <div class="quiz__container">
      <div class="quiz__question">
        <p class="quiz__question-number">
          Question {currentQuestion + 1} of {quiz.questions.length}
        </p>
        <h2 class="quiz__question-text">
          {quiz.questions[currentQuestion].question}
        </h2>
      </div>
      <div class="quiz__question-progress-bar">
        <div
          class="quiz__question-progress"
          style={`width: ${
            ((currentQuestion + 1) / quiz.questions.length) * 100
          }%`}
        ></div>
      </div>
    </div>
    <div class="quiz__options">
      {#each quiz.questions[currentQuestion].options as option (option)}
        <label
          class:correct={isSubmitted &&
            option === quiz.questions[currentQuestion].answer &&
            option === selectedOption}
          class:incorrect={isSubmitted &&
            option !== quiz.questions[currentQuestion].answer &&
            option === selectedOption}
          class:selected={!isSubmitted && selectedOption === option}
          class="quiz__option"
        >
          <input
            type="radio"
            bind:group={selectedOption}
            value={option}
            on:change={() => handleOptionSelect(option)}
            disabled={isSubmitted}
          />
          <span>
            {getLetterLabel(option)}
          </span>
          {option}
          <div class="quiz__option-icon">
            <img
              class="quiz__option-icon-correct"
              src={iconCorrect}
              alt="correct Icon"
            />
            <img
              class="quiz__option-icon-incorrect"
              src={iconIncorrect}
              alt="incorrect Icon"
            />
          </div></label
        >
      {/each}
      <div class="quiz__options-button">
        {#if !isSubmitted}
          <button on:click={handleSubmit}>Submit Answer</button>
        {:else if currentQuestion < quiz.questions.length}
          <button on:click={isLastQuestion ? handleFinish : handleNextQuestion}>
            {isLastQuestion && isSubmitted ? "Finish" : "Next Question"}
          </button>
        {/if}
        {#if noOptionSelected}
          <div class="quiz__error">
            <div class="quiz__error-icon">
              <img src={iconIncorrect} alt="error icon" />
            </div>
            Please select an answer
          </div>
        {/if}
      </div>
    </div>
  </section>
{:else if showScore}
  <section class="score">
    <h2>Quiz completed <span>You scored...</span></h2>
    <div class="score__container">
      <div class="score__card">
        <div class="score__card-subject">
          <div
            class="score__card-subject-icon"
            style="background: {quiz.color};"
          >
            <img src={quiz.icon} alt="{quiz.title} icon" />
          </div>
          <h1 class="score__card-subject-title">
            {quiz.title}
          </h1>
        </div>
        <div class="score__card-count">{correctAnswersCount}</div>
        <span class="score__card-length">out of {quiz.questions.length}</span>
      </div>
      <a class="score__button" href="/" use:link> Play Again </a>
    </div>
  </section>
{:else}
  <!-- loading state -->
{/if}

<style lang="scss">
  .quiz {
    width: 100%;
    padding: 32px 24px 212px;
    display: flex;
    flex-direction: column;
    gap: 40px;

    &__container {
      width: 100%;
      display: flex;
      flex-direction: column;
      gap: 24px;
    }

    &__question {
      width: 100%;
      display: flex;
      flex-direction: column;
      gap: 12px;

      &-number {
        color: var(--grey-navy, #626c7f);
        font-size: 14px;
        font-style: italic;
        font-weight: 400;
        line-height: 150%; /* 21px */
      }

      &-text {
        color: var(--dark-navy, #313e51);
        font-size: 20px;
        font-style: normal;
        font-weight: 500;
        line-height: 120%; /* 24px */
      }

      &-progress-bar {
        width: 100%;
        background-color: var(--pure-white);
        border-radius: 9999px;
        height: 16px;
        padding: 4px;
      }

      &-progress {
        height: 100%;
        background-color: var(--purple);
        transition: width 0.3s ease;
        height: 8px;
        border-radius: 9999px;
      }
    }

    &__options {
      display: flex;
      flex-direction: column;
      width: 100%;
      gap: 12px;

      &-button {
        width: 100%;
        display: flex;
        flex-direction: column;
        gap: 12px;

        button {
          width: 100%;
          padding: 19px 12px;
          border-radius: 12px;
          background: var(--purple);
          border: none;
          color: var(--pure-White, #fff);
          font-size: 18px;
          font-style: normal;
          font-weight: 500;
          line-height: 100%; /* 18px */
          cursor: pointer;

          &:hover,
          &:active {
            background: linear-gradient(
                0deg,
                rgba(255, 255, 255, 0.5) 0%,
                rgba(255, 255, 255, 0.5) 100%
              ),
              var(--purple, #a729f5);
            box-shadow: 0px 16px 40px 0px rgba(143, 160, 193, 0.14);
          }
        }
      }
    }

    &__option {
      padding: 12px;
      background: var(--pure-white);
      border-radius: 12px;
      color: var(--dark-navy, #313e51);
      font-size: 18px;
      font-style: normal;
      font-weight: 500;
      line-height: 100%;
      display: flex;
      align-items: center;
      gap: 16px;
      cursor: pointer;
      transition-property: color, background-color, border-color,
        text-decoration-color, fill, stroke;
      transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
      transition-duration: 150ms;
      border: 3px solid var(--pure-white);

      input {
        appearance: none;
        -webkit-appearance: none;
        -moz-appearance: none;
        display: none;
      }

      span {
        color: var(--grey-navy, #626c7f);
        font-size: 18px;
        font-style: normal;
        font-weight: 500;
        line-height: 100%; /* 18px */
        min-width: 40px;
        height: 40px;
        display: flex;
        align-items: center;
        justify-content: center;
        background: var(--light-grey);
        border-radius: 6px;
      }

      &-icon {
        min-width: 32px;
        height: 32px;
        position: relative;
        margin-left: auto;

        img {
          width: 100%;
          height: 100%;
          object-fit: cover;
          position: absolute;
          top: 0px;
          left: 0px;
        }

        &-correct,
        &-incorrect {
          opacity: 0;
          transition-property: opacity;
          transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
          transition-duration: 150ms;
        }
      }

      &.selected {
        border: 3px solid var(--purple);

        span {
          color: var(--pure-white);
          background: var(--purple);
        }
      }

      &.correct {
        border: 3px solid var(--green);

        span {
          color: var(--pure-white);
          background: var(--green);
        }

        .quiz__option-icon {
          &-correct {
            opacity: 1;
          }
        }
      }

      &.incorrect {
        border: 3px solid var(--red);

        .quiz__option-icon {
          &-incorrect {
            opacity: 1;
          }
        }

        span {
          color: var(--pure-white);
          background: var(--red);
        }
      }
    }

    &__error {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 100%;
      gap: 8px;
      color: var(--red, #ee5454);
      font-size: 18px;
      font-style: normal;
      font-weight: 400;
      line-height: 100%; /* 18px */

      &-icon {
        width: 32px;
        height: 32px;

        img {
          width: 100%;
          height: 100%;
          object-fit: cover;
        }
      }
    }
  }

  .score {
    width: 100%;
    display: flex;
    flex-direction: column;
    gap: 40px;
    padding: 32px 24px 270px;

    h2 {
      color: var(--dark-navy, #313e51);
      font-size: 40px;
      font-style: normal;
      font-weight: 300;
      line-height: 100%; /* 40px */
      display: flex;
      flex-direction: column;
      gap: 8px;

      span {
        font-weight: 500;
      }
    }

    &__container {
      width: 100%;
      display: flex;
      flex-direction: column;
      gap: 12px;
    }
    &__card {
      width: 100%;
      background: var(--pure-white);
      padding: 32px;
      border-radius: 12px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 16px;

      &-subject {
        display: flex;
        align-items: center;
        gap: 16px;

        &-icon {
          padding: 6px;
          border-radius: 6px;
          width: 40px;
          height: 40px;

          img {
            width: 100%;
            height: 100%;
            object-fit: cover;
          }
        }

        &-title {
          color: var(--dark-navy);
          font-size: 18px;
          font-style: normal;
          font-weight: 500;
          line-height: 100%; /* 18px */
        }
      }

      &-count {
        color: var(--dark-navy, #313e51);
        font-size: 88px;
        font-style: normal;
        font-weight: 500;
        line-height: 100%; /* 88px */
      }

      &-length {
        color: var(--grey-navy, #626c7f);
        font-size: 18px;
        font-style: normal;
        font-weight: 400;
        line-height: 100%; /* 18px */
      }
    }
    &__button {
      width: 100%;
      padding: 19px 12px;
      border-radius: 12px;
      background: var(--purple);
      border: none;
      color: var(--pure-White, #fff);
      font-size: 18px;
      font-style: normal;
      font-weight: 500;
      line-height: 100%; /* 18px */
      cursor: pointer;
      text-align: center;

      &:hover,
      &:active {
        background: linear-gradient(
            0deg,
            rgba(255, 255, 255, 0.5) 0%,
            rgba(255, 255, 255, 0.5) 100%
          ),
          var(--purple, #a729f5);
        box-shadow: 0px 16px 40px 0px rgba(143, 160, 193, 0.14);
      }
    }
  }
</style>
