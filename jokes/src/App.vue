<template>
  <div id="app">
    <form class="form_input">
      <input id="searchJoke" 
             placeholder="Введите слово для поиска анекдотов" 
             size="100px"
             v-model="contains"
      />
    </form>
    <div class="container">
      <div 
        class="joke_content" 
        v-for="(joke,idx) in jokes" 
        :key="idx"
      >
        <div class="card">
          <div>
            <p class="category"> 
              {{ joke.category }} 
            </p>

            <span v-if="joke.joke"> 
              {{idx+1}}). {{ joke.joke }} 
            </span> 

            <span v-else> 
              {{idx+1}}). {{ joke.setup }}   <p>{{ joke.delivery }}</p> 
            </span> 
          </div>        
          <i class="fa fa-thumbs-up icon" @click="getLike(idx)"></i>
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
      savedJokes:{},
      activeColor: false,
      locationData: null,
      contains:null,
      jokes: null ,
      category:'',
      answerToTheQuestion: {
        setup:'',
        delivery:''
      }
    }
  },
  created(){
    this.locationData = Object.fromEntries(
      new URL(window.location).searchParams.entries()
    );
    if(this.locationData['Contains']){
        this.contains = this.locationData['Contains'];
    }   
  },

  mounted() {
    this.contains='';
    let res =  this.get_jokes();
    this.jokes = res.jokes;
    this.category = res.category;
  },

  watch: {
    contains(newValue){
      this.get_jokes(newValue);
      window.history.pushState(
        null, 
        document.title,
        `${window.location.pathname}?Contains=${newValue}`
      )
    },
  },
  methods: {
    getLike(index){
      this.activeColor = !this.activeColor;

      let dom_elem_icon = document.querySelectorAll(".icon");
      if (this.activeColor === true){
        return dom_elem_icon[index].style.color = 'gray';
      } else {
        dom_elem_icon[index].style.color = 'green';
          console.log(this.jokes[index]);
          const result = {};
          Object.keys(this.jokes[index]).forEach(key => result[key] = this.jokes[index][key]);
          this.savedJokes = {...this.jokes[index]};
          let div = document.createElement('div')
          div.className = "saved_joke";
          // if( !result.joke ){
          //   document.createElement('div').innerHTML = `<div> result.setup : <p>  joke.delivery  </p> </div>`;
          // } else {
          //   document.createElement('div').innerHTML = `<div> result.joke </div>`;
          // }
          // document.body.append(div)
      }
    },

     async get_jokes() {
      let baseURL = "https://v2.jokeapi.dev";
      let params = {
         amount: 10
      };
      let response = await fetch(`
         ${baseURL}/joke/Any?Contains=${this.contains}&amount=${params.amount}
       `);
        if (response.ok) { 
          let data = await response.json();
          this.jokes = data.jokes;
        } else {
          console.error("Ошибка HTTP: " + response.status);
        }
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
  width: 100%;
  border: none;

}
.category{
  margin:7px 0 0 7px;
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
  align-items: center;
}
.card > div {
  height: 60px;
}
.card > div > span > p {
  margin: 5px 20px;
  font-size: 12px;
}


.container {
  overflow: scroll;
  border: 1px solid black;
  height: 700px;
  width: 500px;
  margin: 0 auto;
  border: 1px solid black;
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

*::-webkit-scrollbar  {
  width: 6px;
}

*::-webkit-scrollbar-track {
  -webkit-box-shadow: 5px 5px 5px -5px rgba(34, 60, 80, 0.2) inset;
  background-color: #f9f9fd;
  border-radius: 10px;
}
*::-webkit-scrollbar-thumb{
  border-radius: 10px;
  background: linear-gradient(180deg, #ffffff, #6a6262);
}
</style>
