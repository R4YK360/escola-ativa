<script setup>
import { ref } from 'vue'

const props = defineProps({
  utilizador: Object
})

const emit = defineEmits(['atualizar-perfil', 'voltar'])

const nome = ref(props.utilizador.nome)
const email = ref(props.utilizador.email)
const password = ref(props.utilizador.password)
const avatar = ref(props.utilizador.avatar || '')
const mensagemSucesso = ref('')
const erro = ref('')

function selecionarImagem(event) {
  const ficheiro = event.target.files[0]
  if (!ficheiro) return

  erro.value = ''
  const leitor = new FileReader()
  leitor.onload = (e) => {
    const img = new Image()
    img.onload = () => {
      // Redimensionar para 200x200 para garantir compatibilidade com localStorage
      const canvas = document.createElement('canvas')
      canvas.width = 200
      canvas.height = 200
      const ctx = canvas.getContext('2d')
      ctx.drawImage(img, 0, 0, 200, 200)
      avatar.value = canvas.toDataURL('image/jpeg', 0.8)
    }
    img.src = e.target.result
  }
  leitor.readAsDataURL(ficheiro)
}

function removerImagem() {
  avatar.value = ''
}

function guardarPerfil() {
  erro.value = ''
  mensagemSucesso.value = ''

  if (!nome.value.trim() || !email.value.trim() || !password.value.trim()) {
    erro.value = 'Todos os campos são obrigatórios.'
    return
  }

  emit('atualizar-perfil', {
    nome: nome.value,
    email: email.value,
    password: password.value,
    avatar: avatar.value
  })

  mensagemSucesso.value = 'Perfil atualizado com sucesso!'
}
</script>

<template>
  <div class="row justify-content-center">
    <div class="col-md-6">
      <div class="card border-0 shadow-sm rounded-4 bg-white p-3">
        <div class="card-body">
          <div class="d-flex justify-content-between align-items-center mb-4">
            <h4 class="fw-bold m-0 text-dark">
              <i class="bi bi-person-gear me-2" style="color: #1a73e8;"></i>Editar Perfil
            </h4>
            <button @click="$emit('voltar')" class="btn btn-sm btn-outline-secondary rounded-pill px-3">
              <i class="bi bi-arrow-left me-1"></i> Voltar
            </button>
          </div>

          <div v-if="erro" class="alert alert-danger py-2 small rounded-3">{{ erro }}</div>
          <div v-if="mensagemSucesso" class="alert alert-success py-2 small rounded-3">{{ mensagemSucesso }}</div>

          <form @submit.prevent="guardarPerfil">
            <div class="text-center mb-4">
              <div class="position-relative d-inline-block mb-3">
                <img 
                  v-if="avatar" 
                  :src="avatar" 
                  alt="Foto de perfil" 
                  class="rounded-circle object-fit-cover shadow-sm border"
                  style="width: 100px; height: 100px;"
                >
                <div 
                  v-else 
                  class="rounded-circle d-flex align-items-center justify-content-center shadow-sm mx-auto"
                  style="width: 100px; height: 100px; background-color: #e9ecef; color: #1a73e8;"
                >
                  <i class="bi bi-person-fill" style="font-size: 3rem;"></i>
                </div>
              </div>

              <div class="d-flex justify-content-center gap-2">
                <label class="btn btn-sm rounded-pill px-3 fw-semibold text-white" style="background-color: #1a73e8; cursor: pointer;">
                  <i class="bi bi-camera me-1"></i> Escolher Foto
                  <input type="file" accept="image/*" @change="selecionarImagem" class="d-none">
                </label>

                <button 
                  v-if="avatar" 
                  type="button" 
                  @click="removerImagem" 
                  class="btn btn-sm btn-outline-danger rounded-pill px-3"
                >
                  <i class="bi bi-trash3 me-1"></i> Remover
                </button>
              </div>
            </div>

            <div class="mb-3">
              <label class="form-label small fw-bold text-secondary">NOME COMPLETO</label>
              <input v-model="nome" type="text" class="form-control border-secondary-subtle py-2 rounded-3 text-dark">
            </div>

            <div class="mb-3">
              <label class="form-label small fw-bold text-secondary">EMAIL ESCOLAR</label>
              <input v-model="email" type="email" class="form-control border-secondary-subtle py-2 rounded-3 text-dark">
            </div>

            <div class="mb-4">
              <label class="form-label small fw-bold text-secondary">NOVA PALAVRA-PASSE</label>
              <input v-model="password" type="password" class="form-control border-secondary-subtle py-2 rounded-3 text-dark">
            </div>

            <button type="submit" class="btn w-100 py-2 fw-bold text-white rounded-pill shadow-sm" style="background-color: #1a73e8;">
              <i class="bi bi-check2-circle me-1"></i> Guardar Alterações
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>