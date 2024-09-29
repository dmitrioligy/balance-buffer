<svelte:options runes={true} />

<script lang="ts">
	/** Components **/
	import { Input, Label, Button } from '$lib/components/ui';
	import * as Table from '$lib/components/ui/table';
	import * as Select from '$lib/components/ui/select';

	/** Types **/
	type Account = {
		startBalance: number;
		startDate: string;
		name: string;
	};
	type Transaction = {
		from: string;
		to: string;
		amount: number;
		date: string;
		name: string;
	};
	type AccountBalances = { [key: string]: number };

	/** Constants **/
	const TODAY = new Date().toISOString().split('T')[0];
	const DEFAULTS = {
		ACCOUNTS: [
			{
				name: 'Checking',
				startBalance: 100,
				startDate: TODAY
			},
			{
				name: 'Savings',
				startBalance: 500,
				startDate: TODAY
			}
		] as Account[],
		TRANSACTION: {
			from: '',
			to: 'Checking',
			amount: 200,
			date: TODAY,
			name: ''
		} as Transaction
	};

	/** Reactive **/
	let tsx: Transaction = $state({ ...DEFAULTS.TRANSACTION });
	let tsxs: Transaction[] = $state([]);
	let accounts: Account[] = $state(DEFAULTS.ACCOUNTS);

	/** Derived **/
	let accountBalancesHistory: AccountBalances[] = $derived(computeBalances(accounts, tsxs));

	/** Functions - Controller **/
	const addTransaction = () => {
		let { from, to, amount, date, name } = tsx;
		const fromAccount = accounts.find((acc) => acc.name === from);
		const toAccount = accounts.find((acc) => acc.name === to);

		amount = Number(amount);

		if (isNaN(amount) || amount === 0) {
			alert('Amount is required and must be a number');
			return;
		}
		if (!date) {
			alert('Date is required');
			return;
		}
		if (from === to) {
			alert('From and To accounts must be different');
			return;
		}
		if (!to && from) {
			alert('From account requires a "To" account');
			return;
		}
		if (toAccount && date < toAccount.startDate) {
			alert('Transaction date must be after the "To" account starting date');
			return;
		}
		if (fromAccount && date < fromAccount.startDate) {
			alert('Transaction date must be after the "From" account starting date');
			return;
		}
		if (fromAccount && toAccount && amount < 0) {
			alert('Amount must be positive if making a transfer');
			return;
		}

		tsxs.push({ from, to, amount, date, name } as Transaction);
		tsxs.sort((a, b) => a.date.localeCompare(b.date));

		tsx = { ...DEFAULTS.TRANSACTION };
	};

	/** Functions - Utility **/
	const formatDate = (dateString: string): string => {
		const [year, month, day] = dateString.split('-');
		return `${month}/${day}/${year}`;
	};

	function computeBalances(accounts: Account[], transactions: Transaction[]) {
		const balances: AccountBalances = {};

		for (let i = 0; i < accounts.length; i++) {
			const { name, startBalance } = accounts[i];
			balances[name] = startBalance;
		}

		const accountBalanceHistory: AccountBalances[] = [];

		for (let i = 0; i < transactions.length; i++) {
			const { from, to, amount } = transactions[i];

			if (from) balances[from] -= amount;
			if (to) balances[to] += amount;

			accountBalanceHistory.push({ ...balances });
		}

		return accountBalanceHistory;
	}
</script>

<div class="container">
	<div class="flex space-x-2 items-end">
		<div>
			<Label for="tsx-amount">From</Label>
			<Select.Root
				selected={{ label: tsx.from, value: tsx.from }}
				onSelectedChange={(selected) => (tsx.from = selected?.value ?? tsx.from)}
			>
				<Select.Trigger class="w-[8rem]">
					<Select.Value placeholder="Account" />
				</Select.Trigger>

				<Select.Content>
					{#each accounts as { name }}
						<Select.Item value={name}>{name}</Select.Item>
					{/each}
				</Select.Content>
			</Select.Root>
		</div>

		<div>
			<Label for="tsx-amount">To</Label>
			<Select.Root
				selected={{ label: tsx.to, value: tsx.to }}
				onSelectedChange={(selected) => (tsx.to = selected?.value ?? tsx.to)}
			>
				<Select.Trigger class="w-[8rem]">
					<Select.Value placeholder="Account" />
				</Select.Trigger>
				<Select.Content>
					{#each accounts as { name }}
						<Select.Item value={name}>{name}</Select.Item>
					{/each}
				</Select.Content>
			</Select.Root>
		</div>

		<div>
			<Label for="tsx-amount">Amount</Label>
			<Input id="tsx-amount" type="text" bind:value={tsx.amount} />
		</div>

		<div>
			<Label for="tsx-name">Name</Label>
			<Input id="tsx-name" type="text" bind:value={tsx.name} />
		</div>

		<div>
			<Label for="tsx-date">Date</Label>
			<Input id="tsx-date" type="date" bind:value={tsx.date} />
		</div>

		<div>
			<Button on:click={addTransaction}>Add Transaction</Button>
		</div>
	</div>

	<br />

	{#if tsxs.length > 0}
		<div>
			<Table.Root>
				<Table.Header>
					<Table.Row>
						<!-- Account balance columns -->
						{#each accounts as account}
							<Table.Cell>{account.name}</Table.Cell>
						{/each}
						<!-- Divider -->
						<Table.Cell class="divider"></Table.Cell>
						<!-- Transaction columns -->
						<Table.Cell>From</Table.Cell>
						<Table.Cell>To</Table.Cell>
						<Table.Cell>Amount</Table.Cell>
						<Table.Cell>Name</Table.Cell>
						<Table.Cell>Date</Table.Cell>
					</Table.Row>
				</Table.Header>

				<Table.Body>
					{#each tsxs as tx, i}
						<Table.Row>
							<!-- Account balances -->
							{#each accounts as { name }}
								<Table.Cell>
									{accountBalancesHistory[i][name]}
								</Table.Cell>
							{/each}
							<!-- Divider -->
							<Table.Cell class="divider"></Table.Cell>
							<!-- Transaction data -->
							<Table.Cell>{tx.from}</Table.Cell>
							<Table.Cell>{tx.to}</Table.Cell>
							<Table.Cell>{tx.amount}</Table.Cell>
							<Table.Cell>{tx.name}</Table.Cell>
							<Table.Cell>{formatDate(tx.date)}</Table.Cell>
						</Table.Row>
					{/each}
				</Table.Body>
			</Table.Root>
		</div>
	{/if}
</div>

<style>
	.divider {
		border-left: 2px solid #ccc;
	}
</style>
