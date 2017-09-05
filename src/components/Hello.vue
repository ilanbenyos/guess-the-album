<template>
  <div class="main ">
    <h1>{{ msg }}</h1>
        <div v-if="!showQuestion" class="card flex col ">
          <button @click="generQuestion()" >start</button>
        </div>
        
        <div v-else class="card flex col center">
            <div  class=" width5 section col center" >
                <div v-if= 'currentQuestion && currentQuestion.album' class=" flex col center margin-b ">
                    <div class=" " >score: {{score}} </div> 
                    <div  class="  margin-b" >
                        <img id="center " :src="currentQuestion.album.artworkUrl100" alt="The Pulpit Rock" width="304" height="228">
                    </div>
                    <div class="  " >artist: {{currentQuestion.album.artistName}} </div>
                </div>
            </div>
            <div  class=" width5 section flex row center" v-if="showQuestion">
              <div  class="item btns-pnl" v-for="(item,idx) in artists">
                    <button @click="makeChose(item.fName)" >{{item.fName}}</button>
              </div>
            </div>
      </div>


        <div  class=" flex col section margin-b">
            <div  class=" flex row center margin-b">
                <div class=" width15 " >dev statistics</div>
            </div>
            <div  class=" flex row  margin-b">
                <div class=" width15 " >currentQuestion.wrongs:{{currentQuestion.wrongs}} </div>
                <div class=" width15 " >currentKombo: {{currentKombo}} </div>
                <div class=" width15 " >komboCount: {{komboCount}} </div>
            </div>
            <div  class=" flex row  margin-b">
                <div class=" width15 " >total: {{rounds.total}} </div>
                <div class=" width15 " >right: {{rounds.right.length}} </div>
                <div class=" width15 " >wrong: {{rounds.wrong.length}} </div>
            </div>
        </div>



  </div>
</template>
<script>
import axios from 'axios'



export default {
  name: 'hello',
  data () {
    return {
      msg: 'Welcome to Your game',
      maxRounds:10,
      albums :[],
      rounds:{right:[],wrong:[],total:0},
      score:0,
      komboCount:0,
      currentQuestion : {},
      currentKombo :0,
      showQuestion:false,
      artists:[ {fName:'Maya Buskila'},
                {fName:'Eyal Golan'},
                {fName:'The Beatles'},
                {fName:'U2'},
                {fName:'Eagles'},
                ]
    }
  },
  mounted () {
      this.search();
  },
methods:{
    initGame(){
      this.rounds= {right:[],wrong:[],total:0}
      this.score=0;
      this.komboCount=0;
      this.currentQuestion={};
      this.currentKombo=0;

      this.generQuestion();
  },
  makeChose(artistName){
    console.log (artistName);
    // var res = false
          //==========================RIGHT ANSWER=========================
    if(artistName ==this.currentQuestion.album.artistName){
        this.rounds.right.push(true)
        // res =true;
        if (this.currentQuestion.wrongs==0) {
          this.score +=10;
        }else if (this.currentQuestion.wrongs==1) {
          this.score +=5;
        }else if (this.currentQuestion.wrongs==2) {
          this.score +=2;
        }else{

        }
        //===============KOMBO======================
          if(this.currentKombo ===2){
                this.currentKombo =0;
                this.komboCount ++;
                //=====KOMBO BONUS SQR=========
                console.log('combo bonus')
                this.score+=Math.pow(10, this.komboCount+1);
           }else{ 
                this.currentKombo ++
           }
          this.generQuestion()
    }else{  
      //==========================WRONG ANSWER=========================
        
        this.rounds.wrong.push(false);
        this.currentQuestion.wrongs ++;
     
      //============3 WRONG ANSWERs=============
        if (this.currentQuestion.wrongs >=3){
          this.currentKombo =0;       //init current combo cuont
          this.komboCount =0;         //init komboCount
          this.score -= 5;
          this.generQuestion()
        }else{
        }
    }

    this.checkLog()
  },
  checkLog(){
        console.log('checkLog:rounds.total' ,this.rounds.total)
        console.log('checkLog.maxRounds:' ,this.maxRounds)
      if(this.rounds.total>=this.maxRounds){
        console.log('score:' ,this.score)
        var res = confirm('your score is '+ this.score + ' would you like to start a new game?');
            if (res == true) {
                this.initGame();
            } else {
                alert( "You pressed Cancel!");
            }
      }
  },
  generQuestion(){
    var answersInQuestion = 5
        var idx =this.getRand(0, this.albums.length);
        var album = this.albums[idx]
    this.currentQuestion = {album, wrongs:0};
    this.rounds.total ++
    this.showQuestion = true;
  },
  getRand(min,max){
        return Math.floor(Math.random() * max)  + min
},
deleteUnWanted(arr,txt){
      var arr1 = arr.filter(function(artist){
        return txt == artist.artistName;
      });   
      return arr1; 
},
  search(){
    var arr = this.artists;
    for (var i = 0; i < arr.length; i++) {
        var obj = arr[i];
          var txt = (obj.sName)? obj.fName + '+'+ obj.sName: obj.fName ;
          this.search1(obj.fName,obj.sName)
    }
  },
  search1(txt){
      var that = this
      axios.get(  `https://itunes.apple.com/search?term=${txt}`)
            .then(function(data){
              var albums = that.albums.concat(data.data.results);
              albums = that.deleteUnWanted(albums,txt)
              that.albums = that.albums.concat(albums);
              
              console.log(that.albums[0])
          })
  }
}
}
</script>

<style scoped>
.card{
  width:90%;
  margin:auto;
}
.flex {
  display:flex;
}
.row{
    flex-direction: row
}
.col{
    flex-direction: column
}
.item{
  border:1px solid green;
  margin-bottom:5px;
}
.width7{
  width:7em;
}
.width15{
  width:15em;
}
.width25{
  width:25em;
}
.section{
  border:1px solid red;
  margin-top : 1em;
}
.margin-b{
  margin-bottom: 1em;
}
.center{
  justify-content: center;
}
button{
  font-size:1.5em;
  margin-left:0.3em;
}
</style>
