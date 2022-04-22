<template>
	<div class="converter-container">

		<div class="heading">Currency Converter</div>

		<form action="" class="enter-amount">
			<div class="enter-amount__message">Enter amount</div>
			<input type="text" v-model="insertedAmount" class="enter-amount__field">
		</form>	

		<div class="currency-fields">
			<div>
				<p>From</p>
				<div class="currency-fields__select-box">
					<img src="images/euro.jpg" alt="eu-flag" class="flag-image">
					<select name="" id="">
						<option value="EUR">EUR</option>
					</select>
				</div>
			</div>

			<button><img src="images/arrow.png" alt="arrow-image" class="arrow-image"></button>
			
			<div>
				<p>To</p>
				<div class="currency-fields__select-box">
					<img :src="chosenCurrencyFlag" alt="flag" class="flag-image" v-if="chosenCurrency">
					<select name="" id="" v-model="chosenCurrency" @change="updateCurrency(chosenCurrency)">
						<option v-for="(currency, index) in currencies" :value="currency + ', ' + index">{{ index }}</option>
					</select>
				</div>
			</div>
		</div>

		<div v-if="!error" class="result-sum-message">{{ result }}</div>
		<div v-if="error">{{ error }}</div>
		
		<button class="convert-button" @click="convertCurrency">Convert</button>
	</div>
</template>

<script>
export default {
	data() {
		return {
			insertedAmount: '',
			currencies: [],
			chosenCurrency: '',
			chosenCurrencyFlag: '',
			result: '',
			currentRate: '',
			currentSymbol: '',
			error: ''
		}
	},

	async created() {
		this.fetchRates();
	},
	
	methods: {
		// Fetching all currency rates as of today using try and catch method to handle possible errors
		async fetchRates() {
			const url = 'https://cdn.jsdelivr.net/gh/fawazahmed0/currency-api@1/latest/currencies/eur.json';
			const response = await fetch(url);
			try {
				await this.handleResponse(response);
			} catch (error) {
				console.log(error);
				this.error = error.message;
			}
		},

		async handleResponse(response) {
			if (response.status >= 200 && response.status < 300) {
				const { eur } = await response.json();
				this.currencies = eur;
				console.log(this.currencies);
				return true;
			} else {
				if (response.status === 404) {
					throw new Error('Url not found');
				}
				if (response.status === 401) {
					throw new Error('Unauthorized');
				}
				if (response.status > 500) {
					throw new Error('Server error');
				}
				throw new Error('Something went wrong');
			}		
		},

		async updateCurrency(chosenCurrency) {
			// Use split method to split the rate and the symbol of the chosen currency
			const current = this.chosenCurrency.split(',');
			this.currentRate= current[0];
			this.currentSymbol = current[1];
				
			//Using the current symbol we fetch the image of the needed country
			const urlFlag = `https://restcountries.com/v3.1/currency/${this.currentSymbol}`;
			//Using replace method here to remove space in the api address
			const urlFlagCorrect = urlFlag.replace(/\s+/g, '');
			const response = await fetch(urlFlagCorrect);
			const resultFlag =  await response.json();
			this.chosenCurrencyFlag = resultFlag[0].flags.png;
		},

		//Function to convert the inserted amount in euro into the chosen currency
		convertCurrency() {
			const resultAmount = this.insertedAmount * this.currentRate;
			this.result = `${this.insertedAmount} EUR = ${resultAmount.toFixed(2)} ${this.currentSymbol.toUpperCase()}`
		}		
	}
}
</script>

<style>
	/* Mobile */
	.converter-container {
		width: 50vw;
		height: 80vh;
		margin-left: auto;
		margin-right: auto;
		margin-top: 5%;
		display: flex;
		flex-flow: column nowrap;
		justify-content: space-around;
		align-items: center;
		gap: 10px;
		background-color: #E3E0F0;
		border-radius: 10px;
	}

	.heading {
		width: 65%;
		height: 60px;
		background-color: #6C5AB2;
		border-radius: 20px;
		color: white;
		font-size: var(--mobile-body-style);
		text-align: center;
	}

	.enter-amount {
		width: 65%;
	}

	.enter-amount__message {
		font-size: var(--mobile-body-style);
	}

	.enter-amount__field {
		width: 100%;
		height: 50px;
		background-color: white;
		border-radius: 10px;
		padding-left: 10px;
		font-size: 0.7em;
	}

	.enter-amount__field:active {
		border: 1px solid white;
	}

	.currency-fields {
		width: 65%;
		display: flex;
		flex-flow: row wrap;
		justify-content: space-between;
		align-items: center;
	}

	p {
		font-size: var(--mobile-body-style)
	}

	.currency-fields__select-box {
		width: 150px;
		height: 50px;
		background-color: white;
		border-radius: 10px;
		display: flex;
		flex-flow: row nowrap;
		justify-content: space-around;
		align-items: center;
	}

	.flag-image {
		width: 40px;
		height: 27px;
	}

	option {
		text-transform: uppercase;
		border: none;
	}

	.arrow-image {
		width: 35px;
		height: 35px;
		margin-top: 1.2em;
	}

	.convert-button {
		width: 40%;
		height: 60px;
		background-color: #6C5AB2;
		border-radius: 20px;
		color: white;
		font-size: var(--mobile-body-style);
		text-align: center;
	}

	/* Desktop */

	@media screen and (min-width: 800px) {
		.heading {
			font-size: var(--body-style);
		}

		.enter-amount__message {
			font-size: var(--body-style);
		}

		.currency-fields {
			flex-flow: row nowrap;
		}

		p {
			font-size: var(--body-style)
		}

		.currency-fields__select-box {
			width: 150px;
		}

		.convert-button {
			font-size: var(--body-style);
		}
	}

	@media screen and (min-width: 1140px) {
		.heading {
			font-size: var(--header-style);
		}

		.currency-fields__select-box {
			width: 170px;
		}

		.convert-button {
			font-size: var(--header-style);
		}
	}

</style>

