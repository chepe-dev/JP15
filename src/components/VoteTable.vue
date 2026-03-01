<script setup>
import { ref, computed, nextTick, onMounted, watch } from 'vue';
import PreferentialModal from './PreferentialModal.vue';
import html2canvas from 'html2canvas';
import CustomAlert from './CustomAlert.vue';

// CONFIGURACIÓN DE LA ALERTA
const alertConfig = ref({
    show: false,
    title: '',
    message: '',
    type: 'warning'
});

// Función auxiliar para llamar la alerta fácilmente
const triggerAlert = (message, title = 'Atención', type = 'warning') => {
    alertConfig.value = { show: true, title, message, type };
};

const captureArea = ref(null);
const isDownloading = ref(false);

// La función que genera y descarga la imagen
const downloadResults = async () => {
    if (!captureArea.value) return;

    try {
        isDownloading.value = true; // 1. Encendemos el modo exportación
        await nextTick(); // 2. Esperamos a que el HTML cambie de forma

        const canvas = await html2canvas(captureArea.value, {
            scale: 2,
            backgroundColor: '#ffffff',
            useCORS: true
        });

        const imageURL = canvas.toDataURL("image/png");

        const link = document.createElement('a');
        link.href = imageURL;
        link.download = 'Mis_Resultados_JP.png';
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);

    } catch (error) {
        console.error("Error al generar la imagen:", error);
    } finally {
        isDownloading.value = false; // 3. Devolvemos la vista del celular a la normalidad
    }
};

    const tableModes = [
    {
        title: 'PRESIDENTE Y VICEPRESIDENTES',
        subtitle: '',
        backgroundColor: '#DCF0FF',
    },
    {
        title: 'SENADORES',
        subtitle: 'A NIVEL NACIONAL',
        backgroundColor: '#FFE1EB',
        preferentialVotes: [{ name: 'José Mercedes Castillo Terrones', number: 1, photo: '/SENADORES NACIONALES/1-Jose.jpg' },
                            { name: 'Bernardo Jaime Quito Sarmiento', number: 7, photo: '/SENADORES NACIONALES/7-Bernardo.jpeg' },
                            { name: 'Margot Palacios Huamán', number: 8, photo: '/SENADORES NACIONALES/8-Margot.jpg' }]
    },
    {
        title: 'SENADORES',
        subtitle: 'A NIVEL REGIONAL (ANCASH)',
        backgroundColor: '#F5E6D7',
        preferentialVotes: [{ name: 'Elías Marcial Varas Meléndez', number: 1, photo: '/SENADORES REGIONALES/1-Elias.png' },
                            { name: 'Domitila Gina Silva Castillo', number: 2, photo: '/SENADORES REGIONALES/2-Domitila.jpeg' }]
    },
    {
        title: 'DIPUTADOS',
        subtitle: 'A NIVEL REGIONAL (ANCASH)',
        backgroundColor: '#E1F5E1',
        preferentialVotes: [{ name: 'Daniel Jefferson Varas Seguín', number: 1, photo: '/DIPUTADOS/1-Daniel.jpg' },
                            { name: 'José Antonio Monzon Mendoza', number: 5, photo: '/DIPUTADOS/5-Jose.jpeg' }]
    },
    {
        title: 'PARLAMENTO ANDINO',
        subtitle: '',
        backgroundColor: '#FFF9C4',
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
            triggerAlert('Por favor, marque primero el símbolo del partido antes de ingresar su voto preferencial.');
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
            triggerAlert(`El candidato #${candidateNumber} ya ha sido seleccionado en la otra casilla. Por favor, elija un candidato diferente o deje la casilla en blanco.`);
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

    // Funcionalidad para mostrar resultado del voto
    const showVoteResult = ref(false);

    const goForward = () => {
        // Validamos que se hayan marcado las cajas requeridas
        const v = currentVotes.value;
        if (!v.box1) {
            triggerAlert(`Por favor, marca el recuadro de Juntos por el Perú antes de avanzar.`);
            return;
        }

        // Si es válido, avanzamos
        if (currentStep.value < tableModes.length - 1) {
            currentStep.value++;
        } else {
            triggerAlert('Has completado el tutorial de votación.', '¡Felicidades!', 'success');
            showVoteResult.value = true;
        }
    };

    const goBack = () => {
        if(showVoteResult.value) {
            showVoteResult.value = false;
            return;
        }
        if (currentStep.value > 0) currentStep.value--;
    };

// --- LÓGICA DE LA ANIMACIÓN DE FILAS ---
const fakeRows = ref([]);
let rowInterval = null;

const playRowAnimation = () => {
    // Llenamos el arreglo con los números del 1 al 13: [1, 2, 3... 13]
    fakeRows.value = Array.from({ length: 13 }, (_, i) => i + 1);
    
    if (rowInterval) clearInterval(rowInterval);
    
    // Cada 80 milisegundos quitamos la fila de MÁS ARRIBA
    rowInterval = setInterval(() => {
        if (fakeRows.value.length > 0) {
            fakeRows.value.shift(); // shift() borra el primer elemento del arreglo
        } else {
            clearInterval(rowInterval);
        }
    }, 100); 
};

// Reproducir al cargar la tabla por primera vez
onMounted(() => {
    playRowAnimation();
});

// Reproducir cada vez que el usuario cambie de paso (siguiente/atrás)
watch(currentStep, () => {
    playRowAnimation();
});
</script>

<template>
    <div class="vote-component-wrapper">
        
        <div v-if="!showVoteResult" class="table-window">

            <table id="vote-table">
                <colgroup>
                    <col style="width: 8.33%;" v-for="i in 12" :key="i">
                </colgroup>
                <thead>
                    <tr id="information-row">
                        <th colspan="3">
                            <img src="../assets/Escudo_nacional_del_Perú.svg" alt="Escudo Perú" class="header-logo">
                        </th>
                        <th colspan="6" class="header-titles">
                            <h2>{{currentMode.title}}</h2>
                            <h3 v-if="currentMode.subtitle">{{ currentMode.subtitle }}</h3>
                        </th>
                        <th colspan="3">
                            <img src="../assets/ONPE.png" alt="Logo ONPE" class="header-logo onpe-logo">
                        </th>
                    </tr>
                    <tr id="instruction-row">
                        <th :colspan="currentStep === 0 ? 12 : 8">
                            MARQUE CON UNA CRUZ <b>+</b> O UN ASPA <b>x</b> DENTRO DEL RECUADRO DEL SÍMBOLO Y/O FOTOGRAFÍA DE SU PREFERENCIA
                        </th>
                        <th v-if="currentStep !== 0" colspan="4" class="pref-instructions">
                            VOTO PREFERENCIAL
                            <h5>SI DESEA COLOQUE DENTRO DEL RECUADRO EL NÚMERO DEL CANDIDATO DE SU PREFERENCIA</h5>
                        </th>
                    </tr>
                </thead>

                <tbody>

                    <tr v-for="i in fakeRows" :key="'fake-' + i" class="fake-row">
                        <td colspan="6" :style="{ backgroundColor: currentMode.backgroundColor }">PARTIDO POLÍTICO {{ i }}</td>
                        <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div v-if="currentStep !== 0" class="vote-box"></div>
                        </td>
                        <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div class="vote-box"></div>
                        </td>
                        <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div v-if="currentStep !== 2" class="vote-box"></div>
                        </td>
                    </tr>

                    <tr>
                        <td colspan="6" :style="{ backgroundColor: currentMode.backgroundColor }">PARTIDO POLÍTICO A</td>
                        <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }"> 
                            <div v-if="currentStep !== 0" class="vote-box"></div>
                        </td>
                        <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div class="vote-box"></div>
                        </td>
                        <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div v-if="currentStep !== 2" class="vote-box"></div>
                        </td>
                    </tr>

                    <tr>
                        <td colspan="6" :style="{ backgroundColor: currentMode.backgroundColor }">PARTIDO POLÍTICO B</td>
                        <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }"> 
                            <div v-if="currentStep !== 0" class="vote-box"></div>
                        </td>
                        <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div class="vote-box"></div>
                        </td>
                        <td class="clickable-cell" colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div v-if="currentStep !== 2" class="vote-box"></div>
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
                        <td colspan="6" :style="{ backgroundColor: currentMode.backgroundColor }">PARTIDO POLÍTICO C</td>
                        <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div v-if="currentStep !== 0" class="vote-box"></div>
                        </td>
                        <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div class="vote-box"></div>
                        </td>
                        <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div v-if="currentStep !== 2" class="vote-box"></div>
                        </td>
                    </tr>

                    <tr>
                        <td colspan="6" :style="{ backgroundColor: currentMode.backgroundColor }">PARTIDO POLÍTICO D</td>
                        <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div v-if="currentStep !== 0" class="vote-box"></div>
                        </td>
                        <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div class="vote-box"></div>
                        </td>
                        <td colspan="2" :style="{ backgroundColor: currentMode.backgroundColor }">
                            <div v-if="currentStep !== 2" class="vote-box"></div>
                        </td>
                    </tr>

                </tbody>
            </table>

        </div>
        <div v-if="!showVoteResult" id="message-container">
            <h3>Toma en cuenta:</h3>
            <p>
                1. JP se encuentra en la posición #16 de la cédula!<br>
                2. Marcar como mínimo el logo del partido (Símbolo).<br>
                3. Se debe marcar el símbolo antes de ingresar el voto preferencial.<br>
                4. El voto preferencial es opcional.<br>
                5. El voto preferencial no puede repetirse.
            </p>
        </div>

        <div v-if="showVoteResult" class="results-container">
            
            <div ref="captureArea" class="capture-wrapper" :class="{ 'exporting': isDownloading }" style="background-color: #ffffff; padding: 2rem; border-radius: 12px;">
            
                <div class="results-grid">
                    <div class="result-card">
                        <div class="result-header">
                            <h4 style="color: red;">YO VOTARÉ ASÍ POR JUNTOS POR EL PERÚ.</h4>
                        </div>
                        <div class="result-body" style="background-color: #ffffff;">
                            <span class="party-name" style="color: green;">¡TÚ TAMBIÉN PUEDES HACERLO!</span>
                            <span class="party-name">INGRESA A: <b>JPvoto.com</b></span>
                        </div>
                    </div>

                    <div v-for="(mode, index) in tableModes" :key="index" class="result-card">
                        
                        <div class="result-header">
                            <h4>{{ mode.title }}</h4>
                            <span v-if="mode.subtitle">{{ mode.subtitle }}</span>
                        </div>

                        <div class="result-body" :style="{ backgroundColor: mode.backgroundColor }">
                            <span class="party-name">JUNTOS POR EL PERÚ</span>

                            <div class="result-boxes">
                                <div class="vote-box result-box" style="background-image: url('/Logo_juntos_por_el_Peru.png');">
                                    <span v-if="votes[index].box1" class="x-mark">X</span>
                                </div>

                                <div v-if="index === 0 && 'box2' in votes[index]" class="vote-box result-box" style="background-image: url('/PRESIDENTE/Roberto.png');">
                                    <span v-if="votes[index].box2" class="x-mark">X</span>
                                </div>

                                <div v-if="'preferentialVote1' in votes[index]" class="vote-box result-box">
                                    <span v-if="votes[index].preferentialVote1 !== null" class="x-mark">{{ votes[index].preferentialVote1 }}</span>
                                </div>

                                <div v-if="'preferentialVote2' in votes[index]" class="vote-box result-box">
                                    <span v-if="votes[index].preferentialVote2 !== null" class="x-mark">{{ votes[index].preferentialVote2 }}</span>
                                </div>
                            </div>
                        </div> 
                    </div>
                
                </div> 
            </div> 
                        <div class="results-footer">
                <img src="../assets/IMAGEN FINAL.png" alt="Mascota Juntos por el Perú" class="party-mascot" />
                
                <button class="share-button" @click="downloadResults">
                    DESCARGAR RESULTADOS
                </button>
            </div>

        </div>

        <div class="button-container">
            <button id="btnBack" class="black-button" @click="goBack" :disabled="currentStep === 0">ATRÁS</button>
            <button id="btnForward" class="black-button" @click="goForward">
                {{ currentStep === tableModes.length - 1 ? 'FINALIZAR' : 'SIGUIENTE' }}
            </button>
        </div>
    </div>

    <PreferentialModal 
        v-if="showModal" 
        :candidates="currentMode.preferentialVotes"
        @selectCandidate="savePreferentialVote" 
        @cancel="showModal = false"
    />

    <CustomAlert 
        :show="alertConfig.show"
        :title="alertConfig.title"
        :message="alertConfig.message"
        :type="alertConfig.type"
        @close="alertConfig.show = false"
    />
