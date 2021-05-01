<template>
  <q-page class="flex flex-center">
    <div class=" q-pt-lg">
      <div class="full-width text-center ">
        <img style="width:300px;" id="imagem" :src="img" />
      </div>
      <div class="full-width q-ma-md text-center" style="width:300px;">
        <q-file
          label="selecione uma imagem termográfica"
          outlined
          v-model="file"
          @input="carregaImagem"
        >
          <template v-slot:prepend> </template>
        </q-file>
        <br />
        <q-btn v-on:click="predict" color="primary" class="full-width"
          >Reconhecer</q-btn
        >
      </div>
      <div class="textoPredito">{{ predictedValue }}</div>
    </div>
  </q-page>
</template>
<style>
.textoPredito {
  white-space: pre-wrap;
  text-align: center;
  height: 200px;
}
</style>
<script>
import * as tf from "@tensorflow/tfjs";
import { fetch as fetchPolyfill } from "whatwg-fetch";
import covidExemplo from "assets/mama.jpg";
export default {
  name: "PageIndex",
  data() {
    return {
      predictedValue: "Clique em Reconhecer imagem",
      valueToPredict: "",
      model: tf.sequential(),
      file: null,
      img: covidExemplo,
      labels: ["ANOMALIA", "NORMAL"]
    };
  },

  mounted() {
    try {
      window.fetch = fetchPolyfill;
      this.carregar_modelo();
    } catch (error) {
      alert(error);
    }
    //URL.createObjectURL(file);
  },
  methods: {
    async carregar_modelo() {
      this.predictedValue = "Carregando Modelo ...";
      try {
        this.model = await tf.loadLayersModel("model/model.json");
      } catch (error) {
        alert(error);
      }
      this.predictedValue = "Modelo Carregado";
    },
    predict() {
      this.predictedValue = "Processando...";
      this.valueToPredict = document.getElementById("imagem");
      let arrInput = tf.browser.fromPixels(this.valueToPredict); //
      this.valueToPredict = tf.image
        .resizeBilinear(arrInput, [64, 64])
        .reshape([1, 64, 64, 3])
        .div(tf.scalar(255));
      let valor = "";
      try {
        valor = this.model.predict(this.valueToPredict);
      } catch (error) {
        alert(error);
      }

      //this.predictedValue = this.labels[valor.argMax(-1).dataSync()[0]];
      let pcentMama = (valor.dataSync()[0] * 100).toFixed(4);
      if (pcentMama > 0.5) {
        this.predictedValue = "NORMAL";
      } else {
        this.predictedValue = "ANOMALIA";
      }
      /*this.predictedValue =
        this.labels[valor.argMax(-1).dataSync()[0]] +
        "\n\n" +
        "ANOMALIA: " +
        pcentCovid +
        "%\n" +
        "NORMAL: " +
        pcentNormal +
        "%\n";*/
    },
    carregaImagem() {
      this.predictedValue = "";
      this.img = URL.createObjectURL(this.file);
      console.log(this.file);
    }
  }
};
</script>
