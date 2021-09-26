<template id="body">
  <div id="app">
    <h1>Java Artifact downloader</h1>
    <h3 id="descript">Download Artifacts by just providing the repo url!</h3>
    <input placeholder="Repo Url" id="urlBar" @keyup.enter="onGo" v-model="repoUrl">
    <br>
    <br>
    <br>
    <button id="glow-on-hover" type="button" @click="onGo">Go!</button>
    <component :is="boxComponent"></component>
  </div>
</template>
<script>




import Vue from "vue";
import axios from "axios";
export default {
  data() {
    return {
      repoUrl: "",
      jitpack: axios.create({
        baseURL: 'https://jitpack.io/',
        timeout: 60000,
      }),
      boxComponent: Vue.component('box')
    }
  },

  methods: {


    onGo() {
      //Parse the url
      try {
        let rawUrl = this.repoUrl.split("https://github.com/")[1].split("/");
        let username = rawUrl[0];
        let repo = rawUrl[1];
        if(repo.endsWith(".git")){
          repo = repo.replace(".git", "");
        }
        this.boxLoading();
        this.jitpack.get('com/github/' + username + '/' + repo+"/-SNAPSHOT").then(result => {
          let rawData = result.data.replaceAll(" ", "").split("\n")
          let newBox = `<div id="jars"><h3>Available Jars</h3> <ul>`

          for (let xy in rawData) {
            let line = rawData[xy].replaceAll("\r", "");
            if (line.endsWith(".jar") && !line.includes("sources.jar")){
              newBox = newBox + `<li><a href="${result.config.baseURL+result.config.url + '/' + line}">${line}</a></li>`
            }
          }

          newBox = newBox+`</ul></div>`;

          this.updateBox(newBox)
        })
      } catch (e) {
        this.boxError();
      }
    },


    boxLoading() {
      this.updateBox(`<h2>Loading your Repo and building....</h2>`);
    },

    boxError() {
      this.updateBox(`<h2>Error, please check the repo url, (or) Please try again later after a while</h2>`)
    },

    updateBox(x) {
      this.boxComponent = Vue.component('box', {
        template: x,
      })
    }
  },
  name: 'App',
}
</script>


<style>

@import url('https://fonts.googleapis.com/css2?family=Lobster&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&display=swap');

a:link {
  color: white;
  background-color: transparent;
  text-decoration: none;
  font-family: 'Bebas Neue', cursive;
  font-size: 30px;
  border-radius: 5px;
  border-spacing: 20px;
  box-sizing: border-box;
  transition: 1s;
  box-shadow: 5px 10px 8px 0 #31777d,
              -5px -5px 8px 1px #31777d;
}
a:link:hover {
  font-size: 35px;
  border-spacing: 21px;
  transition: 1s;
  box-shadow: 6px 12px 8px 0 #31777d,
  -7px -8px 8px 1px #31777d;
}

#jars ul {
  color: white;
  margin-top: 30px;
  list-style: none;
}

html, body {
  /*#253b4c*/
 background-color: #253b4c;
}
#urlBar {
  margin-top: 180px;
  height: 50px;
  width: 50%;
  font-family: Lobster,serif;
  font-size: 30px;
  border-radius: 10px;
  box-shadow: 5px 10px 8px 0 #245a65;
  transition: 1s;
}
#urlBar:hover {
  resize: both;
  height: 65px;
  width: 75%;
  box-shadow: 7px 15px 20px 0 #245a65;
  transition: 1s;
}

#urlGo {

}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #bddaff;
  margin-top: 50px;
}

#descript {
  margin-top: 40px;
}


#glow-on-hover {
  width: 220px;
  height: 50px;
  border: none;
  outline: none;
  color: #fff;
  background: #111;
  cursor: pointer;
  position: relative;
  z-index: 0;
  border-radius: 10px;
}

#glow-on-hover:before {
  content: '';
  background: linear-gradient(45deg, #00ff8c, #00e1ff, #006fff, #0015ff, #00ffd5, #002bff, #7a00ff, #ff00c8, #ff0000);
  position: absolute;
  top: -2px;
  left:-2px;
  background-size: 400%;
  z-index: -1;
  filter: blur(5px);
  width: calc(100% + 4px);
  height: calc(100% + 4px);
  animation: glowing 20s linear infinite;
  opacity: 0;
  transition: opacity .3s ease-in-out;
  border-radius: 10px;
}

#glow-on-hover:active {
  color: #000
}

#glow-on-hover:active:after {
  background: transparent;
}

#glow-on-hover:hover:before {
  opacity: 1;
}

#glow-on-hover:after {
  z-index: -1;
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
  background: #111;
  left: 0;
  top: 0;
  border-radius: 10px;
}



</style>
