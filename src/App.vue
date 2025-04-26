<script setup lang="ts">
import { vMaska } from "maska/vue"
import { ref, watch } from 'vue'

const cep = ref("");
const uf = ref("");
const cidade = ref("");
const rua = ref("");
const bairro = ref("");

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
		<main
			class="flex flex-col justify-center w-5/6 lg:w-3/4 xl:w-3/5 text-base bg-white border-2 border-gray-400 p-4 rounded-lg">

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
	</div>
</template>

<style scoped></style>
