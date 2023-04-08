<script setup>
import { reactive, computed } from 'vue';

const form = reactive({
    firstAccumulate: { min: 0, max: 15, value: 0 },
    firstExam: { min: 0, max: 20, value: 0 },
    secondExam: { min: 0, max: 35, value: 0 },
    finalExam: { min: 0, max: 30, value: 0 },
    finalGrade: { min: 0, max: 100, value: 0 }
});

const letterGrades = [
    { min: 90, max: 100, letter: 'A'},
    { min: 80, max: 89, letter: 'B'},
    { min: 70, max: 79, letter: 'C'},
    { min: 0, max: 69, letter: 'F'},
    { min: -1, max: Number.NEGATIVE_INFINITY, letter: 'Invalid grade'},
];

const submittedGrades = () => {
    form.finalGrade.value = 0;
    for (const key of Object.keys(form).filter(key => key !== 'finalGrade')) {
        form.finalGrade.value += form[key].value;
    }
};

const onGradeChange = (formProperty, $event) => {
    const value = Number($event.target.value);
    if(value < 0) form[formProperty].value = 0;
    else if(value < form[formProperty].min || value > form[formProperty].max) {
        form[formProperty].value = 0;
    } else form[formProperty].value = value;
    submittedGrades();
}

const literalGrade = computed(() => {
    if(!form.finalGrade.value) return 'N/A';
    return letterGrades.find((grade) => 
        form.finalGrade.value >= grade.min && form.finalGrade.value <= grade.max)?.letter ?? 'N/A';
});

const isInvalid = (formProperty) => 
    form[formProperty].value < 0 || (form[formProperty].value < form[formProperty].min || form[formProperty].value > form[formProperty].max);

const showErrorMessage = (input) => 
    `La calificación debe ser un número positivo entre ${ input.min } y ${ input.max }`;

</script>

<template>
    <div class="flex flex-col space-y-4 md:flex-row md:space-x-4 md:space-y-0">
        <div class="flex flex-col w-full md:w-1/6">
            <label 
                for="firstAccumulate" 
                class="text-gray-700 font-bold mb-2"
            >1er acumulado</label>
            <input 
                id="firstAccumulate" 
                type="number" 
                class="text-black py-2 px-3 border border-gray-400 rounded-md appearance-none"
                :class="{ 'input-error': isInvalid('firstAccumulate') }"
                v-model="form.firstAccumulate.value" 
                :min="form.firstAccumulate.min"
                :max="form.firstAccumulate.max"
                @change="onGradeChange('firstAccumulate', $event)"
            >
            <p 
                v-show="isInvalid('firstAccumulate')"
                class="error-message"
            > {{ showErrorMessage(form.firstAccumulate) }}
            </p>

        </div>
        <div class="flex flex-col w-full md:w-1/6">
            <label 
                for="firstExam" 
                class="text-gray-700 font-bold mb-2"
            >1er parcial</label>
            <input 
                id="firstExam" 
                type="number" 
                class="text-black py-2 px-3 border border-gray-400 rounded-md appearance-none"
                :class="{ 'input-error': isInvalid('firstExam') }"
                v-model="form.firstExam.value" 
                :min="form.firstExam.min"
                :max="form.firstExam.max"
                @change="onGradeChange('firstExam', $event)"
            >
            <p 
                v-show="isInvalid('firstExam')"
                class="error-message"
            > {{ showErrorMessage(form.firstExam) }}
            </p>
        </div>
        <div class="flex flex-col w-full md:w-1/6">
            <label 
                for="secondExam" 
                class="text-gray-700 font-bold mb-2"
            >2do parcial</label>
            <input 
                id="secondExam"
                type="number" 
                class="text-black py-2 px-3 border border-gray-400 rounded-md appearance-none"
                :class="{ 'input-error': isInvalid('secondExam') }"
                v-model="form.secondExam.value" 
                :min="form.secondExam.min"
                :max="form.secondExam.max"
                @change="onGradeChange('secondExam', $event)"
            >
            <p 
                v-show="isInvalid('secondExam')"
                class="error-message"
            > {{ showErrorMessage(form.secondExam) }}
            </p>
        </div>
        <div class="flex flex-col w-full md:w-1/6">
            <label 
                for="finalExam" 
                class="text-gray-700 font-bold mb-2"
            >Examen final</label>
            <input 
                id="finalExam" 
                type="number" 
                class="text-black py-2 px-3 border border-gray-400 rounded-md appearance-none"
                :class="{ 'input-error': isInvalid('finalExam') }"
                v-model="form.finalExam.value" 
                :min="form.finalExam.min"
                :max="form.finalExam.max"
                @change="onGradeChange('finalExam', $event)"
            >
            <p 
                v-show="isInvalid('finalExam')"
                class="error-message"
            > {{ showErrorMessage(form.finalExam) }}
            </p>
        </div>
        <div class="flex flex-col w-full md:w-1/6">
            <label for="finalGrade" class="text-gray-700 font-bold mb-2">Resultado</label>
            <input 
                id="finalGrade" 
                type="text" 
                class="text-black py-2 px-3 border border-gray-400 rounded-md appearance-none"
                v-model="form.finalGrade.value" 
                readonly
            >
        </div>
        <div class="flex flex-col w-full md:w-1/6">
                <label for="literalGrade" class="text-gray-700 font-bold mb-2">Letra asignada</label>
                <input 
                    id="literalGrade" 
                    type="text" 
                    class="text-black py-2 px-3 border border-gray-400 rounded-md appearance-none"
                    v-model="literalGrade" 
                    readonly
                >
        </div>
    </div>
</template>
<style scoped>
  .input-error {
    border: 1px solid red;
  }

  .error-message {
    color: red;
    margin-top: 0.5rem;
  }
</style>