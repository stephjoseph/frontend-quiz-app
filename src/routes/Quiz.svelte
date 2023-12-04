<script>
  // @ts-nocheck

  import { onMount } from "svelte";
  import { writable } from "svelte/store";
  import Header from "../components/Header.svelte";

  export let quizName;
  let quiz = null;
  let currentQuestion = 0;
  let selectedOption = null;
  let isSubmitted = false;
  let correctAnswersCount = 0;
  let showScore = false;

  onMount(async () => {
    try {
      const response = await fetch("/data.json");
      const data = await response.json();

      // Assuming the structure of your data.json is { quizzes: [...] }
      const availableQuizzes = data.quizzes;

      quiz = availableQuizzes.find(
        (q) => q.title.toLowerCase() === quizName.toLowerCase(),
      );

      console.log(quiz);
    } catch (error) {
      console.error("Error fetching quiz data:", error);
    }
  });

  function handleOptionSelect(option) {
    if (!isSubmitted) {
      selectedOption = option;
    }
  }

  function handleSubmit() {
    if (selectedOption !== null) {
      const currentQuestionData = quiz.questions[currentQuestion];
      if (selectedOption === currentQuestionData.correctAnswer) {
        correctAnswersCount++;
      }
      isSubmitted = true;
    }
  }

  function handleNextQuestion() {
    currentQuestion++;
    selectedOption = null;
    isSubmitted = false;

    if (currentQuestion === quiz.questions.length) {
      showScore = true;
    }
  }

  function handleFinish() {
    showScore = true;
  }
</script>

{#if quiz}
  <Header {quiz} />
  <section class="quiz">
    <div class="quiz__container">
      <div class="quiz__question">
        <p class="quiz__question-number">
          Question {currentQuestion + 1} of {quiz.questions.length}
        </p>
        <p class="quiz__question-text">
          {quiz.questions[currentQuestion].question}
        </p>
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
            option === quiz.questions[currentQuestion].correctAnswer}
          class:wrong={isSubmitted && option === selectedOption}
        >
          <input
            type="radio"
            bind:group={selectedOption}
            value={option}
            on:change={() => handleOptionSelect(option)}
            disabled={isSubmitted}
          />
          {option}
        </label>
      {/each}
    </div>
    {#if !isSubmitted}
      <button on:click={handleSubmit}>Submit</button>
    {:else if currentQuestion < quiz.questions.length}
      <button on:click={handleNextQuestion}>Next Question</button>
    {/if}
  </section>
  {#if showScore}
    <p>Correct Answers: {correctAnswersCount}</p>
  {/if}
  {#if currentQuestion === quiz.questions.length && !showScore}
    <button on:click={handleFinish}>Finish</button>
  {/if}
{:else}
  <Header />
  <p>Quiz not found!</p>
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
  }
</style>
