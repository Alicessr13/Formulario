<script setup lang="ts">
import { vMaska } from "maska/vue";
import { ref, watch } from 'vue';

const nome = ref("");
const nomeInvalido = ref(false);
const email = ref("");
const emailInvalido = ref(false);
const telefone = ref("");
const telefoneInvalido = ref(false);
const data = ref("");
const dataInvalida = ref(false);
const cpf = ref("");
const cpfInvalido = ref(false);

const cep = ref("");
const uf = ref("");
const cidade = ref("");
const rua = ref("");
const bairro = ref("");
const numero = ref("");
const numeroInvalido = ref(false);

const erroMensagem = ref("");
const erro = ref(false);

const imprimir = () => {

	if (!nome.value || !email.value || !telefone.value || !data.value || !cpf.value || !cep.value || !uf.value || !cidade.value || !rua.value || !bairro.value || !numero.value) {
		erro.value = true;
		erroMensagem.value = ('Preencha os campos vazios para imprimir o PDF');
		return;
	}

	if (emailInvalido.value) {
		erro.value = true;
		erroMensagem.value = ('E-mail inválido corrija para imprimir o PDF');
		return;
	}

	if(nomeInvalido.value){
		erro.value = true
		erroMensagem.value = ('Número da casa inválido corrija para imprimir o pdf')
	}

	if (nomeInvalido.value) {
		erro.value = true;
		erroMensagem.value = ('Nome inválido corrija para imprimir o PDF');
		return;
	}

	if (dataInvalida.value) {
		erro.value = true;
		erroMensagem.value = ('Data inválida corrija para imprimir o PDF');
		return;
	}

	if (telefoneInvalido.value) {
		erro.value = true;
		erroMensagem.value = ('Telefone inválido corrija para imprimir o PDF');
		return;
	}

	if (cpfInvalido.value) {
		erro.value = true;
		erroMensagem.value = ('CPF inválido corrija para imprimir o PDF');
		return;
	}


	erro.value = false;
	erroMensagem.value = '';
	window.print();
}

const validarEmail = (email) => {
	const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
	return emailInvalido.value = !regex.test(email);
}

const validarTelefone = (tel) => {
	// Remove todos os caracteres não numéricos
	const numeroLimpo = tel.replace(/\D/g, '');

	// Verifica se tem o comprimento correto (11 dígitos para celular com DDD)
	if (numeroLimpo.length !== 11) {
		telefoneInvalido.value = true;
		return true;
	}

	telefoneInvalido.value = false;
	return false;
}


const validarCPF = (cpfValor) => {
	const cpfLimpo = cpfValor.replace(/\D/g, '');

	// Verifica se tem o comprimento correto (11 dígitos)
	if (cpfLimpo.length !== 11) {
		cpfInvalido.value = true;
		return true;
	}

	// Verifica se todos os dígitos são iguais (CPF inválido)
	if (/^(\d)\1+$/.test(cpfLimpo)) {
		cpfInvalido.value = true;
		return true;
	}

	// Algoritmo de validação do CPF
	// Cálculo do primeiro dígito verificador
	let soma = 0;
	for (let i = 0; i < 9; i++) {
		soma += parseInt(cpfLimpo.charAt(i)) * (10 - i);
	}
	let resto = soma % 11;
	let dv1 = resto < 2 ? 0 : 11 - resto;

	// Cálculo do segundo dígito verificador
	soma = 0;
	for (let i = 0; i < 10; i++) {
		soma += parseInt(cpfLimpo.charAt(i)) * (11 - i);
	}
	resto = soma % 11;
	let dv2 = resto < 2 ? 0 : 11 - resto;

	// Verifica se os dígitos verificadores estão corretos
	if (parseInt(cpfLimpo.charAt(9)) !== dv1 || parseInt(cpfLimpo.charAt(10)) !== dv2) {
		cpfInvalido.value = true;
		return true;
	}

	cpfInvalido.value = false;
	return false;
}

const validarNome = (nome) => {
	const regex = /^[A-Za-zÀ-ÿ\s]+$/;
	return nomeInvalido.value = !regex.test(nome);
}

const validarData = (data) => {
	if (!data) {
		dataInvalida.value = true;
		return true;
	}

	const regex = /^\d{4}-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$/;
	if (!regex.test(data)) {
		dataInvalida.value = true;
		return true;
	}

	const [ano, mes, dia] = data.split('-').map(Number);
	const dataObj = new Date(ano, mes - 1, dia);
	dataObj.setHours(0, 0, 0, 0); // Normaliza para meia-noite

	// Verifica se a data é válida
	if (dataObj.getFullYear() !== ano || dataObj.getMonth() !== mes - 1 || dataObj.getDate() !== dia) {
		dataInvalida.value = true;
		return true;
	}

	// Obtém o ano atual
	const anoAtual = new Date().getFullYear();

	// Verifica se o ano está dentro do intervalo de 100 anos (mais simples e direto)
	if (ano < anoAtual - 100 || ano > anoAtual + 100) {
		dataInvalida.value = true;
		return true;
	}

	dataInvalida.value = false;
	return false;
};

