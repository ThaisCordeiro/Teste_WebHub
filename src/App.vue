<template>
	<div>
		
		<!-- Selection options Box  -->
		<div class="header">
			
				<select @change="itemChange" v-model="order">
					<option value="" disabled selected>Selecione...</option>
					<option value="nome">Nome</option>
					<option value="sistema_operacional">Sistema Operacional</option>
					<option value="ano_lancamento">Ano de Lançamento</option>
				</select>
				
				<select v-if="order" @change="itemReverse">
					<option v-bind:value="false" :selected="selected">Crescente</option>
					<option v-bind:value="true">Decrescente</option>
				</select>
			
		</div>

		<!-- Items  -->
		<div class="container">
			<!-- Pagination Component -->
			<paginate name="items" :list="items" :per="8">
				<div class="row">		
					<div class="col-lg-3" 
						v-for="{ nome, ano_lancamento, sistema_operacional, image, id } in paginated('items')">
			
						<div class="card">
							<figure class="card-image"  
								v-bind:style="{ backgroundImage: 'url(' + image + ')' }">							
							</figure>
												
							<div class="card-body">
								<h5 class="card-title">{{ nome }}</h5>
			        	<p>
			        		<span>Sistema Operacional/Ano:</span>
			        		<br>	
			        		{{ ` ${ sistema_operacional } / ${ ano_lancamento }` }} 
			        	</p>					
							</div>
						</div>
						
					</div>
				</div>
			</paginate> 
			
			<!-- Links Pagination -->
			<div class="d-flex justify-content-center">			
				<paginate-links class="pagination" for="items" :show-step-links="true"></paginate-links>
			</div>
		</div>

	</div>
</template>

<script>

export default {
	name: 'app',
	data () {
		return {
			items: [],
			reversed: '',
			order: '',
			paginate: ['items'],
			selected: true
		}
	},

	// Data API
	created: function () {
		this.$http.get('http://apis.webhub.com.br/iconic-pcs/modelos')
		.then(response => {
			this.items = response.data;
		});
	},

	methods: {
		// Comparison Function for sorting Data
		compare: function(actual, next) {
			let result;

			if(typeof actual === 'string' && typeof next === 'string') {
				actual = actual.toLowerCase();
				next = next.toLowerCase();
				result = (actual < next) ? -1 : (actual > next) ? 1 : 0;
			}
			else if(typeof actual === 'number' && typeof next === 'number') {
				result = actual - next;
			}

			return result;
		},

		// Sorting Function
		itemSort: function() {

			this.items = this.items.sort((actual, next) => this.compare(actual[this.order], next[this.order]));
			
		},

		// Reverse Data
		itemReverse: function({ target }) {

			this.reversed = target.value;
			this.selected = !target.value;

			this.items = this.reversed ? this.items.reverse() : this.itemSort();

		},

		// Wrap for Change and Sorting
		itemChange: function() {

			this.itemSort();
			this.selected = true;
			this.reversed = false;
		}



	}
}

</script>

<style lang="scss">
@import '../node_modules/bootstrap/scss/bootstrap.scss';


$principalColor: #c7565a;


.header {
	background-color: $principalColor;
	padding: 15px;
	margin-bottom: 30px;
}

.card {

	margin-bottom: 15px;

	.card-body {
		padding: 10px;
		min-height: 135px;

		h5 {
			font-size: 16px;
		}

		p {
			color: #767676;
			span {
				color: #000;				
			}
		}
	}

	.card-image {
		height: 250px;
		background-repeat: no-repeat;
		background-size: contain;
		background-position: center;
		border-bottom: 2px solid #eee;
		max-width: 500px;
	}
}

.pagination {

	.number, .left-arrow, .right-arrow {
		a {
			position: relative;
			display: block;
			padding: .5rem .75rem;
			margin-left: -1px;
			line-height: 1.25;
			color: $principalColor;
			background-color: #fff;
			border: 1px solid #dee2e6;
		}	

	}

	.number.active a {
		z-index: 1;
		color: #fff;
		background-color: $principalColor;
		border-color: $principalColor;
	}
} 
</style>
