<template>
  <div v-if="question">

    <ScoreBoard :scoreHuman="this.scoreHuman" :scoreComputer="this.scoreComputer" />
    
    <hr>
    <h1 v-html="this.question"></h1>
    
    <template v-for="answer in this.answers" :key="answer">
      <input :disabled="isSubmitted" type="radio"  v-model="this.choosenAnswer"  name="options" :value="answer">
      <label  v-html="answer">  </label>
      <br>
    </template>
    <p :class="wrongAnswer ? 'error_msg' : 'success_msg' "><strong>{{message}}</strong></p>
    
    
    <br>

    <button  v-if="choosenAnswer && !isSubmitted" class="send" @click="submitAnswer()">Save</button>
    <button v-if="isSubmitted" class="send" @click="getQuestion()">Next</button>
    <button  class="reset" @click="reset()">Reset</button>


  </div>
  <div v-if="!question">
    <p><strong style="color:red">Question Loading...</strong></p>
  </div>
</template>

<script>
import ScoreBoard from './components/ScoreBoard.vue'



export default {
  components: { ScoreBoard },
  
  
  name: 'App',
  data(){
    return {
      question: undefined,
      incorrectAnsers: undefined,
      correctAnser: undefined,
      choosenAnswer: undefined,
      wrongAnswer: undefined,
      message: undefined,
      scoreHuman:0,
      scoreComputer:0,
      isSubmitted : false
    }
    
  },
  watch: {
    // watching top-level property
    scoreHuman(val, oldVal) {
      console.log(`new: ${val}, old: ${oldVal}`)
      localStorage.setItem("quizgame_scoreHuman",val)
    },
    scoreComputer(val, oldVal) {
      console.log(`new: ${val}, old: ${oldVal}`)
      localStorage.setItem("quizgame_scoreComputer",val)
    },
  },
  methods : {
    getQuestion: function () {
        this.question = undefined;
        this.wrongAnswer = undefined;
        this.message = undefined;
        this.isSubmitted = false;
        this.axios.get("https://opentdb.com/api.php?amount=1&category=18").then((response) => {
        console.log(response.data.results);
        this.question = response.data.results[0].question;
        this.incorrectAnsers = response.data.results[0].incorrect_answers;
        this.correctAnser = response.data.results[0].correct_answer;

      })
    },
    reset: function () {
      localStorage.removeItem("quizgame_scoreHuman")
      localStorage.removeItem("quizgame_scoreComputer")
      this.getQuestion();
      this.scoreHuman = 0;
      this.scoreComputer = 0;
    },
    submitAnswer: function () {
      if (this.choosenAnswer) {
        if (this.choosenAnswer == this.correctAnser) {
          console.log("Your answer is correct");
          this.wrongAnswer=false;
          this.message= "Your answer is Correct";
          this.scoreHuman++;
          localStorage
        }else{
          this.wrongAnswer=true;
          console.log("Your answer is Wrong");
          this.message= "Your answer is wrong";
          this.scoreComputer++;
        }
        this.isSubmitted = true;
      }else{
        this.wrongAnswer=true;
        this.message= "Please select an option";
      }
    },
    
  },
  computed:{
      answers(){
        var answers = JSON.parse(JSON.stringify(this.incorrectAnsers));
        answers.splice(Math.round(Math.random() * answers),0,this.correctAnser)
        //answers.push(this.correctAnser)
        console.log(answers);
        return answers;   
      }
    },
  created(){
    this.getQuestion();
    this.scoreHuman = localStorage.getItem("quizgame_scoreHuman")?localStorage.getItem("quizgame_scoreHuman"):0;
    this.scoreComputer = localStorage.getItem("quizgame_scoreComputer")?localStorage.getItem("quizgame_scoreComputer"):0;
    //this.scoreComputer = localStorage.getItem("quizgame_scoreComputer")
  },updated(){
    //alert("data updated");
  }
  
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  max-width: 960px;
  margin:  60px auto;
  input[type=radio]{
    margin: 12px 14px;
  }
  button.send{
    cursor: pointer;
    padding: 5px 10px;
    background:blue;
    color:white;
    margin-right:5px;
  }
  
  button.reset{
    cursor: pointer;
    padding: 5px 10px;
    background:red;
    color:white;
    margin-right:5px
  }
  

}
.error_msg{
  color: red;
}
.success_msg{
  color: green;
}
</style>
