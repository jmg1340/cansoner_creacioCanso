<template>
  <q-page class="">

    <q-btn class="q-ma-sm" color="primary" label="Eliminar dades" noCaps @click="eliminarDades()"/>

    <div class="row q-ma-md q-gutter-xl">
      <div class="col-5" style="min-width: 0">
        <div class="column">
          
          <div class="row items-center items-center">
            <div class="col-2 text-negative text-negative">Idioma:</div>
            <div class="col">
              <q-select  v-model="canso.idioma" :options="opcionsIdioma" dense/>
            </div>
          </div>


          <div class="row items-center">
            <div class="col-2 text-negative">Titol:</div>
            <div class="col">
              <q-input dense  v-model="canso.titol" />
            </div>
          </div>

          <div class="row items-center">
            <div class="col-2 text-negative">Audio src:</div>
            <div class="col">
              <q-input dense  v-model="canso.audiosrc" />
            </div>
          </div>
 
          <div class="row items-center">
            <div class="col-3 text-negative">Estat ('nova' o res):</div>
            <div class="col">
              <q-input dense v-model="canso.estat" />
            </div>
          </div>

          <div class="row items-center">
            <div class="col-4 text-negative">Cansoner - llibre</div>
            <div class="col">
              <q-input dense v-model="canso.cansoner.nom" />
            </div>
          </div>

          <div class="row items-center">
            <div class="col-4 text-negative">Cansoner - numero</div>
            <div class="col">
              <q-input dense v-model="canso.cansoner.numero" />
            </div>
          </div>

          
          
          <!-- lletra de la cançó -->

          <div v-for="(obj, i) in lletraCanso" :key="i">
            <cmpParagraf 
              :obj="obj"
              :idx ="i"
              @eventNouTipus="handlerNouTipus"
              @eventNouParagraf="handlerNouParagraf"
              />
              <!-- {{i}} -->
          </div>
          <q-btn class="col-1 text-white bg-teal" label="Nou Paragraf" noCaps @click="nouParagraf()"/>
        
        
        </div>
        

      </div>

      <!-- INFORMACIO DEL OBJECTE RESULTANT -->
      <div class="col" style="min-width: 0">
        <pre>{{ canso2 }}</pre>
      </div>
      
    </div>



    <!-- <img
      alt="Quasar logo"
      src="~assets/quasar-logo-vertical.svg"
      style="width: 200px; height: 200px"
    > -->
  </q-page>
</template>

<script>
import { defineComponent, ref, computed } from 'vue';
import cmpParagraf from '../components/paragraf.vue'

export default defineComponent({
  name: 'PageIndex',
  components: { cmpParagraf },

  setup() {

    let canso = ref({
      idioma: {label: "CATALÀ", value: "CAT"},
      titol: null,
      audiosrc: "",
      estat: "",
      cansoner: {
        nom: null,
        numero: null
      },
      lletra: [
        // {
        //   tipus: "estrofa" / "tornada",
        //   paragraf: []
        // }
      ]
    })


    const eliminarDades = () => {
      canso.value = {
        idioma: {label: "CATALÀ", value: "CAT"},
        titol: "",
        audiosrc: "",
        estat: "",
        cansoner: {
          nom: "",
          numero: null
        },
        lletra: [
          // {
          //   tipus: "estrofa" / "tornada",
          //   paragraf: []
          // }
        ]
      }
      lletraCanso.value = []
    }



    const opcionsIdioma = ref ([
      {label: "CASTELLÀ", value: "ES"},
      {label: "CATALÀ", value: "CAT"}
    ])




    //--- LLETRA CANÇÓ -----

    const lletraCanso = ref([])

    const nouParagraf = () => lletraCanso.value.push({
      tipus: "estrofa",
      paragraf: ""
    })

    const handlerNouTipus = (objT) => {
      console.log("tipus", objT)
      lletraCanso.value[objT.index].tipus = objT.tipus
    }

    const handlerNouParagraf = (objP) => {
      console.log("objP", objP)

      let arr = objP.paragraf.split("\n")
      arr = arr.filter( (linia) => linia.length !== 0 )
      lletraCanso.value[objP.index].paragraf = arr
    }




    // ----- OBJECTE RESULTAT ------

    const canso2 = computed( () => {
      console.log("estic a GeneraObjecteCanso")

      let obj = {}

      obj[canso.value.idioma.value] = {
        titol: canso.value.titol,
        
        audio: ((src) => {
            console.log("estic dins funcio audio")
            if (src.length === 0) {
              return null
            } else {
              return [
                  {
                    src: src,
                    type: "audio/mp3"
                  }
                ]
            }
          })(canso.value.audiosrc),

        estat: canso.value.estat || null,
        cansoner: {
          nom : canso.value.cansoner.nom,
          numero : canso.value.cansoner.numero
        },
        lletra: lletraCanso.value
      }


      console.log("obj", obj)

      return obj
    } )




    return { 
      canso,
      eliminarDades,

      opcionsIdioma,
      lletraCanso,
      nouParagraf,
      canso2,

      handlerNouTipus,
      handlerNouParagraf
    }
  }
})
</script>
