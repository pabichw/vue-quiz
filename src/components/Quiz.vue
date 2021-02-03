<template>
    <div>
        <h2>Quiz</h2>
        <div v-show="isFetching">Loading....</div>
        <div class="question-container" v-if="question">
            <h3 class="question-container__title">{{question.question}}</h3>
            <ol class="answers">
                <li 
                    :style="givenAnswers[currentStep] === name && 'color: #425B6E; border-color: #87ECFA; background: #fafafa;'" 
                    v-for="(value, name) in question.answers" 
                    v-show="value"
                    :key="`${value}-${name}`"
                    @click="markAnswer(name)"
                >
                    <span>{{value}}</span>
                </li>
            </ol>
            <div class="question-container__bottom">
                <div class="bottom__indicator">Question: {{currentStep + 1}} out of {{questions.length}}</div>
                <button v-if="currentStep <= questions.length" @click="goToNextQuestion" :disabled="!givenAnswers[currentStep]">Next</button>
            </div>
        </div>
        <div v-if="currentStep + 1 > questions.length">
            <div class="result__answers">
                Your answers: <br/>
                {{givenAnswers}}
            </div>
            <div class="result__score">Correct: {{points}} / {{questions.length}}</div>
            <div class="result__conclusion">{{this.makeConclusion(this.points)}}</div>
            <button @click="resetQuiz">Try again</button>
        </div>
    </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from 'vue-property-decorator';

interface Question {
    question: string;
    description?: string;
    answers: any;
    correct_answer: string;
}

@Component
export default class Quiz extends Vue {
    private questions: Question[];
    private question: Question | null;
    private isFetching: boolean;
    private currentStep: number;
    private points: number;

    private givenAnswers: string[];

    constructor() {
        super();
        this.questions = [];
        this.question = null;
        this.givenAnswers = [];
        this.isFetching = false;
        this.currentStep = 0;
        this.points = 0;
    }

    public async mounted() {
        await this.fetchQuestions();
        this.setQuestion(0);
        this.givenAnswers = Array(this.questions.length);
    }

    public data() {
        return {
            questions: this.questions,
            question: this.question,
            isFetching: this.isFetching,
            currentStep: this.currentStep,
            givenAnswers: this.givenAnswers,
            points: this.points,
        };
    }

    public setQuestion(index: number) {
        if (index <= this.questions.length) {
            this.currentStep = index;
            this.question = this.questions[index];
        }
    }

    public async fetchQuestions() {
        this.isFetching = true;
        await fetch('https://quizapi.io/api/v1/questions?apiKey=3Cjv63lrAxYtJQPDeaipeO46NYMSxzVjkNGjToEu&limit=10')
            .then((res) => res.json())
            .then((data) => this.questions = data)
            .finally(() => this.isFetching = false);
    }

    public markAnswer(ans: string) {
        Vue.set(this.givenAnswers, this.currentStep, ans);
    }

    public goToNextQuestion() {
        if (this.currentStep < this.questions.length) {
            this.setQuestion(this.currentStep + 1);
        }
    }

    public makeConclusion(points: number) {
        return (
            points < 3 ? 'Are you serious?' :
            points < 5 ? 'Not even trying...' :
            points < 8 ? 'OK...ish' :
            'Enough'
        );
    }

    @Watch('currentStep')
    public countPoints() {
        let points = 0;
        this.givenAnswers.forEach((ans, idx) => {
            if (this.questions[idx].correct_answer === ans) {
                points = points + 1;
            }
        });

        this.points = points;
    }

    public resetQuiz() {
        this.currentStep = 0;
        this.givenAnswers = [];
        this.fetchQuestions();
        this.setQuestion(0);
        this.points = 0;
    }
}
</script>

<style lang="scss">
    .question-container { 
        max-width: 1200px;
        padding: 1rem 3rem;
        margin: 3rem auto 0;
    }
    
    .question-container__title {
        margin-bottom: 2rem;
    }
    
    .question-container__bottom {
        margin-top: 3rem;
    }

    .bottom__indicator { 
        margin-bottom: 1rem;
    }
    
    .answers > li {
        position: relative;
        cursor: pointer;
        margin: 0.5rem 0;
        
        border: 2px solid #85B6DE;
        border-radius: 10px;
        padding: 1rem 2rem;

        list-style: none;

        &:hover {
            transition: border .5s ease;
            border: 2px solid #87ECFA;

                &::before {
                    transition: background-color 0.3s ease;
                    background-color: #87ECFA;
                }
            
        } 

        &:first-child {
            counter-reset:index;
        }

        &::before {
            content: counter(index, upper-alpha) '.';
            counter-increment:index;
            background:  #aac7df;
            border-radius: 2px;
            position:absolute;
            left: 1rem;
            z-index:10;
            width: 1.5rem;
            height: 1.5rem;
            color: white;
            font-size: 1.05rem;
        }
    } 
    
    .result__score {
        font-size: 2rem;
        font-weight: 600;
        margin: 1rem 0;
    }

    .result__conclusion {
        margin-bottom: 2rem;
    }
</style>