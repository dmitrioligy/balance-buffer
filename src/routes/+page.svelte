<svelte:options runes={true} />

<!---------------------------------------------------- JavaScript ----------------------------------------------------->
<script lang="ts">
	/** Svelte **/
	/** Components **/
	import { Input, Label, Button } from '$lib/components/ui';
	import * as Table from '$lib/components/ui/table';

	/** Lib **/

	/** Types **/
	type Account = {
		startingBalance: number;
		startingDate: Date;
		transactions: Transaction[];
		name: string;
	};
	type Transaction = {
		id: string;
		amount: number;
		date: Date;
		name: string;
		repeat: {
			frequency: 'daily' | 'weekly' | 'bi-weekly' | 'monthly' | 'yearly' | 'quarterly';
			endDate: Date;
		};
	};

	/** Exports **/
	/** Stores **/

	/** Constants **/
	const DEFAULTS = {
		ACCOUNT: {
			startingBalance: 0,
			startingDate: new Date(),
			transactions: [],
			name: 'Checking'
		} as Account,
		TRANSACTION: {
			id: '',
			amount: 0,
			date: new Date(),
			name: '',
			repeat: {
				frequency: 'monthly',
				endDate: new Date()
			}
		} as Transaction
	};

	/** Declares **/

	/** Reactive **/
	let tsx: Transaction = $state(DEFAULTS.TRANSACTION);
	let accounts: { [key: string]: Account } = $state({
		Checking: DEFAULTS.ACCOUNT
	});

	/** Init **/

	/** Functions - Controller **/
	const addAccount = () => {
		// todo;
	};

	const addTransaction = () => {
		accounts['Checking'].transactions.push({ ...tsx });
		tsx = DEFAULTS.TRANSACTION;
		console.log(accounts);
		accounts['Checking'].transactions.sort((a, b) => a.date.getTime() - b.date.getTime());
	};

	/** Functions - API **/
	/** Functions - UX **/
	/** Functions - Utility **/
</script>

<!------------------------------------------------------- HTML -------------------------------------------------------->
<div class="container">
	<!-- <button onclick={() => {}} aria-label="Add Account"> Add Account </button> -->

	<div class="flex items-end space-x-2">
		<!-- <div>
			<Label for="tsx-name">Account Name</Label>
			<Input id="tsx-name" type="text" bind:value={accounts['Checking'].name} />
		</div> -->

		<div>
			<Label for="tsx-amount">Amount</Label>
			<Input id="tsx-amount" type="text" bind:value={tsx.amount} />
		</div>

		<div>
			<Label for="tsx-name">name</Label>
			<Input id="tsx-name" type="text" bind:value={tsx.name} />
		</div>

		<div>
			<Label for="tsx-date">Date</Label>
			<Input id="tsx-date" type="text" bind:value={tsx.date} />
		</div>

		<div>
			<Label for="tsx-frequency">Frequency</Label>
			<Input id="tsx-frequency" type="text" bind:value={tsx.repeat.frequency} />
		</div>

		<div>
			<Label for="tsx-endDate">End Date</Label>
			<Input id="tsx-endDate" type="text" bind:value={tsx.repeat.endDate} />
		</div>

		<div>
			<Button on:click={addTransaction}>Add Transaction</Button>
		</div>
	</div>

	{#if accounts['Checking'].transactions.length > 0}
		<div>
			<Table.Root>
				<Table.Header>
					<Table.Row>
						<Table.Cell>Amount</Table.Cell>
						<Table.Cell>Name</Table.Cell>
						<Table.Cell>Frequency</Table.Cell>
						<Table.Cell>End Date</Table.Cell>
					</Table.Row>
				</Table.Header>
				<Table.Body>
					{#each accounts['Checking'].transactions as { amount, name, date, repeat: { ...repeat } }, i}
						<Table.Row>
							<Table.Cell>{amount}</Table.Cell>
							<Table.Cell>{name}</Table.Cell>
							<Table.Cell>{repeat.frequency}</Table.Cell>
							<Table.Cell>{repeat.endDate}</Table.Cell>
						</Table.Row>
					{/each}
				</Table.Body>
			</Table.Root>
		</div>
	{/if}
</div>

<!------------------------------------------------'-'------ CSS --------------------------------------------------------->
<style>
</style>