</template>

<style scoped>

.party-name b{
    color: blue;
}
/* Contenedor principal para restringir el ancho global */
.vote-component-wrapper {
    width: 100%;
    max-width: 650px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1.5rem;
}

/* =========================================
   ESTILOS DE LA TABLA
========================================= */
#vote-table {
    border-collapse: separate;
    border: none;
    table-layout: fixed; /* Mantiene las columnas estrictas */
    width: 100%; /* Ahora la tabla es fluida */
    border-spacing: 0 0.25rem;
    font-weight: bold;
    font-size: clamp(0.7rem, 2.5vw, 1rem); /* Texto general elástico */
}

/* Reducimos el padding en celdas para que quepan en celulares */
#vote-table td {
    padding: clamp(0.2rem, 1.5vw, 1rem);
    border-bottom: 0.75rem solid white;
    text-align: center;
}

/* Títulos del encabezado */
.header-titles h2 {
    margin: 0;
    font-size: clamp(0.9rem, 3vw, 1.5rem);
}
.header-titles h3 {
    margin: 0;
    font-size: clamp(0.7rem, 2vw, 1rem);
    color: #444;
}

/* Imágenes del encabezado responsivas */
.header-logo {
    width: clamp(40px, 12vw, 80px);
    height: auto;
    object-fit: contain;
}

