<template>
    <div class="container">
        <div class="header">
            <br><hr>
            <h1><span>Work</span> {{title}} </h1>
            <br> 
            <hr>

            <div class="header-setting"> <!-- setting btn -->
                
                <i v-if="audio" class="bi bi-music-note"></i>   
                {{audio}} <span v-if="audio">||</span> Timer setting 
                
                <i  @click="setting = !setting" :class="{active: setting && !showStopBtn}" class="header-setting-icon bi bi-gear-wide-connected"></i>

                
                <div v-if="setting && !showStopBtn" class="header-wrapper">         
                    <!-- <div class="work" v-if="!showStopBtn">
                        <i @click="timeWork = timeWork - 1" v-if="timeWork >= 1" class="bi bi-dash"></i>
                        <i v-if="timeWork < 1" class="bi bi-dash"></i>
                            Work
                        <i @click="timeWork = timeWork + 1" class="bi bi-plus"></i>
                    </div> -->
                    <!-- <div class="chill" v-if="!showStopBtn">
                        <i @click="timeChill = timeChill - 1" v-if="timeChill >= 1" class="bi bi-dash"></i>
                        <i v-if="timeChill < 1" class="bi bi-dash"></i>
                            Chill
                        <i @click="timeChill = timeChill + 1"  class="bi bi-plus"></i>
                    </div> -->

                    <div class="audio">
                        
                        <form @submit.prevent='onSubmit' class="audio-form">

                            <label for="customRange1" class="form-label">Work time <span>{{timeWork}}</span> min</label>
                            <input type="range" class="range" id="customRange1" min="1" max="120" v-model="timeWork">
                            <hr>

                            <label for="customRange1" class="form-label">Chill time <span>{{timeChill}}</span> min</label>
                            <input type="range" class="range" id="customRange1" min="1" max="120" v-model="timeChill">
                            <hr>
                            
                            <div class="form-check form-switch">
                                <label class="form-check-label" for="flexSwitchCheckDefault">Loop</label>
                                <input class="form-check-input" type="checkbox" role="switch" id="flexSwitchCheckDefault" v-model="loopTime">
                            </div>
                            <hr>

                            <label class="audio-form-label" for="audioSelected">Sound</label>
                            <select @change="onSubmit" class="audio-form-select" v-model="audioSelected" id="audioSelected" >
                                <option class="audio-form-option" disabled >Select the background sound</option>
                                <option class="audio-form-option" value="0">Silence</option>
                                <option class="audio-form-option" value="1">Rain</option>
                                <option class="audio-form-option" value="2">Forest</option>
                            </select>

                        </form>
                    </div>
                </div>
                
            </div> <!-- setting btn -->
            
        </div>
        
            
        <div class="wrapper"> <!-- ///////////// timer -->
            <div :class="{countWorkActive: arrowWork}" class="work-count wrapper-item">
                {{timeWork}}
                 
                <span>Min</span>

                <div v-if="arrowWork" :class="{arrowActive: arrowWork}" class="arrow arrow_work">
                    <div class="arrow-hide">

                    </div>
                </div>
            </div>
            <div :class="{countChillActive: arrowChill }" class="chill-count wrapper-item">
                {{timeChill}}
                
                <span>Min</span>

                <div v-if="arrowChill" :class="{arrowActive: arrowChill }" class="arrow arrow_chill">
                    <div class="arrow-hide">

                    </div>
                </div>
            </div>
        </div> <!-- ///////////// timer -->
        <div class="gowork"> <!-- /////////////button main -->
            <button @click="goWork" :disabled='timeChill === 0 || timeWork === 0 ' v-if="!showStopBtn" type="button" class="btn btn-success">Let's Work</button>
            <button @click="stopTimer" v-if="showStopBtn" type="button" class="btn btn-warning">Stop</button>
        </div> <!-- ///////////// button main -->
    </div>
</template>


