<!---------------------------------------------------- JavaScript ----------------------------------------------------->
<script lang="ts">
	/** Svelte **/
	/** Components **/
	import { Input, Label } from '$lib/components/ui'

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
		description: string;
		repeat: {
			frequency: 'daily' | 'weekly' | 'bi-weekly' | 'monthly' | 'yearly' | 'quarterly';
			endDate: Date;
		};
	};

	type NewTransaction = {
		transaction: Transaction;
		accountName: string;
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
		},
		TRANSACTION: {
			id: null,
			amount: 0,
			date: new Date(),
			description: null,
			repeat: {
				frequency: 'monthly',
				endDate: null,
			}
		}
	};

	/** Declares **/

	/** Reactive **/
	let accounts: { [key: string]: Account } = {
		Checking: DEFAULTS.ACCOUNT
	};
	let tsx: Transaction = DEFAULTS.TRANSACTION;

	/** Init **/

	/** Functions - Controller **/
	const addAccount = () => {
		// todo;
	};

	const addTransaction = ({ transaction, accountName }: NewTransaction) => {
		accounts[accountName].transactions.push(transaction);
	};

	/** Functions - API **/
	/** Functions - UX **/
	/** Functions - Utility **/
</script>

<!------------------------------------------------------- HTML -------------------------------------------------------->
<div class="container">
	<!-- <button onclick={() => {}} aria-label="Add Account"> Add Account </button> -->
	<div>
		<button onclick={addTransaction}> Add Transaction </button>
	</div>
	<div class='flex space-x-2'>
		<div>
			<Label for='tsx-name'> Account Name </Label>
			<Input id='tsx-name' label="name" type="text" bind:value={tsx.name} />
		</div>

		<div>
			<Label for='tsx-amount'> Amount </Label>
			<Input id='tsx-amount' label="amount" type="text" bind:value={tsx.amount} />
		</div>

		<div>
			<Label for='tsx-description'> Description </Label>
			<Input id='tsx-description' label="description" type="text" bind:value={tsx.description} />
		</div>


		<div>
			<Label for='tsx-date'> Date </Label>
			<Input id='tsx-date' label="date" type="text" bind:value={tsx.date} />
		</div>

		<div>
			<Label for='tsx-frequency'> Frequency </Label>
			<Input id='tsx-frequency' label="frequency" type="text" bind:value={tsx.repeat.frequency} />
		</div>

		<div>
			<Label for='tsx-endDate'> End Date </Label>
			<Input id='tsx-endDate' label="end date" type="text" bind:value={tsx.repeat.endDate} />
		</div>
	</div>
</div>

<!------------------------------------------------'-'------ CSS --------------------------------------------------------->
<style>
</style>
