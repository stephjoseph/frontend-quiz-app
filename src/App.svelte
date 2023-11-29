<script>
  // @ts-nocheck

  import { Router, Route } from "svelte-routing";
  import Home from "./routes/Home.svelte";
  import Quiz from "./routes/Quiz.svelte";
</script>

<Router>
  <Route path="/" component={Home} />
  <Route path="/:quizName" let:params>
    <Quiz quizName={params.quizName} />
  </Route>
</Router>

<style></style>
