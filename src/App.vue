<template>
  <div id="app">
    <form class="form_input">
      <input id="searchJoke" 
             placeholder="Введите слово для поиска анекдотов" 
             size="100px"
             v-model="contains"
      >
    </form>
    <div class="container">
      <div class="joke_content" v-for="(joke,idx) in jokes" :key="idx">
        <div class="card">
          <div>
            <p class="category"> {{ joke.category }} </p> 
            <span v-if="joke.joke"> {{idx+1}}). {{ joke.joke }} </span> 
            <span v-else> {{idx+1}}). {{ joke.setup }}   <p>{{ joke.delivery }}</p> </span> 
          </div>        
          <i class="fa fa-thumbs-up icon" :style="{color: activeColor}" @click="getLike"></i>
        </div>

      </div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  data(){
    return {
      activeColor: 'grey',
      locationData: null,
      contains:'',
      jokes: null ,
      category:'',
      answerToTheQuestion: {
        setup:'',
        delivery:''
      }
    }
  },
  created(){
    const localData = localStorage.getItem('joke');

    console.log("create");
    this.locationData = Object.fromEntries(
      new URL(window.location).searchParams.entries()
    );
    if(this.locationData['Contains']){
        this.contains = this.locationData['Contains'];
    }
   
    console.log(JSON.parse(localData))
  },

  async mounted(){
  console.log('mount');
    let res = await this.requestJokes();
    this.jokes = res.jokes;
    this.category = res.category;
    console.log({...this.jokes});
  },
  watch: {
    contains(newValue){
      localStorage.setItem('joke', JSON.stringify(newValue))
      window.history.pushState(
        null, 
        document.title,
        `${window.location.pathname}?Contains=${newValue}`
      )
    },
  },
  methods: {
    getLike(){
      this.activeColor = 'green';
    },
    async requestJokes() {
      let baseURL = "https://v2.jokeapi.dev";
      let params = {
         amount: 10
      };
      let response = await fetch(`
         ${baseURL}/joke/Any?Contains=${this.contains}&amount=${params.amount}
       `)
         .then(req=>req.json())
         .then(data => data )
         .catch((error) => {
           console.error('Error:', error);
         });
      return response
    }
  },
}
</script>

<style>
* {
  box-sizing: border-box;
}
#searchJoke{
  height: 50px;
  width: 99%;
  border: none;

}
.category{
  margin:2px;
  font-size:10px;
  font-weight: 700;
  color: red;
}
.card > span {    
  margin-left: 5px;
}
.card{
  border: 1px solid black;
  margin: 15px;
  height: 60px;
  overflow-y: scroll;
  display: flex;
  justify-content: space-between;
}
.card > div > span > p {
  margin: 5px 20px;
  font-size: 12px
}


.container {
  overflow: scroll;
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
.icon{
  font-size:24px;
  color: grey
}
</style>