<script>
export default {
    name: 'header-item',
    data() {
        return{
            title: 'Chill Timer',
            date: new Date(),
            timeWork: 0,
            timeChill: 0,
            remembserTimeWork: 0,
            remembserTimeChill: 0,
            startTimerWork: null,
            startTimerChill: null,
            showStopBtn: false,
            setting: false,
            ranger: 0,
            arrowWork: false,
            arrowChill: false,
            loopTime: false,
            audio: null,
            audioBack: null,
            audioWorkDone:  require("@/assets/audio/zvuk.mp3"),
            audioChillDone:  require("@/assets/audio/zvuk.mp3"),
            audioBacksound:  null,
            audioSelected: null,
            audioLibrary:[
                {
                    "id":"0",
                    "path": null,
                    "name": "Silence"
                },
                {
                    "id":"1",
                    "path": require("@/assets/audio/rain.mp3"),
                    "name": "Rain"
                },
                {
                    "id":"2",
                    "path": require("@/assets/audio/forest.mp3"),
                    "name": "Forest"
                }
            ]


        }
    },
    methods:{
        updateTimer(){
            setInterval(() => {
                this.date = new Date()
            }, 500)
        },
        goWork(){ //////////start timer work
            this.audioBackground()
            this.setting = false
            this.arrowWork = true
            this.arrowChill = false
            this.remembserTimeWork = this.timeWork
            this.remembserTimeChill = this.timeChill
            this.showStopBtn = true
            this.startTimerWork = setInterval(() => {
                this.timeWork = this.timeWork - 1
                if(this.timeWork < 1){
                    this.timeWork = this.remembserTimeWork
                    clearInterval(this.startTimerWork)

                    let audio = new Audio(this.audioWorkDone)
                        audio.play();

                    this.goChill()
                }
            },60000)
        },
        goChill(){ //////////start timer chill
            
            this.arrowChill = true
            this.arrowWork = false
            this.remembserTimeChill = this.timeChill
            this.startTimerChill = setInterval(() => {
                this.timeChill = this.timeChill - 1
                if(this.timeChill < 1){
                    this.timeChill = this.remembserTimeChill
                    clearInterval(this.startTimerChill )

                    let audio = new Audio(this.audioChillDone)
                        audio.play();
                    
                    if(this.loopTime === true){   //////chek loop timer
                        this.goWork()

                    } else this.stopTimer()
                    
                }
            },60000)

        },
        stopTimer(){ //////////stop timer work && reset timeout
            this.showStopBtn = false
            clearInterval(this.startTimerWork)
            this.startTimerWork = null
            clearInterval(this.startTimerChill)
            this.startTimerChill = null
            this.timeWork = this.remembserTimeWork
            this.timeChill = this.remembserTimeChill

            this.arrowChill = false
            this.arrowWork = false
            // this.audioBack.currentTime = 0;
            
                this.audioBack.currentTime = 0;
                this.audioBack.pause();
                this.audioBack = null
                this.audioSelected = this.audioLibrary[0].path
            
        },
        audioBackground(){
            if (this.audioBacksound !== null){
                this.audioBack = new Audio(this.audioBacksound)
                this.audioBack.loop = true; 
                this.audioBack.play()
            } 
        },

        onSubmit(){
            this.audioBacksound = this.audioLibrary[this.audioSelected].path
            this.audio = this.audioLibrary[this.audioSelected].name

            
        }
    },
    created(){
        this.updateTimer()
    },
    mounted(){
        if(localStorage.timeWork){
            this.timeWork = JSON.parse(localStorage.timeWork)
        }
        if(localStorage.timeChill){
            this.timeChill = JSON.parse(localStorage.timeChill)
        }
        if(localStorage.audioSelected){
            this.audioSelected = JSON.parse(localStorage.audioSelected)
        }
        if(localStorage.audioBacksound){
            this.audioBacksound = JSON.parse(localStorage.audioBacksound)
        }
        if(localStorage.audio){
            this.audio = JSON.parse(localStorage.audio)
        }
        if(localStorage.loopTime){
            this.loopTime = JSON.parse(localStorage.loopTime)
        }
    },

    watch:{
        timeWork:{
            handler(newtimeWork){
                localStorage.timeWork = JSON.stringify(newtimeWork)
            },
            deep: true
        },
        timeChill:{
            handler(newtimeChill){
                localStorage.timeChill = JSON.stringify(newtimeChill)
            },
            deep: true
        },
        loopTime:{
            handler(newLoop){
                localStorage.loopTime = JSON.stringify(newLoop)
            },
            deep: true
        },
        audioSelected:{
            handler(newAudioSelected){
                localStorage.audioSelected = JSON.stringify(newAudioSelected)
            },
            deep: true
        },
        audioBacksound:{
            handler(newAudioBacksound){
                localStorage.audioBacksound = JSON.stringify(newAudioBacksound)
            },
            deep: true
        },
        audio:{
            handler(newAudio){
                localStorage.audio = JSON.stringify(newAudio)
            },
            deep: true
        },

    }


}
</script>

