<template>
    <div class="choose-character">
       <div class="your-name" >
         <div class="txt-wrapper" :class="{focus:params.user_name}">
           <input type="text" v-model.trim="params.user_name" class="txt" :class="{hasValue:params.user_name}"  @blur="lostFocus" :maxlength="limit">
         </div>
         <!--<img src="/static/imgs/输入姓名@2x.png" class="no-name" @click="showInput" v-else>-->
       </div>
      <img src="/static/imgs/character/ball.png"  class="player-ball" onclick="return false;" v-show="is_attack">
      <img src="/static/imgs/character/attack.png"  class="player-attack" onclick="return false;" v-show="is_attack">
      <img  src="/static/imgs/character/defence-player.png" alt="player" class="player-defense animated slideInUp" onclick="return false;" v-show="!is_attack">
      <div class="arrows">
        <img src="/static/imgs/character/l-arrow@2x.png" alt="左箭头" class="l-arrow" @click="Arrow">
        <img src="/static/imgs/character/r-arrow@2x.png" alt="右箭头" class="r-arrow" @click="Arrow">
      </div>
      <div class="stage-wrapper">
        <img src="/static/imgs/character/stage@2x.png" alt="stage" class="stage" onclick="return false;">
      </div>
       <div class="type-btn">
         <img :src="btn_url" class="btn-img" onclick="return false;">
         <div class="attack-txt" @click="chooseAttack"></div>
         <div class="defence-txt" @click="chooseDefence"></div>
       </div>
       <img src="/static/imgs/toast.png" v-show="show_toast" class="toast">
      <img src="/static/imgs/character/enter-btn@2x.png" class="enter-btn" @click="toNext" onclick="return false">
      <img src="/static/video/video1.gif" class="video-attack" v-if="show_defence_video">
      <img src="/static/video/video1.gif" class="video-attack" v-if="show_attack_video">
      <audio src="/static/video/足球准备.mp3" ref="video_bgm" loop></audio>
    </div>
</template>

<script>
    import {mapState,mapActions} from 'vuex'
    export default {
      name: "Character",
      data(){
          return {
            params:{
              user_name:'',
              type:1
            },
            bg_url:'/static/imgs/输入姓名@2x.png',
            show_input:false,
            btn_url:'/static/imgs/character/type-chose@2x.png',
            player_url:'/static/imgs/群組1@2x_1.png',
            is_attack:true,
            show_toast:false,
            show_attack_video:false,
            show_defence_video:false,
             ball:'',
            man:'',
            beishu: 1,
            limit:3
          }
      },
      methods:{
        ...mapActions('common',['palyMusic','pauseMusic']),
        animate(){
          this.ball=anime({
            targets: '.player-ball',
            translateX:[80*this.beishu,0],
            translateY: [80*this.beishu,0],
            duration: 1000,
            scale: [0.5,1],
            rotate:'3turn',
            loop: false,
            easing: 'linear',
          })
          this.man=anime({
            targets: '.player-attack',
            translateX:[275*this.beishu,0],
            translateY: [467*this.beishu,0],
            duration: 1000,
            loop: false,
            easing: 'linear'
          })
        },
        showInput(){
            this.show_input=true
        },
        lostFocus(){
          if(this.params.user_name===''){
                this.show_input=false
          }

        },
        chooseAttack(){
          this.btn_url='/static/imgs/character/type-chose@2x.png'
          this.params.type=1
          this.is_attack=true
          this.ball.restart()
          this.man.restart()
        },
        chooseDefence(){
          this.btn_url='/static/imgs/character/type-chose2@2x.png'
          this.params.type=2
          this.is_attack=false
        },
        Arrow(){
          if(this.is_attack){
            this.chooseDefence()
          }else{
            this.chooseAttack()
          }
        },
        toNext(){
          /*进入下一页*/
          if (this.show_bg_music && this.params.user_name!=='') {
            console.log(document.querySelector('#video_bg2'))
            document.querySelector('#video_bg2').play()
          }
          if(this.params.type==1 && this.params.user_name!==''){
            document.getElementById('audio').pause()
            this.$refs.video_bgm.play()
            this.show_attack_video=true
            setTimeout(()=>{
              this.$refs.video_bgm.pause()
              this.$router.push({name:'Question1',query:{user_name:this.params.user_name,type:1}})
            },5748)
          }else if(this.params.type==2 && this.params.user_name!==''){
            document.getElementById('audio').pause()
            this.$refs.video_bgm.play()
            this.show_defence_video=true
            setTimeout(()=>{
              this.$refs.video_bgm.pause()
              this.$router.push({name:'Question1',query:{user_name:this.params.user_name,type:2}})
            },5748)
          }else{
            this.show_toast=true
            setTimeout(()=>{
                  this.show_toast=false
            },2000)
          }
        }
      },
      computed:{
        ...mapState('common',['show_bg_music']),
      },
      watch:{
        params:{
          handler:function(v){
            let reg = new RegExp("[\\u4E00-\\u9FFF]+","g");
            if(reg.test(v.user_name)){
              this.limit=4
            }else{
              this.limit=6
            }
          },
          deep:true
        }
      },
      mounted(){
        // 计算网页缩放的基数
        this.beishu = parseInt(document.documentElement.style.fontSize)/37.5;
        this.animate()
      }
    }
