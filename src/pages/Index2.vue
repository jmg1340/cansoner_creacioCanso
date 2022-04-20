<template>
  <q-page class="">

    <div class="row q-ma-sm q-gutter-md">
      <div class="col-5 ">
        <q-btn class="q-ma-sm" color="negative" label="Eliminar dades" noCaps @click="eliminarDades()"/>
      </div>
      <div class="col ">
        <q-bar class="row items-between bg-cyan-2">
          <div class="row q-gutter-xl">
            <q-radio v-model="opcioEtiqPRE" val="canso" label="Tota la cançó" color="" />
            <q-radio v-model="opcioEtiqPRE" val="lletra" label="Només la lletra" color="" />
            <q-radio v-model="opcioEtiqPRE" val="audio" label="Només audio" color="" />
          </div>        
          <div class="col text-right">
            <q-btn class="q-mx-sm" color="orange" label="Copiar" noCaps @click="copiarDades()"/>
          </div>
        </q-bar>
      </div>
    </div>
    
    
    <div class="row q-ma-sm q-gutter-md">
      <div class="col-5 " >
        <div class="column">
          
          <q-card v-if="opcioEtiqPRE !== 'lletra'" class="q-pa-md q-mb-md">
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
              <div class="col-3 text-negative">Estat:</div>
              <div class="col">
                <q-select  v-model="canso.estat" :options="opcionsEstat" dense/>
                <!-- <q-input dense v-model="canso.estat" /> -->
              </div>
            </div>

            <div class="row items-center">
              <div class="col-4 text-negative">Cansoner - llibre</div>
              <div class="col">
                <q-select  v-model="canso.cansoner.nom" :options="opcionsCansoner" dense/>
                <!-- <q-input dense v-model="canso.cansoner.nom" /> -->
              </div>
            </div>

            <div class="row items-center">
              <div class="col-4 text-negative">Cansoner - numero</div>
              <div class="col">
                <q-input dense v-model="canso.cansoner.numero" />
              </div>
            </div>
          </q-card>
          
          
          <!-- lletra de la cançó -->

					<q-card>
						<q-input dense  v-model="txtAreaLletra" filled type="textarea" autogrow/>
					</q-card>
        </div>
        

      </div>

      <!-- INFORMACIO DEL OBJECTE RESULTANT -->
      <div class="col ">
        <pre v-if="opcioEtiqPRE == 'canso'">{{ canso2 }}</pre>
        <pre v-else-if="opcioEtiqPRE == 'lletra'">{{ lletraCanso }}</pre>
        <pre v-else>{{ audio2 }}</pre>
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
import { copyToClipboard } from 'quasar'

import cmpParagraf from '../components/paragraf.vue'

export default defineComponent({
  name: 'PageIndex',
  components: { cmpParagraf },

  setup() {

    let canso = ref({
      idioma: {label: "", value: null},
      titol: null,
      audiosrc: "",
      estat: {label: "", value: null},
      cansoner: {
        nom: {label: "", value: null},
        numero: null
      },
      lletra: [
        // {
        //   tipus: "estrofa" / "tornada",
        //   paragraf: []
        // }
      ]
    })

		let txtAreaLletra = ref(``);

    let opcioEtiqPRE = ref("canso");


    const eliminarDades = () => {
      canso.value = {
        idioma: {label: "", value: null},
        titol: "",
        audiosrc: "",
        estat: {label: "", value: null},
        cansoner: {
          nom: {label: "", value: null},
          numero: null
        },
        lletra: [
          // {
          //   tipus: "estrofa" / "tornada",
          //   paragraf: []
          // }
        ],
				txtAreaLletra: ""
      }
      lletraCanso.value = []
    }

    const copiarDades = () => {
      copyToClipboard(document.querySelector('pre').innerHTML)
      .then(() => {
        // success!
      })
      .catch((error) => {
        console.log("error al copiar", error)
      })      
    }

    const opcionsIdioma = ref ([
      {label: "CASTELLÀ", value: "ES"},
      {label: "CATALÀ", value: "CAT"}
    ])

    const opcionsCansoner = ref ([
      {label: "BLAU", value: "blau"},
      {label: "VERMELL", value: "vermell"}
    ])

    const opcionsEstat = ref ([
      {label: "NOVA", value: "nova"},
      {label: "", value: null}
    ])




    //--- LLETRA CANÇÓ -----

    const lletraCanso = computed ( () => {
      
      let arrTxtArea = txtAreaLletra.value.split("\n")
      
      let arrParagraf = []
      let arrParagrafs = arrTxtArea.reduce ( (acc, linia, index, arr) =>{

        linia = linia.replace(/[\t]+/g, '')  // treiem les \t si en te
        linia = linia.trim()
        console.log("==========================")
        console.log("LINIA: ", "'"+linia+"'")

        
        if (index === arr.length - 1  ) {  // si és ultima linia  
          console.log("estic al ULTIM  element de arr i arrParagraf.length no és zero")
          if (linia.length !== 0) arrParagraf.push(linia)
          console.log("index:", index, "arrParagraf:", arrParagraf )
          acc.push(arrParagraf)
          return acc

        } else if (linia.length !== 0) {
          arrParagraf.push(linia)
          console.log("linia.length !== 0")
          console.log("index:", index, "arrParagraf:", arrParagraf )
          return acc

        } else {
          console.log("ELSE")
          console.log("index:", index, "arrParagraf:", arrParagraf )
          if (arrParagraf.length !== 0) acc.push(arrParagraf)
          arrParagraf = []
          console.log("index:", index, "arrParagraf:", arrParagraf )
          return acc
        }

        

      }, [])
      
      

      /* Per cada paragraf creem un objecte
          {
            tipus: "estrofa" / "tornada"
            paragraf: [paragraf]
          }
      */

      console.log("arrParagrafs", arrParagrafs)

      
      const arrLletra = arrParagrafs.map( arrParagraf => {
        let arrP = []
        if (arrParagraf.length !== 0   &&   arrParagraf[0].toLowerCase() === "t" ){
          arrP = arrParagraf.slice(1)   // el paragraf serà array a partir de la segona linia
        } else {
          arrP = arrParagraf
        }

        return { 
          tipus: (arrParagraf.length !== 0   &&   arrParagraf[0].toLowerCase() === "t") ? "tornada" : "estrofa" ,
          paragraf: arrP
        }
      })

      
      return arrLletra
    })


    const funcAudio = (src) => {
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
    }

    const audio2 = computed( () => { 
      return funcAudio(canso.value.audiosrc)
    })

      


    // ----- OBJECTE RESULTAT ------

    const canso2 = computed( () => {
      console.log("estic a GeneraObjecteCanso")

      let obj = {}

      obj[canso.value.idioma.value] = {
        titol: canso.value.titol,
        
        audio: funcAudio(canso.value.audiosrc),

        estat: canso.value.estat.value || null,
        cansoner: {
          nom : canso.value.cansoner.nom.value,
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
      copiarDades,

      opcionsIdioma,
      opcionsCansoner,
      opcionsEstat,
      lletraCanso,
      // nouParagraf,
      canso2,
      audio2,
			txtAreaLletra,

      // handlerNouTipus,
      // handlerNouParagraf

      opcioEtiqPRE
    }
  }
})
</script>


<style scoped>
  .borde{
    border: 2px solid red;
  }
</style>