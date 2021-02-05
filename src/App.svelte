<script lang="ts">
	import dayjs from "dayjs";
	let url: string = "theitteam.com.au";
	const api: string = "36IiXSdFfO2kZWA8llmI1GszoraBf7Nh";
	let headers: Headers = new Headers();
	let isLoading: boolean = false;
	headers.append("apikey", api);

	const requestOptions: any = {
		method: "GET",
		redirect: "follow",
		headers,
	};

	interface resultsT {
		domain_name: string;
		registrar: string;
		expiration_date: string;
	}

	interface APIresponseT {
		ok: boolean;
		status: number;
		result: resultsT | null;
	}

	let APIresponse: APIresponseT = {
		ok: false,
		result: null,
		status: 0,
	};

	async function runWHOIS() {
		isLoading = true;

		const response = await fetch(
			`https://api.promptapi.com/whois/query?domain=${url}`,
			requestOptions
		);

		isLoading = false;
		console.log(response);

		// if (!response.ok) {
		// 	const message = `An error has occured: ${response.status}`;
		// 	throw new Error(message);
		// }

		const { result } = await response.json();

		APIresponse = {
			ok: response.ok,
			status: response.status,
			result,
		};

		console.log(APIresponse);
	}

	function prettyDate(date: string) {
		return dayjs(date).format("DD/MM/YYYY");
	}
</script>

<main>
	<div class="container">
		<h1>my domain info</h1>
		<p>Enter your domain name below:</p>
		<input type="url" bind:value={url} />

		<button on:click={runWHOIS} disabled={!url.length}
			>Lookup My Domain</button
		>

		{#if isLoading}
			<div class="loading">
				<p>Reticulating splines...</p>
			</div>
		{:else if APIresponse.status === 200}
			<div class="results">
				<p>
					G'day, Your domain name {APIresponse.result.domain_name} is registered
					with
					<a
						class="pill"
						href={`https://www.google.com/search?q=${APIresponse.result.registrar}`}
						target="_blank">{APIresponse.result.registrar}</a
					>
				</p>
				<p>
					Your domain name will expire on {prettyDate(
						APIresponse.result.expiration_date
					)}
				</p>
			</div>
		{:else if APIresponse.status === 404}
			<div class="results has-smaller-font">
				<p>
					Uh oh, we didn't get any results for your domain name. This
					means that it has either expired, or is unregistered.
				</p>
				<p>
					Hint: Your emails & website will stop working if your domain
					name has expired!
				</p>
				<p>
					Register your domain name <a
						class="pill"
						href={`https://theitmanhosting.secureapi.com.au/`}
						target="_blank">by clicking here</a
					>
				</p>
			</div>
		{/if}
	</div>
</main>

<style>
</style>