#information-row th:first-child { text-align: left; }
#information-row th:last-child { text-align: right; }

#instruction-row th {
    padding: clamp(0.5rem, 2vw, 1rem);
    background-color: #ccc;
    font-size: clamp(0.6rem, 2vw, 0.9rem);
}
.pref-instructions h5 {
    margin: 0.2rem 0 0 0;
    font-size: clamp(0.5rem, 1.5vw, 0.75rem);
}

/* Las "X" y "+" de las instrucciones */
#vote-table b {
    border: 1px solid black;
    background-color: white;
    font-size: clamp(0.9rem, 3vw, 1.2rem);
    padding: 0px 4px;
    display: inline-block;
}

/* =========================================
   CAJAS DE VOTACIÓN
========================================= */
.vote-box {
    width: 95%;
    margin: 0 auto; /* Centra la caja en su td */
    background-color: white;
    border: 2px solid black;
    /* La altura se ajusta en móviles para no deformar la tabla */
    height: clamp(35px, 8vw, 50px);
    background-position: center;
    background-repeat: no-repeat;
    background-size: contain;
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;
    cursor: not-allowed;
    transition: background-color 0.2s;
}

#vote-table td:has(.vote-box) {
    padding: 0; /* Quitamos el padding si hay caja para aprovechar espacio */
}

