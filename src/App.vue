<script setup lang="ts">
import { vMaska } from "maska/vue";
import { ref, watch } from 'vue';

const cep = ref("");
const uf = ref("");
const cidade = ref("");
const rua = ref("");
const bairro = ref("");
const pdfContent = ref<HTMLElement | null>(null);

const imprimir = () => {
	window.print();
}


watch([cep], async ([novoCep], []) => {
	if (novoCep.length !== 9) {
		return;
	}

	novoCep = novoCep.replace("-", "");

	try {
		const resp = await fetch(`https://viacep.com.br/ws/${novoCep}/json/`);
		const data = await resp.json();

		if (data.erro) {
			alert('CEP não encontrado');
			return;
		}

		// Preenche campos
		uf.value = data.uf;
		cidade.value = data.localidade;
		rua.value = data.logradouro;
		bairro.value = data.bairro;
	} catch (error) {
		console.error('Erro na consulta CEP:', error);
	}
});
</script>

<template>
	<div class="w-screen h-screen flex flex-col items-center">
		<header class="flex items-center justify-center p-4">
			<div class="text-2xl">Formulário</div>
		</header>
		<main ref="pdfContent"
			class="flex flex-col justify-center w-5/6 lg:w-3/4 xl:w-3/5 text-sm bg-white border-2 border-gray-400 p-4 rounded-lg">

			<div class="flex flex-col justify-between md:flex-row md:gap-6">
				<div class="flex flex-col gap-2 pb-2 w-full">
					<div class="w-full md:w-38">Nome completo</div>
					<input type="text" class="border-2 border-gray-400 rounded-lg p-1 w-full" />
				</div>
				<div class="flex flex-col gap-2 py-2 md:pt-0">
					<div class="w-38">Data de nascimento</div>
					<input type="date" class="border-2 border-gray-400 rounded-lg p-1 w-36" />
				</div>
			</div>
			<div class="flex flex-col md:flex-row md:gap-6">
				<div class="flex flex-col gap-2 py-2">
					<div class="w-38">Telefone</div>
					<input type="text" v-maska="'(##) #####-####'"
						class="border-2 border-gray-400 rounded-lg p-1 w-38" />
				</div>

				<div class="flex flex-col gap-2 py-2">
					<div class="w-10">CPF</div>
					<input v-maska="'###.###.###-##'" type="text"
						class="border-2 border-gray-400 rounded-lg p-1 w-36" />
				</div>

				<div class="flex flex-col gap-2 py-2 w-full">
					<div class="w-14">E-mail</div>
					<input type="text" class="border-2 border-gray-400 rounded-lg p-1 w-full" />
				</div>
			</div>

			<div class="">
				<div class="py-2">Endereço completo</div>

				<div class="flex flex-col md:flex-row md:gap-6 w-full">

					<div class="flex flex-col gap-2 py-2 w-full max-w-28">
						<div class="w-10">CEP</div>
						<input type="text" v-maska="'#####-###'" v-model="cep"
							class="border-2 border-gray-400 rounded-lg p-1 w-full" />
					</div>

					<div class="flex flex-col gap-2 py-2 max-w-16">
						<div class="w-10">UF</div>
						<input type="text" v-model="uf" class="border-2 border-gray-400 rounded-lg p-1 w-full" />
					</div>

					<div class="flex flex-col gap-2 py-2 w-full">
						<div class="w-10">Cidade</div>
						<input type="text" v-model="cidade" class="border-2 border-gray-400 rounded-lg p-1 w-full" />
					</div>

					<div class="flex flex-col gap-2 py-2 max-w-40">
						<div class="w-10">Número</div>
						<input type="text" class="border-2 border-gray-400 rounded-lg p-1 w-full" />
					</div>
				</div>

				<div class="flex flex-col md:flex-row md:gap-6 w-full">
					<div class="flex flex-col gap-2 py-2 w-full">
						<div class="w-10">Rua</div>
						<input type="text" v-model="rua" class="border-2 border-gray-400 rounded-lg p-1 w-full" />
					</div>
					<div class="flex flex-col gap-2 py-2 w-full">
						<div class="w-10">Bairro</div>
						<input type="text" v-model="bairro" class="border-2 border-gray-400 rounded-lg p-1 w-full" />
					</div>
				</div>
			</div>
		</main>
		<footer class="flex flex-row items-center gap-8">
			<button class="mt-4 px-4 py-2 bg-black text-white rounded-lg no-print flex items-center gap-2"
				@click="imprimir">
				<svg width="16" height="16" viewBox="0 0 16 16" xmlns="http://www.w3.org/2000/svg" fill="white">
					<path fill-rule="evenodd" clip-rule="evenodd"
						d="M13.85 4.44l-3.28-3.3-.35-.14H2.5l-.5.5V7h1V2h6v3.5l.5.5H13v1h1V4.8l-.15-.36zM10 5V2l3 3h-3zM2.5 8l-.5.5v6l.5.5h11l.5-.5v-6l-.5-.5h-11zM13 13v1H3V9h10v4zm-8-1h-.32v1H4v-3h1.06c.75 0 1.13.36 1.13 1a.94.94 0 0 1-.32.72A1.33 1.33 0 0 1 5 12zm-.06-1.45h-.26v.93h.26c.36 0 .54-.16.54-.47 0-.31-.18-.46-.54-.46zM9 12.58a1.48 1.48 0 0 0 .44-1.12c0-1-.53-1.46-1.6-1.46H6.78v3h1.06A1.6 1.6 0 0 0 9 12.58zm-1.55-.13v-1.9h.33a.94.94 0 0 1 .7.25.91.91 0 0 1 .25.67 1 1 0 0 1-.25.72.94.94 0 0 1-.69.26h-.34zm4.45-.61h-.97V13h-.68v-3h1.74v.55h-1.06v.74h.97v.55z" />
				</svg>
				Gerar PDF</button>

		</footer>
	</div>
</template>

<style scoped>
@media print {
	.no-print {
		display: none;
	}

	body {
		margin: 1cm;
	}
}
</style>
