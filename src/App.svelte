<script>
  // @ts-nocheck
  import { fade } from "svelte/transition";
  import { Router, Route } from "svelte-routing";
  import Home from "./routes/Home.svelte";
  import Quiz from "./routes/Quiz.svelte";
</script>

<Router
  viewtransition={() => {
    return { fn: fade, duration: 500 };
  }}
>
  <Route path="/" component={Home} />
  <Route path="/:quizName" let:params>
    <Quiz quizName={params.quizName} />
  </Route>
</Router>

<style></style>