.x-mark {
    font-size: clamp(1.5rem, 6vw, 3rem); /* La X se achica en celulares */
    font-weight: bold;
    color: #000;
    font-family: Arial, sans-serif;
    user-select: none;
    position: absolute; 
}

.vote-box:hover {
    background-color: #f0f0f0;
}

/* =========================================
   ANIMACIONES
========================================= */
@keyframes boxPulse {
    0% { filter: brightness(1); }
    50% { filter: brightness(0.8); } 
    100% { filter: brightness(1); }
}

.pulsing-box {
    animation: boxPulse 1s infinite;
    cursor: pointer;
}
.pulsing-box:hover {
    animation: none;
    background-color: lightpink !important;
}

.beat-box {
    animation: heartbeatBox 1.5s infinite;
    cursor: pointer;
}
@keyframes heartbeatBox {
    0% { transform: scale(1); box-shadow: 0 0 0 rgba(229, 28, 36, 0); }
    50% { 
        transform: scale(1.05); 
        box-shadow: 0 0 10px rgba(229, 28, 36, 0.6); 
        border-color: #E51C24;
    }
    100% { transform: scale(1); box-shadow: 0 0 0 rgba(229, 28, 36, 0); }
}

/* Flechas laterales animadas */
#main-row td:first-child,
#main-row td:last-child {
    position: relative;
}

