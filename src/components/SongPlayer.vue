<template>
    <div>
        <section id="ctn_header">
            <h2 class="contentSize">Don't forget to press play to record your session!</h2>
        </section>

        <section id="ctn_video">
            <!--Background is a static img-->
            <img draggable="false" src="../assets/musicStudio.png" class="video_musician" id="video_background">

            <!--Musicians playing are png gifs and static ones are a png img-->
            <img draggable="false" v-if="instruments.drums.isActive" src="../assets/playingBatterist.png" class="video_musician screen_light">
            <img draggable="false" v-else src="../assets/staticBatterist.png" class="video_musician screen_light">

            <img draggable="false" src="../assets/playingGuitarist.png" v-if="instruments.guitar.isActive" class="video_musician">
            <img draggable="false" v-else src="../assets/staticGuitarist.png" class="video_musician">

            <img draggable="false" src="../assets/playingSinger.png" v-if="instruments.singer.isActive" class="video_musician">
            <img draggable="false" v-else src="../assets/staticSinger.png" class="video_musician">
        </section>
        
        <section id="ctn_btn">
            <div class="spaceBetween contentSize">
                <div class="spaceEvenly">
                    <!--Instruments-->
                    <div v-for="(instrument, index) in instruments" v-bind:key="index">
                        <img draggable="false" :src="'./src/assets/'+instrument.name+'Icon.png'" :alt=instrument.name>
                        <div class="beatSelector spaceEvenly">
                            <button v-for="(audio, index) in instrument.audios" v-bind:key="index" class="beatSelector" @click="selectInstrumentBeat(instrument, audio, $event)" aria-label="Press to play">{{ index }} </button>
                        </div>
                    </div>
                </div>
                <div>
                    <a class="actAsButton" @click="recordController(isRecording)">
                        <img draggable="false" v-if="isRecording" src="../assets/stopIcon.png" alt="Start recording" aria-label="Press to start recording">
                        <img draggable="false" v-else src="../assets/startIcon.png" alt="Stop recording" aria-label="Press to stop recording">
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
/*import guitar2 from '../assets/audios/guitar2.mp3';
import guitar3 from '../assets/audios/guitar3.mp3';
import guitar4 from '../assets/audios/guitar4.mp3';*/

import singer1 from '../assets/audios/singer1.ogg';
/*import singer2 from '../assets/audios/singer2.mp3';
import singer3 from '../assets/audios/singer3.mp3';
import singer4 from '../assets/audios/singer4.mp3';*/



export default {
    data() {
        return {
            isRecording: false,
            instruments: ['empty']
        }
    },
    created() {
        this.instruments = instrumentsJson;
        
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
        recordController(isRecording) {
            if (isRecording) {
                //start recording
                this.isRecording = false
            }
            else {
                //stop recording
                this.isRecording = true
            }
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

            //if there is at least one active audio, keep the video on
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

            var playPromise = audio.play();
            if (playPromise !== undefined) {
                playPromise.then(function() {
                    console.log('playing')
                }).catch(function(error) {
                    console.log('error: ' + error)
                    // Automatic playback failed.
                    // Show a UI element to let the user manually start playback.
                });
            }
        },
        stopPlaying(selectedAudio) {
            const audio = document.getElementById(selectedAudio.id);
            audio.remove();
        }

    }
}
</script>

<style>
    @import url('../assets/songPlayer.css');
</style>