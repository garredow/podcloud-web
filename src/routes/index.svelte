<script lang="ts">
	import type { Auth0Client } from '@auth0/auth0-spa-js';
	import createAuth0Client from '@auth0/auth0-spa-js';
	import { onMount } from 'svelte';
	import { isAuthenticated, user } from '../store';

	let client: Auth0Client;

	onMount(async () => {
		client = await createAuth0Client({
			domain: 'podcloud-dev.us.auth0.com',
			client_id: 'SlTeN48AGrnMQL2F2iQN34Qi3zRuacQs',
			redirect_uri: window.location.origin,
			scope: 'read:current_user update:current_user_metadata offline_access openid',
			audience: 'https://api.podcloud.social'
		});

		isAuthenticated.set(await client.isAuthenticated());
		user.set(await client.getUser<any>());
	});

	async function login() {
		await client.loginWithPopup();
		user.set(await client.getUser<any>());
		isAuthenticated.set(true);
	}

	function logout() {
		client.logout();
	}

	$: {
		console.log('user', $user);
		if (client && $user) {
			client.getTokenSilently().then((res) => console.log('token', res));
		}
	}
</script>

<svelte:head>
	<title>PodCloud</title>
</svelte:head>

<a class="btn" href="/#" on:click={login}>Log In</a>
<a class="btn" href="/#" on:click={logout}>Log Out</a>