<style lang="scss" scoped>
h1{
    font-weight: 600;
    font-family: 'Comfortaa', sans-serif;
    span{
        color: var(--bs-danger);
    }
}
.active{
    &::before{
        transition: 0.3s all;
        transform: rotateZ(90deg);
    }
}
.bi{
    cursor: pointer;
}
/////////////////
.divider{
    // background-color: rebeccapurple;
    // width: 100%;
    // height: 30px;
}
.form-check{
    display: flex;
    display: flex;
    /* row-gap: 10px; */
    justify-content: space-around;
    padding: 0;
}
/////////////////////@at-root
////////////////////////

.header {
    color: var(--bs-green);
    position: relative;
    &-setting{
        position: absolute;
        right: 0;
        font-size: 20px;
        color: #000;
       
        &-icon{
            &::before{
                transition: 0.3s all;
            }
        }
    }
    &-wrapper{
        position: absolute;
        width: 180px;
        right: 30px;
        border: 1px solid #000;
        border-radius: 5px;
        background: #fff;
        z-index: 10;
        padding: 5px 0 5px 0;
        span{
            color: #8d2b34;
        }
    }
}
.wrapper{
    display: flex;
    justify-content: center;
    column-gap: 30px;
    margin-top: 50px;
    &-item{
        // border: 1px solid #000;
        border-radius: 100%;
        width: 300px;
        height: 300px;
        min-width: 100px;
        min-height: 100px;
        display: flex;
        justify-content: space-around;
        align-items: center;
        font-size: 60px;
        position: relative;
        text-shadow: 2px 2px 0px #000;
        font-weight: 600;
        span{
            position: absolute;
            bottom: 31%;
            left: 50%;
            font-size: 35px;
            z-index: -1;
            color: #000;
            opacity: 0.6;
            text-shadow: none;
            font-weight: 500;
            font-family: 'Comfortaa', sans-serif;
        }
    }
}

.work-count{
    border: 3px solid var(--bs-danger);
    color: var(--bs-danger);
}
.chill-count{
    border: 3px solid var(--bs-teal);
    color: var(--bs-teal);
}

.arrow{
    position: absolute;
    height: 95%;
    width: 2px;     
    z-index: -2;
    transform: rotateZ(0deg);

    &-hide{
        position: absolute;
        height: 75%;
        width: 3px;
        background: #fff;
        right: -50%;
        top: 25%;
        box-shadow: 3px 3px 3px 5px #fff;

    }
    &_work{
        border-radius: 3px;
        background: rebeccapurple;
        // transform: rotateZ(270deg);
    }
    &_chill{
        border-radius: 3px;
        background: var(--bs-orange);
        // transform: rotateZ(270deg);
    }
}

.arrowActive{
    animation: arrow 60s  infinite linear;
    box-shadow: 3px 1px 4px #8d2b34;
    transition: 0.3s all;
}

.countWorkActive{
    animation: countActive 5s  infinite linear;
    box-shadow: 7px 0px 3px;
    transition: 0.3s all;

}
.countChillActive{
    animation: countActive 5s  infinite linear;
    box-shadow: 7px 0px 3px;
    transition: 0.3s all;

}


@keyframes countActive{
    0%{
        box-shadow: 7px 0px 3px, -7px 0px 3px;
    }
    25%{
        box-shadow: 0px 7px 3px, 0px -7px 3px;
    }
    50%{
        box-shadow: -7px 0px 3px, 7px 0px 3px;
    }
    75%{
        box-shadow: 0px -7px 3px, 0px 7px 3px;
    }
    100%{
        
        box-shadow: 7px 0px 3px, -7px 0px 3px;
    }
}

@keyframes arrow{
    0%{
        transform: rotateZ(0deg);
    }
    100%{
        transform: rotateZ(360deg);
    }
}

.audio{
    &-form{
        width: 100%;
        &-select{
        width: 95%;
        margin-bottom: 5px;
        }
        &-label{
            margin-bottom: 5px;
        }
        &-option{
            width: 100px;
        }
    }
}

@media(max-width: 765px){
    .wrapper{
        flex-direction: column;
        row-gap: 15px;
        align-items: center;
        // margin-bottom: 30px;
        margin: 70px 0 30px 0;
        &-item{
            width: 200px;
            height: 200px;
            span{
                bottom: 21%;
                left: 50%;
            }
        }
    }
}

</style>