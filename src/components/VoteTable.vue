<script setup>
import { ref, computed } from 'vue';

    const tableModes = [
    {
        title: 'PRESIDENTE Y VICEPRESIDENTES',
        subtitle: '',
        requiredBoxes: 2,
        backgroundColor: '#DCF0FF',
        candidatoPrincipal: 'CANDIDATO JP #15',
        candidatoExtra1: 'CANDIDATO A',
        candidatoExtra2: 'CANDIDATO B'
    },
    {
        title: 'SENADORES',
        subtitle: 'A NIVEL NACIONAL (DISTRITO ÚNICO)',
        requiredBoxes: 3,
        backgroundColor: '#FFE1EB',
        candidatoPrincipal: 'CANDIDATO JP #15',
        candidatoExtra1: 'CANDIDATO C',
        candidatoExtra2: 'CANDIDATO D'
    },
    {
        title: 'DIPUTADOS',
        subtitle: 'DISTRITO ELECTORAL',
        requiredBoxes: 2,
        backgroundColor: '#F5E6D7',
        candidatoPrincipal: 'CANDIDATO JP #15',
        candidatoExtra1: 'CANDIDATO E',
        candidatoExtra2: 'CANDIDATO F'
    },
    {
        title: 'GOBERNADOR REGIONAL',
        subtitle: 'A NIVEL REGIONAL',
        requiredBoxes: 3,
        backgroundColor: '#E1F5E1',
        candidatoPrincipal: 'CANDIDATO JP #15',
        candidatoExtra1: 'CANDIDATO G',
        candidatoExtra2: 'CANDIDATO H'
    },
    {
        title: 'ALCALDE PROVINCIAL',
        subtitle: 'A NIVEL PROVINCIAL',
        requiredBoxes: 3,
        backgroundColor: '#E1F5E1',
        candidatoPrincipal: 'CANDIDATO JP #15',
        candidatoExtra1: 'CANDIDATO I',
        candidatoExtra2: 'CANDIDATO J'
    }
    ];
    const currentStep = ref(0);

    // Computed property para acceder fácilmente a los datos del modo actual
    const currentMode = computed(() => tableModes[currentStep.value]);

    // 3. Guardamos los votos de cada etapa en un arreglo
    const votes = ref([
    { box1: false, box2: false, box3: false }, // Votos paso 0
    { box1: false, box2: false, box3: false }, // Votos paso 1
    { box1: false, box2: false, box3: false }, // Votos paso 2
    { box1: false, box2: false, box3: false }, // Votos paso 3
    { box1: false, box2: false, box3: false }  // Votos paso 4
    ]);

    const currentVotes = computed(() => votes.value[currentStep.value]);

    // 4. Funciones de los botones
    const alternateVote = (boxNumber) => {
        currentVotes.value[`box${boxNumber}`] = !currentVotes.value[`box${boxNumber}`];
    };

    const goForward = () => {
        // Validamos que se hayan marcado las cajas requeridas
        const v = currentVotes.value;
        const required = currentMode.value.requiredBoxes;
        
        let isValid = false;
        if (required === 3) isValid = v.box1 && v.box2 && v.box3;
        if (required === 2) isValid = v.box2 && v.box3;

        if (!isValid) {
            alert(`Por favor, marca las ${required} casillas correspondientes a Juntos por el Perú (#15) para practicar tu voto.`);
            return;
        }

        // Si es válido, avanzamos
        if (currentStep.value < tableModes.length - 1) {
            currentStep.value++;
        } else {
            alert('¡Felicidades! Has completado el tutorial de votación.');
            // Aquí podrías reiniciar el tutorial: currentStep.value = 0;
        }
    };

    const goBack = () => {
        if (currentStep.value > 0) currentStep.value--;
    };
</script>

