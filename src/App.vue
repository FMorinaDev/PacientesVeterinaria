<script setup>
  import { ref, reactive, onMounted } from "vue";
  import { uid } from "uid";
  import Header from "./components/Header.vue";
  import Formulario from "./components/Formulario.vue";
  import Paciente from './components/Paciente.vue';

  const pacientes = ref([]);
  const paciente = reactive({
    id: null,
    nombre:'',
    propietario:'',
    email:'',
    alta:'',
    sintomas:'',
  });
  const guardarPaciente = () => {
    if (paciente.id) {
      const id = paciente.id;
      const index = pacientes.value.findIndex((pacienteState) => pacienteState.id === id);
      pacientes.value[index] = {...paciente};
    }else{
      pacientes.value.push({
        ...paciente,
        id: uid()
      });
    }
    //Reiniciar valores
    Object.assign(paciente, {
      nombre:'',
      propietario:'',
      email:'',
      alta:'',
      sintomas:'',
    })
    localStorage.setItem('pacientes', JSON.stringify(pacientes.value));
  }
  const actualizarPaciente = (id) => {
    Object.assign(paciente, pacientes.value.find((p) => p.id == id));
    localStorage.setItem('pacientes', JSON.stringify(pacientes.value));
  }
  const eliminarPaciente = (id) => {
    pacientes.value = pacientes.value.filter((p) => p.id != id);
    localStorage.setItem('pacientes', JSON.stringify(pacientes.value));
  }
  onMounted(()=>{
    const pacientesLocal = localStorage.getItem('pacientes');
    if (pacientesLocal) {
      pacientes.value = JSON.parse(pacientesLocal);
    }
  })
</script>

<template>
  <div class="container mx-auto mt-20">
    <Header></Header>
    <div class="mt-12 md:flex">
      <Formulario
      v-model:nombre="paciente.nombre"
      v-model:propietario="paciente.propietario"
      v-model:email="paciente.email"
      v-model:alta="paciente.alta"
      v-model:sintomas="paciente.sintomas"
      v-model:id="paciente.id"
      @guardar-paciente="guardarPaciente"
      ></Formulario>
      <div class="md:w-1/2 md:h-screen overflow-y-scroll">
        <h3 class="font-black text-3xl text-center">Administra tus pacientes</h3>
        <div v-if="pacientes.length > 0">
          <p class="text-lg mt-5 text-center mb-10">
            Información de
            <span class="text-indigo-600 font-bold">Pacientes</span>
          </p>
          <Paciente
            v-for="(paciente, index) in pacientes" :key="index"
            :paciente="paciente"
            @actualizar-paciente="actualizarPaciente"
            @eliminar-paciente="eliminarPaciente"
          />
        </div>
        <p v-else class="mt-10 text-2xl text-center">No hay pacientes</p>
      </div>
    </div>
  </div>  
</template>