#main-row td:first-child::before {
    content: "▶";
    position: absolute;
    /* En PC están lejos, en celular se acercan para no salirse de la pantalla */
    left: clamp(-15px, -3vw, -30px);
    top: 50%;
    transform: translateY(-50%);
    color: lightcoral;
    font-size: clamp(1rem, 3vw, 1.5rem);
    animation: sideArrowsLeft 0.8s infinite alternate;
}

#main-row td:last-child::after {
    content: "◀";
    position: absolute;
    right: clamp(-15px, -3vw, -30px);
    top: 50%;
    transform: translateY(-50%);
    color: lightcoral;
    font-size: clamp(1rem, 3vw, 1.5rem);
    animation: sideArrowsRight 0.8s infinite alternate;
}

@keyframes sideArrowsLeft {
    0% { opacity: 0; transform: translateY(-50%) scale(0.8); }
    100% { opacity: 1; transform: translateY(-50%) translateX(5px) scale(1.1); }
}
@keyframes sideArrowsRight {
    0% { opacity: 0; transform: translateY(-50%) scale(0.8); }
    100% { opacity: 1; transform: translateY(-50%) translateX(-5px) scale(1.1); }
}

/* =========================================
   OTROS CONTENEDORES
========================================= */
#message-container {
    background-color: darkgoldenrod;
    color: white;
    border: 2px solid goldenrod;
    width: 100%;
    padding: clamp(1rem, 3vw, 1.5rem);
    box-sizing: border-box;
    font-size: clamp(0.8rem, 2.5vw, 1rem);
    border-radius: 4px;
}
#message-container h3 { margin-top: 0; }
#message-container p { margin-bottom: 0; line-height: 1.5; }

.button-container {
    display: flex;
    width: 100%;
    gap: 1rem;
}
.black-button {
    border-radius: 4px; /* Bordes ligeramente suaves */
    width: 50%;
    padding: clamp(0.7rem, 2vw, 1rem);
    font-size: clamp(0.9rem, 2.5vw, 1.2rem);
    font-weight: bold;
    color: black;
    background-color: white;
    border: 2px solid black;
    cursor: pointer;
    transition: 0.3s ease;
}
.black-button:disabled {
    background-color: #555;
    border-color: #555;
    cursor: not-allowed;
    opacity: 0.7;
}
.black-button:not(:disabled):hover {
    background-color: black;
    color: white;
}

/* =========================================
   GRILLA DE RESULTADOS (Responsiva)
========================================= */
.results-container {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1.5rem;
    margin-bottom: 2rem;
}

.results-main-title {
    color: #E51C24;
    font-size: clamp(1.5rem, 5vw, 2rem);
    text-align: center;
    margin: 0;
}

.results-grid {
    display: grid;
    /* Diseño por defecto (Mobile-first): 1 sola columna para celulares */
    grid-template-columns: 1fr; 
    gap: 1.5rem;
    width: 100%;
}

/* Cuando la pantalla sea de 600px o más (Tablets y PCs) */
@media (min-width: 600px) {
    .results-grid {
        /* Fuerza exactamente 2 columnas del mismo ancho */
        grid-template-columns: repeat(2, 1fr); 
    }
}

.result-card {
    background-color: white;
    border: 2px solid #ccc;
    border-radius: 8px;
    overflow: hidden; /* Para que el header respete los bordes curvos */
    display: flex;
    flex-direction: column;
    box-shadow: 0 4px 6px rgba(0,0,0,0.05);
    transition: transform 0.2s ease;
}

.result-card:hover {
    transform: translateY(-5px); /* Pequeño salto al pasar el mouse */
    box-shadow: 0 8px 12px rgba(0,0,0,0.1);
}

.result-header {
    padding: 1rem;
    text-align: center;
    border-bottom: 2px solid #ccc;
    min-height: 80px; /* Unifica el tamaño de los encabezados */
    display: flex;
    flex-direction: column;
    justify-content: center;
}

.result-header h4 {
    margin: 0;
    font-size: 1.1rem;
    color: #333;
}

