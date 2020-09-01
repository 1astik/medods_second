<template>
    <div class="main">
        <h1>Simon Says</h1>
        <div class="main-body">
            <div class="game">
                <div>
                    <div class="box box-blue" id="blue" @click="setAnswer"></div>
                    <div class="box box-yellow" id="yellow" @click="setAnswer"></div>
                </div>
                <div>
                    <div class="box box-red" id="red" @click="setAnswer"></div>
                    <div class="box box-green" id="green" @click="setAnswer"></div>
                </div>
            </div>
            <div class="settings">
                <div>
                    <h2>Round: {{ count }}</h2>
                </div>
                <div>
                    <button @click="game">Start</button>
                </div>
                <div class="lose" v-if="lose">
                    <h3>Sorry your lose try again!</h3>
                </div>
                <div class="levels">
                    <h2>Game levels:</h2>
                    <label for="level1"><input type="radio" value="1.5" name="level" v-model="level" checked>Level 1(1.5sec)</label>
                    <label for="level2"><input type="radio" value="1" v-model="level" name="level">Level 2(1.0sec)</label>
                    <label for="level3"><input type="radio" value="0.4" v-model="level" name="level">Level 3(0.4sec)</label>
                </div>
            </div>
            <div class="music">
                <audio id="audio-blue" src="https://raw.githubusercontent.com/kellyk/javascript-simon/master/sounds/1.mp3"></audio>
                <audio id="audio-red" src="https://raw.githubusercontent.com/kellyk/javascript-simon/master/sounds/2.mp3"></audio>
                <audio id="audio-yellow" src="https://raw.githubusercontent.com/kellyk/javascript-simon/master/sounds/3.mp3"></audio>
                <audio id="audio-green" src="https://raw.githubusercontent.com/kellyk/javascript-simon/master/sounds/4.mp3"></audio>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "MainComponent",
        data() {
            return {
                count: 0,
                game_arr: [],
                level: '1.5',
                answer_arr:[],
                answer_id: 0,
                lose: false
            }
        },
        methods:{
            getBox(item){
                let box = '';
                switch (item) {
                    case 'blue':
                        box = 1;
                        break;
                    case 'red':
                        box = 2;
                        break;
                    case 'yellow':
                        box = 3;
                        break;
                    case 'green':
                        box = 4;
                        break;
                }

                return box
            },
            getId(item){
                let id_box = '';
                switch (item) {
                    case 1:
                        id_box = 'blue';
                        break;
                    case 2:
                        id_box = 'red';
                        break;
                    case 3:
                        id_box = 'yellow';
                        break;
                    case 4:
                        id_box = 'green';
                        break;
                }

                return id_box
            },
            setAnswer(e){
                const music = document.getElementById(`audio-${e.target.id}`);
                music.play();

                this.answer_arr.push(this.getBox(e.target.id));

                e.target.classList.add('active');

                setTimeout(() => {
                    e.target.classList.remove('active')
                }, 100);

                if ( this.answer_arr[this.answer_id] !== this.game_arr[this.answer_id] ) {
                    this.count = 0;
                    this.game_arr = [];
                    this.lose = true
                }
                this.answer_id++;

                if ( JSON.stringify(this.answer_arr) === JSON.stringify(this.game_arr) ) {
                    setTimeout(() => {
                        this.game()
                    }, 100);
                }
            },
            game(){
                this.lose = false;
                this.answer_id = 0;
                this.answer_arr = [];
                this.count++;
                this.game_arr.push(this.random());
                this.show_game_arr();
            },
            random(){
                let rand = 1 + Math.random() * (4 + 1 - 1);

                return Math.floor(rand);
            },
            async show_game_arr(){
                const time = Number(this.level)*1000;
                const that = this;
                setTimeout(() =>{
                    const length = this.game_arr.length-1;
                    const arr = this.game_arr;
                    (function iterate(i) {
                        let box =  document.getElementById(that.getId(arr[i]));
                        const music = document.getElementById(`audio-${box.id}`);
                        music.play();
                        box.classList.add('active');
                        setTimeout(function() {

                            box.classList.remove( 'active' );

                            if (i < length) {
                                setTimeout(()=>{
                                    iterate(i + 1);
                                }, time/2)

                            }
                        }, time/2);
                    })(0);
                }, time);

            }
        }
    }
</script>

<style lang="sass">
.main
    text-align: center
    box-sizing: border-box

    .main-body
        @media (max-width: 800px)
            justify-content: center
        padding-top: 2rem
        margin: 0 auto
        max-width: 600px
        display: flex
        justify-content: space-between

    & > h1
        font-size: 2.3rem

    .settings
        @media (max-width: 800px)
            font-size: .5rem
            margin-left: 2rem
        min-width: 25%
        max-width: 50%
        display: flex
        flex-flow: column
        align-items: flex-start

        .lose>h3
            margin-bottom: 0

        & button
            width: 5em
            box-sizing: border-box
            font-size: 1.4em
            -webkit-border-radius: 10px 10px 10px 10px
            border-radius: 10px 10px 10px 10px
            background: #6DABE8
            border: none
            padding: 0.3em 0.6em
            &:hover
                cursor: pointer
                background: #5682b1

        .levels
            & h2
                margin-bottom: 0
            display: flex
            flex-flow: column
            align-items: flex-start

    .game
        display: flex
        box-sizing: border-box


        .box
            @media (max-width: 800px)
                width: 75px
                height: 75px
            width: 150px
            height: 150px
            opacity: 50%
            box-sizing: border-box
            &:hover
                cursor: pointer



        .active
            opacity: 100%


        .box-blue
            background-color: blue
            &:hover
                border-left: 3px solid black
                border-top: 3px solid black
        .box-red
            background-color: red
            &:hover
                border-right: 3px solid black
                border-top: 3px solid black
        .box-yellow
            background-color: yellow
            &:hover
                border-left: 3px solid black
                border-bottom: 3px solid black
        .box-green
            background-color: green
            &:hover
                border-right: 3px solid black
                border-bottom: 3px solid black
</style>