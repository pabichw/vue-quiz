<template>
    <div>
        <p>Quiz</p>
        <div v-if="isFetching">Loading....</div>
        <div v-if="question">
            <h3>{{question.question}}</h3>
            <ol class="questions">
                <li :style="givenAnswers[currentStep] === name ? 'color: red' : 'color: black'" v-for="(value, name) in question.answers" :key="`${value}-${name}`" @click="markAnswer(name)">{{value}}</li>
            </ol>
            <p>Question: {{currentStep + 1}} out of {{questions.length}}</p>
            <button v-if="currentStep <= questions.length" @click="goToNextQuestion" :disabled="!givenAnswers[currentStep]">Next</button>
        </div>
        <div v-if="currentStep + 1 > questions.length">
            Your answers:
            {{givenAnswers}}
            Correct: {{points}} / {{questions.length}}
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
    .questions > li {
        cursor: pointer;
        margin: 0.5rem 0;
    } 
</style>