.result-header span {
    font-size: 0.8rem;
    color: #666;
    margin-top: 0.25rem;
}

.result-body {
    padding: 1.5rem 1rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1rem;
    flex-grow: 1; /* Empuja el contenido para llenar el espacio vertical */
    background-color: #fafafa;
}

.party-name {
    font-weight: bold;
    text-align: center;
    font-size: 0.9rem;
    color: #444;
}

.result-boxes {
    display: flex;
    gap: 1rem;
    justify-content: center;
    width: 100%;
}

/* Reutilizamos la clase .vote-box pero la ajustamos para la tarjeta */
.result-box {
    width: 60px !important;
    height: 60px !important;
    border: 2px solid black;
    cursor: default !important; /* Quitamos la manito porque ya no es clickeable */
    background-color: white !important; /* Forzamos blanco para anular el hover de la tabla */
    animation: none !important; /* Anulamos cualquier latido */
}

.result-box .x-mark {
    font-size: 2.5rem !important; /* X un poco más proporcionada para la tarjeta */
}

/* =========================================
   FOOTER DE RESULTADOS (Mascota y Compartir)
========================================= */
.results-footer {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 1.5rem;
    width: 100%;
    padding-top: 2rem;
    border-top: 2px dashed #ccc; /* Línea separadora sutil */
}

/* Responsividad para la imagen de la mascota */
.party-mascot {
    width: clamp(120px, 25vw, 200px); /* Crece y se achica según la pantalla */
    height: auto;
    object-fit: contain;
    /* Un ligero sombreado para que la mascota resalte del fondo blanco */
    filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.15));
    transition: transform 0.3s ease;
}

/* Pequeña animación: la mascota flota suavemente si quieres darle vida */
.party-mascot:hover {
    transform: translateY(-5px) scale(1.05);
}

/* Estilo del botón de Compartir */
.share-button {
    background-color: #5CBE12; /* Verde llamativo */
    color: white;
    border: none;
    /* Padding amplio y border-radius 50px crea la forma de "píldora" */
    padding: 1rem 2rem; 
    border-radius: 50px; 
    font-size: clamp(1rem, 3vw, 1.2rem);
    font-weight: bold;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 0.75rem; /* Separación entre el icono y el texto */
    box-shadow: 0 4px 10px rgba(92, 190, 18, 0.3); /* Sombra verde difuminada */
    transition: all 0.2s ease;
}

.share-button svg {
    /* Asegura que el icono no se deforme */
    flex-shrink: 0; 
}

.share-button:hover {
    background-color: #4da30f; /* Un verde ligeramente más oscuro */
    transform: translateY(-3px); /* Se levanta un poco */
    box-shadow: 0 6px 15px rgba(92, 190, 18, 0.4);
}

.share-button:active {
    transform: translateY(0); /* Efecto de ser presionado */
}

/* =========================================
   MODO EXPORTACIÓN (Lo que captura la foto)
========================================= */
/* Cuando le damos clic a descargar, esta clase toma el control */
.exporting {
    /* Forzamos un ancho fijo de escritorio para que las tarjetas no se aplasten */
    width: 800px !important; 
    max-width: 800px !important;
}

/* Obligamos a la grilla a tener 2 columnas, sin importar el dispositivo */
.exporting .results-grid {
    grid-template-columns: repeat(2, 1fr) !important;
}

/* =========================================
   ANIMACIÓN DE FILAS A LA #16
========================================= */
/* Opcional: Asegurarnos de que las filas de animación no tengan bordes extraños */
.fake-row td {
    border-bottom: 0.75rem solid white;
    height: 60px; /* Forzamos una altura para que el salto sea constante */
}

.table-window {
    width: 100%;
    padding: 0 clamp(15px, 3.5vw, 30px);
    box-sizing: border-box; /* Fundamental para que no se desborde la página */
    /* Limita la altura para que solo se vean ~5 filas a la vez */
    max-height: 550px; 
    
    /* MAGIA: Oculta las filas falsas que están por debajo de la ventana */
    overflow: hidden; 
    background-color: white;
    
    /* Cuando termine la animación y queden solo 3 filas, 
       la ventana se encogerá suavemente para abrazarlas */
    transition: max-height 0.4s ease; 
}
</style>
