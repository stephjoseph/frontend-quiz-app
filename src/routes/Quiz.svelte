<script>
  // @ts-nocheck

  import { onMount } from "svelte";
  import { writable } from "svelte/store";
  import Header from "../components/Header.svelte";

  export let quizName;
  let quiz = null;

  // let currentQuestion = 0;
  // let score = 0;
  // let selectedOption = null;

  // const quizzes = writable([]);

  // let currentQuiz;

  // onMount(async () => {
  //   const response = await fetch(`/data.json`);
  //   const data = await response.json();
  //   const selectedQuiz = data.quizzes.find(
  //     (quiz) => quiz.title.toLowerCase() === quizName,
  //   );
  //   quizzes.set(selectedQuiz);
  //   currentQuiz = quizzes[currentQuestion];
  // });

  // const selectOption = (option) => {
  //   selectedOption = option;
  // };

  // const submitAnswer = () => {
  //   if (selectedOption === currentQuiz.questions[currentQuestion].answer) {
  //     score += 1;
  //   }

  //   if (currentQuestion < currentQuiz.questions.length - 1) {
  //     currentQuestion += 1;
  //     selectedOption = null;
  //   } else {
  //     alert(
  //       `Quiz completed! Your score: ${score}/${currentQuiz.questions.length}`,
  //     );
  //   }
  // };

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
</script>

{#if quiz}
  <Header {quiz} />
  <section>
    <h2>{quiz.title}</h2>
    {#each quiz.questions as question (question.question)}
      <div>{question.question}</div>
      <!-- Add options rendering here -->
    {/each}
  </section>
{:else}
  <Header />
  <p>Quiz not found!</p>
{/if}
