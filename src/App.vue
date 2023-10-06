<script>
import axios from "axios";

export default {
    data() {
      return {
        keyword: "",
        resultList: [],
        message: 'Wikipedia Viewer',
        buttonText: "search"
      }
    },
    methods: {
      async search() {
        try {
          this.resultList = null;
          const searchTerm = this.keyword;
          const api = 'https://en.wikipedia.org/w/api.php?format=json&action=query&origin=*&generator=search&gsrnamespace=0&gsrlimit=10&prop=pageimages|extracts&pilimit=max&exintro&explaintext&exsentences=1&exlimit=max&gsrsearch=';
          const page = 'https://en.wikipedia.org/?curid=';

          const res = await axios.get(api+searchTerm);
          const { data } = await res;
          const results = await data.query.pages;
          const list = await Object.values(results)
          list.forEach(d => {
            if (!this.resultList) this.resultList = [];
            this.resultList.push({
              pageid: d.pageid,
                title: d.title,
                body: d.extract,
                page: page + d.pageid
              })
            });
          } catch (error) {
            console.log(error.message);
          }
      },
      mounted() {
        this.search();
      },
      watch: {
        keyword() {
          this.search();
        }
      }
    }
  };
</script>

<template>
  <div id="app">
    <section id="app_wrapper">
      <div id="app_container">
        <h1>{{message}}</h1>
        <p>Enter the keyword to browse</p>
        <form @submit.prevent="search">
          <input v-model="keyword" class="form_input" />
          <button
                  type="submit"
                  class="button form_submit"
                  >
            {{buttonText}}
          </button>
        </form>
        <a
           href='https://en.wikipedia.org/wiki/Special:Random'
           target='_blank'
           class="button surprise"
           >
          surprise
        </a>
      </div>
      <div class="logo">
        <img src="https://www.kamiviolet.site/lazy_uranus.svg" class="uranus">
      </div>
    </section>
    <p v-if="!resultList" class="loading_status">Loading...</p>
    <ul v-else class="list">
      <li v-for="result in resultList" :key="result.pageid">
        <a href={{result.page}} target='_blank'  class="list_item">
         <h2>{{result.title}}</h2>
         <p>{{result.body}}</p>
        </a>
       </li>
     </ul>
  </div>
</template>

<style>
  * {
    --blue: #092B40;
    --lightBlue: #85DEF2;
    --water: #52A5D9;
    --sky: #5BB5D9;
    --orange: #D96F32;
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  
  body {
    min-height: 100vh;
    width: 100vw;
    background: radial-gradient(ellipse at top, var(--blue), transparent),
            radial-gradient(ellipse at bottom, var(--sky), transparent)
      ;
    overflow: auto;
    display: grid;
    place-items: center start;
    @media (width > 900px) {
      background: radial-gradient(ellipse at top, var(--blue), transparent),
            radial-gradient(ellipse at bottom, var(--sky), transparent), url('https://www.kamiviolet.site/lazy_uranus.svg')
      ;
      background-attachment: fixed;
      background-position: center;
      background-size: 100%;
    }
  }
  
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    text-align: center;
    color: #333;
    display: grid;
    place-items: center;

    height: fit-content;
    width: 100vw;
    margin-block: 3em;
  }
  
  #app_wrapper {
    display: grid;
    -webkit-backdrop-filter: blur(5px);
    backdrop-filter: blur(5px);
    align-items: first-baseline;
    justify-items: center;
    grid-template-rows: auto auto auto 30%;
    grid-template-columns: 1fr;
    width: 90%;
    max-width: 900px;
    background-color: rgba(255,255,255,0.45);
    box-shadow: slategray 1px 1px 10px;
    padding-block: 2em;

    @media (width > 900px) {
      grid-template-columns: 1fr 1fr;
    }
  }
  
  #app_container {
    display: grid;
  }

  a,
  button {
    color: #333;
    text-decoration: none;
  }
  
  h1 {
    line-height: 1.5em;
    font-size: 2.5em;
  }
  
  h2 {
    font-size: 1.5em;
    padding-block-end: 1em;
  }
  
  p {
    padding-block-end: 1em;
    font-size: 1em;
  }
  
  .form_input {
    height: 2.15em;
    width: 100%;
    font-size: 1.15em;
    margin-block: .75em;
  }

  .button {
    border: none;
    font: inherit;
    padding: 0.75em 2em;
    cursor: pointer;
    align-self: end;   
    font-size: 1em;
    border: gray 1px solid;
  }
  
  .uranus {
    object-fit: contain;
    width: 85%;
    height: auto;
    align-self: center;
    display: none;

    @media (width> 900px) {
      display: block;
    }
  }
  
  .form_submit {
    margin-block: 1em;
    background-color: #ccc;
    
    &:hover {
      background-color: var(--orange);
      color: white;
    }
  }
  
  .surprise {
    margin-block: 2.25em;
    background-color: var(--water);
    color: white;
    
    &:hover {
      color: black;
      background-color: var(--lightBlue);
    }
  }
  
  ul {
    list-style-type: none;
    display: grid;
    width: 100%;
    max-width: 900px;
    place-items: center;
  }
  
  li {
    width: inherit;
    margin-block-start: 2em;
  }

  .list_item {
    background-color: rgba(255,255,255, 0.8);

    width: inherit;
    display: grid;
    text-align: left;
    color: black;
    border-inline-start: 5px whitesmoke solid;
    padding-block: 1em;

    * {
      padding-inline: 15px;
    }

    &:hover {
      border-inline-start: orange 5px solid;
    }
  }
  
  .loading_status {
    width: 100%;
    max-width: 900px;
    padding-block: 2em;
    margin-block: 1em;
    font-weight: 700;
    background-color: rgba(255,255,255, 0.8);
  }
</style>