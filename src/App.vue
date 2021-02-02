<template>
    <body>
    <label for="maxCP" class="max-cp">
        <input type="checkbox" id="maxCP" v-model="checked"/>
        <small>
            Maximum Combat Points
        </small>
    </label>
    <input v-model="message" type="text" class="input" placeholder="Pokemon or type" /> 
    <div class="loader" v-show="loading"></div>
    <ul class="suggestions">
    <!-- to list of pokemon -->
      {{result}}  
      <!-- if it doesn't have any list  -->
        <li v-if="pokemon.length < 1">
            <img
                src="https://cyndiquil721.files.wordpress.com/2014/02/missingno.png"
                alt="No results"
            />
            <div class="info">
                <h1 class="no-results">
                    No results
                </h1>
            </div>
        </li>     
        <!-- list pokemon info with key i.id -->
        <li v-for="i in pokemon" v-bind:key="i.id">
            <img
                :src=i.img
                :alt=i.Name
            />
            <div class="info">
                <!-- to highlight my input, it needs v-html for <span> tag. If I write {{i.Name}} it treat <span> tag as a string-->
                <h1><p v-html="i.Name"></p></h1>
                <h1><p>{{i.MaxCP}}</p></h1>
                <span class="type" v-bind:class="i.Types[0].toLowerCase()">{{i.Types[0]}}</span>
                <span v-if="i.Types.length > 1" class="type normal">{{i.Types[1]}}</span>
            </div>
        </li>        
    </ul>
    </body>
</template>

<script>

export default {
    name: 'app',
    data() {
        return {
            info: [],
            pokemon: [],
            copied: [],
            message: '',
            checked: false,
            loading: true,
            mystring: '',
        }
    },
    computed: {
        result: function() {
            
            if(this.message !== '' && this.info !== null) {
                //if the new message is typed
                if(this.message) {
                    //the pokemon list is []
                    // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                        this.pokemon = [];
                        //and i.Name will be resest
                        // eslint-disable-next-line vue/no-side-effects-in-computed-properties
                        this.info = JSON.parse(this.copied);
                    }
                    // map this.info to check if the list of this.info is matching with input message
                    this.info.map(i => {
                        if(i.Name.toLowerCase().includes(this.message.toLowerCase()) || i.Types.includes(this.message)){
                            //to list maximum four pokemon
                            if(this.pokemon.length < 4) {
                                //highlight the part of name if the search matches
                                var find = this.message.toLowerCase();
                                var regex = new RegExp(find);
                                i.Name = i.Name.toLowerCase().replace(regex, "<span class='hl'>" + find + "</span>");
                            
                                this.pokemon.push(i); 
                                    
                                //if Maximum Combat Points is checked, sort them by MaxCP 
                                if(this.checked === true ) {
                                    console.log("true")
                                    this.pokemon = this.pokemon.sort(function(a,b) {
                                        if( a.MaxCP < b.MaxCP) return 1;
                                        if ( a.MaxCP === b.MaxCP) return 0;
                                        if( a.MaxCP > b.MaxCP) return -1;
                                    })
                                }
                            }
        
                            this.pokemon.filter((item, index) => this.pokemon.indexOf(item) === index);
                          }
                    })
                }
            return null;
        }
    },
    mounted() {
        //get the data from JSON url
         const URL_PATH =
                'https://gist.githubusercontent.com/bar0191/fae6084225b608f25e98b733864a102b/raw/dea83ea9cf4a8a6022bfc89a8ae8df5ab05b6dcc/pokemon.json';
            this.$http.get(`${URL_PATH}`)
            .then((result) => {
                this.info = result.data; 
                // copy the data to reset this.info when I type the input message 
                // since the result.data will be asigned as call by reference, it needes to be assigned as JSON.stringfy(result.data)
                this.copied = JSON.stringify(result.data)
            return this.info
            })

    },
    watch: {
        //to show loader while data is receiving
        pokemon: function(newVal, oldVal) {
            this.mystring = oldVal + "downloaded"
            if(this.mystring === '') {
                this.loading = true;
            } else {
                this.loading = false;
            }
        }
    }
};
</script>
