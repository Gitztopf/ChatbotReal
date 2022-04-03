<template>

  <div class="chat">
    <div class="textinput">
      <input v-model=chatInput @keyup.enter="sendChat()">
      <button class="material-icons">send</button>
    </div>  
  <div class="stateIndicator"> 
    Status: {{botstate}}
  </div>
    
  <div id="messageContainer">
    <div v-for="message,index in messageList" :key="index" class="chatmessage" :class="{'right':message.sender==='Gitztopf'}">
        <p class="senderText">{{message.sender}}</p>
        <p class="messageText">{{message.text}}</p>
    </div>
  </div>
    
  </div>
  

</template>

<script>


export default {
  name: 'App',
  data(){
    return{
      chatInput:"",
      botstate:"",
      messageList:[]
    }
  },
  methods:{

    
    getChat(){
      
      fetch("http://localhost:8000/botanswer").then((response)=>{
        return response.json()
      }).then(data=>{
        this.botstate=data.state
        this.messageList.push({'sender':'Benutzer','text':data.msg})
      })
      this.chatInput=""
    },

    sendChat(){
      this.messageList.push({'sender':'Gitztopf','text':this.chatInput})
      fetch("http://localhost:8000/respond",{method:'POST',headers:{'Content-Type':'application/json'}, body:JSON.stringify({'state':this.botstate,'cmd': this.chatInput})}).then((response)=>{
        return response.json()
      }).then(data=>{
        this.botstate=data.state??this.botstate
        this.messageList.push({'sender':'Bot','text':data.msg})
        this.chatInput=""
        
      }).then(()=>{
        document.getElementById('messageContainer').scrollIntoView(false);
      })

    }

    
  },
  mounted(){
    this.getChat()
  }


}
</script>

<style>

  @import url('https://fonts.googleapis.com/css2?family=Lato:wght@400;700;900&display=swap');
  *{
    font-family: 'Lato', sans-serif;
    margin:none;
    scroll-behavior: smooth;
  }
  .chat{
    padding:0;
    margin:0;
    min-height: 100%;
  
    background-image: url(assets/ChatBG.svg);
    background-size: contain;
    background-attachment: fixed;
    background-position-y: -100px;

  }
  p{
    margin: 0;
  }

  .right{
    align-self:  flex-end !important;
    background-color:#4285EA !important;
    color:white !important;
  }

  .right>.messageText{
    color: rgba(255,255,255,0.8) !important
  }

  #messageContainer{
    min-height: 100%;
    left:32px;
    right:32px;
    overflow-y:auto;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: flex-start;
    padding:32px;
    
  }
  .chatmessage{
    margin:8px;
    max-width: 70%;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    padding: 8px 16px 8px 12px ;
    width: max-content;
    border-radius: 8px;
    background-color:#FFFFFF;
    color: rgba(0,0,0,0.9)
  }

  .chatmessage > .messageText{
    color: rgba(0,0,0,0.8)
  }

  .chatmessage:last-of-type{
    margin-bottom:128px;
  }

  .senderText{
    font-weight: 700;
    font-size:1.25em;
  }
  .senderMessage{
    font-size: 1em;
  }
  .textinput{
    display: flex;
    gap:8px;
    align-items: center;
    justify-content: center;
    position:fixed;
    bottom:16px;
    max-width: 100%;
    left: 50%;
    transform: translateX(-50%);
    padding:8px;
  }

  .textinput > input{
    transition: all 0.25s ease-in-out;
    padding:16px;
    border-radius: 48px;
    border:none;
    outline: 1px solid lightgray;
    width:500px;
    font-size: 1.125em; 
  }

  .textinput >input:focus{
    outline:1px solid #4285EA;
  }

  .textinput > button{
    font-size:18px;
    border-radius: 48px;
    padding:16px;
    border:none;
    background-color:#4285EA;
    color:white;
  }

  .stateIndicator{
    top:16px;
    right:16px;
    padding:16px;
    color:#FFFFFF;
    background-color: #25BA85 !important ;
    border-radius: 8px;
    position:fixed;
  
  }



</style>