<template>
    <div class="card vote-message">
      <h1>Practica tu voto!</h1>
      <img src="../assets/Logo_juntos_por_el_Peru.svg" width="100px" height="100px"/>
    </div>

    <table id="vote-table">
        <colgroup>
            <col style="width: 8.33%;" v-for="i in 12" :key="i">
        </colgroup>
        <thead>
            <tr id="information-row">
                <th colspan="3"><img src="../assets/Escudo_nacional_del_Perú.svg" alt="Escudo Perú" width="80px" height="80px"></th>
                <th colspan="6">
                    <h2>{{currentMode.title}}</h2>
                    <h3>{{ currentMode.subtitle }}</h3>
                </th>
                <th colspan="3"><img src="../assets/ONPE.png" alt="Logo ONPE" width="80px" height="60px"></th>
            </tr>
            <tr id="instruction-row">
                <th :colspan="currentStep === 0 ? 12:8">MARQUE CON UNA CRUZ <b>+</b> O UN ASPA <b>x</b> DENTRO DEL RECUADRO DEL SÍMBOLO Y/O FOTOGRAFÍA DE SU PREFERENCIA</th>
                <th v-if="currentStep !== 0" colspan="4">
                    VOTO PREFERENCIAL
                    <h5>SI DESEA COLOQUE DENTRO DEL RECUADRO EL NÚMERO DEL CANDIDATO DE SU PREFERENCIA</h5>
                </th>
            </tr>
        </thead>

        <tbody>

            <tr>
                <td colspan="6" :style="{ backgroundColor: currentMode.backgroundColor }">CANDIDATO X</td>
                <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }"> 
                    <div v-if="[1, 3, 4].includes(currentStep)" class="vote-box">

                    </div>
                </td>
                <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div class="vote-box" >
                    </div>
                </td>
                <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div class="vote-box">
                    </div>
                </td>
            </tr>

            <tr class="main-row">
                <td colspan="6" :style="{ backgroundColor: currentMode.backgroundColor }">
                    {{ currentMode.candidatoPrincipal }}
                </td>
                <td @click="alternateVote(1)" class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div v-if="[1, 3, 4].includes(currentStep)" class="vote-box" 
                        style="background-image: url('/Logo_juntos_por_el_Peru.svg');">
                        <span v-if="currentVotes.box1" class="x-mark">X</span>
                    </div>
                </td>
                <td @click="alternateVote(2)" class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div class="vote-box" 
                        style="background-image: url('/Logo_juntos_por_el_Peru.svg');">
                        <span v-if="currentVotes.box2" class="x-mark">X</span>
                    </div>
                </td>
                <td @click="alternateVote(3)" class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div class="vote-box"
                        style="background-image: url('/Logo_juntos_por_el_Peru.svg');">
                        <span v-if="currentVotes.box3" class="x-mark">X</span>
                    </div>
                </td>
            </tr>

            <tr>
                <td colspan="6" :style="{ backgroundColor: currentMode.backgroundColor }">CANDIDATO Y</td>
                <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div v-if="[1, 3, 4].includes(currentStep)" class="vote-box">

                    </div>
                </td>
                <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div class="vote-box" >
                    </div>
                </td>
                <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div class="vote-box">
                    </div>
                </td>
            </tr>
        </tbody>
    </table>

    <div class="button-container">
        <button id="btnBack" class="black-button" @click="goBack" :disabled="currentStep === 0">ATRÁS</button>
        <button id="btnForward" class="black-button" @click="goForward">
        {{ currentStep === tableModes.length - 1 ? 'FINALIZAR' : 'SIGUIENTE' }}
        </button>
    </div>
</template>

<style scoped>
    .button-container{
        display: flex;
        width: 100%;
        gap: 1rem;
    }

    .black-button{
        border-radius: 0;
        width: 50%;
        padding: 0.5rem;
        font-weight: bold;
        color: white;
        background-color: black;
    }

    .black-button:hover{
        background-color: white;
        color: black;
        transition: 0.35s;
        border: 1px solid black;
    }

    #vote-table{
        border-collapse: separate;
        border: none;
        table-layout: fixed;
        max-width: 600px;
        border-spacing: 0 0.25rem;
        font-weight: bold;
    }

    #vote-table td{
        padding: 1rem;
        border-bottom: 0.75rem solid white;
    }

    #information-row th:first-child{
        text-align: left;
    }
    #information-row th:last-child{
        text-align: right;
    }
    
    #instruction-row th{
        padding: 1rem 2rem;
        background-color: #ccc;
    }

    #vote-table b{
        border: 1px solid;
        border-color: black;
        background-color: white;
        font-size:x-large;
        padding: 0px 5px;
    }

    .vote-box{
        width: 95%;
        background-color: white;
        border: 2px solid black;
        height: 50px;
        position: relative;
        background-position: center;
        background-repeat: no-repeat;
        background-size:contain;
        display: flex;
        justify-content: center;
        align-items: center;
        position: relative;
    }

    #vote-table td:has(.vote-box) {
        padding: 0;
    }

    .x-mark {
        font-size: 3rem;
        font-weight: bold;
        color: #000;
        font-family: Arial, sans-serif;
        user-select: none;
        position: absolute; 
    }

    .clickable-cell:hover .vote-box {
        background-color: #f0f0f0;
        cursor: pointer;
    }

    .card{
        width: 100%;
        background-color: white;
        color: black;
        border: 1px solid black;
        margin-bottom: 3rem;
        padding: 1rem;
    }
</style>
