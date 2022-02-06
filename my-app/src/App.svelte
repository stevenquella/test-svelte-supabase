<script lang="ts">
  import { createClient } from "@supabase/supabase-js";
  const supabase_url = import.meta.env.VITE_SUPABASE_URL as string;
  const supabase_anon_key = import.meta.env.VITE_SUPABASE_ANON_KEY as string;

  let user: string;
  let email: string;
  let password: string;

  const client = createClient(supabase_url, supabase_anon_key);
  client.auth.onAuthStateChange(function (event, session) {
    console.log(event, session);
    user = session?.user?.id;
  });

  user = client.auth.user()?.id;

  async function onSignUp() {
    const response = await client.auth.signUp({
      email,
      password,
    });

    if (response.error) {
      console.error(response.error);
    }
  }

  async function onSignIn() {
    const response = await client.auth.signIn({
      email,
      password,
    });

    if (response.error) {
      console.error(response.error);
    }
  }

  async function onSignOut() {
    await client.auth.signOut();
  }

  async function retrieveMeals() {
    return client.from("meals").select("*");
  }
</script>

<main>
  <p>{user}</p>

  <form on:submit|preventDefault="{onSignUp}">
    <input name="email" placeholder="email" type="email" bind:value="{email}" />
    <input
      name="password"
      placeholder="password"
      type="password"
      bind:value="{password}"
    />
    <button type="submit"> SIGN UP </button>
  </form>

  <form on:submit|preventDefault="{onSignIn}">
    <input name="email" placeholder="email" type="email" bind:value="{email}" />
    <input
      name="password"
      placeholder="password"
      type="password"
      bind:value="{password}"
    />
    <button type="submit"> SIGN IN </button>
  </form>

  <button type="input" on:click="{onSignOut}">SIGN OUT</button>

  <div>
    {#if user}
      {#await retrieveMeals()}
        <p>Loading meals...</p>
      {:then result}
        <p>{JSON.stringify(result)}</p>
      {/await}
    {/if}
  </div>
</main>
