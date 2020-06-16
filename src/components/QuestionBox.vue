<template>
    <div class="question-box-container">
        <b-jumbotron class="mt-3">
            <template v-slot:lead>
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">

            <b-list-group 
                v-for="(answer, index) in shuffledAnswers" 
                :key="index"
                @click="selectedAnswer(index)"
                >
                <b-list-group-item 
                :class="answerClass(index)">
                    {{ answer }}
                </b-list-group-item>
            </b-list-group>
            
            <!-- <b-button @click="previous" class="mt-3" variant="success" href="#">
                <svg viewBox="0 0 16 16" width="1em" height="1em" focusable="false" role="img" alt="icon" fill="currentColor" class="bi-arrow-left mx-auto b-icon bi" data-v-8892b924="">
                    <g data-v-8892b924="">
                        <path fill-rule="evenodd" d="M5.854 4.646a.5.5 0 0 1 0 .708L3.207 8l2.647 2.646a.5.5 0 0 1-.708.708l-3-3a.5.5 0 0 1 0-.708l3-3a.5.5 0 0 1 .708 0z">
                        </path>
                        <path fill-rule="evenodd" d="M2.5 8a.5.5 0 0 1 .5-.5h10.5a.5.5 0 0 1 0 1H3a.5.5 0 0 1-.5-.5z">
                        </path>
                    </g>
                </svg>
                Prev
            </b-button> -->
            
            <b-button @click="next" class="ml-2 mt-3" variant="success" href="#">
                Next
                <svg viewBox="0 0 16 16" width="1em" height="1em" focusable="false" role="img" alt="icon" fill="currentColor" class="bi-arrow-right mx-auto b-icon bi" data-v-8892b924="">
                    <g data-v-8892b924="">
                        <path fill-rule="evenodd" d="M10.146 4.646a.5.5 0 0 1 .708 0l3 3a.5.5 0 0 1 0 .708l-3 3a.5.5 0 0 1-.708-.708L12.793 8l-2.647-2.646a.5.5 0 0 1 0-.708z"></path>
                        <path fill-rule="evenodd" d="M2 8a.5.5 0 0 1 .5-.5H13a.5.5 0 0 1 0 1H2.5A.5.5 0 0 1 2 8z"></path>
                    </g>
                </svg>
            </b-button>

            <b-button class="ml-2 mt-3" 
                variant="primary"
                @click = "submitAnswer"
                :disabled="selectedIndex === null || answered">
                Submit
            </b-button>

        </b-jumbotron>
    </div>
</template>

<script>

    export default{
       
        props: {
            currentQuestion: Object,
            next: Function,
            previous: Function,
            increment: Function
        },
        data: function() {
            return {
                selectedIndex: null,
                correctIndex: null,
                shuffledAnswers: [],
                answered: false
            }
        },
        computed: {
            answers() {
                let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
                return answers;
            }
        },
        watch: {
            currentQuestion: {
                immediate: true,
                handler(){
                    this.selectedIndex = null;
                    this.answered = false;
                    this.shuffleAnswers();
                }
            },
        },
        methods: {
            selectedAnswer(index){
                this.selectedIndex = index;
            },
            shuffleAnswers(){
                this.shuffledAnswers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
                for (let i = this.shuffledAnswers.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [this.shuffledAnswers[i], this.shuffledAnswers[j]] = [this.shuffledAnswers[j], this.shuffledAnswers[i]];
                }
                this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
            },
            submitAnswer(){
                let isCorrect = false;
                if (this.selectedIndex === this.correctIndex){
                    isCorrect = true;
                }
                this.answered = true;
                this.increment(isCorrect);
            },
            answerClass(index){
                let answeredClass = '';
                if (!this.answered && this.selectedIndex === index){
                    answeredClass = 'selected';
                }
                else if(this.answered && this.correctIndex === index){
                    answeredClass = 'correct';
                }
                else if(this.answered && this.selectedIndex === index && this.correctIndex !== index){
                    answeredClass = 'incorrect';
                }
                return answeredClass;
            }
        }
    }
</script>


<style scoped>
    .list-group-item{
        font-weight: bold;
        cursor: pointer;
        margin-bottom: 5px;
        transition: 0.3s all;
    }
    .list-group-item:hover{
        background-color: lightgray;
    }
    .list-group-item.selected{
        background-color:lightseagreen;
    }
    .list-group-item.correct{
        background-color: lightgreen;
    }
    .list-group-item.incorrect{
        background-color: lightcoral;
    }
</style>