const validarNumeros = (valor) => {
  // Se não houver valor, consideramos inválido
  if (!valor) {
    numeroInvalido.value = false;
    return true;
  }
  
  // Regex para validar se contém apenas números
  const regex = /^\d+$/;
  
  // Testa se o valor contém apenas números
  const eValido = regex.test(valor);
  
  // Atualiza o estado de validade
  numeroInvalido.value = !eValido;
  
  // Retorna true se válido (apenas números), false se inválido
  return eValido;
};

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
			<div class="text-xl">Formulário</div>
		</header>
		<main ref="pdfContent"
			class="flex flex-col justify-center w-5/6 lg:w-3/4 xl:w-3/5 text-sm bg-white border-2 border-gray-400 p-4 rounded-lg">

			<div class="flex flex-col justify-between md:flex-row md:gap-6">
				<div class="flex flex-col gap-2 pb-2 w-full">
					<div class="w-full md:w-38">Nome completo</div>
					<input v-model="nome" @blur="validarNome(nome)" type="text"
						class="border-2 border-gray-400 rounded-lg p-1 w-full" />
					<span v-if="nomeInvalido" class="text-red-600">Nome inválido!</span>
				</div>
				<div class="flex flex-col gap-2 py-2 md:pt-0">
					<div class="w-38">Data de nascimento</div>
					<input @blur="validarData(data)" v-model="data" type="date"
						class="border-2 border-gray-400 rounded-lg p-1 w-36" />
					<span v-if="dataInvalida" class="text-red-600">Data inválida!</span>
				</div>
			</div>
			<div class="flex flex-col md:flex-row md:gap-6">
				<div class="flex flex-col gap-2 py-2">
					<div class="w-38">Telefone</div>
					<input v-model="telefone" @blur="validarTelefone(telefone)" type="text" v-maska="'(##) #####-####'"
						class="border-2 border-gray-400 rounded-lg p-1 w-38" />
					<span v-if="telefoneInvalido" class="text-red-500">Telefone inválido!</span>
				</div>

				<div class="flex flex-col gap-2 py-2">
					<div class="w-10">CPF</div>
					<input v-model="cpf" @blur="validarCPF(cpf)" v-maska="'###.###.###-##'" type="text"
						class="border-2 border-gray-400 rounded-lg p-1 w-36" />
					<span v-if="cpfInvalido" class="text-red-500">CPF inválido!</span>
				</div>

				<div class="flex flex-col gap-2 py-2 w-full">
					<div class="w-14">E-mail</div>
					<input @blur="validarEmail(email)" v-model="email" type="text"
						class="border-2 border-gray-400 rounded-lg p-1 w-full" />
					<span v-if="emailInvalido" class="text-red-500">Email inválido!</span>
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
						<input v-model="numero" @blur="validarNumeros(numero)" type="text" class="border-2 border-gray-400 rounded-lg p-1 w-full" />
						<span v-if="numeroInvalido" class="text-red-600">Número inválido</span>
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
		<footer class="flex justify-start items-center no-print w-5/6 lg:w-3/4 xl:w-3/5 gap-2">

			<button class="mt-4 px-4 py-2 bg-black text-white rounded-lg  flex items-center gap-2" @click="imprimir">
				<svg width="16" height="16" viewBox="0 0 16 16" xmlns="http://www.w3.org/2000/svg" fill="white">
					<path fill-rule="evenodd" clip-rule="evenodd"
						d="M13.85 4.44l-3.28-3.3-.35-.14H2.5l-.5.5V7h1V2h6v3.5l.5.5H13v1h1V4.8l-.15-.36zM10 5V2l3 3h-3zM2.5 8l-.5.5v6l.5.5h11l.5-.5v-6l-.5-.5h-11zM13 13v1H3V9h10v4zm-8-1h-.32v1H4v-3h1.06c.75 0 1.13.36 1.13 1a.94.94 0 0 1-.32.72A1.33 1.33 0 0 1 5 12zm-.06-1.45h-.26v.93h.26c.36 0 .54-.16.54-.47 0-.31-.18-.46-.54-.46zM9 12.58a1.48 1.48 0 0 0 .44-1.12c0-1-.53-1.46-1.6-1.46H6.78v3h1.06A1.6 1.6 0 0 0 9 12.58zm-1.55-.13v-1.9h.33a.94.94 0 0 1 .7.25.91.91 0 0 1 .25.67 1 1 0 0 1-.25.72.94.94 0 0 1-.69.26h-.34zm4.45-.61h-.97V13h-.68v-3h1.74v.55h-1.06v.74h.97v.55z" />
				</svg>
				Gerar PDF</button>
			<span v-if="erro" class="mt-4 py-2 text-red-500">{{ erroMensagem }}</span>

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
