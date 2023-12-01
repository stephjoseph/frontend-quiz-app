<script>
  // @ts-ignore
  import { Link } from "svelte-routing";
  import Header from "../components/Header.svelte";
</script>

<Header />
<main>
  <h1>Choose a Quiz</h1>
  <ul>
    <li><Link to="/html">HTML Quiz</Link></li>
    <li><Link to="/css">CSS Quiz</Link></li>
    <li><Link to="/javascript">JavaScript Quiz</Link></li>
    <li><Link to="/accessibility">Accessibility Quiz</Link></li>
  </ul>
</main>

<style>
  /* Add your styling here */
</style>
