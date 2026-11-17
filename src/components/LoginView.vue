<script setup>
import { ref } from 'vue'

const emit = defineEmits(['login-sucesso'])

const modoRegisto = ref(false)
const nome = ref('')
const email = ref('')
const password = ref('')
const erro = ref('')

function submeter() {
  erro.value = ''

  if (!email.value.trim() || !password.value.trim() || (modoRegisto.value && !nome.value.trim())) {
    erro.value = 'Preenche todos os campos obrigatórios.'
    return
  }

  const utilizadores = JSON.parse(localStorage.getItem('escola_ativa_users') || '[]')

  if (modoRegisto.value) {
    if (utilizadores.some(u => u.email === email.value)) {
      erro.value = 'Este email já está registado.'
      return
    }

    const novoUser = { nome: nome.value, email: email.value, password: password.value, avatar: '' }
    utilizadores.push(novoUser)
    localStorage.setItem('escola_ativa_users', JSON.stringify(utilizadores))
    emit('login-sucesso', novoUser)
  } else {
    const userEncontrado = utilizadores.find(u => u.email === email.value && u.password === password.value)
    if (userEncontrado) {
      emit('login-sucesso', userEncontrado)
    } else {
      erro.value = 'Email ou palavra-passe incorretos.'
    }
  }
}
</script>

<template>
  <div class="row justify-content-center align-items-center min-vh-100 py-4">
    <div class="col-md-5 col-lg-4">
      <div class="card border-0 shadow-sm rounded-4 bg-white p-3">
        <div class="card-body p-4">
          <div class="text-center mb-4">
            <!-- Logo com tamanho fixo e quadrado para nunca esticar -->
            <div class="d-inline-flex align-items-center justify-content-center rounded-circle mb-3 shadow-sm mx-auto" style="width: 64px; height: 64px; background-color: #f1f3f5; color: #1a73e8;">
              <i class="bi bi-mortarboard-fill fs-3"></i>
            </div>
            <h2 class="fw-bold text-dark m-0" style="letter-spacing: -0.5px;">EscolaAtiva</h2>
            <p class="text-muted small mt-1">
              {{ modoRegisto ? 'Cria a tua conta para gerir atividades' : 'Inicia sessão na tua conta' }}
            </p>
          </div>

          <div v-if="erro" class="alert alert-danger py-2 small rounded-3 mb-3">
            <i class="bi bi-exclamation-circle me-1"></i> {{ erro }}
          </div>

          <form @submit.prevent="submeter">
            <div v-if="modoRegisto" class="mb-3">
              <label class="form-label small fw-bold text-secondary">NOME COMPLETO</label>
              <input v-model="nome" type="text" class="form-control border-secondary-subtle py-2 rounded-3 text-dark" placeholder="Ex: Alina Ji">
            </div>

            <div class="mb-3">
              <label class="form-label small fw-bold text-secondary">EMAIL ESCOLAR</label>
              <input v-model="email" type="email" class="form-control border-secondary-subtle py-2 rounded-3 text-dark" placeholder="nome@escola.pt">
            </div>

            <div class="mb-4">
              <label class="form-label small fw-bold text-secondary">PALAVRA-PASSE</label>
              <input v-model="password" type="password" class="form-control border-secondary-subtle py-2 rounded-3 text-dark" placeholder="••••••••">
            </div>

            <button type="submit" class="btn w-100 py-2 fw-bold text-white rounded-pill shadow-sm mb-3" style="background-color: #1a73e8;">
              <i class="bi" :class="modoRegisto ? 'bi-person-plus-fill' : 'bi-box-arrow-in-right'"></i> 
              {{ modoRegisto ? 'Criar Conta' : 'Entrar' }}
            </button>
          </form>

          <div class="text-center">
            <button @click="modoRegisto = !modoRegisto; erro = ''" class="btn btn-link btn-sm text-decoration-none fw-semibold" style="color: #1a73e8;">
              {{ modoRegisto ? 'Já tens conta? Inicia sessão' : 'Não tens conta? Regista-te aqui' }}
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>