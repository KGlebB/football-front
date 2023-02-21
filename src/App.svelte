<script>
	import { onMount } from 'svelte';

  let state = { teams: [], matches: [], homeTeam: '', awayTeam: '', homeTeamScore: 0, awayTeamScore: 0 };

  onMount(async () => {
    const teamsResponse = await fetch('http://127.0.0.1:3000/teams');
    const matchesResponse = await fetch('http://127.0.0.1:3000/matches');

    const teams = await teamsResponse.json();
    const matches = await matchesResponse.json();

    state = { ...state, teams, matches };
  });

  const addTeam = async () => {
    const response = await fetch('http://127.0.0.1:3000/teams', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ name: state.teamName }),
    });

    const team = await response.json();

    state = { ...state, teams: [...state.teams, team], teamName: '' };
  };

  const addMatch = async () => {
    const response = await fetch('http://127.0.0.1:3000/matches', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        homeTeam: state.homeTeam,
        awayTeam: state.awayTeam,
        homeTeamScore: state.homeTeamScore,
        awayTeamScore: state.awayTeamScore,
      }),
    });

    const match = await response.json();

    state = {
      ...state,
      matches: [...state.matches, match],
      homeTeam: null,
      awayTeam: null,
      homeTeamScore: 0,
      awayTeamScore: 0,
    };
  };
</script>

<div class="container">

	<div class="create-team">
		<h1>Регистрация команды</h1>
		<form on:submit|preventDefault={addTeam}>
			<label for="team-name">Название команды:</label>
			<input type="text" id="team-name" bind:value={state.teamName} required />
			{#if state.teamName}
				<button type="submit">Создать</button>
			{:else}
				<button class="disabled" type="submit" disabled>Создать</button>
			{/if}
		</form>
	</div>

	<div class="create-match">
		<h1>Регистрация матча</h1>
		<form on:submit|preventDefault={addMatch}>
			<label for="home-team">Домашняя команда:</label>
			<select id="home-team" bind:value={state.homeTeam} required>
				{#each state.teams as team}
					<option value={team}>{team.name}</option>
				{/each}
			</select>
			<label for="away-team">Гостевая команда:</label>
			<select id="away-team" bind:value={state.awayTeam} required>
				{#each state.teams as team}
					<option value={team}>{team.name}</option>
				{/each}
			</select>
			<label for="home-score">Счёт домашней команды:</label>
			<input type="number" id="home-score" bind:value={state.homeTeamScore} required />
			<label for="away-score">Счёт гостевой команды:</label>
			<input type="number" id="away-score" bind:value={state.awayTeamScore} required />
			{#if state.homeTeam !== state.awayTeam
					 && state.homeTeam && state.awayTeam}
				<button type="submit">Создать</button>
			{:else}
			<button class="disabled" type="submit" disabled>Создать</button>
			{/if}
		</form>
	</div>

	<div class="match-list">
		<h1>Список матчей</h1>
		<table>
			<thead>
				<tr>
					<th>Домашняя команда</th>
					<th>Гостевая команда</th>
					<th>Счёт</th>
				</tr>
			</thead>
			<tbody>
				{#each state.matches as match}
					<tr>
						<td>{match.homeTeam.name}</td>
						<td>{match.awayTeam.name}</td>
						<td>{match.homeTeamScore} : {match.awayTeamScore}</td>
					</tr>
				{/each}
			</tbody>
		</table>
	</div>
</div>

<style>
  body {
    margin: 0;
    padding: 0;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }

	form {
		display: flex;
    flex-direction: column;
	}

	label {
    font-size: 16px;
    font-weight: bold;
    margin-bottom: 8px;
  }

  input,
  select {
    border: 1px solid #ccc;
    border-radius: 4px;
    background-color: #fff;
    padding: 8px 16px;
    font-size: 16px;
    width: 100%;
    margin-bottom: 16px;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
    transition: box-shadow 0.2s ease-in-out;
  }

  input:focus,
  select:focus {
    outline: none;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
  }

  button {
    border: none;
    border-radius: 2px;
    background-color: #4285f4;
    color: #fff;
    padding: 12px 24px;
    font-size: 16px;
		font-weight: bold;
    cursor: pointer;
    transition: background-color 0.2s ease-in-out;
  }

  button:hover {
    background-color: #2d64ca;
  }

	button.disabled {
		background-color: #b8b8b8;
		cursor: not-allowed;
	}

	button.disabled:hover {
		background-color: #969696;
	}

  table {
    border-collapse: collapse;
    width: 100%;
    margin-top: 24px;
  }

  th,
  td {
    font-size: 16px;
    padding: 8px;
    text-align: left;
    border-bottom: 1px solid #ddd;
  }

  th {
    font-size: 16px;
    font-weight: bold;
    background-color: #f2f2f2;
  }

  tr:hover {
    background-color: #f5f5f5;
  }

  .container {
    max-width: 800px;
    margin: 0 auto;
    padding: 24px;
  }

  h1 {
    font-size: 32px;
    font-weight: 400;
    margin-top: 0;
    margin-bottom: 16px;
  }

	.create-team {
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 16px;
    margin-bottom: 32px;
  }
  .create-match {
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 16px;
    margin-bottom: 32px;
  }
  .match-list {
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 16px;
  }
</style>