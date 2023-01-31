<script>
  import { API_URL } from '../../constantes';
  import {push} from 'svelte-spa-router';
  let username;
  let password;

  const handleLogin = async () => {
    const result = await fetch(API_URL + 'auth/login', {
      method: "POST",
      headers: {
        'Content-Type': 'application/json' // les types de donées que je t'envoi c'est du json
      },
      body: JSON.stringify({
        username,
        password
      })
    });

    const jsonResult = await result.json();

    if(jsonResult.token) {
      localStorage.setItem('token', jsonResult.token);
      push('/');
    } else {
      alert("Wrong username or password");
      password = '';
    }
  }
</script>

<div class="container">
  <div class="row">
    <div class="col-md-3"></div>
    <div class="col-md-6">
      <h1>oKanban</h1>
      <form on:submit|preventDefault={handleLogin}>
        <!-- Email input -->
        <div class="form-outline mb-4">
          <input bind:value={username} type="text" id="form2Example1" name="username" class="form-control" />
          <label class="form-label" for="form2Example1">Username</label>
        </div>
      
        <!-- Password input -->
        <div class="form-outline mb-4">
          <input bind:value={password} type="password" id="form2Example2" name="password" class="form-control" />
          <label class="form-label" for="form2Example2">Password</label>
        </div>
    
        <!-- Submit button -->
        <button type="submit" class="btn btn-primary btn-block mb-4">Sign in</button>
      
      </form>
    </div>
    <div class="col-md-3"></div>
  </div>
</div>
