<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import { vMaska } from "maska/vue"
</script>

<template>
	<div class="w-screen h-screen flex flex-col items-center">
		<header class="flex items-center justify-center p-4">
			<div class="text-2xl">Formulário</div>
		</header>
		<main
			class="flex flex-col justify-center w-5/6 lg:w-3/4 xl:w-3/5 text-base bg-white border-2 border-gray-400 p-4 rounded-lg">
			<div class="flex flex-col md:flex-row gap-2 py-2 md:items-center">
				<div class="w-full md:w-50">Nome completo:</div>
				<input type="text" class="border-2 border-gray-400 rounded-lg p-1 w-full" />
			</div>

			<div class="flex justify-between flex-col lg:flex-row lg:gap-6">
				<div class="flex flex-col md:flex-row gap-2 py-2 md:items-center">
					<div class="w-50">Data de nascimento:</div>
					<input type="date" class="border-2 border-gray-400 rounded-lg p-1" />
				</div>

				<div class="flex flex-col md:flex-row gap-2 py-2 md:items-center w-full">
					<div class="w-50">CPF ou CNPJ</div>
					<input v-maska="'###.###.###-##'" type="text" class="border-2 border-gray-400 rounded-lg p-1 w-full" />
				</div>
			</div>

			<!-- <div>
				<div>Endereço completo</div>
				<div class="flex flex-col p-6">
					<div class="flex justify-between flex-col md:flex-row flex-1">
						<div class="flex gap-2 py-4">
							<div>Rua</div>
							<input type="text" class="bg-gray-200 flex-1">
						</div>
						<div class="flex gap-2 py-4">
							<div>Bairro</div>
							<input type="text" class="bg-gray-200 flex-1">
						</div>
					</div>
					<div class="flex justify-between">
						<div class="flex gap-2 py-4">
							<div>Número</div>
							<input type="text" class="bg-gray-200">
						</div>
						<div class="flex gap-2 py-4">
							<div>Cidade</div>
							<input type="text" class="bg-gray-200">

						</div>

					</div>
					<div class="flex justify-between">
						<div class="flex gap-2 py-4">
							<div>Estado</div>
							<input type="text" class="bg-gray-200">
						</div>
						<div class="flex gap-2 py-4">
							<div>CEP</div>
							<input type="text" class="bg-gray-200">
						</div>
					</div>
				</div>
			</div>

			<div class="flex gap-2 py-4">
				<div>telefone</div>
				<input type="text" class="bg-gray-200">
			</div>

			<div class="flex gap-2 py-4">
				<div>email</div>
				<input type="text" class="bg-gray-200">
			</div>

			<div class="flex gap-2 py-4">
				<div>data do preenchimento</div>
				<input type="text" class="bg-gray-200">
			</div>

			<div class="flex gap-2 py-4">
				<div>observações ou comentários</div>
				<input type="text" class="bg-gray-200">
			</div> -->
		</main>
	</div>
</template>

<style scoped></style>