</script>

<style scoped lang="scss">
    .choose-character{
      width: 100%;
      height: 100%;
      background: url('/static/imgs/character/dir-line@2x.png') no-repeat center 80px;
      background-size: 146%;
      .your-name{
        width: 375px;
        height: 59px;
        position: absolute;
        top:16px;
        margin: auto;
        left:0;
        right: 0;
        text-align: center;
        z-index: 3;
        .no-name{
          width: 182px;
          height: 59px;
          display: inline-block;
        }
        .txt-wrapper{
          background: url('/static/imgs/输入姓名@2x.png') no-repeat center;
          background-size: 182px 59px;
          width: 182px;
          height: 59px;
          display: inline-block;
          padding-top: 14px;
          text-align: center;
          box-sizing: border-box;
          &.focus {
            background: url('/static/imgs/输入姓名@2x_1.png') no-repeat center;
            background-size: 182px 59px;
          }
          .txt{
            width: 140px;
            height: 30px;
            padding: 0 12px;
            box-sizing: border-box;
            border:none;
            text-align: center;
            font-size: 18px;
            opacity: 0;
            &:focus {
              opacity: 1;
            }
            &.hasValue {
              opacity: 1;
            }
          }
          input:focus { outline: none;background: #fff;border:none;}
        }

      }
      .player-ball{
        position: absolute;
        width: 75px;
        height: 75px;
        left:53px;
        top:74px;
      }
      .player-attack{
        width: 297px;
        height: 360px;
        display: block;
        position: absolute;
        margin: auto;
        top:89px;
        left:40px;
        right: 0;
        z-index: 1;
      }
      .player-defense{
        width: 375px;

        display: block;
        position: absolute;
        margin: auto;
        top:-45px;
        z-index: 1;
      }
      .arrows{
        width: 375px;
        height: 72px;
        display: flex;
        justify-content: space-between;
        position: absolute;
        top:190px;
        margin: auto;
        left:0;
        right: 0;
        z-index: 2;
        .l-arrow{
          width: 59px;
          height: 72px;
          margin-left: 12px;
        }
        .r-arrow{
          width: 61px;
          height: 68px;
          margin-right: 10px;
        }
      }
      .stage-wrapper{
        width: 375px;
        height: 83px;
        position: absolute;
        margin: auto;
        left:0;
        right: 0;
        bottom: 140px;
        z-index: 0;
        .stage{
          display: block;
          margin-left: 58px;
          width: 263px;
          height: 83px;
        }
      }
      .type-btn{
        width: 375px;
        height: 68px;
        position: absolute;
        margin: auto;
        left:0;
        right: 0;
        bottom: 80px;
        z-index: 3;
        .btn-img{
          width: 255px;
          height: 68px;
          margin-left: 60px;
        }
        .attack-txt{
          width: 100px;
          height: 30px;
          position: absolute;
          top:19px;
          left:84px;
        }
        .defence-txt{
          width: 100px;
          height: 30px;
          position: absolute;
          left:195px;
          top:19px;
        }
      }
      .enter-btn{
        width: 184px;
        height: 69px;
        margin: auto;
        position: absolute;
        bottom:12px;
        left:0;
        right: 0;
        z-index: 3;
      }
      .toast{
        position:absolute;
        bottom:70px;
        width: 239px;
        height: 67px;
        margin: auto;
        left:0;
        right: 0;
        z-index: 4;
      }
      .video-attack{
        position: absolute;
        width: 100%;
        height: 100%;
        margin: auto;
        left:0;
        right: 0;
        top:0;
        z-index: 10;
      }
    }
</style>
