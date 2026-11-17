<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import AtividadeForm from './components/AtividadeForm.vue'
import AtividadeCard from './components/AtividadeCard.vue'
import FiltrosBar from './components/FiltrosBar.vue'
import LoginView from './components/LoginView.vue'
import NavbarUser from './components/NavbarUser.vue'
import PerfilView from './components/PerfilView.vue'

const utilizadorAtual = ref(null)
const paginaAtual = ref('dashboard')
const mostrarModal = ref(false)
const modoEscuro = ref(false)

const atividades = ref([])
const textoPesquisa = ref('')
const estadoFiltro = ref('todas')
const atividadeEmEdicao = ref(null)

onMounted(() => {
  // Carregar preferência de tema (Claro / Escuro)
  const temaGuardado = localStorage.getItem('escola_ativa_dark_mode')
  if (temaGuardado === 'true') {
    modoEscuro.value = true
    document.body.classList.add('dark-mode')
    document.documentElement.setAttribute('data-bs-theme', 'dark')
  }

  const sessaoGuardada = localStorage.getItem('escola_ativa_sessao')
  if (sessaoGuardada) {
    utilizadorAtual.value = JSON.parse(sessaoGuardada)
  }

  const dadosGuardados = localStorage.getItem('escola_ativa_atividades')
  if (dadosGuardados) {
    atividades.value = JSON.parse(dadosGuardados)
  } else {
    atividades.value = [
      { id: 1, titulo: 'Workshop de Vue.js', descricao: 'Introdução básica aos componentes', data: '2026-11-25', estado: 'pendente' }
    ]
  }
})

watch(atividades, (novasAtividades) => {
  localStorage.setItem('escola_ativa_atividades', JSON.stringify(novasAtividades))
}, { deep: true })

function alternarTema() {
  modoEscuro.value = !modoEscuro.value
  localStorage.setItem('escola_ativa_dark_mode', modoEscuro.value)
  if (modoEscuro.value) {
    document.body.classList.add('dark-mode')
    document.documentElement.setAttribute('data-bs-theme', 'dark')
  } else {
    document.body.classList.remove('dark-mode')
    document.documentElement.setAttribute('data-bs-theme', 'light')
  }
}

function iniciarSessao(user) {
  utilizadorAtual.value = user
  localStorage.setItem('escola_ativa_sessao', JSON.stringify(user))
  paginaAtual.value = 'dashboard'
}

function terminarSessao() {
  utilizadorAtual.value = null
  localStorage.removeItem('escola_ativa_sessao')
}

function atualizarPerfil(dadosAtualizados) {
  const emailAntigo = utilizadorAtual.value.email
  utilizadorAtual.value = { ...dadosAtualizados }
  localStorage.setItem('escola_ativa_sessao', JSON.stringify(utilizadorAtual.value))

  const utilizadores = JSON.parse(localStorage.getItem('escola_ativa_users') || '[]')
  const index = utilizadores.findIndex(u => u.email === emailAntigo)
  if (index !== -1) {
    utilizadores[index] = { ...dadosAtualizados }
    localStorage.setItem('escola_ativa_users', JSON.stringify(utilizadores))
  }
}

function abrirModalCriacao() {
  atividadeEmEdicao.value = null
  mostrarModal.value = true
}

function iniciarEdicao(atividade) {
  atividadeEmEdicao.value = { ...atividade }
  mostrarModal.value = true
}

function fecharModal() {
  mostrarModal.value = false
  atividadeEmEdicao.value = null
}

function adicionarAtividade(novaAtividade) {
  atividades.value.push(novaAtividade)
  fecharModal()
}

function atualizarAtividade(atividadeAtualizada) {
  const index = atividades.value.findIndex(a => a.id === atividadeAtualizada.id)
  if (index !== -1) {
    atividades.value[index] = atividadeAtualizada
  }
  fecharModal()
}

function eliminarAtividade(id) {
  atividades.value = atividades.value.filter(ativ => ativ.id !== id)
}

function mudarEstado(id) {
  const ativ = atividades.value.find(a => a.id === id)
  if (ativ) {
    ativ.estado = ativ.estado === 'pendente' ? 'concluida' : 'pendente'
  }
}

const atividadesFiltradas = computed(() => {
  return atividades.value.filter(ativ => {
    const correspondePesquisa = ativ.titulo.toLowerCase().includes(textoPesquisa.value.toLowerCase())
    const correspondeEstado = estadoFiltro.value === 'todas' || ativ.estado === estadoFiltro.value
    return correspondePesquisa && correspondeEstado
  })
})

const totalPendentes = computed(() => atividades.value.filter(a => a.estado === 'pendente').length)
const totalConcluidas = computed(() => atividades.value.filter(a => a.estado === 'concluida').length)
</script>

<template>
  <div class="container my-4">
    <LoginView v-if="!utilizadorAtual" @login-sucesso="iniciarSessao" />

    <div v-else>
      <NavbarUser 
        :utilizador="utilizadorAtual"
        :totalAtividades="atividades.length"
        :pendentes="totalPendentes"
        :concluidas="totalConcluidas"
        :paginaAtual="paginaAtual"
        :modoEscuro="modoEscuro"
        @logout="terminarSessao"
        @mudar-pagina="(pag) => paginaAtual = pag"
        @alternar-tema="alternarTema"
      />

      <PerfilView 
        v-if="paginaAtual === 'perfil'" 
        :utilizador="utilizadorAtual"
        @atualizar-perfil="atualizarPerfil"
        @voltar="paginaAtual = 'dashboard'"
      />

      <main v-else>
        <FiltrosBar 
          v-model:pesquisa="textoPesquisa" 
          v-model:estadoFiltro="estadoFiltro"
          @abrir-modal="abrirModalCriacao"
        />

        <div class="card border-0 shadow-sm rounded-4 bg-white p-3">
          <div class="card-body">
            <h4 class="card-title mb-4 fw-bold text-dark">
              Atividades Escolares
            </h4>
            
            <p v-if="atividadesFiltradas.length === 0" class="text-muted text-center py-4">
              Nenhuma atividade encontrada com os critérios atuais.
            </p>
            
            <div v-else>
              <AtividadeCard 
                v-for="ativ in atividadesFiltradas" 
                :key="ativ.id" 
                :atividade="ativ"
                @eliminar="eliminarAtividade"
                @mudar-estado="mudarEstado"
                @editar="iniciarEdicao"
              />
            </div>
          </div>
        </div>

        <AtividadeForm 
          v-if="mostrarModal"
          :atividadeEmEdicao="atividadeEmEdicao"
          @adicionar-atividade="adicionarAtividade"
          @atualizar-atividade="atualizarAtividade"
          @fechar-modal="fecharModal"
        />
      </main>
    </div>
  </div>
</template>