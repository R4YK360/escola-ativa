<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  atividadeEmEdicao: {
    type: Object,
    default: null
  }
})

const emit = defineEmits(['adicionar-atividade', 'atualizar-atividade', 'fechar-modal'])

const titulo = ref('')
const descricao = ref('')
const data = ref('')
const estado = ref('pendente')
const erro = ref('')

watch(() => props.atividadeEmEdicao, (novaAtiv) => {
  if (novaAtiv) {
    titulo.value = novaAtiv.titulo
    descricao.value = novaAtiv.descricao
    data.value = novaAtiv.data
    estado.value = novaAtiv.estado
    erro.value = ''
  } else {
    limparCampos()
  }
}, { immediate: true })

function limparCampos() {
  titulo.value = ''
  descricao.value = ''
  data.value = ''
  estado.value = 'pendente'
  erro.value = ''
}

function submeterFormulario() {
  if (!titulo.value.trim() || !descricao.value.trim() || !data.value) {
    erro.value = 'Por favor, preenche todos os campos obrigatórios.'
    return
  }

  if (props.atividadeEmEdicao) {
    emit('atualizar-atividade', {
      id: props.atividadeEmEdicao.id,
      titulo: titulo.value,
      descricao: descricao.value,
      data: data.value,
      estado: estado.value
    })
  } else {
    emit('adicionar-atividade', {
      id: Date.now(),
      titulo: titulo.value,
      descricao: descricao.value,
      data: data.value,
      estado: estado.value
    })
  }

  limparCampos()
}
</script>

<template>
  <!-- Fundo escuro sobreposto à página -->
  <div class="modal-backdrop-custom d-flex justify-content-center align-items-center">
    <div class="card border-0 shadow-lg rounded-4 bg-white p-3" style="width: 100%; max-width: 500px; z-index: 1055;">
      <div class="card-body">
        <div class="d-flex justify-content-between align-items-center mb-4">
          <div class="d-flex align-items-center gap-2">
            <div class="p-2 rounded-3 text-white d-flex align-items-center justify-content-center" style="background-color: #1a73e8; width: 38px; height: 38px;">
              <i :class="atividadeEmEdicao ? 'bi bi-pencil-square' : 'bi bi-plus-lg'"></i>
            </div>
            <h4 class="card-title fw-bold m-0 text-dark">
              {{ atividadeEmEdicao ? 'Editar Atividade' : 'Nova Atividade' }}
            </h4>
          </div>
          <button @click="$emit('fechar-modal')" class="btn-close" aria-label="Fechar"></button>
        </div>
        
        <div v-if="erro" class="alert alert-danger py-2 small rounded-3" role="alert">
          <i class="bi bi-exclamation-circle me-1"></i> {{ erro }}
        </div>

        <form @submit.prevent="submeterFormulario">
          <div class="mb-3">
            <label class="form-label small fw-bold text-secondary">TÍTULO</label>
            <input v-model="titulo" type="text" class="form-control border-secondary-subtle py-2 rounded-3 text-dark" placeholder="Ex: Visita de Estudo ao Museu">
          </div>

          <div class="mb-3">
            <label class="form-label small fw-bold text-secondary">DESCRIÇÃO</label>
            <textarea v-model="descricao" class="form-control border-secondary-subtle py-2 rounded-3 text-dark" rows="3" placeholder="Detalhes da atividade..."></textarea>
          </div>

          <div class="row">
            <div class="col-md-6 mb-3">
              <label class="form-label small fw-bold text-secondary">DATA</label>
              <input v-model="data" type="date" class="form-control border-secondary-subtle py-2 rounded-3 text-dark">
            </div>

            <div class="col-md-6 mb-4">
              <label class="form-label small fw-bold text-secondary">ESTADO</label>
              <select v-model="estado" class="form-select border-secondary-subtle py-2 rounded-3 text-dark">
                <option value="pendente">Pendente</option>
                <option value="concluida">Concluída</option>
              </select>
            </div>
          </div>

          <div class="d-flex justify-content-end gap-2">
            <button type="button" @click="$emit('fechar-modal')" class="btn btn-light rounded-pill px-4 fw-semibold text-secondary">
              Cancelar
            </button>
            <button type="submit" class="btn py-2 px-4 fw-bold text-white rounded-pill shadow-sm" style="background-color: #1a73e8;">
              <i class="bi bi-check2 me-1"></i> {{ atividadeEmEdicao ? 'Guardar Alterações' : 'Criar Atividade' }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
.modal-backdrop-custom {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(15, 17, 21, 0.65);
  backdrop-filter: blur(4px);
  z-index: 1050;
  padding: 1rem;
}
</style>