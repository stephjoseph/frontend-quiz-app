<script>
  import { onMount } from "svelte";
  import { writable } from "svelte/store";

  export let quizName;

  let currentQuestion = 0;
  let score = 0;
  let selectedOption = null;

  const quizzes = writable([]);

  let currentQuiz;

  onMount(async () => {
    const response = await fetch(`/data.json`);
    const data = await response.json();
    const selectedQuiz = data.quizzes.find(
      (quiz) => quiz.title.toLowerCase() === quizName,
    );
    quizzes.set(selectedQuiz);
    currentQuiz = quizzes[currentQuestion];
  });

  const selectOption = (option) => {
    selectedOption = option;
  };

  const submitAnswer = () => {
    if (selectedOption === currentQuiz.questions[currentQuestion].answer) {
      score += 1;
    }

    if (currentQuestion < currentQuiz.questions.length - 1) {
      currentQuestion += 1;
      selectedOption = null;
    } else {
      alert(
        `Quiz completed! Your score: ${score}/${currentQuiz.questions.length}`,
      );
    }
  };
</script>
