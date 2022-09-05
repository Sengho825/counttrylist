<template>
  <v-app>
    

    <v-main>
      <v-container class="mt-8">
        <v-row>
          <v-col 
            cols="12"
            lg="4"
            md="5"
            sm="12"
            >
            <v-list three-line elevation="2" class="pa-2">
                <v-list-item
                 
                >
                  <v-list-item-avatar>
                    <v-img src="@/assets/profile.jpg" ></v-img>
                  </v-list-item-avatar>

                  <v-list-item-content>
                    <v-list-item-title>
                      <b>KIMSEA SENGHOU</b>
                    </v-list-item-title>
                    <v-list-item-subtitle>VueJS</v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
            </v-list>
            </v-col>
          <v-col 
          cols="12"
          lg="8"
          md="6"
          sm="12"
          >
          
          <v-text-field
          v-model="keyword"
            label="Search Country"
            outlined
          ></v-text-field>
          
          
          <div class="text-h6" v-if="keyword != ''">
            <v-divider/>
            Search Result For {{keyword}}
            
          </div>
          </v-col>
        </v-row>
      </v-container>
      <v-container class="mt-2">
        <v-data-table
        :headers="headers"
        :loading="is_loading"
        :items="responsesData"
        item-key="name.official"
        loading-text="Loading Data..."
      class="elevation-1"
      >
      <template v-slot:[`item.flags.png`]="{ item }">
        <v-avatar class="pa-2">
          <img
            :src=item.flags.png
            alt="John"
          >
        </v-avatar>
        
      </template>
      
    </v-data-table>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Axios from 'axios';
export default {
  name: 'App',

  components: {
    
  },

  data: () => ({
    responsesData:[],
    is_loading:false,
    keyword:''
  }),
  computed:{
    headers () {
        return [
          {
            text: 'Flag',
            align: 'center',
            sortable: true,
            value: 'flags.png',
          },
          
          {
            text: 'Name',
            align: 'start',
            sortable: true,
            value: 'name.official',
          },
          
          { text: '2 character Country Code', value: 'cca2',sortable: true, },
          { text: '3 character Country Code', value: 'cca3',sortable: true, },
          { text: 'Native Name', value: 'name.nativeName.zho.official' },
          { text: 'Alternative', value: 'altSpellings' },
          { text: 'Country Calling Codes', value: 'idd.root' },
        ]
      }
  },
  async created(){
    this.is_loading=true;
    await Axios.get("https://restcountries.com/v3.1/all").then((res)=>{
      this.responsesData = res.data
    });
    this.is_loading=false;
  },
  methods:{
    async entriesDebounced() {
    
    clearTimeout(this._timerId)

    this._timerId = setTimeout( async ()  => {
      this.is_loading=true;
      var url = `https://restcountries.com/v3.1/name/${this.keyword}`
      console.log(this.keyword == "")
      if(this.keyword != ""){
        url = `https://restcountries.com/v3.1/name/${this.keyword}`
      }else{
        url = `https://restcountries.com/v3.1/all`
      }
      console.log(url);
      await Axios.get(url).then((res)=>{
      this.responsesData = res.data
    });
    this.is_loading=false;
    }, 500)
  }
  },
  watch:{
    keyword:function(new_value){
      if (!new_value) {
        return
      }
      this.entriesDebounced();
    }
  }
};
</script>
