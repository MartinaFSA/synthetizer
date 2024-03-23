<template>
    <div v-show="!isLoading" id="pageContainer">
        <section id="ctn_header">
            <h3>Don't forget to press play to record your session!</h3>
        </section>

        <section id="ctn_video">
            <!--Background is a static img-->
            <img src="../assets/musicStudio.png" alt="" draggable="false" class="video_musician" id="video_background">

            <!--Musicians playing are png gifs and static ones are a png img-->
            <img v-if="instruments.drums.isActive" src="../assets/playingBatterist.gif" alt="" draggable="false" class="video_musician screen_light">
            <img v-else src="../assets/staticBatterist.png" alt="" draggable="false" class="video_musician screen_light">

            <img src="../assets/playingGuitarist.gif" alt="" draggable="false" v-if="instruments.guitar.isActive" class="video_musician">
            <img v-else src="../assets/staticGuitarist.png" alt="" draggable="false" class="video_musician">

            <img src="../assets/playingSinger.gif" alt="" draggable="false" v-if="instruments.singer.isActive" class="video_musician">
            <img v-else src="../assets/staticSinger.png" alt="" draggable="false" class="video_musician">
        </section>
        
        <section id="ctn_btn">
            <div class="spaceBetween">
                <div class="spaceEvenly">
                    <!--Instruments-->
                    <div v-for="(instrument, index) in instruments" v-bind:key="index">
                        <img draggable="false" :src="getImage(instrument.name)" :alt=instrument.name>
                        <div class="beatSelector spaceEvenly">
                            <button v-for="(audio, index) in instrument.audios" v-bind:key="index" class="beatSelector" @click="selectInstrumentBeat(instrument, audio, $event)" aria-label="Press to play">{{ index }} </button>
                        </div>
                    </div>
                </div>
                <div>
                    <a class="actAsButton" @click="recordController()" tabindex="0">
                        <img draggable="false" v-if="isRecording" src="../assets/stopIcon.png" alt="Stop recording" aria-label="Press to stop recording">
                        <img draggable="false" v-else src="../assets/startIcon.png" alt="Start recording" aria-label="Press to start recording">
                    </a>
                </div>
            </div>
        </section>
    </div>

</template>

<script>
import instrumentsJson from '../assets/instruments.json'

import drums1 from '../assets/audios/drums1.ogg';
/*import drums2 from '../assets/audios/drums2.mp3';
import drums3 from '../assets/audios/drums3.mp3';
import drums4 from '../assets/audios/drums4.mp3';*/

import guitar1 from '../assets/audios/guitar1.ogg';
/*import guitar2 from '../assets/audios/guitar2.ogg';
import guitar3 from '../assets/audios/guitar3.ogg';
import guitar4 from '../assets/audios/guitar4.ogg';*/

import singer1 from '../assets/audios/singer1.ogg';
/*import singer2 from '../assets/audios/singer2.mp3';
import singer3 from '../assets/audios/singer3.mp3';
import singer4 from '../assets/audios/singer4.mp3';*/

import singerIcon from '@/assets/singerIcon.png';
import guitarIcon from '@/assets/guitarIcon.png';
import drumsIcon from '@/assets/drumsIcon.png';

export default {
    data() {
        return {
            isLoading: true,
            isRecording: false,
            instruments: ['empty'],
            blob: null, 
            deviceRecorder: null,
            chunks: [],
        }
    },
    created() {
        this.instruments = instrumentsJson;
        
        setTimeout(() => this.isLoading = false, 5500);
        
        this.instruments.drums.audios[0].fileName = drums1;
        /*this.instruments.drums.audios[1].fileName = drums2;
        this.instruments.drums.audios[2].fileName = drums3;
        this.instruments.drums.audios[3].fileName = drums4;*/

        this.instruments.guitar.audios[0].fileName = guitar1;
        /*this.instruments.guitar.audios[1].fileName = guitar2;
        this.instruments.guitar.audios[2].fileName = guitar3;
        this.instruments.guitar.audios[3].fileName = guitar4;*/

        this.instruments.singer.audios[0].fileName = singer1;
        /*this.instruments.singer.audios[1].fileName = singer2;
        this.instruments.singer.audios[2].fileName = singer3;
        this.instruments.singer.audios[3].fileName = singer4;*/
    },
    methods: {
        async recordController() {
            if (!this.isRecording) {
                //Start recording
                var stream =  await navigator.mediaDevices.getDisplayMedia(
                    {video: true, audio: true}
                );

                this.deviceRecorder = new MediaRecorder(stream, {mimeType: "video/webm"});

                this.deviceRecorder.ondataavailable = (e) => {
                    if(e.data.size > 0){
                        this.chunks.push(e.data);
                    }
                }
        
                this.deviceRecorder.start(250);
                this.isRecording = true;
            }
            else {
                //Stop recording
                var filename = window.prompt("How should your video be named?", "My amazing beats");

                this.deviceRecorder.stop();
                this.blob = new Blob(this.chunks, {type: "video/webm"})
                this.chunks = []
                var dataDownloadUrl = URL.createObjectURL(this.blob);

                let a = document.createElement('a')
                a.href = dataDownloadUrl;
                a.download = `${filename}.webm`
                a.click()
    
                URL.revokeObjectURL(dataDownloadUrl);
                
                this.isRecording = false;

            }
        },
        getImage(instrumentName) {  
            return '/src/assets/'+instrumentName+'Icon.png'  
        },
        selectInstrumentBeat(instrument, selectedAudio, element) {
            if(selectedAudio.isActive) {
                this.stopPlaying(selectedAudio);
                selectedAudio.isActive = false;
                element.target.classList.remove('selected');
                element.target.classList.add('unselected');
            }
            else {
                this.startPlaying(selectedAudio);
                selectedAudio.isActive = true;
                element.target.classList.remove('unselected');
                element.target.classList.add('selected');
            }

            //If there is at least one active audio, keep the video on
            let isThereActiveAudios = false;

            for (var i = 0; i < instrument.audios.length; i++) {
                if(instrument.audios[i].isActive) {
                    isThereActiveAudios = true;
                }
            }
            
            isThereActiveAudios ? instrument.isActive = true : instrument.isActive = false;

        },
        startPlaying(selectedAudio) {
            const audio = document.createElement("AUDIO");
            document.body.appendChild(audio);
            audio.setAttribute("controls", "controls");
            audio.setAttribute("src", selectedAudio.fileName);
            audio.setAttribute("class",'hidden');
            audio.setAttribute("id", selectedAudio.id);
            audio.loop = true;

            audio.play();
        },
        stopPlaying(selectedAudio) {
            const audio = document.getElementById(selectedAudio.id);
            audio.remove();
        }

    }
}
</script>

<style scoped>
    @import url('../assets/styles/songPlayer.css');
</style>