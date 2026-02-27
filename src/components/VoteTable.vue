<script setup>
import { ref, computed } from 'vue';
import PreferentialModal from './PreferentialModal.vue';

    const tableModes = [
    {
        title: 'PRESIDENTE Y VICEPRESIDENTES',
        subtitle: '',
        requiredBox: 1,
        backgroundColor: '#DCF0FF',
    },
    {
        title: 'SENADORES',
        subtitle: 'A NIVEL NACIONAL',
        requiredBox: 1,
        backgroundColor: '#FFE1EB',
        preferentialVotes: [{ name: 'José Mercedes Castillo Terrones', number: 1, photo: '/SENADORES NACIONALES/1-Jose.jpg' },
                            { name: 'Bernardo Jaime Quito Sarmiento', number: 7, photo: '/SENADORES NACIONALES/7-Bernardo.jpeg' },
                            { name: 'Margot Palacios Huamán', number: 8, photo: '/SENADORES NACIONALES/8-Margot.jpg' }]
    },
    {
        title: 'SENADORES',
        subtitle: 'A NIVEL REGIONAL (ANCASH)',
        requiredBox: 1,
        backgroundColor: '#F5E6D7',
        preferentialVotes: [{ name: 'Elías Marcial Varas Meléndez', number: 1, photo: '/SENADORES REGIONALES/1-Elias.png' },
                            { name: 'Domitila Gina Silva Castillo', number: 2, photo: '/SENADORES REGIONALES/2-Domitila.jpeg' }]
    },
    {
        title: 'DIPUTADOS',
        subtitle: 'A NIVEL REGIONAL (ANCASH)',
        requiredBox: 1,
        backgroundColor: '#E1F5E1',
        preferentialVotes: [{ name: 'Daniel Jefferson Varas Seguín', number: 1, photo: '/DIPUTADOS/1-Daniel.jpg' },
                            { name: 'José Antonio Monzon Mendoza', number: 5, photo: '/DIPUTADOS/5-Jose.jpeg' }]
    },
    {
        title: 'PARLAMENTO ANDINO',
        subtitle: '',
        requiredBox: 1,
        backgroundColor: '#E1F5E1',
        preferentialVotes: [{ name: 'Paúl Pércy Fernández Bravo', number: 1, photo: '/PARLAMENTO ANDINO/1-Paul.jpg' },
                            { name: 'Rosario Del Carmen Mori Isuisa', number: 2, photo: '/PARLAMENTO ANDINO/2-Rosario.jpg' },
                            { name: 'Jorge Eusebio Manco Zaconetti', number: 3, photo: '/PARLAMENTO ANDINO/3-Jorge.jpg' },
                            { name: 'Angélica Rosario Espinoza Morales', number: 4, photo: '/PARLAMENTO ANDINO/4-Angelica.jpg' },
                            { name: 'Richard Michael Hidalgo Moreno', number: 5, photo: '/PARLAMENTO ANDINO/5-Richard.jpg' },
                            { name: 'Williams Mijaíl Sosa Gonzalo', number: 7, photo: '/PARLAMENTO ANDINO/7-Williams.jpg' },
                            { name: 'Juán Rojas Vargas', number: 9, photo: '/PARLAMENTO ANDINO/9-Juan.jpg' },
                            { name: 'Ana Ysabel Narvaez Llancachahua', number: 10, photo: '/PARLAMENTO ANDINO/10-Ana.jpg' },
                            { name: 'Gustavo Puma Cáceres', number: 11, photo: '/PARLAMENTO ANDINO/11-Gustavo.jpg' },
                            { name: 'Fány Idelba Chahuares Arpasi', number: 12, photo: '/PARLAMENTO ANDINO/12-Fany.jpg' },
                            { name: 'Andersón Duberli', number: 13, photo: '/PARLAMENTO ANDINO/13-Anderson.jpg' },
                            { name: 'Jorge Luis Cardenas Soto', number: 15, photo: '/PARLAMENTO ANDINO/15-Jorge.jpg' },
                            { name: 'Silvia Yanet Piñas Colonio', number: 16, photo: '/PARLAMENTO ANDINO/16-Silvia.jpg' }]
    }
    ];
    const currentStep = ref(0);

    // Computed property para acceder fácilmente a los datos del modo actual
    const currentMode = computed(() => tableModes[currentStep.value]);

    // Funcionalidad del modal
    const showModal = ref(false);
    const activePreferentialBox = ref(null);
    const openModal = (boxNumber) => {
        if (!currentVotes.value.box1) {
            alert('Por favor, marque primero el símbolo del partido antes de ingresar su voto preferencial.');
            return;
        }

        activePreferentialBox.value = boxNumber;
        showModal.value = true;
    };

    const savePreferentialVote = (candidateNumber) => {
        // Permitir guardar si el voto es NULO
        if (candidateNumber === null) {
            currentVotes.value[`preferentialVote${activePreferentialBox.value}`] = null;
            showModal.value = false;
            return;
        }

        const v = currentVotes.value;
        const otherBoxNumber = activePreferentialBox.value === 1 ? 2 : 1;
        const otherBoxKey = `preferentialVote${otherBoxNumber}`;

        // Validación: Evitar que el voto preferencial se repita
        // Verificamos si la otra caja existe en este modo y si tiene el mismo número
        if (otherBoxKey in v && v[otherBoxKey] === candidateNumber) {
            alert(`El candidato #${candidateNumber} ya ha sido seleccionado en la otra casilla. Por favor, elija un candidato diferente o deje la casilla en blanco.`);
            return; // Detenemos la función aquí, el modal no se cierra
        }

        currentVotes.value[`preferentialVote${activePreferentialBox.value}`] = candidateNumber;
        showModal.value = false;
    };

    // Guardamos los votos de cada etapa en un arreglo
    const votes = ref([
    { box1: false, box2: false}, // Votos paso 0
    { box1: false, preferentialVote1: null, preferentialVote2: null}, // Votos paso 1
    { box1: false, preferentialVote1: null}, // Votos paso 2
    { box1: false, preferentialVote1: null, preferentialVote2: null}, // Votos paso 3
    { box1: false, preferentialVote1: null, preferentialVote2: null}  // Votos paso 4
    ]);

    const currentVotes = computed(() => votes.value[currentStep.value]);

    // Funciones de los botones
    const alternateVote = (boxNumber) => {
        currentVotes.value[`box${boxNumber}`] = !currentVotes.value[`box${boxNumber}`];
    };

    const goForward = () => {
        // Validamos que se hayan marcado las cajas requeridas
        const v = currentVotes.value;
        const required = currentMode.value.requiredBox;
        
        let isValid = false;
        if (required === 1) isValid = v.box1;

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
                    <div v-if="currentStep !== 0" class="vote-box">
                    </div>
                </td>
                <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div class="vote-box" >
                    </div>
                </td>
                <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div v-if="currentStep !== 2" class="vote-box">
                    </div>
                </td>
            </tr>

            <tr id="main-row">
                <td :colspan="currentStep === 0 ? 8 : 6" :style="{ backgroundColor: currentMode.backgroundColor }">
                    PARTIDO JUNTOS POR EL PERÚ
                </td>
                <td @click="alternateVote(1)" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div :class="`vote-box ${currentVotes.box1 ? '' : 'beat-box'}`" 
                        style="background-image: url('/Logo_juntos_por_el_Peru.svg');">
                        <span v-if="currentVotes.box1" class="x-mark">X</span>
                    </div>
                </td>

                <td v-if="currentStep === 0" @click="alternateVote(2)" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div :class="`vote-box ${currentVotes.box2 ? '' : 'pulsing-box'}`" 
                        style="background-image: url('/PRESIDENTE/Roberto.png');">
                        <span v-if="currentVotes.box2" class="x-mark">X</span>
                    </div>
                </td>

                <td v-if="currentStep !== 0" @click="openModal(1)" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div :class="`vote-box ${currentVotes.preferentialVote1 ? '' : 'pulsing-box'}`">
                        <span v-if="currentVotes.preferentialVote1" class="x-mark">{{ currentVotes.preferentialVote1 }}</span>
                    </div>
                </td>

                <td v-if="currentStep !== 0" @click="openModal(2)" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div v-if="[1, 3, 4].includes(currentStep)" :class="`vote-box ${currentVotes.preferentialVote2 ? '' : 'pulsing-box'}`">
                        <span v-if="currentVotes.preferentialVote2" class="x-mark">{{ currentVotes.preferentialVote2 }}</span>
                    </div>
                </td>
            </tr>

            <tr>
                <td colspan="6" :style="{ backgroundColor: currentMode.backgroundColor }">CANDIDATO Y</td>
                <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div v-if="currentStep !== 0" class="vote-box">

                    </div>
                </td>
                <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div class="vote-box" >
                    </div>
                </td>
                <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                    <div v-if="currentStep !== 2" class="vote-box">
                    </div>
                </td>
            </tr>
        </tbody>
    </table>

    <div id="message-container">
        <h3>Toma en cuenta:</h3>
        <p>
            1. Marcar como mínimo el logo del partido (Símbolo).<br>
            2. Se debe marcar el símbolo antes de ingresar el voto preferencial.<br>
            3. El voto preferencial es opcional.<br>
            4. El voto preferencial no puede repetirse.
        </p>
    </div>

    <div class="button-container">
        <button id="btnBack" class="black-button" @click="goBack" :disabled="currentStep === 0">ATRÁS</button>
        <button id="btnForward" class="black-button" @click="goForward">
        {{ currentStep === tableModes.length - 1 ? 'FINALIZAR' : 'SIGUIENTE' }}
        </button>
    </div>

    <PreferentialModal 
        v-if="showModal" 
        :candidates="currentMode.preferentialVotes"
        @selectCandidate="savePreferentialVote" 
        @cancel="showModal = false"
    />

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
        cursor:not-allowed;
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

    .vote-box:hover {
        background-color: #f0f0f0;
    }

    .card{
        width: 100%;
        background-color: white;
        color: black;
        border: 1px solid black;
        margin-bottom: 3rem;
        padding: 1rem;
    }

    @keyframes boxPulse {
        0% { filter: brightness(1); }
        50% { filter: brightness(0.8); } /* Oscurece el fondo ligerísimamente */
        100% { filter: brightness(1); }
    }

    .pulsing-box{
        animation: boxPulse 1s infinite;
        cursor: pointer;
    }

    .pulsing-box:hover{
        animation: none;
        background-color: lightpink !important;
    }

    .beat-box {
        animation: heartbeatBox 1.5s infinite;
        cursor: pointer;
    }

    @keyframes heartbeatBox {
        0% { 
            transform: scale(1); 
            box-shadow: 0 0 0 rgba(229, 28, 36, 0); 
        }
        50% { 
            transform: scale(1.08); /* Crece un 8% */
            box-shadow: 0 0 15px rgba(229, 28, 36, 0.6); /* Sombra roja externa */
            border-color: #E51C24;
        }
        100% { 
            transform: scale(1); 
            box-shadow: 0 0 0 rgba(229, 28, 36, 0); 
        }
    }

    #main-row td:first-child,
    #main-row td:last-child {
        position: relative;
    }

    #main-row td:first-child::before {
        content: "▶";
        position: absolute;
        left: -30px;
        top: 50%; /* Lo centra verticalmente */
        color: lightcoral;
        font-size: 1.5rem;
        animation: sideArrows 0.8s infinite alternate;
    }

    #main-row td:last-child::after {
        content: "◀";
        position: absolute;
        right: -30px;
        top: 50%;
        color: lightcoral;
        font-size: 1.5rem;
        animation: sideArrows 0.8s infinite alternate;
    }

    /* 4. La animación que las hace aparecer y desaparecer */
    @keyframes sideArrows {
        0% { 
            opacity: 0; /* Totalmente invisibles */
            transform: translateY(-50%) scale(0.8); /* Ligeramente más pequeñas */
        }
        100% { 
            opacity: 1; /* Totalmente visibles */
            transform: translateY(-50%) scale(1.2); /* Ligeramente más grandes */
        }
    }

    #message-container{
        background-color: darkgoldenrod;
        color: white;
        border: 2px solid goldenrod;
        width: 100%;
        display: flex;
        flex-direction: column;
        margin-bottom: 1rem;
        padding: 1.5rem;
    }
</style>
