<template>
  <div id="app">
    <form class="form_input">
      <input id="searchJoke" value="Введите слово для поиска анекдотов" size="100px">
    </form>
    <div class="container">
      <div class="joke_content">Анекдот 1</div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  data(){
    return {
      jokes: null ,
      category:'',
      answerToTheQuestion: {
        setup:'',
        delivery:''
      }
    }
  },
  components: {
    
  },
  async mounted(){
    let res = await this.requestJokes();
    this.jokes = res
    console.log(this.jokes);
  },
  methods: {
    async requestJokes() {
      let baseURL = "https://v2.jokeapi.dev";
      let params = {
          contains: '',
          amount: 10
      };
      let response = await fetch(`
          ${baseURL}/joke/Any?Contains=${params.contains}&amount=${params.amount}
        `)
          .then(req=>req.json())
          .then(data => data )
          .catch((error) => {
            console.error('Error:', error);
          });
          return response
    },
    divisionOfAnObject(){
      
    },
  },
}
</script>

<style>
#searchJoke{
  height: 50px;
  width: 99%;
  border: none;

}
.container {
  border: 1px solid #black;
  height: 700px;
  width: 500px;
  margin: 0 auto;
  border: 1px solid #black;
  border: #0b0b0a solid 2px;
}
.form_input{
  width: 500px;
  margin: 50px auto;
  margin-top: 50px;
  border: #0b0b0a solid 2px;
}
.joke_content{
  display: inline;
  width: 100%;
}
</style>
